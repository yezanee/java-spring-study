# 1.2 DAO의 분리   

## 1.2.1 관심사의 분리   
###### 관심이 같은것끼리는 하나의 객체 안으로 모이게 하고, 관심이 다른것은 가능한 한 따로 떨어져서 서로 영향을 주지 않도록 분리해줌으로써 같은 관심에 효과적으로 집중할 수 있게 만들어주는것이다.


## 1.2.2 커넥션 만들기의 추출
<pre>
<code>
package springbook.user.dao;
...
public class UserDao {

   public void add(User user) throws ClassNotFoundException, SQLException { 
   		Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection(
           	"jdbc:mysql://localhost/springbook", "spring", "book"); 
               
   PreparedStatement ps = c.prepareStatement(
   		"insert into user(id, name, password) values(?,?,?)");   
    ps.setString(1, user.getId());
    ps.setString(2, user.getName());
    ps.setString(3, user.getPassword());
    
    ps.executeUpdate();
  
    ps.close();
    c.close();
}

   
   public User get(String id) throws ClassNotFoundException, SQLException { 
   		Class.forName("com.mysql.jdbc.Driver");
           Connection c = DriverManager.getConnection (
           				"jdbc:mysql://localhost/springbook", "spring", "book");
           
           PreparedStatement ps = c.prepareStatement(
           				"select * from users where id = ?");
           ps.setString(1, id);
           
           ResultSet rs = ps.executeQuery();
           rs.next();
           User user = new User();
           user.setId(rs.getString("id"));
           user.setName(rs.getString("name"));
           user.setPassword(rs.getString("password"));
           
           rs.close();
           ps.close();
           c.close();
           
           return user;
   	}
} 
</code>
</pre>




