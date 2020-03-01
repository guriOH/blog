Mybatis 사용시 쿼리문에 문자열 비교연사자나 부등호를 사용할 때 가 있다.

select * from user where salary > 100;

일때 '>'와 같은 기호가 괄호인지 비교연산자인지 모르는데,

이럴때 \'<![CDATA[' 을 사용하면 CDATA 안에 들어가는 문장을 문자열로 인식하게 된다.

<![CDATA[select * from user where salary > 100]]> 이렇게 사용하면 특수문자가 들어가도 문자열로 인식한다.