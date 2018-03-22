# JDBC

## 概念

**Java Database Connectivity** (JDBC)

通过Java语言操作数据库

## 数据持久化

**持久化(persistence)：**

把数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘上加以”固化”，而持久化的实现过程大多通过各种关系数据库来完成。

持久化的主要应用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML数据文件中。 

## JDBC技术种类的说明

JDBC是java访问数据库的基石，JDO, Hibernate等只是更好的封装了JDBC。

**JDBC驱动程序：**

各个数据库厂商根据JDBC的规范制作的 JDBC 实现类的类库

**JDBC驱动程序总共有四种类型：**

- 第一类：JDBC-ODBC桥。

- 第二类：部分本地API部分Java的驱动程序。

- 第三类：JDBC网络纯Java驱动程序。

- 第四类：本地协议的纯 Java 驱动程序。

> 第三、四两类都是纯Java的驱动程序，因此，对于Java开发者来说，它们在性能、可移植性、功能等方面都有优势。

## JDBC连接数据库步骤

代码示例：

```java
public class 如何获取连接 {

    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.加载驱动类
        Class.forName("com.mysql.jdbc.Driver");//就是对Driver进行实例化对象的操作
        Connection conn = null;
        // 2.获取数据库连接 jdbc:mysql://localhost:端口号/数据库名称
        conn = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/数据库名", "数据库账号", "数据库密码");

        System.out.println(conn);//如果获取到内存地址代表连接成功

        conn.close();

    }

}

public class 运行数据库指令对象Statement {

    public static void main(String[] args) throws ClassNotFoundException, SQLException {
        // 1.加载驱动类
        Class.forName("com.mysql.jdbc.Driver");//就是对Driver进行实例化对象的操作 疑问: 使用反射
        Connection conn = null;
        // 2.获取数据库连接
        conn = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/数据库名", "数据库账号", "数据库密码");
        // 3.获取执行SQL语句的对象 Statement
        Statement st = conn.createStatement();

        String sql = "INSERT INTO student (student_code,student_name,sex,school) VALUES ('9797','张三','男','三国')";

        //executeUpdate 可以执行INSERT INTO/UPDATE/DELETE
        int row = st.executeUpdate(sql);
        System.out.println(row);
        if(row>0){
            System.out.println("成功");
        }else{
            System.out.println("失败");
        }
        //4.关闭资源
        st.close();
        conn.close();

    }

}
```

## 使用 JDBC 驱动程序处理元数据

Java 通过JDBC获得连接以后，得到一个Connection 对象，可以从这个对象获得有关数据库管理系统的各种信息，包括数据库中的各个表，表中的各个列，数据类型，触发器，存储过程等各方面的信息。根据这些信息，JDBC可以访问一个实现事先并不了解的数据库。

获取这些信息的方法都是在DatabaseMetaData类的对象上实现的，而DataBaseMetaData对象是在Connection对象上获得的。

```java
/**
 * 数据库连接
 * @author gobha
 *
 */
public class DataBaseConnection {
    private final static String DRIVER = "com.mysql.jdbc.Driver";
    private final static String URL = "jdbc:mysql://127.0.0.1:3306/数据库名";
    private final static String USERNAME = "数据库账号";
    private final static String PWD = "数据库账号";
    private Connection connection;
    /**
     * 创建数据库连接
     */
    public DataBaseConnection() {
        try {
            //1.加载驱动类
            Class.forName(DRIVER);
            //2.获取数据库连接,并且赋值
            this.connection = DriverManager.getConnection(URL, USERNAME, PWD);
        } catch (Exception e) {
            e.printStackTrace();//详细的异常信息,交给JVM处理
        }
    }
    /**
     * 获取数据库连接
     * @return
     *      Connection connection
     */
    public Connection getConnection() {
        return this.connection;
    }
    /**
     * 关闭数据库连接资源
     * @param connection
     */
    public void close(Connection connection) {
        if(this.connection!=null){
            try {
                this.connection.close();
            } catch (SQLException e) {
                e.printStackTrace();//详细的异常信息,交给JVM处理
            }
        }
    }

}

public class SuperDao {
    private Connection connection;
    public SuperDao(Connection connection){
        this.connection = connection;
    }
    public Map<String,Object> queryForMap(String sql,Integer id){
        Map<String,Object> map = new HashMap<String,Object>();
        PreparedStatement ps = null;
        ResultSet rs = null;
        try {
            ps = this.connection.prepareStatement(sql);

            ps.setObject(1, id);//1代表第一个问号
            //获取结果集
            rs = ps.executeQuery();
            //获取结果集的元数据
            ResultSetMetaData rsmd = rs.getMetaData();
            //获取结果集查询的列数
            int columnCount = rsmd.getColumnCount();
            while(rs.next()){
                for(int i=1;i<=columnCount;i++){
                    //取得字段名称
                    String columnName = rsmd.getColumnLabel(i);
                    Object value = rs.getObject(columnName);//取得字段对应的值
                    //存储到Map当中,使用字段名称当
                    map.put(columnName, value);
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();//详细的异常输出
            throw new RuntimeException("queryForMap执行查询失败");
        }finally{

            try {
                if(rs!=null){
                    rs.close();
                }
                if(ps!=null){
                    ps.close();
                }
                if(this.connection!=null){
                    this.connection.close();
                }
            } catch (SQLException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
                throw new RuntimeException("关闭资源失败");
            }

        }
        return map;
    }

}
```