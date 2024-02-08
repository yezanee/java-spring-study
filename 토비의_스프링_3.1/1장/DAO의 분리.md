# 1.2 DAO의 분리   

## 1.2.1 관심사의 분리   
###### 관심이 같은것끼리는 하나의 객체 안으로 모이게 하고, 관심이 다른것은 가능한 한 따로 떨어져서 서로 영향을 주지 않도록 분리해줌으로써 같은 관심에 효과적으로 집중할 수 있게 만들어주는것이다.


## 1.2.2 커넥션 만들기의 추출

```java   

package springbook.user.dao;
...

// UserDao 클래스: 사용자 정보에 대한 데이터 액세스 객체를 제공하는 클래스
public class UserDao {

    // 사용자 정보를 추가하는 메서드
    public void add(User user) throws ClassNotFoundException, SQLException { 
        // JDBC 드라이버 로딩
        Class.forName("com.mysql.jdbc.Driver");
        // 데이터베이스 연결
        Connection c = DriverManager.getConnection(
                "jdbc:mysql://localhost/springbook", "spring", "book"); 
               
        // SQL 실행을 위한 PreparedStatement 생성
        PreparedStatement ps = c.prepareStatement(
                "insert into user(id, name, password) values(?,?,?)");   
        // 사용자 정보 설정
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());
        
        // SQL 실행
        ps.executeUpdate();
  
        // 리소스 정리
        ps.close();
        c.close();
    }

    // 사용자 정보를 조회하는 메서드
    public User get(String id) throws ClassNotFoundException, SQLException { 
        // JDBC 드라이버 로딩
        Class.forName("com.mysql.jdbc.Driver");
        // 데이터베이스 연결
        Connection c = DriverManager.getConnection (
                "jdbc:mysql://localhost/springbook", "spring", "book");
        
        // SQL 실행을 위한 PreparedStatement 생성
        PreparedStatement ps = c.prepareStatement(
                "select * from users where id = ?");
        ps.setString(1, id);
        
        // SQL 실행 결과를 ResultSet에 저장
        ResultSet rs = ps.executeQuery();
        rs.next();
        
        // 조회된 사용자 정보를 User 객체에 설정
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));
        
        // 리소스 정리
        rs.close();
        ps.close();
        c.close();
        
        // 조회된 사용자 정보를 반환
        return user;
    }
}   
```

##### UserDao의 구현된 메소드를 다시 살펴보면, add() 메소드 하나에서만 적어도 세 가지의 관심사항을 발견할 수 있다.

> UserDao의 관심사항
* 첫째는 DB와 연결을 위한 커넥션을 어떻게 가져올까 라는 관심이다. 더 세분화해서 어떤 DB를 쓰고, 어떤 드라이버를 사용할 것이고, 어떤 로그인 정보를 쓰는데, 그 커넥션을 생성하는 방법은 또 어떤 것이다라는 것까지 구분해서 볼 수도 있다.

* 둘째는 사용자 등록을 위해 DB에 보낼 SQL 문장을 담을 Statement를 만들고 실행하는 것이다. 여기서의 관심은 파라미터로 넘어온 사용자 정보를 Statement에 바인딩시키고, Statement에 담긴 SQL을 DB를 통해 실행시키는 방법이다.

* 셋째는 작업이 끝나면 사용한 리소스인 Statement와 Connection오브젝트를 닫아줘서 공유 리소스를 시스템에 돌려주는 것이다.

  >중복 코드의 메소드 추출
  *가장 먼저 할 일은 커넥션을 가져오는 중복된 코드를 분리하는 것이다. 중복된 DB 연결코드를 getConnection()이라는 이름의 독립적인 메소드로 만들어둔다. 각 DAO메소드에서는 이렇게 분리한 getConnection()메소드를 호출해서 DB커넥션을 가져오게 만든다. 다음은 수정한 UserDao코드의 일부분이다.

  ```java
  public void add(User user) throws ClassNotFoundException, SQLException {
    Connection c = getConnection ();
...
}

public User get(String id) throws ClassNotFoundException, SQLException {
    Connection c = getConnection();
...
}

//DB 연결 기능이 필요하면 getConnection() 메소드를 이용하게 한다.
//중복된 코드를 독립적인 메소드로 만들어서 중복을 제거했다.

private Connection getConnection() throws ClassNotFoundException, SQLException {
    Class. forName ("com.mysql.jdbc.Driver");
    Connection c = DriverManager.getConnection(
        "jdbc: mysql://localhost/springbook", "spring", "book");
    return c;
    ```





