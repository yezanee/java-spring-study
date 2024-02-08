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





