# <center>全网最详细 Spring Security 使用说明</center> #

> 项目开发过程中，几乎每一次的项目交付都会遇到系统认证与安全问题。而之前的方法都是“兵来将挡水来土掩”，没有一套完善的开发流程与规范，受尽折磨。所以这次借助开发新项目的机会，尝试了 `Spring Security` 框架来进行安全模块的开发。  

## pom.xml ##

	<!-- Spring security start -->
	<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-core</artifactId>
		<version>4.2.3.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-config</artifactId>
		<version>4.2.3.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-taglibs</artifactId>
		<version>4.2.3.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-web</artifactId>
		<version>4.2.3.RELEASE</version>
	</dependency>
	<!-- Spring security end -->  

想要使用 `Spring Security` 安全框架，首先需要引入上述相关 `jar` 包。  

## web.xml ##

	    <!-- spring security 的过滤器配置 -->
	    <filter>
	        <filter-name>springSecurityFilterChain</filter-name>
	        <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
	    </filter>
	    <filter-mapping>
	        <filter-name>springSecurityFilterChain</filter-name>
	        <url-pattern>/*</url-pattern>
	    </filter-mapping>

在 `web.xml` 文件中添加 `Spring Security` 过滤器。  

## spring-security.xml ##

	<?xml version="1.0" encoding="UTF-8"?>
	<beans:beans
	        xmlns="http://www.springframework.org/schema/security"
	        xmlns:beans="http://www.springframework.org/schema/beans"
	        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	        xsi:schemaLocation="http://www.springframework.org/schema/security
		http://www.springframework.org/schema/security/spring-security-4.2.xsd
	    http://www.springframework.org/schema/beans 
	    http://www.springframework.org/schema/beans/spring-beans-4.2.xsd">
	    <http auto-config='true'>
	        <!--使用表单方式进行登录验证，并制定登录失败后的自定义处理器-->
	        <form-login login-page="/login.jsp" authentication-failure-handler-ref="myAuthenticationFailureHandler"/>
	        <!--配置需要拦截的资源。需要注意的是，配置顺序必须根据拦截范围的大小从小到大依次配置-->
	        <!--放行 login.jsp ，让所有人都能访问-->
	        <intercept-url pattern="/login.jsp" access="permitAll()"/>
	        <!--只有拥有 ROLE_USER 权限的 user 用户可以访问 user.jsp 页面-->
	        <intercept-url pattern="/jspDispatcher/user" access="hasRole('ROLE_USER')"/>
	        <!--只有拥有 ROLE_ADMIN 权限的 admin 用户可以访问 admin.jsp 页面-->
	        <intercept-url pattern="/jspDispatcher/admin" access="hasRole('ROLE_ADMIN')"/>
	        <!-- 只有登录过的用户才能访问 -->
	        <intercept-url pattern="/**" access="isFullyAuthenticated()"/>
	        <!--关闭 CSRF 安全验证-->
	        <csrf disabled="true"/>
	    </http>
	
		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~  1 Start  ~~~~~~~~~~~~~~~~~~~~~~~~ -->
	    <!--通过在该 xml 文件中进行用户配置，该方式为基础配置，一般不用在生产环境中。当 Java 代码认证方式开启时，该方式失效。-->
	    <!--<authentication-manager>
	        <authentication-provider>
	            <user-service>
	                &lt;!&ndash;配置用户名、密码、角色&ndash;&gt;
	                <user name="admin" password="admin" authorities="ROLE_ ADMIN"/>
	                <user name="user" password="user" authorities="ROLE_USER"/>
	            </user-service>
	        </authentication-provider>
	    </authentication-manager>-->
		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~  1 End  ~~~~~~~~~~~~~~~~~~~~~~~~ -->

		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~  2 Start  ~~~~~~~~~~~~~~~~~~~~~~~~ -->
	    <!--通过 Java 代码进行用户信息认证，该方式也是生产环境中的必需方式。-->
	    <authentication-manager>
	        <!--指定动态获取用户认证信息的类-->
	        <authentication-provider user-service-ref="myUserDetailService">
	            <!--绑定用户密码对比类-->
	            <password-encoder ref="passwordEncoder"/>
	        </authentication-provider>
	    </authentication-manager>
	
	    <!--注册动态获取用户认证信息类-->
	    <beans:bean id="myUserDetailService" class="com.supreme.security.MyUserDetailService"></beans:bean>
	    <!--注册用户密码对比类-->
	    <beans:bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>
		<!-- ~~~~~~~~~~~~~~~~~~~~~~~~  2 End  ~~~~~~~~~~~~~~~~~~~~~~~~ -->

	    <!--注册自定义登录失败处理器-->
	    <beans:bean id="myAuthenticationFailureHandler" class="com.supreme.security.MyAuthenticationFailureHandler"/>
	</beans:beans>

上面的配置文件默认使用的并不是基础配置，因为在实际使用中我们也不会使用基础配置。详细解析如下：

1、`http` 标签内定义了验证方式和资源拦截规则等，这是不管是使用 `xml` 配置用户信息还是通过 `Java` 代码从数据动态获取用户信息都会用的配置。  
2、标记为 `1` 的部分默认是不起作用的，因为用户名、密码、权限都是通过明文写死在配置文件中的，所以事实应用价值不大。但在某些特殊情况下也有其使用价值。如果想使用把标记为 `2` 的部分注释掉，并把 `1` 部分打开即可。  
3、标记为 `2` 的部分是本示例中默认的配置。该配置方式定义用户信息认证是通过 `Java` 代码动态读取数据库的方式获取用户信息。同时定义了用于对比加密后的用户密码的类。  
4、文件提供了两种方式的登录事件处理器，一个为默认的登录成功后进行页面跳转，另一个为登录失败后返回 `JSON` 格式数据信息，这两种方式的样例实现，可以很好的满足日常工作中的开发需求。

## spring-mvc.xml ##
	
	    <!-- 定义跳转的文件的前后缀 ，视图模式配置 -->
	    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <property name="prefix" value="/WEB-INF/jsp/"/>
	        <property name="suffix" value=".jsp"/>
	    </bean>

## UserLoginDTO.java ##

	import java.io.Serializable;

	/**
	 * @ClassName UserLoginDTO
	 * @Description  用户登录认证模块 DTO
	 * @Author Supreme_Sir
	 * @Date 2019/7/10 9:48
	 * @Version 1.0
	 **/
	public class UserLoginDTO implements Serializable {
	    private String userID;
	
	    private String userName;
	
	    private String realName;
	
	    private String password;
	
	    private String createTime;
	
	    private String lastLogTime;
	
	    private Integer avaliable;
	
	    private Integer overdue;
	
	    private Integer locked;
	
	    private Integer licence;
	
	    private String privilegeID;
	
	    private String privilegeName;
	
	    private String privilegeTag;
	
	    private static final long serialVersionUID = 1L;
	
	    public String getUserID() {
	        return userID;
	    }
	
	    public void setUserID(String userID) {
	        this.userID = userID;
	    }
	
	    public String getUserName() {
	        return userName;
	    }
	
	    public void setUserName(String userName) {
	        this.userName = userName;
	    }
	
	    public String getRealName() {
	        return realName;
	    }
	
	    public void setRealName(String realName) {
	        this.realName = realName;
	    }
	
	    public String getPassword() {
	        return password;
	    }
	
	    public void setPassword(String password) {
	        this.password = password;
	    }
	
	    public String getCreateTime() {
	        return createTime;
	    }
	
	    public void setCreateTime(String createTime) {
	        this.createTime = createTime;
	    }
	
	    public String getLastLogTime() {
	        return lastLogTime;
	    }
	
	    public void setLastLogTime(String lastLogTime) {
	        this.lastLogTime = lastLogTime;
	    }
	
	    public Integer getAvaliable() {
	        return avaliable;
	    }
	
	    public void setAvaliable(Integer avaliable) {
	        this.avaliable = avaliable;
	    }
	
	    public Integer getOverdue() {
	        return overdue;
	    }
	
	    public void setOverdue(Integer overdue) {
	        this.overdue = overdue;
	    }
	
	    public Integer getLocked() {
	        return locked;
	    }
	
	    public void setLocked(Integer locked) {
	        this.locked = locked;
	    }
	
	    public Integer getLicence() {
	        return licence;
	    }
	
	    public void setLicence(Integer licence) {
	        this.licence = licence;
	    }
	
	    public String getPrivilegeID() {
	        return privilegeID;
	    }
	
	    public void setPrivilegeID(String privilegeID) {
	        this.privilegeID = privilegeID;
	    }
	
	    public String getPrivilegeName() {
	        return privilegeName;
	    }
	
	    public void setPrivilegeName(String privilegeName) {
	        this.privilegeName = privilegeName;
	    }
	
	    public String getPrivilegeTag() {
	        return privilegeTag;
	    }
	
	    public void setPrivilegeTag(String privilegeTag) {
	        this.privilegeTag = privilegeTag;
	    }
	
	    public static long getSerialVersionUID() {
	        return serialVersionUID;
	    }
	
	    @Override
	    public String toString() {
	        return "UserLoginDTO{" +
	                "userID='" + userID + '\'' +
	                ", userName='" + userName + '\'' +
	                ", realName='" + realName + '\'' +
	                ", password='" + password + '\'' +
	                ", createTime='" + createTime + '\'' +
	                ", lastLogTime='" + lastLogTime + '\'' +
	                ", avaliable=" + avaliable +
	                ", overdue=" + overdue +
	                ", locked=" + locked +
	                ", licence=" + licence +
	                ", privilegeID='" + privilegeID + '\'' +
	                ", privilegeName='" + privilegeName + '\'' +
	                ", privilegeTag='" + privilegeTag + '\'' +
	                '}';
	    }
	}

## UserLoginDTOMapper.java ##

	import com.supreme.dto.UserLoginDTO;

	import java.util.List;
	
	/**
	 * @ClassName UserLoginDTOMapper
	 * @Description
	 * @Author Supreme_Sir
	 * @Date 2019/7/10 10:00
	 * @Version 1.0
	 **/
	public interface UserLoginDTOMapper {
	
	    //  根据用户名查询用户权限
	    public List<UserLoginDTO> selectByUserName(String userName);
	}

## UserLoginDTOMapper.xml ##

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
	<mapper namespace="com.supreme.dao.UserLoginDTOMapper">
	    <resultMap id="BaseResultMap" type="com.supreme.dto.UserLoginDTO">
	        <id column="user_id" property="userID" jdbcType="VARCHAR"/>
	        <result column="user_name" property="userName" jdbcType="VARCHAR"/>
	        <result column="user_realname" property="realName" jdbcType="VARCHAR"/>
	        <result column="user_password" property="password" jdbcType="VARCHAR"/>
	        <result column="user_createtime" property="createTime" jdbcType="VARCHAR"/>
	        <result column="user_lastlogtime" property="lastLogTime" jdbcType="VARCHAR"/>
	        <result column="user_avaliable" property="avaliable" jdbcType="INTEGER"/>
	        <result column="user_overdue" property="overdue" jdbcType="INTEGER"/>
	        <result column="user_locked" property="locked" jdbcType="INTEGER"/>
	        <result column="user_licence" property="licence" jdbcType="INTEGER"/>
	        <result column="privilege_id" property="privilegeID" jdbcType="VARCHAR"/>
	        <result column="privilege_name" property="privilegeName" jdbcType="VARCHAR"/>
	        <result column="privilege_tag" property="privilegeTag" jdbcType="VARCHAR"/>
	    </resultMap>
	
	    <select id="selectByUserName" resultMap="BaseResultMap" parameterType="java.lang.String">
	        SELECT
	            u.user_id,
	            u.user_name,
	            u.user_password,
	            u.user_realname,
	            u.user_createtime,
	            u.user_lastlogtime,
	            u.user_avaliable,
	            u.user_overdue,
	            u.user_locked,
	            u.user_licence,
	            p.privilege_id,
	            p.privilege_name,
	            p.privilege_tag
	        FROM
	            privilege p
	                INNER JOIN role_privilege rp ON rp.role_privilege_privilegeid = p.privilege_id
	                INNER JOIN user_role ur ON ur.user_role_roleid = rp.role_privilege_roleid
	                INNER JOIN `user` u ON ur.user_role_userid = u.user_id
	                AND u.user_name = #{userName,jdbcType=VARCHAR}
	    </select>
	</mapper>

## CommonUtil.java ##
	
	import org.springframework.security.core.GrantedAuthority;
	import org.springframework.security.core.authority.SimpleGrantedAuthority;
	import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
	import org.springframework.security.crypto.password.PasswordEncoder;
	import java.util.Map;
	import java.util.HashMap;
	import java.util.List;
	import java.util.ArrayList;
	import com.supreme.dto.UserLoginDTO;
	
	public class CommonUtil {
	    /**
	     * @Author  Supreme_Sir
	     * @Description  将查询到的用户登录对象转换成自定义 Spring Security 用户验证所需格式
	     * @Date  2019/7/10 15:59
	     * @Param  [list]
	     * @return  java.util.Map<java.lang.String,java.lang.Object>
	     **/
	    public static Map<String, Object> convertUserLoginDTO2Map(List<UserLoginDTO> list) {
	        Map<String, Object> result = new HashMap<String, Object>();
	        for (UserLoginDTO dto : list) {
	            if (result.size() < 1) {
	                result.put("userName", dto.getUserID());
	                result.put("password", dto.getPassword());
	                /*result.put("userID", dto.getUserID());
	                result.put("realName", dto.getUserID());
	                result.put("createTime", dto.getUserID());
	                result.put("lastLogTime", dto.getUserID());*/
	                result.put("avaliable", (dto.getAvaliable()>0)?true:false);
	                result.put("overdue", (dto.getOverdue()>0)?true:false);
	                result.put("locked", (dto.getLocked()>0)?true:false);
	                result.put("licence", (dto.getLicence()>0)?true:false);
	                List<GrantedAuthority> authorities = new ArrayList<GrantedAuthority>();
	                authorities.add(new SimpleGrantedAuthority(dto.getPrivilegeTag()));
	                result.put("privilege", authorities);
	            } else {
	                ((List<GrantedAuthority>) result.get("privilege")).add(new SimpleGrantedAuthority(dto.getPrivilegeTag()));
	            }
	        }
	        return result;
	    }	

	    /**
	     * @Author  Supreme_Sir
	     * @Description  生成 Spring Security 用户密码，新增用户时必须使用该方法对密码进行加密存储
	     * @Date  2019/7/10 16:03
	     * @Param  [originalPassword]
	     * @return  java.lang.String
	     **/
	    public String passwordGenertor(String originalPassword){
	        PasswordEncoder encoder = new BCryptPasswordEncoder();
	        return encoder.encode(originalPassword);
	    }															
	}

由于在密码加密时“加盐”的次数不同，所以即使是相同的密码在调用 `passwordGenertor()` 方法后，生成的密码也是不同的。但这并不影响用户密码验证。

## MyAuthenticationFailureHandler.java ##

	import com.fasterxml.jackson.databind.ObjectMapper;
	import org.springframework.security.core.AuthenticationException;
	import org.springframework.security.web.authentication.AuthenticationFailureHandler;
	
	import javax.servlet.ServletException;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	import java.io.IOException;
	import java.util.HashMap;
	import java.util.Map;
	
	/**
	 * @ClassName MyAuthenticationFailureHandler
	 * @Description  自定义认证失败处理器，主要用以示范登录失败返回 JSON 数据功能。
	 * 如果想自定义登录成功处理器，则实现 AuthenticationSuccessHandler 接口即可
	 * @Author Supreme_Sir
	 * @Date 2019/7/10 16:09
	 * @Version 1.0
	 **/
	public class MyAuthenticationFailureHandler implements AuthenticationFailureHandler {
	    private ObjectMapper objectMapper = new ObjectMapper();
	
	    @Override
	    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response,
			AuthenticationException exception) throws IOException, ServletException {
	        Map<String, String> result = new HashMap<String, String>();
	        result.put("result","登录失败");
	        response.setContentType("text/json;charset=utf-8");
	        response.getWriter().print(objectMapper.writeValueAsString(result));
	    }
	}

## MyUserDetailService.java ##

	import com.supreme.dao.UserLoginDTOMapper;
	import com.supreme.utils.CommonUtil;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.security.core.GrantedAuthority;
	import org.springframework.security.core.userdetails.User;
	import org.springframework.security.core.userdetails.UserDetails;
	import org.springframework.security.core.userdetails.UserDetailsService;
	import org.springframework.security.core.userdetails.UsernameNotFoundException;
	
	import java.util.ArrayList;
	import java.util.Map;
	
	/**
	 * @ClassName MyUserDetailService
	 * @Description  自定义用户登录
	 * @Author Supreme_Sir
	 * @Date 2019/7/10 16:09
	 * @Version 1.0
	 **/
	public class MyUserDetailService implements UserDetailsService {
	
	    @Autowired
	    private UserLoginDTOMapper mapper;
	
	    /**
	     * @Author  Supreme_Sir
	     * @Description  根据用户名动态读取用户信息并认证
	     * @Date  2019/7/10 17:03
	     * @Param  [username]
	     * @return  org.springframework.security.core.userdetails.UserDetails
	     **/
	    @Override
	    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
	        Map<String, Object> map = CommonUtil.convertUserLoginDTO2Map(mapper.selectByUserName(username));
	        User user = new User((String) map.get("userName"), (String) map.get("password"), (boolean) map.get("avaliable"),
	                (boolean) map.get("overdue"), (boolean) map.get("licence"), (boolean) map.get("locked"),
	                (ArrayList<GrantedAuthority>) map.get("privilege"));
	        return user;
	    }
	}

## authority.sql ##

	/*
	 Navicat Premium Data Transfer
	
	 Source Server         : locationMySQL
	 Source Server Type    : MySQL
	 Source Server Version : 50560
	 Source Host           : localhost:3306
	 Source Schema         : authority
	
	 Target Server Type    : MySQL
	 Target Server Version : 50560
	 File Encoding         : 65001
	
	 Date: 11/07/2019 11:38:12
	*/
	
	SET NAMES utf8mb4;
	SET FOREIGN_KEY_CHECKS = 0;
	
	-- ----------------------------
	-- Table structure for privilege
	-- ----------------------------
	DROP TABLE IF EXISTS `privilege`;
	CREATE TABLE `privilege`  (
	  `privilege_id` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '权限ID',
	  `privilege_name` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '权限名',
	  `privilege_tag` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '权限标识符',
	  PRIMARY KEY (`privilege_id`) USING BTREE
	) ENGINE = InnoDB CHARACTER SET = gbk COLLATE = gbk_chinese_ci ROW_FORMAT = Compact;
	
	-- ----------------------------
	-- Records of privilege
	-- ----------------------------
	INSERT INTO `privilege` VALUES ('1', '访问用户内容', 'ROLE_USER');
	INSERT INTO `privilege` VALUES ('2', '访问管理员内容', 'ROLE_ADMIN');
	
	-- ----------------------------
	-- Table structure for role
	-- ----------------------------
	DROP TABLE IF EXISTS `role`;
	CREATE TABLE `role`  (
	  `role_id` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL DEFAULT '' COMMENT '角色ID',
	  `role_name` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '角色名',
	  `role_description` varchar(100) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '角色说明',
	  PRIMARY KEY (`role_id`) USING BTREE
	) ENGINE = InnoDB CHARACTER SET = gbk COLLATE = gbk_chinese_ci ROW_FORMAT = Compact;
	
	-- ----------------------------
	-- Records of role
	-- ----------------------------
	INSERT INTO `role` VALUES ('1', 'user', '普通用户');
	INSERT INTO `role` VALUES ('2', 'admin', '管理员');
	
	-- ----------------------------
	-- Table structure for role_privilege
	-- ----------------------------
	DROP TABLE IF EXISTS `role_privilege`;
	CREATE TABLE `role_privilege`  (
	  `role_privilege_roleid` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '角色ID',
	  `role_privilege_privilegeid` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '权限ID'
	) ENGINE = InnoDB CHARACTER SET = gbk COLLATE = gbk_chinese_ci ROW_FORMAT = Compact;
	
	-- ----------------------------
	-- Records of role_privilege
	-- ----------------------------
	INSERT INTO `role_privilege` VALUES ('1', '1');
	INSERT INTO `role_privilege` VALUES ('2', '2');
	
	-- ----------------------------
	-- Table structure for user
	-- ----------------------------
	DROP TABLE IF EXISTS `user`;
	CREATE TABLE `user`  (
	  `user_id` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '主键',
	  `user_name` varchar(30) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '用户名',
	  `user_realname` varchar(30) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '真实姓名',
	  `user_password` varchar(255) CHARACTER SET gbk COLLATE gbk_chinese_ci NULL DEFAULT NULL COMMENT '密码',
	  `user_createtime` datetime NULL DEFAULT NULL COMMENT '创建日期',
	  `user_lastlogtime` datetime NULL DEFAULT NULL COMMENT '最后登录时间',
	  `user_avaliable` int(1) NULL DEFAULT NULL COMMENT '是否可用',
	  `user_overdue` int(1) NULL DEFAULT NULL COMMENT '是否过期',
	  `user_locked` int(1) NULL DEFAULT NULL COMMENT '是否锁定',
	  `user_licence` int(1) NULL DEFAULT NULL COMMENT '证书是否过期',
	  PRIMARY KEY (`user_id`) USING BTREE,
	  UNIQUE INDEX `用户名`(`user_name`) USING BTREE COMMENT '用户名唯一'
	) ENGINE = InnoDB CHARACTER SET = gbk COLLATE = gbk_chinese_ci COMMENT = '用户表' ROW_FORMAT = Compact;
	
	-- ----------------------------
	-- Records of user
	-- ----------------------------
	INSERT INTO `user` VALUES ('1', 'tom', '汤姆', '$2a$10$byaRxiw/DEyig7y0pniDW.D5OPdJnJXwDnB3f/NcNPq2MGTk1wPom', '2019-07-04 12:00:00', '2019-07-04 00:00:00', 1, 1, 1, 1);
	INSERT INTO `user` VALUES ('2', 'jack', '杰克', '$2a$10$p47hUT5n0tfPesWo/uN88.tFXwuTH85KBLnGiaq.NkeRDvy3Qg90y', '2019-07-04 12:00:00', '2019-07-04 00:00:00', 1, 1, 1, 1);
	
	-- ----------------------------
	-- Table structure for user_role
	-- ----------------------------
	DROP TABLE IF EXISTS `user_role`;
	CREATE TABLE `user_role`  (
	  `user_role_userid` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '用户ID',
	  `user_role_roleid` varchar(50) CHARACTER SET gbk COLLATE gbk_chinese_ci NOT NULL COMMENT '角色ID'
	) ENGINE = InnoDB CHARACTER SET = gbk COLLATE = gbk_chinese_ci ROW_FORMAT = Compact;
	
	-- ----------------------------
	-- Records of user_role
	-- ----------------------------
	INSERT INTO `user_role` VALUES ('1', '1');
	INSERT INTO `user_role` VALUES ('2', '2');
	INSERT INTO `user_role` VALUES ('2', '1');
	
	-- ----------------------------
	-- Triggers structure for table user
	-- ----------------------------
	DROP TRIGGER IF EXISTS `user_delete_trigger`;
	delimiter ;;
	CREATE TRIGGER `user_delete_trigger` AFTER DELETE ON `user` FOR EACH ROW BEGIN 
		DELETE FROM user_role WHERE user_role_userid=old.user_id;
	END
	;;
	delimiter ;
	
	SET FOREIGN_KEY_CHECKS = 1;

	
别忘了要先创建 `authority` 数据库，字符集为 `gbk`。  

## login.jsp ##

	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>自定义登录页</title>
	</head>
	<body>
	    <h1>THIS IS MYSELF LOGIN PAGE</h1>
	
	    <form action="login" method="post">
	        <input type="text" name="username" value="" placeholder="请输入用户名"/><br/>
	        <input type="password" name="password" value="" placeholder="请输入密码"/><br/>
	        <input type="submit" value="提交">
	    </form>
	</body>
	</html>

## index.jsp ##

	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<body>
	<h2>Hello World!</h2>
	
	<a href="${pageContext.request.contextPath}/jspDispatcher/user">User的专属页面</a><br/>
	<a href="${pageContext.request.contextPath}/jspDispatcher/admin">Admin的专属页面</a><br/>
	<a href="${pageContext.request.contextPath}/jspDispatcher/file">文件操作页面</a><br/>
	<a href="${pageContext.request.contextPath}/logout">登出</a><br/>
	
	</body>
	</html>

## admin.jsp ##

	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>Admin</title>
	</head>
	<body>
	    <h1>THIS IS ADMIN PAGE</h1>
	</body>
	</html>

## user.jsp ##

	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>User</title>
	</head>
	<body>
	    <h1>THIS IS USER PAGE</h1>
	</body>
	</html>

## filePage.jsp ##

	<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>文件操作页</title>
	</head>
	<body>
	    <form action="#" method="post" enctype="multipart/form-data">
	        <input type="file" name="file"/><br/>
	        <input type="submit" value="上传"/>
	    </form><br/><br/>
	    <a href="#">下载 测试.txt</a>
	</body>
	</html>




**<div style="float:right;margin-top:150px;">Made By: tangtao &nbsp;<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Date: 2019/7/11</div>**