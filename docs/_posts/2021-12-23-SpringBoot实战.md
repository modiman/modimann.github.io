# 1 SpringBoot实战

## 1.1 根据md文件生成网页

1. 默认展示指定文件夹下的所有md文件

```java
   @ResponseBody
    @RequestMapping(value = "",method = RequestMethod.GET)
    public String pageIndex() {
        File folder = new File("E:/gitee/modige/_posts"); //展示该路径下所有md文件
        File[] listOfFiles = folder.listFiles(); //获取该路径下所有文件
        List<String> ul = new ArrayList<>();   //前端以<ul>标签展示，
        ul.add("<ul>");   
        for (File file : listOfFiles) {  //遍历所有文件
            if (file.isFile()) {
                String fname = file.getName();
                String li = "<li><a href='pages/"+fname+"'>"+fname+"</a></li>";
                //根据文件名生成<li>标签，<li>由<a>标签组成，拼接出形如<a href="pages/a.md">a.md</a>
                //的标签，点击后会向后台请求localhost:8080/pages/a.md，等待响应
                ul.add(li);
            }
        }
        ul.add("</ul>");
        String res = head;
        for(String s:ul){
            res+=s;
        }
        res +=  tail;
        //返回res,res是一个存放了拼接好的html源码字符串
        return res;
    }
```

效果：

![文件目录](https://gitee.com/modige/modige/raw/master/_posts/imgs/index.png)

2. 响应点击目录时发送的请求，生成详情页面

```java
 @ResponseBody
//这里以变量的形式接收参数，本地没有html文件，返回的是字符串形式的html源码,参数value对应文件名
    @RequestMapping(value = "/{value}",method = RequestMethod.GET)
    public String pageGenerator(@PathVariable String value) {
        String filePath = "E:/gitee/modige/_posts/" +value;
        File file = new File(filePath);
        String ans = head;
        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line = null;

            while ((line = br.readLine()) != null) {
               line = markdownService.lineToLabel(line);//将字符串形式的文件内容转化为html标签
                ans+=line;
            }
        }
        catch (Exception e){
            System.out.println(e);
//                    continue;
        }
        finally {
//                    continue;
        }
        return ans+tail;
    }
```

效果：

![页面详情展示](https://gitee.com/modige/modige/raw/master/_posts/imgs/pagedetail.png)





## 写数据到MongoDB

```java
@RequestMapping(value = "add")
    public String addEntity(){
        String path = "E:\\oneDrive\\桌面\\前端\\network-test\\src\\main\\resources\\static\\node_detail.json";

        File file = new File(path);
        String ans = "";
        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line = null;

            while ((line = br.readLine()) != null) {
                if(line.length()!=0)
                    ans+=line;
            }
        }
        catch (Exception e){
            System.out.println(e);
        }

        //存放整个文档
        JSONObject j =  JSONObject.parseObject(ans);
        List<JSONObject> jsonEntities = new ArrayList<>();
        List<Entity> entities = new ArrayList<>();
        for (String key:j.keySet()
        ) {
            JSONObject entity = (JSONObject)j.get(key);
            jsonEntities.add(entity);
        }
        for (JSONObject e:jsonEntities){
            Entity thisEntity = new Entity();
            if (e.get("identity")!=null) thisEntity.setIdentity(e.get("identity").toString());

            if (e.get("name")!=null) thisEntity.setName((String) e.get("name"));
            if (e.get("label")!=null) thisEntity.setLabel((String) e.get("label"));

            if (e.get("中文名称")!=null) thisEntity.setChineseName((String) e.get("中文名称"));
            if (e.get("英文名称")!=null) thisEntity.setEnglishName((String) e.get("英文名称"));
            if (e.get("英文缩写")!=null) thisEntity.setAbbreName((String) e.get("英文缩写"));
            mongoService.addEntity(thisEntity);
            System.out.println(thisEntity);


        }
        return "插入成功";
    }

```

## 处理Json文件

```java
@RequestMapping(value = "/entities",method = RequestMethod.GET)
    public JSONObject entitiesJson(){
        File file = new File("E:\\oneDrive\\桌面\\前端\\network-test\\src\\main\\resources\\static\\node_detail.json");
        String ans = "";
        try {
            BufferedReader br = new BufferedReader(new FileReader(file));
            String line = null;

            while ((line = br.readLine()) != null) {
                if(line.length()!=0)
                    ans+=line;
            }
        }
        catch (Exception e){
            System.out.println(e);
        }
        finally {
        }

        try {
            JSONObject j = JSONObject.parseObject(ans);
            System.out.println("取值成功");
            return j;
        }
        catch (Exception e){
            System.out.println(e);
        }
        finally {

        }
        return null;

    }
```



# mybatis 参数

##  1. parameterType 

**输入参数  可以为java基本数据类型如Integer,String[]等，也可以是自定义的数据类型   **  


例1：

```mysql 
<select id="getTagByEmployee" parameterType="String" resultMap="tag_employee">       
        select *
        from tag
        where employee_id = #{employee_id}
</select> 
```

其中employee_id是函数的形参变量名  
对应接口函数：  
Tag getTagByEmployee(String employee_id);

### 例2：

```mysql
        <insert id="addTag" parameterType="Tag">  
        
        INSERT INTO `vishishtadvaita`.`tag`  
        
        (`id`, `last_known_x`, `last_known_y`, `name`, `last_known_area_id`, `isknown`, isonline, `type`, tag_employee)  
        
        VALUES (UUID(), #{lastKnownX}, #{lastKnownY}, #{name}, #{lastKnownArea.name}, #{isknown}, #{isonline}, #{type},  
        
        #{tagEmployee.employee_id});  
</insert>  
```

这里的parameType就是自定义的Tag型数据，包含name等属性，这种输入类型常用于插入和修改操作  

对应接口函数：

void addTag(Tag tag);  

## 2.  resultType

查询结果类型参数 resultType ,resultType可以包含java基本数据类型List等，也可以是自定义的数据类型，如之前的Tag

例1.  java基本数据类型String 这一类型常见于查询数据表中的某一列，比如查询tag表的属性（type）

```mysql
<select id="selectTagType" resultType="java.lang.String">
        
  SELECT DISTINCT `type`
  
  FROM tag
  
</select>
```

虽然其返回结果是一个由字符串String组成的List，但在resultType中写的是String而非List

对应的接口函数为：  List<String> selectTagType()
        

例2.自定义的数据类型Tag

  如果查询结果为一整张表或表的多个属性，不能像只查询一个属性那样用String就可以表示，resultType就要用与数据库表对应的实体类，也就是自定义

  的数据类型了

  首先要在命名空间标签中标注对应的数据类型，

```mysql
<mapper namespace="cn.edu.bjut.vishishtadvaita.mapper.mockloceng.TagMapper">
```

  之后再写查询语句

```mysql      
<select id="selectTags" resultMap="tag">
  
    select*
    
    from tag        

</select>
```

*  这里跟上例相同，虽然返回的结果是一个由Tag组成的标签，但resultType对应的仍然是tag
*  对应的接口函数：
*  List<Tag> selectTags();

# 3. resultMap

查询结果类型参数 resultMap，这一参数扩大了数据库的可操作性，经常用于多表查询

使用resultMap是因为现有的数据类型不足以表示数据库查询结果，需要自定义返回值的数据类型 ，或者数据表中的属性名与自定义数据类型中属性名不同时

例如tag的name属性,在tag.java 中为 name,在数据表中却为tag_name,这时如果把返回值设置成resultType = "tag"，就会查不出结果       

例1. 多表查询

假设有Tag和Employee，Area三个实体类，三张表，Tag有外键tag_enployee用来表示tag 与employee之间一对一的关系
tag有外键last_known_area_id 用来表示与area之间一对一的关系

* 这里给出 Tag.java Employee.java  Area.java 以及三张表的建议表示
  Tag.java:

```Java
@Entity
@Table(name = "tag")
public class Tag extends BaseModel {
@Column(name = "name")
private String name;

@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "tag_employee")
private Employee tagEmployee;

@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "last_known_area_id")
private Area lastKnownArea;}
```

tag表
id    name   tag_employee last_known_area_id  
首先从这里就可以看出查询tags时要用resultMap而非resultType的原因了，
即java类中的属性与数据表中对应的属性不同名

当只需要tag的信息时自然简单，但是有时候需要知道三张表之间对应的关系

比如想知道每个标签对应的员工是谁，属于哪个区域，这就需要三表连接查询，这样得到的结果既不能用tag,也不能用employee,area表示

因此需要用到reultMap. 下面给出三表连接查询的一个例子      

## 3.1. 首先 准备employee_as_member 和 area_as_member，注意这两个标签卸载其各自的.xml文件中而非tag.xml中

```mysql
<resultMap id="employee_as_member" type="Employee">
<id column="employee_id" property="employee_id"/>
<result column="employee_name" property="employee_name"/>
</resultMap>   
<resultMap id="area_as_member" type="Area">
<id column="area_id" property="id"/>
<result column="area_name" property="name"/>
</resultMap>
```

注意：column属性对应的是数据库表中的属性名，property对应的则是java类中的属性名  

在sql语句中用到的是column属性，例如select area_id 而非id

## 3.2. 写tag自己的resultMap

```mysql
<resultMap id="tag" type="Tag">
<id column="tag_id" property="id"/> 
<result column="tag_name" property="name"/> 

<association property="tagemployee"
               resultMap="cn.edu.bjut.vishishtadvaita.mapper.employee.EmployeeMapper.employee_as_member"/>
<association property="lastKnownArea"
            resultMap="cn.edu.bjut.vishishtadvaita.mapper.mockloceng.AreaMapper.area_as_member"/>
</resultMap>
```



注意：上述resultMap中涉及四个属性，分别是tag自身的id、name以及外键tagemployee,lastKnownArea(对应tag.java中的tagEmployee和tagEmployee)


resultMap="cn.edu.bjut.vishishtadvaita.mapper.mockloceng.AreaMapper.area_as_member"则对应第一步写的两个标签



## 3.3. 查询语句

```mysql
<select id="selectTagsAndEmployee" resultMap="tag_employee">
SELECT  * FROM
(SELECT tag.id AS tag_id,

    tag.name AS tag_name,

    employee.employee_name AS employee_name,
    employee.employee_id AS employee_id
        FROM tag
        LEFT JOIN employee ON employee.employee_id = tag.tag_employee) AS t1
LEFT JOIN (SELECT area.id AS area_id,area.name AS area_name,  FROM `area`)AS t2 ON t1.last_known_area_id = t2.area_id

    </select>
```

注意：这里的select tag.name AS tag_name和SELECT area.id AS area_id等是有必要的，不把area.id表示为数据表中的area_id查询不到结果

