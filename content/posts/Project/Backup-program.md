---
title: Migration_java_program
date: 2019-11-01 19:37:57
tags: [socket, migration, database]
categories:
 - [project]
---
Autor : 오 훈

Title : Backup-program



#   Backup-program ([GitHub Link](https://github.com/guriOH/backup-program.git))

## 1. 시스템 구성

![](/image/backup_program/1.png)

###  1.1 역할 

#### Master

- 0.1초 주기로 Random 하게 생성되는 정수 값을 TimeStamp와 함께 DB에 저장.
- 소켓 연결 시 1초 단위로 꺼내서 데이터 전달.

#### Slave 

- 소켓 통신을 이용하여 DataSource로부터 데이터를 가져옴.
- 설정한 back up DB에 데이터 저장.  




### 1.2 개발 방법

- 다중 Slave 접속을 위한 멀티 쓰레드 활용
- ScheduledExecutorService를 이용한 Thread trigger
- Slave 세션별 Offset맵핑을 통한 데이터 유실 보안



## 2. Master 상세 설명

![스크린샷 2019-08-26 오후 11.21.21](/image/backup_program/2.png)



Master는 위와 같은 데이터 플로우를 가지고 있습니다.

코드의 실행은 다음과 같습니다.

1. **Generator 실행**

   Master 프로그램은 데이터를 생성하는 중요한 프로그램입니다. 
   따라서 멀티 유저와의 소켓 연결시 예측할수 없는 에러로 부터 최대한 분리하여야 하기때문에 별도의 Thread로 실행합니다.
   즉, 유저의 연결과 상관없이 Master가 실행이 되면 데이터 생성 및 적재를 시작합니다.

2. **SocketService** 실행
   SocketService는 다중 연결 접속을 지원합니다.
   먼저 SocketService는 소켓서버를 생성합니다.
   이후 Client(Slave)가 연결이 되면,  WorkerPool(쓰레드풀)에서  Thread를 생성하여 해당 소켓 연결을 별도의 Thread로 동작 시킵니다.
   Thread실행 후, 다른 소켓 연결을 대기합니다. 
   ​

#### **2.1 코드 설명** ![스크린샷 2019-08-28 오전 1.15.52](/image/backup_program/3.png)

### 2.1.1 ServerStarter

```java
public class ServerStarter {
	private final static Logger logger = LogManager.getLogger(ServerStarter.class);
	
	private static void Start(AppProperties props) {
		// 데이터 제너레이터 실행
		Generator generator = new Generator(props); 
		generator.start();
		
        //소켓 서비스 실행
		SocketService service = new SocketService();
		service.initialize(props);
		service.start();
	}
	
	public static void main(String[] args) {
		String propFile = "";
		for(String l_arg : args) { 
            /*
              =/Users/hoon/pjt/project/backup-program/p1/slave/src/main/resources/config/app.properties
            */
			if(l_arg.indexOf("") > -1) {
				propFile = l_arg.split("=")[1];
				
				AppProperties props = new AppProperties();
				props.initialize();
				props.loadConfig(propFile);
				
				
				Start(props);
			} else {
				logger.info("Put properties location");
			}
		}
		logger.info("There are no properties info");
	}
```

프로그램의 실행을 위해 설정 파일의 Path를 읽어와야 합니다.

만일 Path가 없거나 args 인자가 없다면 실행하지 않습니다.



### 2.1.2. Generator.class

```java
public class Generator{
	private final static Logger logger = LogManager.getLogger(Generator.class);
	private AbstractRepositoryManager repositoryManager = null;
	private String target_data_table = "myData";
	
	public Generator(AppProperties props) {
		this.repositoryManager = new PostgresqlRepositoryManager(props); 
		if(props.getPropsMap().get(Constant.DB_TARGET_TABLE_NAME) != null){
    		target_data_table = props.getPropsMap().get(Constant.DB_TARGET_TABLE_NAME);
    	}
	}
	.
    .
	.
}
```

Generator는 생성자로 부터 설정정보 객체를 전달 받아 DB커넥션 정보를 얻기 위한 RepositoryManager, 데이터 적재를 위한 테이블 네이밍 값을 초기화 합니다.



```java
public void start() {
		try{
			final Connection con = repositoryManager.getConnection();
			logger.info("DataGenerator DB connection set");
			
			if (!repositoryManager.isExist(con, target_data_table)){
                //테이블이 존재하지 않는다면 생성.
				repositoryManager.createTargetDataTable(con, target_data_table);
			}
			
			Runnable runnable = new Runnable() {
				String query = "INSERT INTO "+target_data_table+"(value, created) VALUES(?, ?)";
				
	            public void run() {
	            	try {
	            		Random rf = new Random();
						PreparedStatement pst = con.prepareStatement(query);
						pst.setInt(1,rf.nextInt());
						pst.setTimestamp(2, new Timestamp(System.currentTimeMillis()));
						pst.executeUpdate();
					} catch (Exception e) {
						e.printStackTrace();
					}
	            }
	        };
	        ScheduledExecutorService service = Executors.newScheduledThreadPool(1);
	        service.scheduleAtFixedRate(runnable, 0, 100, TimeUnit.MILLISECONDS);
		
		}catch (Exception e) {
			logger.error(e.getMessage());
		}finally {
		}
	}
```

Generator의 수행 로직입니다. 

1. Connection 생성.
2. 테이블 생성.
3. 주기적으로 Insert를 실행시켜 데이터를 생성.

주기적인 Thread 로직 수행을 위해 ScheduledExecutorService 클래스를 사용하였습니다.
자세한 이유는 뒤에서 설명하겠습니다.



### 2.1.3. SocketService.class

```java
public class SocketService {
	private final static Logger logger = LogManager.getLogger(SocketService.class);
	private WorkerPool workerPool;
	private AbstractRepositoryManager repositoryManager = null;
	private int DEFAULT_PORT_NUM = 20000;
	private int port;
	private String sync_table = "sync_info";
	private ServerSocket _serverSocket;
	
	public SocketService() {}

	public void initialize(AppProperties props) {
		workerPool = new WorkerPool(props);
		this.repositoryManager = new PostgresqlRepositoryManager(props);
		if(props.getPropsMap().get("PORT") != null){
			port = Integer.valueOf(props.getPropsMap().get("PORT"));
		}else{
			port = DEFAULT_PORT_NUM;
		}
		
		if(props.getPropsMap().get(Constant.DB_SYNC_TABLE_NAME) != null){
			sync_table = props.getPropsMap().get(Constant.DB_SYNC_TABLE_NAME);
    	}
		
		try {
			repositoryManager.createSyncDataTable(sync_table);
		} catch (SQLException e) {
			e.printStackTrace();
		};

	}

	public void start() {
		try {
			_serverSocket = new ServerSocket(port);
			Socket l_socket = null;
			while(true) {
				l_socket = _serverSocket.accept();
				Sender l_worker = (Sender) workerPool.getWorker();
				l_worker.setSocket(l_socket);
				
				ScheduledExecutorService service = Executors.newSingleThreadScheduledExecutor();
				service.scheduleAtFixedRate(l_worker, 0, 1000, TimeUnit.MILLISECONDS);
			}
		} catch (Exception e) {
			logger.error(e.getMessage());
		}
	}

}
```

SocketService는 slave와의 소켓 통신을 위한 소켓서버 역할을 합니다.

SocketService 초기화시 Port번호와 Sync_table이 생성됩니다.

먼저 설정된 Port 가 있다면 해당 Port로 초기화 하고, 이후 Start() 메소드에서 해당 Port 번호로 소켓서버를 생성합니다.

sync_table은 데이터 유실을 막기위한 Client별 데이터 Offset정보를 가지는 테이블의 이름입니다.



소켓연결 방법은 아래와 같습니다.

1. 소켓서버 연결대기 상태
2. WorkerPool에서 Thread(Worker)생성
3. 소켓 연결 
4. Thread(Worker) 실행
5. 다시 1번 부터 반복


Slave(Client)로 주기적인 데이터 전송을 위해 ScheduledExecutorService를 사용하였습니다.



### 2.1.4 WorkerPool.class

```java
public class WorkerPool {
	private final static Logger logger = LogManager.getLogger(WorkerPool.class);
	private List<AbstractWorker> senderList = null;
	
	private final Object lockObject = new Object();
	private int activeThreadCount = 0;
	
	private AppProperties props;
	private AbstractRepositoryManager repositoryManager = null;
	
	
	public WorkerPool(AppProperties props) {
		this.senderList = new ArrayList<AbstractWorker>();
		this.props = props;
		this.repositoryManager = new PostgresqlRepositoryManager(props);
		logger.debug("WorkerPool is created.");
	}

	public List<AbstractWorker> getSenderList() {
		return senderList;
	}

	public void setSenderList(List<AbstractWorker> senderList) {
		this.senderList = senderList;
	}

	public int getActiveThreadCount() {
		return activeThreadCount;
	}

	public void setActiveThreadCount(int activeThreadCount) {
		this.activeThreadCount = activeThreadCount;
	}
	
}
```

WorkerPool은 현재 생성된 Thread의 상태 또는 갯수를 관리 합니다.



```java
public AbstractWorker getWorker() {
		AbstractWorker l_worker = null;
		
		synchronized (lockObject) {
			try {
				l_worker = (AbstractWorker)Class.forName("org.opensource.master.socket.Sender")
						.getConstructor(AppProperties.class, AbstractRepositoryManager.class)
						.newInstance(props, repositoryManager);
				l_worker.setId("Test");
				this.senderList.add(l_worker);
				this.activeThreadCount = this.senderList.size();
			} catch(Exception e) {
				e.printStackTrace();
			} 
		}
		
		return l_worker;
}
```

Master의 소켓서비스는 다중 연결을 지원하기때문에, Thread 생성시 데드락 방지를 위해 synchronized를 사용합니다.

Thread객체인 Sender를 생성하며, 생성된 Thread를 리스트에 담고, 사용 Thread 갯수를 최신화 합니다.

위 정보는 소켓 연결이 끊어지거나 Thread에 문제가 있을경우, Thread 컨드롤에 사용하기 위해 추가 하였습니다.

하지만 현재 기능을 개발은 못하였습니다.



### 2.1.4 Sender.class

```java
public class Sender extends AbstractWorker{

	private final static Logger logger = LogManager.getLogger(Sender.class);
	private AbstractRepositoryManager repositoryManager = null;
	private int LIMIT = 100;
	private Socket _socket;
	
	private String target_data_table = "myData";
	private String sync_table = "sync_info";
	
	String slaveID = "";
	ObjectInputStream ois = null;
	ObjectOutputStream oos = null;
	Connection con = null;
	public Sender(AppProperties props, AbstractRepositoryManager repositoryManager) {
		this.repositoryManager = repositoryManager; 
		if(props.getPropsMap().get(Constant.DB_SYNC_TABLE_NAME) != null){
			sync_table = props.getPropsMap().get(Constant.DB_SYNC_TABLE_NAME);
    	}
		if(props.getPropsMap().get(Constant.DB_TARGET_TABLE_NAME) != null){
    		target_data_table = props.getPropsMap().get(Constant.DB_TARGET_TABLE_NAME);
    	}
		if(props.getPropsMap().get(Constant.DB_DATA_LIMIT) != null){
			LIMIT = Integer.parseInt(props.getPropsMap().get(Constant.DB_DATA_LIMIT));
    	}
	}
```

Sender는 Slave와 연결된 소켓으로 데이터를 전송하는 실질적인 로직이 수행되는 역할을 하고 있습니다.

target_data_table은 데이터를 적제할 테이블이고, Limit값은 데이터 전송을 위해 DB에서 Select시 한번에 얼마만큼 가져올지를 나타내는 설정입니다.



```java
public void setSocket(Socket p_socket) {
		_socket = p_socket;
		try {
			ois = new ObjectInputStream(_socket.getInputStream());
            slaveID = (String) ois.readObject();
            logger.info("Connected client ID : " + slaveID);
            oos = new ObjectOutputStream(_socket.getOutputStream());
			
            con = repositoryManager.getConnection();
			logger.info("Client connection set");
			
		} catch (Exception e) {
			e.printStackTrace();
			logger.error(e);
		}finally {
			
		}
	}
```

setSocket 메소드는 소켓 연결과 stream연결과 DB 커넥션을 연결하는 메소드입니다.

이때, 소켓이 연결 되면 Slave는 자신의 ID를 서버에 전송합니다.

해당 ID는 클래스변수로 관리되며 해당 세션에서 Offset과 함께 사용이 됩니다.



```java
@Override
	public void run() {
       // 데이터 전송에 사용할 DataContainer 객체를 생성
		DataContainer dataContainer = new DataContainer();
		dataContainer.setSlaveID(slaveID); // 현재 연결된 Slave의 ID값을 헤더정보로 세팅.
    	String sql = buildSyncInfoSQL(slaveID); 
        // Sync_table에 현재 Slave에 전송된 데이터 Offset값을 가져쿼리 생성.
    	int offset = 0; //Default값은 0
    	
    	try {
    		Statement st = con.createStatement();
    		ResultSet rs = st.executeQuery(sql);
    		
    		if (rs.next()) {
                //만약 Offset 값이 있다면 해당 값으로 Offset값을 변경
    			offset = rs.getInt(2);
            }
    		sql = buildSelectDataSQL(String.valueOf(offset));
    		
    		//target_data_table에 Offset 값 만큼 데이터를 가져온다.
            rs = st.executeQuery(sql);
    		int count = 0;
    		while(rs.next()){
               //해당 데이터들을 객체로 변환
    			dataContainer.add(
    				new ArrayList<>(
    					Arrays.asList(
    							String.valueOf(rs.getInt(1)), 
    							rs.getTimestamp(2).toString()
    					)
    				)
    			);
    			count++;
    		}
    		
            // 객체 전송
    		oos.writeObject(dataContainer);
    		
            // 객체 전송이 완료가 되면, 현재의 Offset값을 최신화 시킨다.
     		sql = buildUpdateOffsetSQL(slaveID, offset+count);
    		int var = st.executeUpdate(sql);
    		
		} catch (Exception e) {
			setWorkerState(WorkerState.NON_ACTIVE);
			try {
				ois.close();
				oos.close();
			} catch (Exception e1) {
				e1.printStackTrace();
			}
    		try {
				this.wait();
				
			} catch (InterruptedException e1) {
				e1.printStackTrace();
			}
		}
	}
```

Sender의 수행 로직입니다. 

1. 데이터 전송에 사용할 DataContainer 객체를 생성
2. 해당 DataContainer의 헤더 정보로 현재 연결된 Slave의 ID값을 세팅
3. Sync_table에 현재 Slave에 전송된 데이터 Offset값을 가져쿼리 생성
4. 만약 Offset 값이 있다면 해당 값으로 Offset값을 변경
5. target_data_table에 Offset 값 만큼 데이터 로드
6. DataContainer에 데이터 추가
7. 데이터 전송
8. 객체 전송이 완료가 되면, 현재의 Offset값을 최신화
   ​

### 2.1.4 Master Sequence flow chart![](/image/backup_program/4.png)



## 3. Slave 상세 설명

### 3.1 코드 설명![](/image/backup_program/5.png)

### 3.1.1 ClientStarter.class

```java
public class ClientStarter {
private final static Logger logger = LogManager.getLogger(ClientStarter.class);
	
	private static void Start(AppProperties props) {
		SocketService service = new SocketService();
		service.initialize(props);
		service.socketClientStart();
	}
	
	public static void main(String[] args) {
		
		String propFile = "";
		for(String l_arg : args) {
			if(l_arg.indexOf("") > -1) {
				propFile = l_arg.split("=")[1];
				
				AppProperties props = new AppProperties();
				props.initialize();
				props.loadConfig(propFile);
				
				
				Start(props);
			} else {
				logger.info("Put properties location");
			}
		}
		logger.info("There are no properties info");
	}
}
```

Slave(Client)역시 프로그램의 실행을 위해 설정 파일의 Path를 읽어와야 합니다.

만일 Path가 없거나 args 인자가 없다면 실행하지 않습니다.



### 3.1.2 SocketService.class

```java
public class SocketService {
	private final static Logger logger = LogManager.getLogger(SocketService.class);
	
	private AbstractRepositoryManager repositoryManager = null;
	private AppProperties props;
	private String back_data_table = "myData";
	
	public SocketService() {
	}
	
	public void initialize(AppProperties props) {
		repositoryManager = new PostgresqlRepositoryManager(props);
		this.props = props;
		
		if(props.getPropsMap().get(Constant.DB_BACKUP_TABLE_NAME) != null){
			back_data_table = props.getPropsMap().get(Constant.DB_BACKUP_TABLE_NAME);
    	}
		
		try {
			repositoryManager.createTargetDataTable(back_data_table);
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	
	public void socketClientStart() {
		Reciever reciever = new Reciever(props, repositoryManager);
		reciever.startReciving();
		logger.info("client start");
	}
	
}
```

SocketService 초기화시 벡업 테이블이 생성됩니다.

먼저 설정된 벡업 테이블 네임이없다면 default네임은 myData입니다.



### 3.1.1 Reciever.class

```java
public class Reciever {
	private final static Logger logger = LogManager.getLogger(Reciever.class);

	private int PORT = 20000;
	private String IP = "127.0.0.1";
	private String ID = "TEST";
	
	AbstractRepositoryManager repositoryManager;
	
	private String back_data_table = "myData";
	
	public Reciever(AppProperties props, AbstractRepositoryManager repositoryManager) {
		this.repositoryManager = repositoryManager;
		if(props.getPropsMap().get(Constant.MASTERPORT) != null){
			PORT = Integer.valueOf(props.getPropsMap().get(Constant.MASTERPORT));
		}else{
			PORT = 20000;
		}
		if(props.getPropsMap().get(Constant.MASTERIP) != null){
			IP = props.getPropsMap().get(Constant.MASTERIP);
		}else{
			IP = "127.0.0.1";
		}
		if(props.getPropsMap().get(Constant.SLAVEID) != null){
			ID = props.getPropsMap().get(Constant.SLAVEID);
		}else{
			ID = "Slave";
		}
		
		if(props.getPropsMap().get(Constant.DB_BACKUP_TABLE_NAME) != null){
			back_data_table = props.getPropsMap().get(Constant.DB_BACKUP_TABLE_NAME);
    	}
	}
	.
	.
```

Reciever는 Slave에서 데이터의 수신과 저장을 하는 역할을 합니다.

생성자를 통해 접속할 서버의 IP, PORT 정보를 설정값으로 초기화 하고, 현재 Slave의 ID값도 초기화 합니다.

각 변수의 Default값은 각각 "127.0.0.1",  "20000", "Slave" 입니다.



```java
public void startReciving(){
		Socket socket = null;
		ObjectOutputStream oos =null;
		ObjectInputStream ois = null;
		try {
			socket = new Socket(IP,PORT);
		
			oos = new ObjectOutputStream(socket.getOutputStream());
            // 현재 실행된 Slave의 ID를 전송.
			oos.writeObject(ID);
			logger.info("Sending request to Socket Server");
			ois = new ObjectInputStream(socket.getInputStream());
			
			while(true){
				if(!socket.isConnected()) break; // 소켓이 끊겼다면 서비스 로직 종료.
		        DataContainer dataContainer = (DataContainer) ois.readObject();
		        if(dataContainer.getSlaveID().equals(ID)){ // 현재 연결된 Slave의 ID가 아니라면 데이터를 적재하지 않는다.
		        	logger.info("Reciever Data Count: " + dataContainer.getRowCount());
	            	backUpData(dataContainer); // 수신한 데이터 백업테이블에 적재.
		        }
			}
		} catch (Exception e) {
			e.printStackTrace();
			logger.error(e.getMessage());
		}finally{
			 try {
				ois.close();
				oos.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}
```

소켓 연결시 현재 Slave의 ID를 Master에 전송합니다.

이후, DataContainer객체로 데이터를 수신하여 backUpData(dataContainer)에서 데이터를 백업 테이블에 저장합니다.

하지만 만일 전달 받은  DataContainer가 현재 연결된 Slave의 ID가 아니라면 데이터를 저장하지 않습니다.



```java
private void backUpData(DataContainer dataContainer) {
		SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss.SSS");
		Connection con = null;
		try {
			con = repositoryManager.getConnection();
			
			Statement stmt = con.createStatement();
            con.setAutoCommit(false);
			PreparedStatement pstmt = con.prepareStatement(
                    "INSERT INTO "+back_data_table+"(value, created) VALUES(?,?)");
			
			for (List<String> row : dataContainer.getRowList()) {
                // Add each parameter to the row.
                pstmt.setInt(1, Integer.parseInt(row.get(0)));
                Date d = format.parse(row.get(1));
                pstmt.setTimestamp(2,  new Timestamp(d.getTime()));
                pstmt.addBatch();
            }
	     
            try {
                pstmt.executeBatch();
            } catch (Exception e) {
                System.out.println("Error message: " + e.getMessage());
                return; 
            }
            
            con.commit();
		} catch(Exception e) {
			logger.error(e);
		} finally {
			try {
				con.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
```

backUpData 메소드에서는 다중 PreparedStatement을 생성하여 batch로 데이터 Insert를 수행 합니다.



## 5. 테스트방법 Case 1

- JDK 8


- eclipse 설치 
- Postgresql 9.4 of higher 설치



###  Postgresql 설치 주의 사항![](/image/backup_program/6.png)

- ### **데이터베이스 변경**

  - ##### DB_URL의 jdbc:postgresql://localhost:5432/{DB_Name}

    postgres는 postgres설치시 디폴트 DB 입니다. 다른것을 이용하고자 한다면 DB 생성후 해당 DB이름으로 변경 

    ex> jdbc:postgresql://localhost:5432/opensource

  - 소문자로 생성 하는 것을 권장합니다.

  

- ### **DB_USER, DB_PASSWORD**

  DB_USER, DB_PASSWORD 역시 postgres 설치 시 설정한 유저명과 비밀번호 입니다. 새로운 유저로 사용하고자 한다면 해당 DB 접속 후
  USER, PASSWORD 생성 후 properties에 변경 (소문자로 생성 하는 것을 권장합니다.)

  ex> ![](/image/backup_program/스크린샷 2019-08-28 오전 6.17.07.png)



#### 5.1.1 git clone https://github.com/guriOH/backup-program.git

#### 5.1.2 eclipse maven import

<img src="/image/backup_program/7.png" width="300"/>

#### 5.1.3 Maven build

 <img src="/image/backup_program/8.png" width="300"/>



#### 5.1.4 Run Master & Slave ![](/image/backup_program/9.png)

  Program arguments : '={app.properties 파일 위치}'   

  VM arguments : '-Dlog4j.configurationFile=file:////{log4j2.xml 파일 위치}'   



## 6. 테스트방법 case 2

#### 6.1 5.1.1~3 까지 동일

#### 6.2 Runnable JAR file 각각 export

<img src="/image/backup_program/10.png" width="300"/> 

#### 6.3 Terminal command

```shell
java -Dlog4j.configurationFile=file:////{log4j2.xml 파일 위치} -jar master.jar {=app.properties 파일 위치}
java -Dlog4j.configurationFile=file:////{log4j2.xml 파일 위치} -jar slave.jar {=app.properties 파일 위치}
```








