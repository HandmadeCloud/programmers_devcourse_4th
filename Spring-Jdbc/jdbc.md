
# JDBC란
- java application - database간 연결해서 쿼리를 날리고 db로부터 정보를 받아오는 브릿지 역할을 함
- 둘을 연결해주는 표준 인터페이스임 db 종류제 관계없이 제공됨

##  jdbc API
- dbms 벤드에서 제공 
- backend 엔지니어도 jdbc api로 driver와 커넥션을 맺고 요청을 보낸다.
- application 범위
## jdbc db driver
- 각 db 드라이버와 연결해 명령을 수행한다.

## 과정
driverManager로부터 커넥션을 받는다 -> 커넥션을 통해서 statementt를 받는다. 
-> statement를 통해 쿼리를 실행해 resultsett을 가져오거나 update 진행
-> 커넥션을 종료한다.

```java
Connection connection = null; //초기화
Statement statement = null; //초기화
ResultSet resultSet = null; //초기화
connection = DriverManage.getConnection("jdbc:mysql//localhost/order_mgmt", "root","password")

statement = connection.createStatement(); // 생성
resultset = statement.executeQuery("select * from customers");   // 쿼리 실행
resultSet.getString("name"); // customer의 name이란 컬럼에 있는 값을 빼옴
    
connection.close(); //사용후 반환
statement.close(); //사용후 반환
resultSet.close(); //사용후 반환
```

항상 수동으로 닫지는 않음. 자동으로 닫히게 할 수 있음 autoClose()

# Spring JDBC
매번 커넥션을 가져오고 닫으면 비용이 많이 발생한다. 
커넥션 풀이라는 개념을 도입해 driver대신에 dataSource를 이용해서 커넥션을 얻고 반환하는 식으로 사용한다.

## 과정(dataSource, database connection pool)
1. 풀에서 커넥션을 미리 생성해준다.
2. 풀에서 커넥션을 가져온다.
3. 커넥션을 사용한다.
4. 커넥션을 반환한다. (커넥션을 물리적으로 끊는 것이 아님)

## HicariCP
jdbc 커넥션 풀 구현체 (spring 2.0 이후 사용)

```java
@Bean
public DataSource dataSource() {
	DataSource datasource = DataSourceBuilder.create()
    .url()
    .username()
    .password()
    .type()
    .build();
	
	dataSource.setMaximumSize(100);
	daatSource.setMinimumIdle(100);
    }
```
# JdbcTemplate
- 커넥션을 얻고 예외처리를 하는 부분이 반복적으로 구성됨
- 템플릿 콜백 패턴을 이용해서 dataSource 문제를 해결함
- jdbcTemplate사용으로 dataSource를 사용할 필요가 없어진다.
 ```java
jdbcTemplate.query("select * from customers", customerRowMapper);

@Bean
public JdbcTemplate jdbcTemplate(DataSource dataSource){
	reutrn new JdbcTemplate(dataSource);
}
```

