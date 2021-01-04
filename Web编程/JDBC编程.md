# JDBC编程
## 1. JDBC简介

JDBC（Java DataBase Connectivity）是Java语言访问数据库的标准，是用于执行SQL语句的JavaAPI，该API由 java.sql.* 、javax.sql.* 包中的一些类和接口组成。

Java程序访问数据库的基本方式是通过JDBC。

## 2. JDBC工作原理

![](https://upload-images.jianshu.io/upload_images/25392987-a26bcbb3eac783a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用接口必须有其实现类（由数据库厂商提供），通常把厂商提供的特定于数据库的访问API称为指定语言的驱动程序。

在Java中使用数据库需要驱动程序，但并不直接依赖驱动程序。也就是说，我们不需要关心底层数据库，只需要关心JDBC的API。这就为我们更换数据库类型时提供了便利，即提高了程序的可移植性。

JDBC有四种驱动方式：JDBC-ODBC桥接、本地API驱动、网络协议驱动和本地协议驱动

## 3. JDBC使用流程图

![](https://upload-images.jianshu.io/upload_images/25392987-10d4c702f51f4196.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- API是一个接口，具体的实现类需要数据库厂商提供，这就需要加载数据库驱动
- 建立连接（mysql -u root -p）
- 创建命令（select * from等）
- 执行语句（回车）
- 处理返回结果（mysql返回的结果类似于表格，Java中将结果抽象为一个对象，通过对象来描述表格中的数据）
- 先创建的后关闭

## 4. JDBC常用接口和类

在JDBC编程前，需要准备好数据库驱动包哦！https://dev.mysql.com/downloads/connector/j/

### 4.1 数据库连接

Connection接口实现类由数据库提供，获取Connection对象通常有两种方式：通过DriverManager的静态方法获取、通过DataSource（数据源）对象获取。

```java
//MySQL数据连接的URL参数格式： jdbc:mysql://服务器地址:端口/数据库名?参数名=参数值


public static Connection getConnection(String url,String user, String password)
```

![wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 4.2 操作命令的创建

Statement对象主要是将SQL语句发送到数据库中

```
Statement createStatement() throws SQLException;
```

- JDBC API中主要提供了三种Statement对象

  - Statement
    - 用于执行不带参数的简单SQL语句
  - PreparedStatement
    - 用于执行带或者不带参数的SQL语句
    - SQL语句会预编译在数据库系统
    - 执行速度快于Statement对象
  - CallableStatement
    - 用于执行数据库存储过程的调用

- 实际开发中最常用的是PreparedStatement

  - 参数化SQL查询
  - SQL预编译，防止SQL注入攻击
  - 占位符 —— ？，下标从1开始；不能使用多值
  - 性能高于Statement

### 4.3 两种执行SQL的方法

  - executeQuery() 方法执行后返回单个结果集的，通常用于select语句 （查）
  - executeUpdate()方法返回值是一个整数，指示受影响的行数，通常用于insert、delete、update语句 （增、删、改）

### 4.4 结果集 —— ResultSet对象 

代表符合SQL语句条件的所有行，并且它通过一套getXXX方法提供了对这些行中数据的访问。

ResultSet里的数据按行排列，每行有多个字段，通过记录指针指向当前数据行（只能来操作当前数据行）。若想要取得某一条记录，就要使用ResultSet的next()方法 ；若想要取得ResultSet里的所有记录，就要使用while循环。

## 5. 案例

### 5.1 简单实现JDBC

#### 5.1.1 查询

```
import java.sql.*;


import java.time.LocalDateTime;


/**
 *
 *
 *
 * 简单的JDBC编程 —— 查询
 *
 *
 *
 * Author:zz
 */


public class Test {
	public static void main( String[] args ) throws SQLException
	{
		/* 1.加载驱动程序 */


		try {
			/* 获取到驱动，和具体实现类耦合了 */


			/* java.sql.Driver driver=new com.mysql.jdbc.Driver(); */


			/*传入的是一个字符串，与具体的实现类完全解耦（没有关系） */


			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}


		/* 将下列三者置于try块外，便于最后通过finally块进行关闭操作 */


		/* 连接 */


		Connection connection = null;


		/* 命令 */


		Statement statement = null;


		/* 结果集 */


		ResultSet resultSet = null;


		/* 2.获取连接-DriverManager */


		/* 数据库产品名称——mysql小写 */


		String url = "jdbc:mysql://127.0.0.1:3306/memo";


		try {
			connection = DriverManager.getConnection( url, "root", "1234" );


			/* 3. 创建命令 */


			statement = connection.createStatement();


			/* 4. 准备SQL语句，执行 */


			String sql = "select id,name,created_time,modify_time from memo_group";


			resultSet = statement.executeQuery( sql );


			/* 5.返回结果，处理结果 */


			/*按行读取 */


			while ( resultSet.next() )
			{
				/* 根据每行的列名取得对应的数据 */


				int id = resultSet.getInt( "id" );


				String name = resultSet.getString( "name" );


				LocalDateTime createdtime = resultSet.getTimestamp( "created_time" ).toLocalDateTime();


				LocalDateTime modifytime = resultSet.getTimestamp( "modify_time" ).toLocalDateTime();


				System.out.println( String.format( "id=%d\tname=%s\t\tcreated_time=%s\t\tmodify_time=%s", id, name, createdtime, modifytime ) );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			/* 6.关闭资源（由于Connection、Statement和ResultSet都继承了AutoCloseable，因此可以自动关闭，这里不做演示） */


			/* 将关闭放在finally块中，是为了防止程序抛出异常后不会向下继续执行，而导致关闭操作没有执行 */


			/* 先创建的后关闭 */


			/* 结果集 -> 命令 -> 连接 */


			/* 避免空指针异常，关闭前进行检验 */


			/* 关闭结果集 */


			if ( resultSet != null )
			{
				resultSet.close();
			}


			/* 关闭命令 */


			if ( statement != null )
			{
				statement.close();
			}


			/* 关闭连接 */


			if ( connection != null )
			{
				connection.close();
			}
		}
	}
}
```

#### 5.1.2 更新

与查询相比较，更新只是第4步和第5步不同：sql语句不同、执行sql语句不同 & 使用int接收返回的结果

```
import java.sql.*;


/**
 * 简单的JDBC编程 —— 更新
 * Author:zz
 */


public class Jdbc1 {
	public static void main( String[] args )
	{
		try {
			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}


		Connection connection = null;


		Statement statement = null;


		try {
			connection = DriverManager.getConnection( "jdbc:mysql://127.0.0.1:3306/memo", "root", "1234" );


			statement = connection.createStatement();


			String sql = "insert into memo_group (id ,name,created_time) values (5,'Go组', '2019-03-7 21:54:00')";


			int result = statement.executeUpdate( sql );


			System.out.println( result );
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			if ( statement != null )
			{
				try {
					statement.close();
				} catch ( SQLException e ) {
					e.printStackTrace();
				}
			}


			if ( connection != null )
			{
				try {
					connection.close();
				} catch ( SQLException e ) {
					e.printStackTrace();
				}
			}
		}
	}
}
```

### 5.2 模板设计模式实现JDBC

JDBC中只区分查询数据及更新数据（增删改），两者之间只有准备SQL语句并执行、返回结果并处理这两步的步骤不同，这就让我们想到了模板设计模式。

- **5.2.1 简单实现**

```
import java.sql.*;


/**
 *
 *
 *
 * 简单的模板设计模式实现JDBC
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public abstract class JdbcTemplate {
	private Connection connection = null;


	private Statement statement = null;


	private ResultSet resultSet = null;


	/* 加载驱动 */


	public void load()
	{
		try {
			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}
	}


	/* 连接 */


	public void link()
	{
		try {
			connection = DriverManager.getConnection( "jdbc:mysql://127.0.0.1:3306/memo", "root", "1234" );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	/* 创建命令 */


	public void createStatement()
	{
		try {
			statement = connection.createStatement();
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	/* 子类提供sql语句 */


	public abstract String createSql();


	/* 获得结果并处理 */


	/*
	 *
	 *
	 *
	 * 如果使用抽象方法实现，会有两个Handle方法需要覆写：
	 *
	 *
	 *
	 * public abstract ResultSet Handle();
	 *
	 *
	 *
	 * public abstract int Handle1();
	 *
	 *
	 *
	 * 因此，采用非抽象空实现的方法
	 *
	 *
	 *
	 * 通过方法重载，让子类选择性的去覆写（查询 or 更新)
	 *
	 *
	 *
	 */


	public < T > T handle( ResultSet resultSet )
	{
		return(null);
	}


	public < T > T handle( Integer result )
	{
		return(null);
	}


	/* 关闭资源 */


	public void close()
	{
		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( statement != null )
		{
			try {
				statement.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( connection != null )
		{
			try {
				connection.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}
	}


	/* 流程 */


	public final <T> T execute()
	{
		this.load();


		this.link();


		this.createStatement();


		String sql = this.createSql();


		try {
			/* 查询 */


			if ( sql.trim().startsWith( "select" ) || sql.trim().startsWith( "SELECT" ) )
			{
				resultSet = statement.executeQuery( sql );


				return(this.handle( resultSet ) );


				/* 更新 */
			} else {
				Integer result = statement.executeUpdate( sql );


				return(this.handle( result ) );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			this.close();
		}


		return(null);
	}
}
import java.sql.ResultSet;


import java.sql.SQLException;


import java.time.LocalDateTime;


import java.util.ArrayList;


import java.util.List;


/**
 *
 *
 *
 * 查询
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TemplateSelect extends JdbcTemplate {
	public static void main( String[] args )
	{
		JdbcTemplate jdbcTemplate = new TemplateSelect();


		/* 利用列表存放获得的数据 */


		List<MemoGroup> datas = jdbcTemplate.execute();


		/* 防止空指针异常 */


		if ( datas != null )
		{
			/* 打印每一条数据 */


			for ( MemoGroup memoGroup : datas )
			{
				System.out.println( memoGroup );
			}
		}
	}


	@Override


	public String createSql()
	{
		return("select id,name,created_time,modify_time from memo_group");
	}


	/*不直接打印返回的数据，而是按将数据存储到List，返回List */


	public < T > T handle( ResultSet resultSet )
	{
		/* ORM  对象关系映射 将对象和关系联系起来 */


		/* Java面向对象编程  => Object */


		/* 关系型数据库      => Relationship */


		/* 阻抗不匹配 */


		List<MemoGroup> memoGroupList = new ArrayList<>();


		try {
			/* 根据每行的列名取得对应的数据 */


			while ( resultSet.next() )
			{
				int id = resultSet.getInt( "id" );


				String name = resultSet.getString( "name" );


				LocalDateTime createdtime = resultSet.getTimestamp( "created_time" ).toLocalDateTime();


				LocalDateTime modifytime = resultSet.getTimestamp( "modify_time" ).toLocalDateTime();


				/* 把每一行数据转换为memoGroup对象 */


				MemoGroup memoGroup = new MemoGroup();


				memoGroup.setId( id );


				memoGroup.setName( name );


				memoGroup.setCreatedTime( createdtime );


				memoGroup.setModifyTime( modifytime );


				/* 再把对象放入集合中去 */


				memoGroupList.add( memoGroup );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		}


		return( (T) memoGroupList);
	}
}


/**
 *
 *
 *
 * 删除
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TemplateDrop extends JdbcTemplate {
	@Override


	public String createSql()
	{
		return("delete from memo_group where id = 2 ");
	}


	public Integer handle( Integer result )
	{
		return(result);
	}


	public static void main( String[] args )
	{
		JdbcTemplate jdbcTemplate = new TemplateDrop();


		System.out.println( "删除结果：" + jdbcTemplate.execute() );
	}
}
```

- **5.2.2 优化handle()**

```
import java.sql.*;


/**
 *
 *
 *
 * 优化：只有一个handle()
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class OptiTemplate {
	Connection connection = null;


	Statement statement = null;


	ResultSet resultSet = null;


	public void load()
	{
		try {
			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}
	}


	public void link()
	{
		try {
			connection = DriverManager.getConnection( "jdbc:mysql://127.0.0.1:3306/memo", "root", "1234" );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	public void createdStatement()
	{
		try {
			statement = connection.createStatement();
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	public void close()
	{
		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( statement != null )
		{
			try {
				statement.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}
	}


	/* 将sql语句和handle()当做参数 */


	public final <T, R> R execute( String sql, Handle<T, R> handle )
	{
		this.load();


		this.link();


		this.createdStatement();


		try {
			if ( sql.trim().startsWith( "select" ) || sql.trim().startsWith( "SELECT" ) )
			{
				resultSet = statement.executeQuery( sql );


				return(handle.handle( (T) resultSet ) );
			} else {
				Integer result = statement.executeUpdate( sql );


				return(handle.handle( (T) result ) );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			this.close();
		}


		return(null);
	}
}


/* 去掉抽象方法，将handle抽象为一个接口 */


interface Handle<T, R> {
	R handle( T t );
}
import java.sql.ResultSet;


import java.sql.SQLException;


import java.time.LocalDateTime;


import java.util.ArrayList;


import java.util.List;


/**
 *
 *
 *
 * 查询
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TestSelect {
	public static OptiTemplate optiTemplate = new OptiTemplate();


	/* 查找全部 */


	public static void selectAll()
	{
		String sql = "select id,name,created_time,modify_time from memo_group";


		List<MemoGroup> datas = optiTemplate.execute( sql, new ResultSetHandle() );


		if ( datas != null )
		{
			/* 打印每一条数据 */


			for ( MemoGroup memoGroup : datas )
			{
				System.out.println( memoGroup );
			}
		}
	}


	/* 通过name查找 */


	public static void selectByName( String name )
	{
		/* 注意：name要用''引起来，若不加''则认为是列名 */


		String sql = "select id,name,created_time,modify_time from memo_group where name='" + name + "'";


		List<MemoGroup> datas = optiTemplate.execute( sql, new ResultSetHandle() );


		if ( datas != null )
		{
			/* 打印每一条数据 */


			for ( MemoGroup memoGroup : datas )
			{
				System.out.println( memoGroup );
			}
		}
	}


	public static void main( String[] args )
	{
		selectAll();


		selectByName( "Go组" );


/*        //SQL注入攻击，本来只打印Java新组的这一条，结果全部打印 */


/*        selectByName("PHP组 'or 1=1 or 1='"); */
	}
}


class ResultSetHandle implements Handle<ResultSet, List<MemoGroup> >{
	@Override


	public List<MemoGroup> handle( ResultSet resultSet )
	{
		List<MemoGroup> memoGroupList = new ArrayList<>();


		try {
			/* 根据每行的列名取得对应的数据 */


			while ( resultSet.next() )
			{
				int id = resultSet.getInt( "id" );


				String name = resultSet.getString( "name" );


				LocalDateTime createdtime = resultSet.getTimestamp( "created_time" ).toLocalDateTime();


				LocalDateTime modifytime = resultSet.getTimestamp( "modify_time" ).toLocalDateTime();


				/* 把每一行数据转换为memoGroup对象 */


				MemoGroup memoGroup = new MemoGroup();


				memoGroup.setId( id );


				memoGroup.setName( name );


				memoGroup.setCreatedTime( createdtime );


				memoGroup.setModifyTime( modifytime );


				/* 再把对象放入集合中去 */


				memoGroupList.add( memoGroup );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		}


		return(memoGroupList);
	}
}


/**
 *
 *
 *
 * 更新
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TestUpdate {
	public static void main( String[] args )
	{
		OptiTemplate optiTemplate = new OptiTemplate();


		String sql = "update memo_group set id=2 where id=5";


		/* Lambda表达式 */


		Integer result = optiTemplate.execute( sql, (Handle<Integer, Integer>)result1 - > result1 );


		System.out.println( "更新结果" + result );
	}
}
```

#### 5.2.1 预编译命令

防止注入攻击，使用预编译命令。

```
import java.sql.*;


/**
 *
 *
 *
 * 预编译命令
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class PrecompileTemplate {
	Connection connection = null;


	PreparedStatement statement = null;


	ResultSet resultSet = null;


	public void load()
	{
		try {
			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}
	}


	public void link()
	{
		try {
			connection = DriverManager.getConnection( "jdbc:mysql://127.0.0.1:3306/memo", "root", "1234" );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	/* 创建预编译命令 */


	public void createdStatement( String sql )
	{
		try {
			statement = connection.prepareStatement( sql );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	public void close()
	{
		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( statement != null )
		{
			try {
				statement.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}
	}


	/* 将sql语句和handle()当做参数 */


	public final <T, R> R execute( String sql, String[] args, Handler<T, R> handle )
	{
		this.load();


		this.link();


		this.createdStatement( sql );


		for ( int i = 0; i < args.length; i++ )
		{
			try {
				/* 参数赋值 */


				/* 所有参数类型都是String */


				/* 参数下标，参数值 */


				/* 预编译命令的占位符从1 ... n */


				this.statement.setString( i + 1, args[i] );
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		try {
			if ( sql.trim().startsWith( "select" ) || sql.trim().startsWith( "SELECT" ) )
			{
				/* 预编译命令此处执行时，SQL语句不需要传入 */


				/* 创建预编译命令时，已经把编译后的sql语句传进去了 */


				/*下次使用时直接执行，不需要再次解析命令，效率比较高 */


				resultSet = this.statement.executeQuery();


				return(handle.handle( (T) resultSet ) );
			} else {
				Integer result = this.statement.executeUpdate();


				return(handle.handle( (T) result ) );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			this.close();
		}


		return(null);
	}
}


interface Handler<T, R> {
	R handle( T t );
}
import com.qqy.jdbc.optitemplate.MemoGroup;


import java.sql.ResultSet;


import java.sql.SQLException;


import java.time.LocalDateTime;


import java.util.ArrayList;


import java.util.List;


/**
 *
 *
 *
 * 查询
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TestSelect {
	public static PrecompileTemplate precompileTemplate = new PrecompileTemplate();


	/* 通过name查找 */


	public static void selectByName( String name )
	{
		/* ? 表示占位符   预编译命令下标从1 ... n */


		String sql = "select id,name,created_time,modify_time from memo_group where name=?";


/*        //查询语句2 */


/*        String sql = "select id,name,created_time,modify_time from memo_group where name in(?)"; */


		List<MemoGroup> datas = precompileTemplate.execute( sql, new String[] { name }, new ResultSetHandle() );


		if ( datas != null )
		{
			for ( MemoGroup memoGroup : datas )
			{
				System.out.println( memoGroup );
			}
		}
	}


	public static void main( String[] args )
	{
		/* 抵抗SQL注入攻击，无结果 */


		selectByName( "PHP组 'or 1=1 or 1='" );


/*        //对应查询语句2 */


/*        //in中， 如果？不能传入多个参数 -> 防止注入，将？的值当做一个整体 */


/*        selectByName("'PHP组','C++组'"); */
	}
}


class ResultSetHandle implements Handler<ResultSet, List<MemoGroup> >{
	@Override


	public List<MemoGroup> handle( ResultSet resultSet )
	{
		List<MemoGroup> memoGroupList = new ArrayList<>();


		try {
			/* 根据每行的列名取得对应的数据 */


			while ( resultSet.next() )
			{
				int id = resultSet.getInt( "id" );


				String name = resultSet.getString( "name" );


				LocalDateTime createdtime = resultSet.getTimestamp( "created_time" ).toLocalDateTime();


				LocalDateTime modifytime = resultSet.getTimestamp( "modify_time" ).toLocalDateTime();


				/* 把每一行数据转换为memoGroup对象 */


				MemoGroup memoGroup = new MemoGroup();


				memoGroup.setId( id );


				memoGroup.setName( name );


				memoGroup.setCreatedTime( createdtime );


				memoGroup.setModifyTime( modifytime );


				/* 再把对象放入集合中去 */


				memoGroupList.add( memoGroup );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		}


		return(memoGroupList);
	}
}
```

#### 5.2.2 事务管理

```
import java.sql.*;


/**
 *
 *
 *
 * 预编译命令
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class PrecompileTemplate {
	Connection connection = null;


	PreparedStatement statement = null;


	ResultSet resultSet = null;


	public void load()
	{
		try {
			Class.forName( "java.sql.Driver" );
		} catch ( ClassNotFoundException e ) {
			e.printStackTrace();
		}
	}


	public void link()
	{
		try {
			connection = DriverManager.getConnection( "jdbc:mysql://127.0.0.1:3306/memo", "root", "1234" );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	/* 创建预编译命令 */


	public void createdStatement( String sql )
	{
		try {
			statement = connection.prepareStatement( sql );
		} catch ( SQLException e ) {
			e.printStackTrace();
		}
	}


	public void close()
	{
		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( statement != null )
		{
			try {
				statement.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		if ( resultSet != null )
		{
			try {
				resultSet.close();
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}
	}


	/* 将sql语句和handle()当做参数 */


	public final <T, R> R execute( String sql, String[] args, Handler<T, R> handle )
	{
		this.load();


		this.link();


		this.createdStatement( sql );


		for ( int i = 0; i < args.length; i++ )
		{
			try {
				/* 参数赋值 */


				/* 所有参数类型都是String */


				/* 参数下标，参数值 */


				/* 预编译命令的占位符从1 ... n */


				this.statement.setString( i + 1, args[i] );
			} catch ( SQLException e ) {
				e.printStackTrace();
			}
		}


		try {
			if ( sql.trim().startsWith( "select" ) || sql.trim().startsWith( "SELECT" ) )
			{
				/* 预编译命令此处执行时，SQL语句不需要传入 */


				/* 创建预编译命令时，已经把编译后的sql语句传进去了 */


				/*下次使用时直接执行，不需要再次解析命令，效率比较高 */


				resultSet = this.statement.executeQuery();


				return(handle.handle( (T) resultSet ) );
			} else {
				Integer result = this.statement.executeUpdate();


				return(handle.handle( (T) result ) );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		} finally {
			this.close();
		}


		return(null);
	}
}


interface Handler<T, R> {
	R handle( T t );
}
import com.qqy.jdbc.optitemplate.MemoGroup;


import java.sql.ResultSet;


import java.sql.SQLException;


import java.time.LocalDateTime;


import java.util.ArrayList;


import java.util.List;


/**
 *
 *
 *
 * 查询
 *
 *
 *
 * Author:zz
 *
 *
 *
 */


public class TestSelect {
	public static PrecompileTemplate precompileTemplate = new PrecompileTemplate();


	/* 通过name查找 */


	public static void selectByName( String name )
	{
		/* ? 表示占位符   预编译命令下标从1 ... n */


		String sql = "select id,name,created_time,modify_time from memo_group where name=?";


/*        //查询语句2 */


/*        String sql = "select id,name,created_time,modify_time from memo_group where name in(?)"; */


		List<MemoGroup> datas = precompileTemplate.execute( sql, new String[] { name }, new ResultSetHandle() );


		if ( datas != null )
		{
			for ( MemoGroup memoGroup : datas )
			{
				System.out.println( memoGroup );
			}
		}
	}


	public static void main( String[] args )
	{
		/* 抵抗SQL注入攻击，无结果 */


		selectByName( "PHP组 'or 1=1 or 1='" );


/*        //对应查询语句2 */


/*        //in中， 如果？不能传入多个参数 -> 防止注入，将？的值当做一个整体 */


/*        selectByName("'PHP组','C++组'"); */
	}
}


class ResultSetHandle implements Handler<ResultSet, List<MemoGroup> >{
	@Override


	public List<MemoGroup> handle( ResultSet resultSet )
	{
		List<MemoGroup> memoGroupList = new ArrayList<>();


		try {
			/* 根据每行的列名取得对应的数据 */


			while ( resultSet.next() )
			{
				int id = resultSet.getInt( "id" );


				String name = resultSet.getString( "name" );


				LocalDateTime createdtime = resultSet.getTimestamp( "created_time" ).toLocalDateTime();


				LocalDateTime modifytime = resultSet.getTimestamp( "modify_time" ).toLocalDateTime();


				/* 把每一行数据转换为memoGroup对象 */


				MemoGroup memoGroup = new MemoGroup();


				memoGroup.setId( id );


				memoGroup.setName( name );


				memoGroup.setCreatedTime( createdtime );


				memoGroup.setModifyTime( modifytime );


				/* 再把对象放入集合中去 */


				memoGroupList.add( memoGroup );
			}
		} catch ( SQLException e ) {
			e.printStackTrace();
		}


		return(memoGroupList);
	}
}
```

 
