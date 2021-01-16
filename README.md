基于ssm框架实现的视频电影系统

项目介绍
----

  本系统使用Spring+SpringMVC+MyBatis架构，数据库使用MySQL,共分为以下四大模块：
1. 登录注册及用户管理
提供登录注册以及个人基本资料的修改查询
2. 资源管理
管理员从后端添加电影资源并赋予资源的信息如名称、图片、时长、电影上映时间、评分、导演、演员、类型、地区等信息，发布成功在后端以及用户页面军可查看播放。
3. 评论管理
在增加互动性且维护良好环境的同时，对用户评论内置了敏感词汇过滤。
4. 收藏管理
用户可以将喜欢的电影添加到收藏中心，便于随时观看影片。


项目适用人群
----
正在做毕设的学生，或者需要项目实战练习的Java学习者

开发环境：
-----
1. jdk 8
2. intellij idea
3. tomcat 8.5.40
4. mysql 5.7

所用技术：
-----
1. Spring+SpringMVC+MyBatis
2. layui
3. jsp

项目访问地址
----
前端访问地址
```
http://localhost:8090/index
```
后台访问地址
```
http://localhost:8090/admin/index
admin/admin
```
 
项目截图
----
- 首页
![](/src/image/首页.png)
- 热门电影
![](/src/image/热门电影.png)
- 电影详情
![](/src/image/电影详情.png)
- 电影播放
![](/src/image/播放界面.png)
- 后端-敏感词
![](/src/image/后端-敏感词.png)
- 后端-评价列表
![](/src/image/后端-评价列表.png)
- 后端-收藏列表
![](/src/image/后端-收藏列表.png)
- 后端-用户列表
![](/src/image/后端-用户列表.png)

数据库配置
----
1. 数据库配置信息
```
#配置文件
jdbc.url=jdbc:mysql://localhost:3306/movies?characterEncoding=UTF-8
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.username=root
jdbc.password=root123
```
2. 数据库配置加载
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 引入配置文件 -->  
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
    	<property name="location" value="classpath:pro.properties" />  
	</bean>
    <!-- 定义数据源Bean -->
    <!-- Druid -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    	<property name="driverClassName" value="${jdbc.driverClass}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

    <!-- 注册SqlSessionFactoryBean -->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- 自动扫描mappers.xml文件 -->
        <property name="mapperLocations" value="classpath*:com/lrfalse/dao/**/*.xml" />
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>
    
    <!-- DAO接口所在包名，Spring会自动查找其下的类 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.lrfalse.dao" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>
    
    <!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->  
    <bean id="transactionManager"  
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">  
        <property name="dataSource" ref="dataSource" />  
    </bean>
</beans>
```
3. springMvc配置
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc 
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
        
	<!-- 启动自动扫描 -->
	<context:component-scan	base-package="com.lrfalse" />
	<!-- 注册MVC注解驱动 -->  
    <mvc:annotation-driven />    
    <!-- 静态资源可访问的设置方式 -->
	<mvc:default-servlet-handler />
	<mvc:resources location="/resource/"  mapping="/resource/**" />
    <mvc:resources location="/" mapping="/**/*.js"/>
    <mvc:resources location="/" mapping="/**/*.css"/>
    <mvc:resources location="/" mapping="/**/*.mp4"/>
    <mvc:resources location="/assets/" mapping="/assets/**/*"/>
    <mvc:resources location="/3rd/" mapping="/assets/**/*"/>
    <mvc:resources location="/assets/pc" mapping="/assets/pc/**/*"/>
 	<!--装配Swagger配置-->
    <bean class="com.lrfalse.common.SwaggerConfig"/>
	<!--避免IE执行AJAX时，返回JSON出现下载文件 -->
	<bean id="mappingJacksonHttpMessageConverter"
		class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
		<property name="supportedMediaTypes">
			<list>
				<value>text/html;charset=UTF-8</value>
			</list>
		</property>
	</bean>
    <!--增加tkmybatis注解依赖-->
    <bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.lrfalse.dao"/>
        <property name="beanName" value="normal"/>
    </bean>

	<!-- 配置视图解析器，可以显式设置，也可以不设置，不设置会依据SpringMVC的默认设置 -->
	<bean id="viewResolver"	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/" />
		<property name="suffix" value=".jsp" />
	</bean>
	<!-- 配置文件上传，如果没有使用文件上传可以不用配置，当然如果不配，那么配置文件中也不必引入上传组件包 -->
	<bean id="multipartResolver"  
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">  
        <!-- 默认编码 -->
        <property name="defaultEncoding" value="utf-8" />  
        <!-- 文件大小最大值 -->
        <property name="maxUploadSize" value="10485760000" />  
        <!-- 内存中的最大值 -->
        <property name="maxInMemorySize" value="40960" />  
    </bean>
</beans>
```
4. web.xml 配置
```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	version="3.1" metadata-complete="true">

	<display-name>Archetype Created Web Application</display-name>
    <welcome-file-list>
        <welcome-file>/index</welcome-file>
    </welcome-file-list>

	<!-- Spring和mybatis的配置文件 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-mybatis.xml</param-value>
	</context-param>
	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<async-supported>true</async-supported>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

	<!-- Spring监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- 防止Spring内存溢出监听器 -->
	<listener>
		<listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
	</listener>
	<session-config>
		<session-timeout>600</session-timeout>
	</session-config>
	<!-- Spring MVC servlet -->
	<servlet>
		<servlet-name>SpringMVC</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring-mvc.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
		<async-supported>true</async-supported>
	</servlet>
	<servlet-mapping>
		<servlet-name>SpringMVC</servlet-name>
		<!-- 此处可以可以配置成*.do，对应struts的后缀习惯 -->
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	<welcome-file-list>
		<welcome-file>/index.jsp</welcome-file>
	</welcome-file-list>

    <servlet-mapping>
           <servlet-name>default</servlet-name>
            <url-pattern>*.js</url-pattern>
           <url-pattern>*.css</url-pattern>
           <url-pattern>*.png</url-pattern>
           <url-pattern>/assets/*"</url-pattern>
         <url-pattern>/3rd/*</url-pattern>
          </servlet-mapping>
</web-app>
```

业务代码
-----
1. 电影首页
```diff
@GetMapping(value = {"index",""})
public ModelAndView index(ModelAndView mav, HttpSession session){
    List<Movies> randomList=moviesService.randomMovies();	//预告电影
    List<Movies> hotList=moviesService.findHotMovies();		//热门电影
    List<Movies> newestList=moviesService.findNewestList();	//最新电影
    List<Movies> indexNewestList=new ArrayList<>();
    for(Movies movies:newestList){
        List<MoviesType> moviesTypes=moviesTypeService.findListById(movies.getId());
        movies.setMoviesTypes(moviesTypes);
        indexNewestList.add(movies);
    }
    List<Movies> recommendList;
    Person person=(Person) session.getAttribute("loginUser");
    if(person!=null){	//如果登录，查看收藏类型的电影 ，
        recommendList=moviesService.findRecommendList(person.getId());	//电影推荐
    }else{//如果没登录，查询热门的10个电影
        recommendList=moviesService.findHotMovies();
    }
    mav.addObject("randomList",randomList);
    mav.addObject("hotList",hotList);
    mav.addObject("newestList",newestList);
    mav.addObject("recommendList",recommendList);
    mav.setViewName("pc/index");
    return mav;
}
//前端jsp
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>程序帮，爱电影</title>
    <meta name="keywords" content="">
    <meta name="description" content="">
    <script src="${pageContext.request.contextPath}/assets/pc/js/jquery-1.11.3.min.js"></script>
    <script src="${pageContext.request.contextPath}/assets/pc/js/index.js"></script>
    <link rel="stylesheet" href="${pageContext.request.contextPath}/assets/pc/css/index.css">
</head>
<body>
<jsp:include page="head.jsp"/>

<div class="gg">公告：如果你喜欢程序帮，爱电影，希望你能把程序帮，爱电影推广给更多的朋友，多谢！</div>
<div class="out l">
    <div class="hot">
        ![](${pageContext.request.contextPath}/assets/pc/images/ribbon.png)
    </div>
    <ul class="img ">
        <li>
            <a href="# ">
                ![](${pageContext.request.contextPath}/assets/pc/images/1.jpg )
            </a>
        </li>
        <li>
            <a href="# ">
                ![](${pageContext.request.contextPath}/assets/pc/images/2.jpg )
            </a>
        </li>
        <li>
            <a href="# ">
                ![](${pageContext.request.contextPath}/assets/pc/images/3.jpg )
            </a>
        </li>
        <li>
            <a href="# ">
                ![](${pageContext.request.contextPath}/assets/pc/images/4.jpg )
            </a>
        </li>
        <li>
            <a href="# ">
                ![](${pageContext.request.contextPath}/assets/pc/images/5.jpg )
            </a>
        </li>
    </ul>
    <ul class="num " >
    </ul>

    <div class="left btn "><</div>
    <div class="right btn right-btn">></div>
</div>
<div class="box-line">
<%--    <span>随机电影资源</span>--%>
    <span>电影预告</span>
</div>
<div class="menu">
    <div class="menu_item">
        <c:forEach items="${randomList}" var="randoms">
            <div class="menu_item_img">
                <a href="${pageContext.request.contextPath}/details/${randoms.id}">
                    ![](${fn:substring(randoms.img, 0, fn:indexOf(randoms.img, )
                    ![](${fn:substring(randoms.img,  fn:indexOf(randoms.img, )
                </a>
            </div>
        </c:forEach>
    </div>
</div>
<main>
    <div class="main">
        <div class="main_title">
            <h3>最新电影</h3>
            <a href="">更多>></a>
        </div>
        <c:forEach items="${newestList}" var="newest">
            <div class="main_item">
                <a class="main_item_img" href="${pageContext.request.contextPath}/details/${newest.id}">
                    ![](${fn:substring(newest.img, 0, fn:indexOf(newest.img, )
                    ![](${pageContext.request.contextPath}/assets/pc/images/play-icon.png)
                    <span class="cq">超清</span>
                </a>
                <div class="main_item_title">
                    <h1>
                        <a href="#" target="_blank" title="${newest.name}">${newest.name}</a>
                        <em> - ${newest.score}分</em>
                    </h1>
                    <div class="main_item_title_lei">
                        类型：
                        <c:forEach items="${newest.moviesTypes}" var="type" varStatus="index">
                            <c:if test="${index.index<=3}">
                                <a class="movietype"  href="#">${type.typeName}</a>
                            </c:if>
                        </c:forEach>
                    </div>
                </div>
            </div>
        </c:forEach>
        <div class="cl"></div>
    </div>
    <div class="right">
        <div class="right_title">
            <h3>热门电影</h3>
        </div>
        <c:forEach items="${hotList}" var="hot" varStatus="index" >
            <div class="list-group">
                <a class="list-group-item"   target="_blank" href="${pageContext.request.contextPath}/details/${hot.id}">
                    <span class="${index.index<3?'square-item-red':'square-item-blue'}">${index.index+1}</span>
                   ${hot.name}
                </a>
            </div>
        </c:forEach>

    </div>
</main>
<div class="main3">
    <div class="main3_title">
        <h3>电影推荐</h3>
        <a href="">更多>></a>
    </div>
    <c:forEach items="${recommendList}" var="recommend" varStatus="index">
        <c:if test="${index.index<10}">
            <div class="main3_item">
                <div class="main3_item_img">
                    <a href="#">
                        ![](${fn:substring(recommend.img, 0, fn:indexOf(recommend.img, )
                    </a>
                    <span class="lg">蓝光</span>
                    <div class="main3_item_title">
                        <h1>
                            <a href="#" target="_blank" title="${recommend.name}">${recommend.name}</a>
                            <em> - ${recommend.score}分</em>
                        </h1>
                        <div class="main3_item_title_lei">
                            类型：
                            <c:forEach items="${recommend.moviesTypes}" var="type" varStatus="index">
                                <c:if test="${index.index<=3}">
                                    <a class="movietype"  href="#">${type.typeName}</a>
                                </c:if>
                            </c:forEach>
                        </div>
                    </div>
                </div>
            </div>
        </c:if>
    </c:forEach>
</div>
</body>
</html>
```

2. 电影收藏
```diff
@ResponseBody
@PostMapping(value = {"addCollection"})
@ApiOperation(value = "添加收藏",response = MyCollection.class)
public Map addCollection( LikeMoviesDto likeMoviesDto){
    Map map=ResponseMessage.success();
    try{
        MyCollection myCollection=new MyCollection();
        BeanUtils.copyProperties(likeMoviesDto,myCollection);
        int result=myCollectionService.addCollection(myCollection);
        if(result==2){
            myCollectionService.delCollection(myCollection);
            map=ResponseMessage.success("取消收藏成功");
        }else if(result==1){
            Map data=new HashMap();
            data.put("collectionId",myCollection.getId());
            map=ResponseMessage.success("电影收藏成功");
            map.put("data",data);
        }else{
            map=ResponseMessage.fail("电影收藏失败");
        }
    }catch (Exception e){
        e.printStackTrace();
        map=ResponseMessage.fail("电影收藏失败");
    }
    return map;
}
//ajax请求
function addCollection() {
    var user='${loginUser}';
    if(null==user||user==''){
        layer.msg('请先登录', {icon: 5});
    }else{
        var data={mId:${details.movies.id},uId:'${loginUser.id}'};
        $.post('${pageContext.request.contextPath}/addCollection', data, function (data) {
            console.log(data);
            if (200 == data.returnCode) {
                layer.msg(data.returnMsg, {icon: 1, time: 1500}, function () {
                    window.location.reload();
                });
            } else {
                layer.closeAll('loading');
                layer.msg(data.returnMsg, {icon: 5});
            }
        }, 'JSON');
    }
}
```

项目后续
----
其他ssh，springboot版本后续迭代更新，持续关注

程序有问题联系[程序帮](http://suo.nz/530ijn)