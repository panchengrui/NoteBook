[toc]

---



# 1.支持类

## SqlParameterSource 简介

可以使用SqlParameterSource实现作为来实现为命名参数设值，默认实现有 ：

1. MapSqlParameterSource实现非常简单，只是封装了java.util.Map；
2. BeanPropertySqlParameterSource封装了一个JavaBean对象，通过JavaBean对象属性来决定命名参数的值。
3. EmptySqlParameterSource 一个空的SqlParameterSource ，常用来占位使用

## RowMapper简介

这个接口为了实现sql查询结果和对象间的转换，可以自己实现，也可以使用系统实现，主要实现类有：

1. SingleColumnRowMapper ，sql结果为一个单列的数据，如`List , List,String,Integer`等
2. BeanPropertyRowMapper， sql结果匹配到对象 `List< XxxVO> , XxxVO`

# 2.插入/修改/删除数据,使用updateXXX方法

## 使用Map作为参数

**API:** `int update(String sql, Map paramMap)`

示例：

```java
Map<String, Object> paramMap = new HashMap<>();
paramMap.put("id", UUID.randomUUID().toString());
paramMap.put("name", "小明");
paramMap.put("age", 33);
paramMap.put("homeAddress", "乐山");
paramMap.put("birthday", new Date());
template.update( 
     "insert into student(id,name,age,home_address,birthday) values (:id,:name,:age,:homeAddress,:birthday)",
     paramMap
);
```



## 使用BeanPropertySqlParameterSource作为参数

**API:** `int update(String sql, SqlParameterSource paramSource)`

使用 BeanPropertySqlParameterSource作为参数

```java
public class StudentDTO{
    private String id;
    private String name;
    private String homeAddress;

     //getter,setter
}1234567
StudentDTO dto=new StudentDTO();//这个DTO为传入数据
dto.setId(UUID.randomUUID().toString());
dto.setName("小红");
dto.setHomeAddress("成都");
//------------------------------
template.update("insert into student(id,name,home_address) values (:id,:name,:homeAddress)",
                new BeanPropertySqlParameterSource(dto));
```



## 使用MapSqlParameterSource 作为参数

**API:** `int update(String sql, SqlParameterSource paramSource)`

//使用 MapSqlParameterSource 作为参数

```java
MapSqlParameterSource mapSqlParameterSource = new MapSqlParameterSource()
        .addValue("id", UUID.randomUUID().toString())
        .addValue("name", "小王")
        .addValue("homeAddress", "美国");
template.update("insert into student(id,name,home_address) values    
                   (:id,:name,:homeAddress)",mapSqlParameterSource);
```

或者

```java
Map<String, Object> paramMap = new HashMap<>();
paramMap.put("id", UUID.randomUUID().toString());
paramMap.put("name", "小明");
paramMap.put("homeAddress", "乐山");
//---------------
MapSqlParameterSource mapSqlParameterSource = new MapSqlParameterSource(paramMap);
```

# 3.查询

## 3.1 返回单列数据

### 3.1.1 返回单行单列数据

**API:** `public < T > T queryForObject(String sql, Map paramMap, Class requiredType)`

**API:** `public < T > T queryForObject(String sql, SqlParameterSource paramSource, Class requiredType)`

示例(注意EmptySqlParameterSource的使用)：

```java
Integer count = template.queryForObject(
                "select count(*) from student", new HashMap<>(), Integer.class);
String name = template.queryForObject( "select name from student where home_address  limit 1 ", EmptySqlParameterSource.INSTANCE, String.class); 
```



### 3.1.2 返回多行单列数据

**API:** `public < T> List< T> queryForList(String sql, Map paramMap, Class< T > elementType)`

**API:** `public < T> List< T> queryForList(String sql, SqlParameterSource paramSource, Class< T> elementType)`
示例：

```java
List< String> namelist = template.queryForList("select name from student", new HashMap<>(), String.class);
```



## 3.2 返回多列数据

### 3.2.1 返回单行多列数据

**API:** `public < T> T queryForObject(String sql, Map< String, ?> paramMap, RowMapper< T>rowMapper)`

**API:** `public < T> T queryForObject(String sql, SqlParameterSource paramSource, RowMapper< T> rowMapper)`
示例：

```java
Student  stu = template.queryForObject(
                "select * from student limit 1", new HashMap<>(), new BeanPropertyRowMapper<Student>(Student.class));
//BeanPropertyRowMapper会把下划线转化为驼峰属性
//结果对象可比实际返回字段多或者少
```

注意：这两个API也可以使用SingleColumnRowMapper返回单行单列数据

```java
String name = template.queryForObject(
                "select name from student limit 1", EmptySqlParameterSource.INSTANCE, new SingleColumnRowMapper<>(String.class));
```



### 3.2.2 返回Map形式的单行多列数据

**API:** `public Map< String, Object> queryForMap(String sql, Map< String, ?> paramMap)`

**API:** `public Map< String, Object> queryForMap(String sql, SqlParameterSource paramSource)`
示例：

```java
 Map< String, Object> studentMap = template.queryForMap("select * from student limit 1", new HashMap<>());
```



### 3.2.3 返回多行多列数据

**API:** `public < T> List< T> query(String sql, Map< String, ?> paramMap, RowMapper< T> rowMapper)`
**API:** `public < T> List< T> query(String sql, SqlParameterSource paramSource, RowMapper< T> rowMapper)`
**API:** `public < T> List< T> query(String sql, RowMapper< T> rowMapper)`
示例：

```java
List< Student> studentList = template.query(
                "select * from student",  
                new BeanPropertyRowMapper<>(Student.class)
);
```

同理，也可以使用SingleColumnRowMapper返回单行列表List< String>,List< Integer>等



### 3.2.4 返回Map形式的多行多列数据

**API:** `public List< Map< String, Object>> queryForList(String sql, Map< String, ?> paramMap)`
**API:** `public List< Map< String, Object>> queryForList(String sql, SqlParameterSource paramSource)`

示例：

```java
List<Map<String, Object>> mapList = template.queryForList(
                "select * from student", new HashMap<>());
```

# 总结

1. 开发中尽量使用NamedParameterJdbcTemplate代替JdbcTemplate，如果想使用JdbcTemplate，也可以通过NamedParameterJdbcTemplate#getJdbcOperations()获取
2. 不建议使用查询结构为Map的API