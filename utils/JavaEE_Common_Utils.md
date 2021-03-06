# JavaEE_Common_Utils

## JedisUtils

```java
package com.itheima.utils;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

import java.util.ResourceBundle;

/**
 * Jedis连接池工具类
 */
public class JedisUtils {
    // 定义静态成员变量：连接池对象
    private static JedisPool jedisPool;

    // 静态代码块：完成连接池对象的初始化操作
    static {
        // ResourceBundle作用：专门读取src目录下的properties文件的数据
        // 创建ResourceBundle对象
        ResourceBundle bundle = ResourceBundle.getBundle("jedis");
        // 根据键获得值
        // 获得最大连接数
        String maxTotal = bundle.getString("maxTotal");
        // 获得最大等待时间
        String maxWaitMillis = bundle.getString("maxWaitMillis");
        // 数据库服务器地址
        String host = bundle.getString("host");
        // 数据库服务器的端口号
        int port = Integer.parseInt(bundle.getString("port"));


        // 创建配置对象
        JedisPoolConfig config = new JedisPoolConfig();
        // 设置最大连接数
        config.setMaxTotal(Integer.parseInt(maxTotal));
        // 设置最大等待时间
        config.setMaxWaitMillis(Long.parseLong(maxWaitMillis));

        // 创建连接池对象
        jedisPool = new JedisPool(config, host, port);
    }

    // 定义公共的静态成员方法：返回连接对象(Jedis对象)
    public static Jedis getJedis(){
        return jedisPool.getResource();
    }

}

```

## ServiceUtils

```java
package com.itheima.utils;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 业务层工具类
 */
public class ServiceUtils {

    /**
     * 根据Class对称创建类的对象
     * @param <T>
     * @return 返回代理对象
     */
    public static <T> T getService(Class<T> serviceClass){
        try{
            // 创建业务层对象(真实对象)
            T obj = serviceClass.getConstructor().newInstance();
            // 创建代理对象
            return (T)Proxy.newProxyInstance(
                    obj.getClass().getClassLoader(),
                    serviceClass.getInterfaces(),
                    new InvocationHandler() {
                        @Override
                        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                            // 调用真实业务层对象的方法:transfer
                            Object result = method.invoke(obj, args);
                            // 提交事务并关闭连接、
                            SqlSessionUtils.closeAndCommit();
                            return result;
                        }
                    });
        } catch(Exception e){
           return null;
        }
    }
}

```

## SQLSessionUtils

```java
package com.itheima.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class SqlSessionUtils {
    // 创建ThreadLocal对象：用来存储连接对象
    private static  ThreadLocal<SqlSession> local = new ThreadLocal<>();

    // sqlSession工厂对象
    private static SqlSessionFactory factory;

    static {
        try{
            // 读取主配置文件
            InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
            // 创建SqlSessionFactoryBuilder对象
            SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
            // 获得SqlSessionFactory对象
            factory = builder.build(in);
        } catch(Exception e){
            e.printStackTrace();
        }
    }

    public static SqlSessionFactory factory(){
        return factory;
    }

    public static SqlSession openSession(){
        return factory.openSession();
    }


    /**
     * 获得接口的代理对象
     */
    public  static <T> T getMapper(Class<T> interfaceClass){
        // 先从local中获得sqlSession
        SqlSession sqlSession = local.get();
        if (sqlSession == null) {
            // 获得连接对象
            sqlSession = factory.openSession();
            local.set(sqlSession);
        }
        // 2. 获得接口代理对象(真实对象)
        T dao = sqlSession.getMapper(interfaceClass);
        // 3. 创建代理对象
        /*T proxy = (T)Proxy.newProxyInstance(dao.getClass().getClassLoader(), // 真实对象的类加载器
                new Class[]{interfaceClass},// 真实对象实现的接口
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("代理对象调用了: " + method.getName());
                        // 执行真实对象的方法
                        Object result = method.invoke(dao,args);

                        // 提交事务并关闭连接
                        //sqlSession.commit();
                        // sqlSession.close();

                        return result;
                    }
                }
        );*/
        // 返回代理对象
        return dao;
    }
    /**
     * 提交事务并关闭连接
     */
    public static void closeAndCommit(){
        // 从local中获得连接对象
        SqlSession sqlSession = local.get();
        if (sqlSession != null) {
            System.out.println("提交事务......");
            // 提交事务
            sqlSession.commit();
            // 关闭连接对象
            sqlSession.close();
            // 将连接对象从local中移出
            local.remove();
        }
    }
}

```

## Export-Excel报表导出-DownloadUtil

```java
package cn.itcast.web.utils;

import javax.servlet.ServletOutputStream;
import javax.servlet.http.HttpServletResponse;
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

public class DownloadUtil {
	
	/**
	 * @param filePath 要下载的文件路径
	 * @param returnName 返回的文件名
	 * @param response HttpServletResponse
	 * @param delFlag 是否删除文件
	 */
	protected void download(String filePath,String returnName,HttpServletResponse response,boolean delFlag){
		this.prototypeDownload(new File(filePath), returnName, response, delFlag);
	}


	/**
	 * @param file 要下载的文件
	 * @param returnName 返回的文件名
	 * @param response HttpServletResponse
	 * @param delFlag 是否删除文件
	 */
	protected void download(File file,String returnName,HttpServletResponse response,boolean delFlag){
		this.prototypeDownload(file, returnName, response, delFlag);
	}
	
	/**
	 * @param file 要下载的文件
	 * @param returnName 返回的文件名
	 * @param response HttpServletResponse
	 * @param delFlag 是否删除文件
	 */
	public void prototypeDownload(File file,String returnName,HttpServletResponse response,boolean delFlag){
		// 下载文件
		FileInputStream inputStream = null;
		ServletOutputStream outputStream = null;
		try {
			if(!file.exists()) return;
			response.reset();
			//设置响应类型	PDF文件为"application/pdf"，WORD文件为："application/msword"， EXCEL文件为："application/vnd.ms-excel"。  
			response.setContentType("application/octet-stream;charset=utf-8");

			//设置响应的文件名称,并转换成中文编码
			//returnName = URLEncoder.encode(returnName,"UTF-8");
			returnName = response.encodeURL(new String(returnName.getBytes(),"iso8859-1"));	//保存的文件名,必须和页面编码一致,否则乱码
			
			//attachment作为附件下载；inline客户端机器有安装匹配程序，则直接打开；注意改变配置，清除缓存，否则可能不能看到效果
			response.addHeader("Content-Disposition",   "attachment;filename="+returnName);  
			
			//将文件读入响应流
			inputStream = new FileInputStream(file);
			outputStream = response.getOutputStream();
			int length = 1024;
			int readLength=0;
			byte buf[] = new byte[1024];
			readLength = inputStream.read(buf, 0, length);
			while (readLength != -1) {
				outputStream.write(buf, 0, readLength);
				readLength = inputStream.read(buf, 0, length);
			}
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				outputStream.flush();
			} catch (IOException e) {
				e.printStackTrace();
			}
			try {
				outputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
			try {
				inputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
			//删除原文件
			
			if(delFlag) {				
				file.delete();
			}
		}
	}

	/**
	 * by tony 2013-10-17
	 * @param byteArrayOutputStream 将文件内容写入ByteArrayOutputStream
	 * @param response HttpServletResponse	写入response
	 * @param returnName 返回的文件名
	 */
	public void download(ByteArrayOutputStream byteArrayOutputStream, HttpServletResponse response, String returnName) throws IOException{
		response.setContentType("application/octet-stream;charset=utf-8");
		returnName = response.encodeURL(new String(returnName.getBytes(),"iso8859-1"));			//保存的文件名,必须和页面编码一致,否则乱码
		response.addHeader("Content-Disposition",   "attachment;filename=" + returnName);  
		response.setContentLength(byteArrayOutputStream.size());
		
		ServletOutputStream outputstream = response.getOutputStream();	//取得输出流
		byteArrayOutputStream.writeTo(outputstream);					//写到输出流
		byteArrayOutputStream.close();									//关闭
		outputstream.flush();											//刷数据
	}
}

```

## 七牛云-文件上传FileUploadUtil

```java
package cn.itcast.web.utils;

import com.google.gson.Gson;
import com.qiniu.common.QiniuException;
import com.qiniu.http.Response;
import com.qiniu.storage.Configuration;
import com.qiniu.storage.Region;
import com.qiniu.storage.UploadManager;
import com.qiniu.storage.model.DefaultPutRet;
import com.qiniu.util.Auth;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.web.multipart.MultipartFile;

import java.util.UUID;


@Component
public class FileUploadUtil {

    @Value("${qiniu.accessKey}")
    private String accessKey ;

    @Value("${qiniu.secretKey}")
    private String secretKey;

    @Value("${qiniu.bucket}")
    private String bucket;

    @Value("${qiniu.rtValue}")
    private String rtValue;

    /**
     * 将图片上传到七牛云服务
     *      1.更新用户图片信息（用户id=key）
     *      2.访问图片
     *          存储空间分配的临时域名（免费用户有效期一个月）：http://pkbivgfrm.bkt.clouddn.com+上传的文件名
     *      3.对于更新之后访问图片，防止缓存
     *          更新图片之后：访问的时候，再请求连接添加上时间戳
     *
     */
    public String upload(MultipartFile multipartFile)throws Exception{
	    String img = "";
        try {
            //取出原始文件名
            String fileName = multipartFile.getOriginalFilename();
            //随机化文件名
            String uuid = UUID.randomUUID().toString().replace("-","").toUpperCase();
            fileName = uuid+"_"+fileName;
            //构造一个带指定Zone对象的配置类
            //指定上传文件服务器地址：
            Configuration cfg = new Configuration(Region.region2());
            //...其他参数参考类注释
            //上传管理器
            UploadManager uploadManager = new UploadManager(cfg);
            //身份认证
            Auth auth = Auth.create(accessKey, secretKey);
            //指定覆盖上传
            String upToken = auth.uploadToken(bucket,fileName);
            //上传
            Response response = uploadManager.put(multipartFile.getBytes(), fileName, upToken);
            //解析上传成功的结果
            DefaultPutRet putRet = new Gson().fromJson(response.bodyString(), DefaultPutRet.class);
            img = rtValue+"/"+fileName;
        } catch (QiniuException ex) {
            System.err.println(ex.getMessage());
            Response r = ex.response;
            System.err.println(r.toString());
            try {
                System.err.println(r.bodyString());
            } catch (QiniuException ex2) {
            }
        }
        return img;
    }
}

```

## 自动记录日志切面类-LogAspect

```java
package cn.itcast.web.utils;

import cn.itcast.domain.system.SysLog;
import cn.itcast.domain.system.User;
import cn.itcast.service.system.SysLogService;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.util.Date;

@Component
@Aspect
public class LogAspect {
    @Autowired
    private SysLogService sysLogService;
    @Autowired
    private HttpSession session;
    @Autowired
    private HttpServletRequest request;

    /*
     * 切面类
     * 1.获取登录的用户信息
     * 2.获取方法名,类全名
     * 3.构造对象
     * 4.往日志对象存操作日志
     * 5.调用service保存日志
     * */
    @Around(value = "execution(* cn.itcast.web.controller.*.*.*(..))")
    public Object Log(ProceedingJoinPoint joinPoint) throws Throwable {
        //获得已登录的用户信息
        User user = (User) session.getAttribute("loginUser");
        //获取方法名 类全名
        String methodName = joinPoint.getSignature().getName();
        String className = joinPoint.getTarget().getClass().getName();
        //System.out.printf("className==>" + className);
        SysLog sysLog = new SysLog();
        //创建日志的时间
        Date date = new Date();
        sysLog.setTime(date);
        if (user != null) {
            sysLog.setUserName(user.getUserName());
            sysLog.setCompanyId(user.getCompanyId());
            sysLog.setCompanyName(user.getCompanyName());
        }
        sysLog.setAction(className);
        sysLog.setMethod(methodName);
        sysLog.setIp(request.getLocalAddr());
        Object[] args = joinPoint.getArgs();
        //调用service 保存的方法
        sysLogService.saveLog(sysLog);
        return  joinPoint.proceed(args);
    }
}

```

## Shiro-粗粒度权限控制

### 自定义加盐加密凭证匹配器

```java
package cn.itcast.web.shiro;

import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authc.credential.SimpleCredentialsMatcher;
import org.apache.shiro.crypto.hash.Md5Hash;

/**
 * 自定义加盐加密凭证匹配器
 *  1.获取登录用户的安全信息
 *  2.对用户加盐加密
 *  3.转换成字符串格式
 *  4.获取数据库用户的密码
 *  5.判断两者是否相等
 */
public class CustomCredentialsMatcher extends SimpleCredentialsMatcher {

    @Override
    public  boolean doCredentialsMatch(AuthenticationToken token, AuthenticationInfo info){

        System.out.println("调用了密码对比器");

        UsernamePasswordToken uptoken = (UsernamePasswordToken) token;
        String password = new String (uptoken.getPassword());//原本是字符数组 转换成字符串
        Md5Hash md5Hash = new Md5Hash(password, uptoken.getUsername());
        String md5password = md5Hash.toString();
        //获取数据库的密码
        String dbpassword = (String) info.getCredentials();

        return dbpassword.equals(md5password);
    }
}

```

### Realm

```java
package cn.itcast.web.shiro;

import cn.itcast.domain.system.Module;
import cn.itcast.domain.system.User;
import cn.itcast.service.system.ModuleService;
import cn.itcast.service.system.UserService;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.apache.shiro.subject.Subject;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.List;

public class AuthRealm extends AuthorizingRealm {
    @Autowired
    private UserService userService;
    @Autowired
    private ModuleService moduleService;
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        //授权SimpleAuthorizationInfo
        System.out.println("进入授权方法");
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //获取登录的用户信息
        Subject subject = SecurityUtils.getSubject();
        User user = (User) subject.getPrincipal();
        //查询用户模块权限
        List<Module> moduleList = moduleService.findModuleByUserId(user.getId());
        if (moduleList!=null || moduleList.size()!=0){
            for (Module module : moduleList) {
                //将权限标记存入到shiro的授权对象中
                if (module.getName()!=null){
                    info.addStringPermission(module.getName());
                }
            }
        }
        return info;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("调用认证方法");

        UsernamePasswordToken upToken = (UsernamePasswordToken) token;
        String email = upToken.getUsername();
        //判断用户信息
        User user = userService.findByEmail(email);
        if (user!=null){
            //第一个参数 user安全用户 数据库密码 当前调用realm域的名称
            /*
            * 参数一: principal
            * 参数二:数据库密码
            * 参数三:realm别名,只有在多个realm的时候才用
            * */
            SimpleAuthenticationInfo Info = new SimpleAuthenticationInfo(user, user.getPassword(), this.getName());
            return Info;
        }
        return null;
    }
}

```

## 自定义日期转换器

```java
package cn.itcast.web.converter;

import org.springframework.core.convert.converter.Converter;
import org.springframework.util.StringUtils;

import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * 自定义date格式转化
 */
public class StringToDateConverter implements Converter<String,Date> {

    private String pattern;

    public void setPattern(String pattern) {
        this.pattern = pattern;
    }

    @Override
    public Date convert(String source) {
        try{
            if(StringUtils.isEmpty(pattern)){
                pattern = "yyyy-MM-dd";
            }
            DateFormat format = new SimpleDateFormat(pattern);
            return format.parse(source);
        }catch (Exception e){
            throw new IllegalArgumentException("日期格式不对，请输入正确的日期格式。格式为年-月-日");
        }
    }
}

```

### 判断是否早于当前系统时间 忽略时分秒

```java
/**
     * 判断是否早于当前系统时间 忽略时分秒
     * -1 则 表示早于当前系统时间 返回true
     *
     * @param d
     * @return
     */
    private static boolean compareTo(Date d) {
        LocalDate localDate1 = ZonedDateTime.ofInstant(d.toInstant(), ZoneId.systemDefault()).toLocalDate();
        LocalDate localDate2 = ZonedDateTime.ofInstant(new Date().toInstant(), ZoneId.systemDefault()).toLocalDate();
        int i = localDate1.compareTo(localDate2);
        return i == -1 ? true : false;
    }
```

## Excel导出模板

```java
package com.csa.taml.controller.export;

import com.alibaba.dubbo.config.annotation.Reference;
import com.csa.taml.common.tools.ResponseResult;
import com.csa.taml.domain.export.entity.ShippingCargoDetailsEntity;
import com.csa.taml.service.export.ExportShippingOrderService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.ss.util.CellRangeAddress;
import org.apache.poi.xssf.streaming.SXSSFWorkbook;
import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.OutputStream;
import java.util.List;

/**
 * Excel导出模板
 */
@RestController
@RequestMapping("/export/order")
@Api("Excel导出模板")
public class ExportOutOrderController {

    @Reference
    private ExportShippingOrderService exportShippingOrderService;


    /**
     * 导出Excel格式的订单表
     * 返回：excel文件
     */
    @PostMapping("/printExcel")
    @ApiOperation("Excel导出模板")
    public ResponseResult printExcel(@RequestBody Long[] ids,HttpServletResponse response) throws IOException {
        if (ids==null&&ids.length==0){
            return new ResponseResult<>(ResponseResult.StatusCode.SEVER_ERROR, "请勾选需要导出的货物");
        }
        //只查询已分拨未提取的货物信息
        //shippingQuery.setStatus(ShippingOrderStatusEnum.assignNotReceive.code);
        List<ShippingCargoDetailsEntity> list = exportShippingOrderService.queryListByCargoId(ids);
        if (list == null && list.size() == 0) {
            return new ResponseResult<>(ResponseResult.StatusCode.CLIENT_ERROR, "当前数据为空，请重新查询");
        }
        //1.创建工作簿
        Workbook wb = new SXSSFWorkbook();
        //2.生成工作单
        Sheet sheet = wb.createSheet("待提货订单表");
        //设置合并列
        /**
         * 参数依次为： 开始行号，结束行号，开始列号，结束列号
         */
        sheet.addMergedRegion(new CellRangeAddress(0, 0, 1, 14));
        //设置列宽， 参数为：列索引，列宽度值
        sheet.setColumnWidth(0, 6 * 256);
        sheet.setColumnWidth(1, 11 * 256);
        sheet.setColumnWidth(2, 16 * 256);
        sheet.setColumnWidth(3, 11 * 256);
        sheet.setColumnWidth(4, 11 * 256);
        sheet.setColumnWidth(5, 29 * 256);
        sheet.setColumnWidth(6, 11 * 256);
        sheet.setColumnWidth(7, 11 * 256);
        sheet.setColumnWidth(8, 11 * 256);
        sheet.setColumnWidth(9, 11 * 256);
        sheet.setColumnWidth(10, 11 * 256);
        sheet.setColumnWidth(11, 11 * 256);
        sheet.setColumnWidth(12, 11 * 256);
        sheet.setColumnWidth(13, 11 * 256);

        //3.创建表头行
        Row row = sheet.createRow(0);
        //3.4.设置行高度
        row.setHeightInPoints(36);

        //3.1 设置标题
        String title = "待提货订单表";
        //3.2 设置第二列的内容作为标题
        Cell cell = row.createCell(1);
        cell.setCellValue(title);
        //3.3 设置大标题样式
        cell.setCellStyle(bigTitle(wb));


        //4.创建内容行
        //4.1 创建标题
        row = sheet.createRow(1);
        //设置行高
        row.setHeightInPoints(26);
        String[] titles = {"紧急度", "装箱单号", "合同号", "合同类型", "送修/采购单位", "采购业务员", "收货人", "目的国", "件号", "序号", "货物名称", "分拨日期", "分拨人"};
        for (int i = 0; i < titles.length; i++) {
            cell = row.createCell(i + 1);
            cell.setCellValue(titles[i]);
            //设置样式
            cell.setCellStyle(title(wb));
        }

        //4.2 内容
        //获取列表数据
        int num = 2;
        //List<ShippingCargoDetailsEntity> cargoDetailsEntities = new ArrayList<>();
        //List<?> objects = list.get(0);
        //cargoDetailsEntities.addAll((List<ShippingCargoDetailsEntity>) objects);
        for (ShippingCargoDetailsEntity orderEntity : list) {
            //创建行
            row = sheet.createRow(num++);
            //设置行高
            row.setHeightInPoints(24);

            //设置列内容
            //紧急度
            cell = row.createCell(1);
            if (orderEntity.getAog() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                if (orderEntity.getAog()==0){
                    cell.setCellValue("");
                }
                if (orderEntity.getAog()==1){
                    cell.setCellValue("AOG");
                }
            }

            //装箱单号列
            cell = row.createCell(2);
            if (orderEntity.getShipmentNumber() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getShipmentNumber());
            }
            //合同号列
            cell = row.createCell(3);
            if (orderEntity.getContractNo() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getContractNo());
            }

            //合同类型列
            cell = row.createCell(4);
            if (orderEntity.getContarctType() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getContarctType());
            }

            //送修采购单位列
            cell = row.createCell(5);
            if (orderEntity.getPurchaser() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getPurchaser());
            }

            //采购业务员列
            cell = row.createCell(6);
            if (orderEntity.getPurchasingClerk() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getPurchasingClerk());
            }

            //收货人列
            cell = row.createCell(7);
            if (orderEntity.getReceiver() != null) {

                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getReceiver());
            }

            //目的港|国列
            cell = row.createCell(8);
            if (orderEntity.getDestinationCountry() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getDestinationHarbor());
            }
            //件号列
            cell = row.createCell(9);
            if (orderEntity.getPartNumber() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getPartNumber());
            }
            //序号列
            cell = row.createCell(10);
            if (orderEntity.getSerialNumber() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getSerialNumber());
            }
            //中文货物名称列
            cell = row.createCell(11);
            if (orderEntity.getChnCargoName() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getChnCargoName());
            }
            //分拨日期列
            cell = row.createCell(12);
            if (orderEntity.getAssignDate() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //设置日期格式
                //Date assignDate = orderEntity.getAssignDate();
                //内容
                cell.setCellValue(orderEntity.getAssignDate());
            }
            //分拨人列
            cell = row.createCell(13);
            if (orderEntity.getAssignee() != null) {
                //样式
                cell.setCellStyle(text(wb));
                //内容
                cell.setCellValue(orderEntity.getAssignee());
            }
        }

        //3.把工作簿写出到浏览器响应（给用户下载Excel)
        //3.2. 设置写出的文件格式： excel格式
        response.setContentType("application/octet-stream;charset=utf-8");
        //3.2 提示浏览器一个下载的文件名称: Content-Disposition
        //response.addHeader("Content-Disposition", "attachment;filename=order.xlsx");

        OutputStream outputStream = response.getOutputStream();

        //3.3 把工作簿写出到响应中
        wb.write(outputStream);
        outputStream.flush();

        return new ResponseResult<>(ResponseResult.StatusCode.SUCCESS, "操作成功");
    }

    //大标题的样式
    public CellStyle bigTitle(Workbook wb) {
        CellStyle style = wb.createCellStyle();
        Font font = wb.createFont();
        font.setFontName("宋体");
        font.setFontHeightInPoints((short) 16);
        font.setBold(true);//字体加粗
        style.setFont(font);
        style.setAlignment(HorizontalAlignment.CENTER);                //横向居中
        style.setVerticalAlignment(VerticalAlignment.CENTER);        //纵向居中
        return style;
    }

    //小标题的样式
    public CellStyle title(Workbook wb) {
        CellStyle style = wb.createCellStyle();
        Font font = wb.createFont();
        font.setFontName("黑体");
        font.setFontHeightInPoints((short) 12);
        style.setFont(font);
        style.setAlignment(HorizontalAlignment.CENTER);                //横向居中
        style.setVerticalAlignment(VerticalAlignment.CENTER);        //纵向居中
        style.setBorderTop(BorderStyle.THIN);                        //上细线
        style.setBorderBottom(BorderStyle.THIN);                    //下细线
        style.setBorderLeft(BorderStyle.THIN);                        //左细线
        style.setBorderRight(BorderStyle.THIN);                        //右细线
        style.setAlignment(HorizontalAlignment.CENTER);                //横向居中
        style.setVerticalAlignment(VerticalAlignment.CENTER);        //纵向居中
        return style;
    }

    //文字样式
    public CellStyle text(Workbook wb) {
        CellStyle style = wb.createCellStyle();
        Font font = wb.createFont();
        font.setFontName("Times New Roman");
        font.setFontHeightInPoints((short) 10);

        //设置日期格式
        style.setDataFormat(wb.createDataFormat().getFormat("yyyy-MM-dd"));
        style.setAlignment(HorizontalAlignment.LEFT);                //横向居左
        style.setVerticalAlignment(VerticalAlignment.CENTER);        //纵向居中
        style.setBorderTop(BorderStyle.THIN);                        //上细线
        style.setBorderBottom(BorderStyle.THIN);                    //下细线
        style.setBorderLeft(BorderStyle.THIN);                        //左细线
        style.setBorderRight(BorderStyle.THIN);                        //右细线
        style.setFont(font);
        style.setAlignment(HorizontalAlignment.CENTER);                //横向居中
        style.setVerticalAlignment(VerticalAlignment.CENTER);        //纵向居中
        return style;
    }
}

```

