---
title: Spring_CRUD_practice
date: 2019-11-01 19:37:57
tags: [Spring, H2, CRUD]
categories:
 - [Study]
---

Autor : 오 훈

Title : AddressBook



# AddressBook ([GitHub Link](https://github.com/guriOH/AddressBook.git))

## 1. 목적

- GET, PUT, POST, DELETE 를 이용한  주소록  CRUD 구현



## 2. 어플리케이션 디자인 



### ![](/image/addressInfo/1.png)



구현에 앞서 기본적인 어플리케이션 디자인을 설명 드리겠습니다.

어플리케이션은 크게 데이터 클래스 (Request, Response, Model), 비즈니스 로직을 포함한 데이터 처리 클래스로 나뉩니다.



## 3. 프로젝트 구성 

## ![](/image/addressInfo/2.png)

프로젝트는  SpringBoot 어플리케이션을 사용하여 작성하였습니다.

Repository 컨트롤로는 JPA api를 사용하였고,  DataBase로는  인메모리디비 H2를 사용하였습니다.



### 3.1 데이터 구성

AddressInfo.class

```java
@Entity
@ToString
@Getter @Setter
public class AddressInfo implements Serializable {
	private static final long serialVersionUID = 1L;
	
	@Id
	@GeneratedValue(strategy= GenerationType.SEQUENCE)
	private Integer id;
	
	
	private String name;
	private String phonenumber;
	private String address;
	private String email;
	
	public AddressInfo() {
		
	}
	
	public AddressInfo(String name, String phonenumber, String address, String email) {
		this.name = name;
		this.phonenumber = phonenumber;
		this.address = address;
		this.email = email;
	}
}
```

AddressInfo 클래스는 데이터 테이블에 저장될 데이터의 모델로서 Key값은 Integer형태의 Id를 Auto 생성합니다.
그리고 주소록에 들어갈 내용으로는 이름, 연락처, 주소, 이메일이 있습니다.

Getter, Setter 어노테이션을 사용하도록  org.projectlombok.lombok 라이브러리를 사용하였습니다.



## 4. 상세 설명

### 4.1 GET

```java
@Controller
@RequestMapping(path = "/address")
public class AddressController {

	@Autowired
	private AddressServiceImpl addressService;

  	@RequestMapping(value = "/{id}", method = RequestMethod.GET)
	public @ResponseBody AddressResponse getAddress(@PathVariable("id")final Integer id) {
		List<String> errors = new ArrayList<>();
		AddressInfo toDoItem = null;
		try {
			toDoItem = addressService.findById(id);
		} catch (final Exception e) {
			e.printStackTrace();
			errors.add(e.getMessage());
		}
		return AddressAdapter.addressInfoResponse(toDoItem, errors, null);
	}
  
	@RequestMapping(method = RequestMethod.GET)
	public @ResponseBody List<AddressResponse> getAllMember() {
		List<String> errors = new ArrayList<>();
		List<AddressInfo> toDoItems = addressService.findAllMembers(); 
		List<AddressResponse> toDoItemResponses = new ArrayList<>();
		toDoItems.stream().forEach(toDoItem -> {
			toDoItemResponses.add(AddressAdapter.addressInfoResponse(toDoItem, errors, null));
		});
		return toDoItemResponses;
	}
```

AddressController는 어노테이션을 이용하여 이 클래스가 RESTFul API임을 명시합니다.

그리고 @RequestMapping을 사용하여 해당 컨트롤러의 최상위 URL을 정의 합니다.



@RequestMapping(value = "/{id}", method = RequestMethod.GET)

- AddressResponse getAddress(@PathVariable("id")final Integer id)

  1. getAddress는 GET 방식 사용하여 호출합니다. @PathVariable으로 정의되어 있는 값을  URL에서 선택하여 getAddress의 인자 ID로 넣어 줍니다.

  2. addressService.findById(id)에서는 내부적으로 repository서비스 구현 클래스를 호출하여 해당  ID(key value)를 가진 데이터를 가져옵니다.

     ```java
     @Override
     public AddressInfo findById(Integer id) {
     	return addressRepository.findById(id).orElse(null);
     }
     ```

  3. AddressAdapter.addressInfoResponse(toDoItem, errors, null) 
     가져온 결과값을 Adapter에서 Response객체로 변환하여 반환합니다.

- List<AddressResponse> getAllMember()

  1. getAllMember GET 방식 사용하여 호출합니다.

  2. addressService.findAllMembers()에서는 addressRepository.findAll()를 호출하여 모든 주소 데이터를 가져옵니다.

     ```java
     @Override
     public List<AddressInfo> findAllMembers() {
     	return addressRepository.findAll();
     }
     ```

  3. AddressAdapter.addressInfoResponse(toDoItem, errors, null) 가져온 결과값을 Adapter에서 Response객체로 변환하여 반환합니다.

  

### 4.2 POST

```java
@RequestMapping(method = RequestMethod.POST)
	public @ResponseBody AddressResponse create(@RequestBody final AddressRequest addressReqeust) {
		List<String> errors = new ArrayList<>();
		AddressInfo addressInfo = AddressAdapter.addressInfo(addressReqeust);
		try {
			addressInfo = addressService.saveMember(addressInfo);
		} catch (final Exception e) {
			e.printStackTrace();
			errors.add(e.getMessage());
		}
		return AddressAdapter.addressInfoResponse(addressInfo, errors, null);
	}
```

@RequestBody를 사용하여 HTTP 요청을 AddressRequest로 받고, AddressAdapter에서는 이  Http요청을 AddressInfo객체로 변환한다.

- AddressResponse create(@RequestBody final AddressRequest addressReqeust) 

  1. addressService.saveMemver(addressInfo)에서는 addressRepository.save(member)를 호출하여 데이터를 저장한다. 

     ```java
     @Override
     public AddressInfo saveMember(AddressInfo member) {
     	return addressRepository.save(member);
     }
     ```

@ResponseBody는 create 메소드 결과 객체를 Http 결과를  Json형태로 돌려준다.

### 4.3 PUT

```java
@RequestMapping(value = "/{id}", method = RequestMethod.PUT)
	public @ResponseBody AddressResponse updateAddress(@PathVariable("id") Integer id,  @RequestBody final AddressRequest addressReqeust) {
		List<String> errors = new ArrayList<>();
		AddressInfo source = AddressAdapter.addressInfo(addressReqeust);
		AddressInfo updated = addressService.updateMember(id, source);
		return AddressAdapter.addressInfoResponse(updated, errors, "");
	}
```

PUT을 사용하여 데이터를 수정합니다. 먼저 @PathVariable("id")와 같이 URL에서 변환 대상 객체의 Key(Id)와 수정 내용를 전달 받습니다.

- AddressAdapter.addressInfo(addressReqeust) 먼저 수정할 내용의 객체를 AddressAdapter에서 Model 객체로 변환합니다.

- addressService.updateMember(id, source)는 내부적으로 모든 주소록을 가져와 해당 ID값을 가지는  Model을 수정할  Model로 업데이트 합니다.

  ```java
  @Override
  	public AddressInfo updateMember(Integer id, AddressInfo source) {
  		AddressInfo target = addressRepository.findById(id).orElse(null);
  		if(target == null) return null;
  		
  		target.setAddress(source.getAddress());
  		target.setEmail(source.getEmail());
  		target.setName(source.getName());
  		target.setPhonenumber(source.getPhonenumber());
  		
  		addressRepository.save(target);
  		return target;
  	}
  ```

@ResponseBody는 create 메소드 결과 객체를 Http 결과를  Json형태로 돌려준다.

### 4.4 DELETE

```java
@RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
	public @ResponseBody AddressResponse deleteAddress(@PathVariable(value="id") Integer id) {
		List<String> errors = new ArrayList<>();
		String info = "Delete fail";
		try {
			if(addressService.deleteMember(id)) {
				info = "Delete success";
			}
		}catch(Exception e) {
			e.printStackTrace();
			errors.add(e.getMessage());
		}
		return AddressAdapter.addressInfoResponse(null, errors, info);
	}
```

DELETE를 사용하여 데이터를 삭제합니다. @PathVariable("id")에 명시된 내용을 URL에서 가져옵니다.

- addressService.deleteMember(id) 에 삭제할 대상  key값을 주어, 해당 객체를 가져와 delete 합니다.

  ```java
  @Override
  	public Boolean deleteMember(Integer id) {
  		AddressInfo target = addressRepository.findById(id).orElse(null);
  		if( target == null)
  			return false;
  		else
  			addressRepository.delete(target);
  		return true;
  	}
  ```



## 5. 테스트

1. Postman을 활용한 로직 호출 (DB 로그 확인)

   



