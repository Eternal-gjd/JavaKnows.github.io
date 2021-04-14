## Java代码部分

### 1.16位优惠码的生成

```java
/**
     * 16位优惠码生成：时间戳后8位+4位随机数+用户id后4位
     */
    private String generateCouponCode(Long memberId) {
        StringBuilder sb = new StringBuilder();
        Long currentTimeMillis = System.currentTimeMillis();
        String timeMillisStr = currentTimeMillis.toString();
        sb.append(timeMillisStr.substring(timeMillisStr.length() - 8));
        for (int i = 0; i < 4; i++) {
            sb.append(new Random().nextInt(10));
        }
        String memberIdStr = memberId.toString();
        if (memberIdStr.length() <= 4) {
            sb.append(String.format("%04d", memberId));
        } else {
            sb.append(memberIdStr.substring(memberIdStr.length()-4));
        }
        return sb.toString();
    }
```

### 2.redis相关代码

#### 1.redisConfig

   ```java
   package com.macro.mall.security.config;
   
   import com.fasterxml.jackson.annotation.JsonAutoDetect;
   import com.fasterxml.jackson.annotation.PropertyAccessor;
   import com.fasterxml.jackson.databind.ObjectMapper;
   import org.springframework.cache.annotation.CachingConfigurerSupport;
   import org.springframework.cache.annotation.EnableCaching;
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.data.redis.cache.RedisCacheConfiguration;
   import org.springframework.data.redis.cache.RedisCacheManager;
   import org.springframework.data.redis.cache.RedisCacheWriter;
   import org.springframework.data.redis.connection.RedisConnectionFactory;
   import org.springframework.data.redis.core.RedisTemplate;
   import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
   import org.springframework.data.redis.serializer.RedisSerializationContext;
   import org.springframework.data.redis.serializer.RedisSerializer;
   import org.springframework.data.redis.serializer.StringRedisSerializer;
   
   import java.time.Duration;
   
   /**
    * Redis配置类(似乎没用)
    */
   @EnableCaching
   @Configuration
   public class RedisConfig extends CachingConfigurerSupport {
   
       @Bean
       public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
           RedisSerializer<Object> serializer = redisSerializer();
           RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
           redisTemplate.setConnectionFactory(redisConnectionFactory);
           redisTemplate.setKeySerializer(new StringRedisSerializer());
           redisTemplate.setValueSerializer(serializer);
           redisTemplate.setHashKeySerializer(new StringRedisSerializer());
           redisTemplate.setHashValueSerializer(serializer);
           redisTemplate.afterPropertiesSet();
           return redisTemplate;
       }
   
       @Bean
       public RedisSerializer<Object> redisSerializer() {
           //创建JSON序列化器
           Jackson2JsonRedisSerializer<Object> serializer = new Jackson2JsonRedisSerializer<>(Object.class);
           ObjectMapper objectMapper = new ObjectMapper();
           objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
           objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
           serializer.setObjectMapper(objectMapper);
           return serializer;
       }
   
       @Bean
       public RedisCacheManager redisCacheManager(RedisConnectionFactory redisConnectionFactory) {
           RedisCacheWriter redisCacheWriter = RedisCacheWriter.nonLockingRedisCacheWriter(redisConnectionFactory);
           //设置Redis缓存有效期为1天
           RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig()
                   .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer())).entryTtl(Duration.ofDays(1));
           return new RedisCacheManager(redisCacheWriter, redisCacheConfiguration);
       }
   
   }
   
   ```

RedisService&&RedisServiceImpl

```java
package com.macro.mall.security.service;

import java.util.List;
import java.util.Map;
import java.util.Set;

/**
 * redis操作Service
 */
public interface RedisService {

    /**
     * 保存属性
     */
    void set(String key, Object value, long time);

    /**
     * 保存属性
     */
    void set(String key, Object value);

    /**
     * 获取属性
     */
    Object get(String key);

    /**
     * 删除属性
     */
    Boolean del(String key);

    /**
     * 批量删除属性
     */
    Long del(List<String> keys);

    /**
     * 设置过期时间
     */
    Boolean expire(String key, long time);

    /**
     * 获取过期时间
     */
    Long getExpire(String key);

    /**
     * 判断是否有该属性
     */
    Boolean hasKey(String key);

    /**
     * 按delta递增
     */
    Long incr(String key, long delta);

    /**
     * 按delta递减
     */
    Long decr(String key, long delta);

    /**
     * 获取Hash结构中的属性
     */
    Object hGet(String key, String hashKey);

    /**
     * 向Hash结构中放入一个属性
     */
    Boolean hSet(String key, String hashKey, Object value, long time);

    /**
     * 向Hash结构中放入一个属性
     */
    void hSet(String key, String hashKey, Object value);

    /**
     * 直接获取整个Hash结构
     */
    Map<Object, Object> hGetAll(String key);

    /**
     * 直接设置整个Hash结构
     */
    Boolean hSetAll(String key, Map<String, Object> map, long time);

    /**
     * 直接设置整个Hash结构
     */
    void hSetAll(String key, Map<String, Object> map);

    /**
     * 删除Hash结构中的属性
     */
    void hDel(String key, Object... hashKey);

    /**
     * 判断Hash结构中是否有该属性
     */
    Boolean hHasKey(String key, String hashKey);

    /**
     * Hash结构中属性递增
     */
    Long hIncr(String key, String hashKey, Long delta);

    /**
     * Hash结构中属性递减
     */
    Long hDecr(String key, String hashKey, Long delta);

    /**
     * 获取Set结构
     */
    Set<Object> sMembers(String key);

    /**
     * 向Set结构中添加属性
     */
    Long sAdd(String key, Object... values);

    /**
     * 向Set结构中添加属性
     */
    Long sAdd(String key, long time, Object... values);

    /**
     * 是否为Set中的属性
     */
    Boolean sIsMember(String key, Object value);

    /**
     * 获取Set结构的长度
     */
    Long sSize(String key);

    /**
     * 删除Set结构中的属性
     */
    Long sRemove(String key, Object... values);

    /**
     * 获取List结构中的属性
     */
    List<Object> lRange(String key, long start, long end);

    /**
     * 获取List结构的长度
     */
    Long lSize(String key);

    /**
     * 根据索引获取List中的属性
     */
    Object lIndex(String key, long index);

    /**
     * 向List结构中添加属性
     */
    Long lPush(String key, Object value);

    /**
     * 向List结构中添加属性
     */
    Long lPush(String key, Object value, long time);

    /**
     * 向List结构中批量添加属性
     */
    Long lPushAll(String key, Object... values);

    /**
     * 向List结构中批量添加属性
     */
    Long lPushAll(String key, Long time, Object... values);

    /**
     * 从List结构中移除属性
     */
    Long lRemove(String key, long count, Object value);
}


/**======================================================================================*/

package com.macro.mall.security.service.impl;

import com.macro.mall.security.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

/**
 * redis操作实现类
 */
@Service
public class RedisServiceImpl implements RedisService {
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @Override
    public void set(String key, Object value, long time) {
        redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
    }

    @Override
    public void set(String key, Object value) {
        redisTemplate.opsForValue().set(key, value);
    }

    @Override
    public Object get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    @Override
    public Boolean del(String key) {
        return redisTemplate.delete(key);
    }

    @Override
    public Long del(List<String> keys) {
        return redisTemplate.delete(keys);
    }

    @Override
    public Boolean expire(String key, long time) {
        return redisTemplate.expire(key, time, TimeUnit.SECONDS);
    }

    @Override
    public Long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }

    @Override
    public Boolean hasKey(String key) {
        return redisTemplate.hasKey(key);
    }

    @Override
    public Long incr(String key, long delta) {
        return redisTemplate.opsForValue().increment(key, delta);
    }

    @Override
    public Long decr(String key, long delta) {
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    @Override
    public Object hGet(String key, String hashKey) {
        return redisTemplate.opsForHash().get(key, hashKey);
    }

    @Override
    public Boolean hSet(String key, String hashKey, Object value, long time) {
        redisTemplate.opsForHash().put(key, hashKey, value);
        return expire(key, time);
    }

    @Override
    public void hSet(String key, String hashKey, Object value) {
        redisTemplate.opsForHash().put(key, hashKey, value);
    }

    @Override
    public Map<Object, Object> hGetAll(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    @Override
    public Boolean hSetAll(String key, Map<String, Object> map, long time) {
        redisTemplate.opsForHash().putAll(key, map);
        return expire(key, time);
    }

    @Override
    public void hSetAll(String key, Map<String, Object> map) {
        redisTemplate.opsForHash().putAll(key, map);
    }

    @Override
    public void hDel(String key, Object... hashKey) {
        redisTemplate.opsForHash().delete(key, hashKey);
    }

    @Override
    public Boolean hHasKey(String key, String hashKey) {
        return redisTemplate.opsForHash().hasKey(key, hashKey);
    }

    @Override
    public Long hIncr(String key, String hashKey, Long delta) {
        return redisTemplate.opsForHash().increment(key, hashKey, delta);
    }

    @Override
    public Long hDecr(String key, String hashKey, Long delta) {
        return redisTemplate.opsForHash().increment(key, hashKey, -delta);
    }

    @Override
    public Set<Object> sMembers(String key) {
        return redisTemplate.opsForSet().members(key);
    }

    @Override
    public Long sAdd(String key, Object... values) {
        return redisTemplate.opsForSet().add(key, values);
    }

    @Override
    public Long sAdd(String key, long time, Object... values) {
        Long count = redisTemplate.opsForSet().add(key, values);
        expire(key, time);
        return count;
    }

    @Override
    public Boolean sIsMember(String key, Object value) {
        return redisTemplate.opsForSet().isMember(key, value);
    }

    @Override
    public Long sSize(String key) {
        return redisTemplate.opsForSet().size(key);
    }

    @Override
    public Long sRemove(String key, Object... values) {
        return redisTemplate.opsForSet().remove(key, values);
    }

    @Override
    public List<Object> lRange(String key, long start, long end) {
        return redisTemplate.opsForList().range(key, start, end);
    }

    @Override
    public Long lSize(String key) {
        return redisTemplate.opsForList().size(key);
    }

    @Override
    public Object lIndex(String key, long index) {
        return redisTemplate.opsForList().index(key, index);
    }

    @Override
    public Long lPush(String key, Object value) {
        return redisTemplate.opsForList().rightPush(key, value);
    }

    @Override
    public Long lPush(String key, Object value, long time) {
        Long index = redisTemplate.opsForList().rightPush(key, value);
        expire(key, time);
        return index;
    }

    @Override
    public Long lPushAll(String key, Object... values) {
        return redisTemplate.opsForList().rightPushAll(key, values);
    }

    @Override
    public Long lPushAll(String key, Long time, Object... values) {
        Long count = redisTemplate.opsForList().rightPushAll(key, values);
        expire(key, time);
        return count;
    }

    @Override
    public Long lRemove(String key, long count, Object value) {
        return redisTemplate.opsForList().remove(key, count, value);
    }
}

```

### 3.MQ相关代码

#### RabbitMqConfig-->路由模式

```java
package com.macro.mall.portal.config;

import com.macro.mall.portal.domain.QueueEnum;
import org.springframework.amqp.core.*;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 消息队列配置
 * Created by macro on 2018/9/14.
 */
@Configuration
public class RabbitMqConfig {

    /**
     * 订单消息实际消费队列所绑定的交换机
     */
    @Bean
    DirectExchange orderDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * 订单延迟队列队列所绑定的交换机
     */
    @Bean
    DirectExchange orderTtlDirect() {
        return (DirectExchange) ExchangeBuilder
                .directExchange(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getExchange())
                .durable(true)
                .build();
    }

    /**
     * 订单实际消费队列
     */
    @Bean
    public Queue orderQueue() {
        return new Queue(QueueEnum.QUEUE_ORDER_CANCEL.getName());
    }

    /**
     * 订单延迟队列（死信队列）
     */
    @Bean
    public Queue orderTtlQueue() {
        return QueueBuilder
                .durable(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getName())
                .withArgument("x-dead-letter-exchange", QueueEnum.QUEUE_ORDER_CANCEL.getExchange())//到期后转发的交换机
                .withArgument("x-dead-letter-routing-key", QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey())//到期后转发的路由键
                .build();
    }

    /**
     * 将订单队列绑定到交换机
     */
    @Bean
    Binding orderBinding(DirectExchange orderDirect,Queue orderQueue){
        return BindingBuilder
                .bind(orderQueue)
                .to(orderDirect)
                .with(QueueEnum.QUEUE_ORDER_CANCEL.getRouteKey());
    }

    /**
     * 将订单延迟队列绑定到交换机
     */
    @Bean
    Binding orderTtlBinding(DirectExchange orderTtlDirect,Queue orderTtlQueue){
        return BindingBuilder
                .bind(orderTtlQueue)
                .to(orderTtlDirect)
                .with(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getRouteKey());
    }
}
```

#### CancelOrderSender&&CancelOrderReceiver

```java
package com.macro.mall.portal.component;

import com.macro.mall.portal.domain.QueueEnum;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.AmqpException;
import org.springframework.amqp.core.AmqpTemplate;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.core.MessagePostProcessor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * 取消订单消息的发出者
 */
@Component
public class CancelOrderSender {
    private static Logger LOGGER =LoggerFactory.getLogger(CancelOrderSender.class);
    @Autowired
    private AmqpTemplate amqpTemplate;

    public void sendMessage(Long orderId,final long delayTimes){
        //给延迟队列发送消息
        amqpTemplate.convertAndSend(QueueEnum.QUEUE_TTL_ORDER_CANCEL.getExchange(), QueueEnum.QUEUE_TTL_ORDER_CANCEL.getRouteKey(), orderId, new MessagePostProcessor() {
            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                //给消息设置延迟毫秒值
                message.getMessageProperties().setExpiration(String.valueOf(delayTimes));
                return message;
            }
        });
        LOGGER.info("send orderId:{}",orderId);
    }
}


/**=========================================================================**/
package com.macro.mall.portal.component;

import com.macro.mall.portal.service.OmsPortalOrderService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

/**
 * 取消订单消息的处理者
 */
@Component
@RabbitListener(queues = "mall.order.cancel")
public class CancelOrderReceiver {
    private static Logger LOGGER =LoggerFactory.getLogger(CancelOrderReceiver.class);
    @Autowired
    private OmsPortalOrderService portalOrderService;
    @RabbitHandler
    public void handle(Long orderId){
        portalOrderService.cancelOrder(orderId);
        LOGGER.info("process orderId:{}",orderId);
    }
}

```

### 4.导出Excel问题

#### 工具类

```java
 package com.ycxc.vmts.common.util.utils;


import com.ycxc.vmts.common.util.DateUtils;
import org.apache.commons.lang.BooleanUtils;
import org.apache.commons.lang.CharUtils;
import org.apache.commons.lang.StringUtils;
import org.apache.commons.lang.math.NumberUtils;
import org.apache.poi.hssf.usermodel.HSSFCellStyle;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.ss.util.CellRangeAddress;
import org.apache.poi.ss.util.CellReference;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.servlet.http.HttpServletResponse;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.math.BigDecimal;
import java.net.URLEncoder;
import java.util.Date;
import java.util.List;

/**
 * @author gjd
 * @date 2019/9/10 15:54
 */
public class ExcelUtils {

    private final static Logger log = LoggerFactory.getLogger(ExcelUtils.class);

    private final static String EXCEL2003 = "xls";
    private final static String EXCEL2007 = "xlsx";


    private static <T> void handleField(T t, String value, Field field) throws Exception {
        Class<?> type = field.getType();
        if (type == null || type == void.class || StringUtils.isBlank(value)) {
            return;
        }
        if (type == Object.class) {
            field.set(t, value);
            //数字类型
        } else if (type.getSuperclass() == null || type.getSuperclass() == Number.class) {
            if (type == int.class || type == Integer.class) {
                field.set(t, NumberUtils.toInt(value));
            } else if (type == long.class || type == Long.class) {
                field.set(t, NumberUtils.toLong(value));
            } else if (type == byte.class || type == Byte.class) {
                field.set(t, NumberUtils.toByte(value));
            } else if (type == short.class || type == Short.class) {
                field.set(t, NumberUtils.toShort(value));
            } else if (type == double.class || type == Double.class) {
                field.set(t, NumberUtils.toDouble(value));
            } else if (type == float.class || type == Float.class) {
                field.set(t, NumberUtils.toFloat(value));
            } else if (type == char.class || type == Character.class) {
                field.set(t, CharUtils.toChar(value));
            } else if (type == boolean.class) {
                field.set(t, BooleanUtils.toBoolean(value));
            } else if (type == BigDecimal.class) {
                field.set(t, new BigDecimal(value));
            }
        } else if (type == Boolean.class) {
            field.set(t, BooleanUtils.toBoolean(value));
        } else if (type == Date.class) {
            //
            field.set(t, value);
        } else if (type == String.class) {
            field.set(t, value);
        } else {
            Constructor<?> constructor = type.getConstructor(String.class);
            field.set(t, constructor.newInstance(value));
        }
    }

    /**
     * 浏览器下载excel
     *
     * @param fileName
     * @param response
     */
    public static void buildExcelDocument(String fileName, HttpServletResponse response) {
        try {
            //response.setContentType(MediaType.APPLICATION_OCTET_STREAM_VALUE);
            //前端接受这种ContentType
            response.setContentType("application/vnd.ms-excel;charset=utf-8");
            response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName+ DateUtils.getYMDHMS2(), "utf-8")+".xls");
            response.flushBuffer();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 生成excel文件
     *
     * @param path 生成excel路径
     * @param wb
     */
    private static void buildExcelFile(String path, Workbook wb) {

        File file = new File(path);
        if (file.exists()) {
            file.delete();
        }
        try {
            wb.write(new FileOutputStream(file));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * @param columnListName 第二行表头数据
     * @param list 目标数据
     * @param outputStream
     * @param title 第一行标题
     * @param columnWidth 设置列宽
     * @return void
     * @author gjd
     * @Description:Excel导出列表
     * @Date 11:19 2020/11/24/0024
     */
    public static <T> void copy(List<String> columnListName, List<List<Object>> list, OutputStream outputStream, String title,int columnWidth) {
        Workbook workbook = new HSSFWorkbook(); // 创建一个excel
        Sheet sheet = workbook.createSheet(title);// 新建sheet页
        //设置合并列
        /**
         * 参数依次为： 开始行号，结束行号，开始列号，结束列号
         */
        sheet.addMergedRegion(new CellRangeAddress(0, 0, 0, columnListName.size()-1));
        //设置列宽， 参数为：列索引，列宽度值
        for (int i = 0; i < columnListName.size(); i++) {
            sheet.setColumnWidth(i, columnWidth);
        }

        Row titleRow = sheet.createRow(0);
        //设置行高度
        titleRow.setHeightInPoints(36);
        int size = columnListName.size();
        Cell createCell = titleRow.createCell(0);
        //设置标题
        createCell.setCellValue(title);
        //设置大标题样式
        createCell.setCellStyle(bigTitle(workbook));
        //设置表头列名
        //创建内容行
        //创建标题
        titleRow = sheet.createRow(1);
        //设置行高
        titleRow.setHeightInPoints(26);
        for (int i = 0; i < size; i++) {
            createCell = titleRow.createCell(i);
            createCell.setCellValue(columnListName.get(i));
            //设置样式
            createCell.setCellStyle(title(workbook));
        }
        
        //避免遍历循环设施格式,导致消耗增大引发格式部分有问题
        CellStyle text = text(workbook);

        for (int i = 0; i < list.size(); i++) {
            List<Object> valueList = list.get(i);
            Row row = sheet.createRow(i + 2);
            size = valueList.size();
            for (int j = 1; j <= size; j++) {
                //样式
                createCell = row.createCell(j-1);
                Object object = valueList.get(j-1);
                createCell.setCellValue(object == null ? null : object.toString());
                createCell.setCellStyle(text);
            }
        }
        OutputStream out = outputStream;
        try {
            workbook.write(out);
            out.flush();
            out.close();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (out != null)
                try {
                    out.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            out = null;
            System.out.println("导出成功");
        }
        try {
            workbook.close();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    /*=============================导入数据===================================*/
    /**
     * 用来得到真实行数
     *
     * @param sheet
     * @return
     */
    public static int getExcelRealRow(Sheet sheet) {
        CellReference cellReference = new CellReference("A4");
        boolean flag = false;
        for (int i = cellReference.getRow(); i <= sheet.getLastRowNum(); ) {
            Row r = sheet.getRow(i);
            if (r == null) {
                //如果是空行（即没有任何数据、格式），直接把它以下的数据往上移动
                sheet.shiftRows(i + 1, sheet.getLastRowNum(), -1);
                continue;
            }
            flag = false;
            for (Cell c : r) {
                String cellValue = c.getStringCellValue();
                if (StringUtils.isBlank(cellValue)) {
                    flag = false;
                    break;
                }
                if (c.getCellTypeEnum() != CellType.BLANK) {
                    flag = true;
                    break;
                }
            }
            if (flag) {
                i++;
                continue;
            } else {//如果是空白行（即可能没有数据，但是有一定格式）
                if (i == sheet.getLastRowNum()) {
                    //如果到了最后一行，直接将那一行remove掉
                    sheet.removeRow(r);
                } else {
                    //如果还没到最后一行，则数据往上移一行
                    sheet.shiftRows(i + 1, sheet.getLastRowNum(), -1);
                }
            }
        }
        return sheet.getLastRowNum() + 1;
    }



    /*<-------------------------辅助的私有方法----------------------------------------------->*/

    /**
     * @MethodName  : getFieldByName
     * @Description : 根据字段名获取字段
     * @param fieldName 字段名
     * @param clazz 包含该字段的类
     * @return 字段
     */
    private static Field getFieldByName(String fieldName, Class<?>  clazz){
        //拿到本类的所有字段
        Field[] selfFields=clazz.getDeclaredFields();

        //如果本类中存在该字段，则返回
        for(Field field : selfFields){
            if(field.getName().equals(fieldName)){
                return field;
            }
        }

        //否则，查看父类中是否存在此字段，如果有则返回
        Class<?> superClazz=clazz.getSuperclass();
        if(superClazz!=null  &&  superClazz !=Object.class){
            return getFieldByName(fieldName, superClazz);
        }

        //如果本类和父类都没有，则返回空
        return null;
    }


    //大标题的样式
    public static CellStyle bigTitle(Workbook wb) {
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
    public static CellStyle title(Workbook wb) {
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
    public static CellStyle text(Workbook wb) {
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

#### ExcelUtils的使用

```java
 /**
     * @param vo
     * @param httpServletResponse
     * @return com.ycxc.vmts.common.utils2.DcResponse
     * @author gjd
     * @Description:
     * @Date 9:36 2021/3/12/0012
     */
    @ApiOperation(value = "导出流水统计报列表数据")
    @PostMapping(value = "/exportRunningWaterExcel")
    public DcResponse exportRunningWaterExcel(@RequestBody WasteRequest vo, HttpServletResponse httpServletResponse) throws IOException {
        vo.setPageNum(1);
        vo.setPageSize(9999);
        DcResponse response = iWasteTransferListService.getRunningWater(vo);
        PageInfo<HashMap<String,Object>> pageInfo = (PageInfo<HashMap<String, Object>>) response.getData();
        List<HashMap<String, Object>> resList = pageInfo.getList();
        if (CollectionUtils.isEmpty(resList))
            return DcResponse.ok("操作成功,但暂无导出数据");
        ArrayList<List<Object>> arrayList = new ArrayList<>();
        for (HashMap<String, Object> res : resList) {
            List<Object> valueList = new ArrayList<>();
            valueList.add(res.get("district"));
            valueList.add(res.get("enterprise_operating_address"));
            
            Double standing_book_number = (Double) res.get("standing_book_number");
            BigDecimal decimal = new BigDecimal(Double.toString(standing_book_number));
            valueList.add(decimal);
            //............
            arrayList.add(valueList);
        }
        //3.1 设置标题
        String title = "导出流水统计报列表数据";
        //设置列宽
        int columnWidth = 20 * 256;
        ExcelUtils.buildExcelDocument(title, httpServletResponse);
        // 导出的EXCEL列属性
        List<String> columnListName = Arrays.asList("地区", "街道","经营类别", "企业名称", "废物代码", "固废物名称", "数量(吨)", "日期");
        ExcelUtils.copy(columnListName, arrayList, httpServletResponse.getOutputStream(), title, columnWidth);
        return DcResponse.ok();
    }
```



#### 4.1 导出是 BigDecimal 字段 去掉小数点后的有效位

1. sql 方面处理

   ```mysql
   0 +cast(s.standing_book_number as CHAR)
   ```

2. 返回的字段是 double 类型

   - BigDecimal 接收double 需要先将double 转String
     再new  BigDecimal(String str)

#### 4.2 手机号的处理

```java
//手机号码
Double moblie = row.getCell(8).getNumericCellValue();
BigDecimal bd = new BigDecimal(moblie.toString());//要修改的值，需要string类型
String enterpriseLinkMobile=bd.setScale(0,BigDecimal.ROUND_HALF_UP).toPlainString();

```



## Sql部分

### 1.获取超时订单sql

主要是时间的函数的使用,以及时间的比较

```java
  /**
       * 获取超时订单
       * @param minute 超时时间（分）
       */
       List<OmsOrderDetail> getTimeOutOrders(@Param("minute") Integer minute);
```

```xml
 <select id="getTimeOutOrders" resultMap="orderDetailMap">
        SELECT
            o.id,
            o.order_sn,
            o.coupon_id,
            o.integration,
            o.member_id,
            o.use_integration,
            ot.id               ot_id,
            ot.product_name     ot_product_name,
            ot.product_sku_id   ot_product_sku_id,
            ot.product_sku_code ot_product_sku_code,
            ot.product_quantity ot_product_quantity
        FROM
            oms_order o
            LEFT JOIN oms_order_item ot ON o.id = ot.order_id
        WHERE
            o.status = 0
            AND o.create_time &lt; date_add(NOW(), INTERVAL -#{minute} MINUTE);
    </select>
```

### 2.修改 pms_sku_stock表的锁定库存及真实库存

主要是case when then的使用以及库存的控制

```java
int updateSkuStock(@Param("itemList") List<OmsOrderItem> orderItemList);
```

```xml
<update id="updateSkuStock">
        UPDATE pms_sku_stock
        SET
            stock = CASE id
            <foreach collection="itemList" item="item">
              WHEN #{item.productSkuId} THEN stock - #{item.productQuantity}
            </foreach>
            END,
            lock_stock = CASE id
            <foreach collection="itemList" item="item">
              WHEN #{item.productSkuId} THEN lock_stock - #{item.productQuantity}
            </foreach>
            END
        WHERE
            id IN
        <foreach collection="itemList" item="item" separator="," open="(" close=")">
            #{item.productSkuId}
        </foreach>
    </update>
```

#### 2.1解除取消订单的库存锁定

```java
    int releaseSkuStockLock(@Param("itemList") List<OmsOrderItem> orderItemList);
```

```xml
<update id="releaseSkuStockLock">
        UPDATE pms_sku_stock
        SET
        lock_stock = CASE id
        <foreach collection="itemList" item="item">
            WHEN #{item.productSkuId} THEN lock_stock - #{item.productQuantity}
        </foreach>
        END
        WHERE
        id IN
        <foreach collection="itemList" item="item" separator="," open="(" close=")">
            #{item.productSkuId}
        </foreach>
    </update>
```

#### **2.2一对多的xml写法==>mall->SmsCouponHistoryDao**

### 3.经纬度计算距离的Sql

- enterprise_addr_x : x轴坐标
- enterprise_addr_y : y轴坐标
- latitude : 纬度
- longitude : 经度

```mysql
-- 公里数
SELECT 6371 * acos(
        cos(radians(#{longitude}))
                * cos(radians(enterprise_addr_x))
                      * cos(radians(enterprise_addr_y) - radians({latitude}))
                    + sin(radians(#{longitude}))
                                  * sin(radians(enterprise_addr_x))
                              ) * 1000
                      ) AS distance
                FROM isz_enterprise
                HAVING distance & lt;20
    ORDER BY distance;
```

### 4.获取 一个字段里面的所有图片的次数

#### 4.1 replace 函数

- 语法：replace(object,search,replace)

- 意义：把object中出现search的全部替换为replace

```mysql
update  t_user_score  set cname = REPLACE(cname ,' ','') ;
```

```mysql
sum((char_length(substring_index(ev.image,',-',1)) - char_length( replace(substring_index(ev.image,',-',1), 'htt','' ))) div 3) as'totalSplitImage',
```

### 5.获取所有单字段所有图片

```mysql
substring_index(ev.image,',-',1) 'splitImage',
```

### 6.时间戳 转 时间格式

- 10位的时间戳

```mysql
select FROM_UNIXTIME(1251820800,'%Y-%m-%d')
```

- 13位的时间戳的处理

```mysql
select FROM_UNIXTIME(CONVERT(1616133400952/1000,signed), '%Y-%m-%d %T')

-- ---不加 %T 只有 年月日
```

- 时间转时间戳

```mysql
select unix_timestamp('2009-09-02');
```

7.varchar类型判断非空

```mysql
LENGTH(trim(columns))>0
```

## Mybatis部分

### 1. sql日期处理

```sql
-- datetime----------
date_format(s.create_time,'%Y-%m-%d')  'create_time'
```

### 2.case .... when ...的使用

```sql
   CASE WHEN be.enterprise_operating_state = '01' THEN '营业'
        WHEN be.enterprise_operating_state = '02' THEN '停业'
        WHEN be.enterprise_operating_state = '03' THEN '整顿'
        WHEN be.enterprise_operating_state = '04' THEN '停业整顿'
        WHEN be.enterprise_operating_state = '05' THEN '歇业'
        WHEN be.enterprise_operating_state = '06' THEN '注销'
        WHEN be.enterprise_operating_state = '07' THEN '其他'
        ELSE '营业'
   END as 'enterpriseOperatingState'
```

```sql
select
sum(case when (ev.score &lt; 2 or ev.description &lt; 2) then 1 else 0 end ) 'totalNegativeCounts'
from A
```

### 3.自选日期统计

dto

```java
@ApiModel(value="StatisticsDto", description="统计返回实体")
public class StatisticsDto implements Serializable{

    private static final long serialVersionUID = 1L;

    @ApiModelProperty(value = "日期(可用于X轴)")
    private String date;

    @ApiModelProperty("名字(可用于X轴)")
    private String name;

    @ApiModelProperty(value = "统计数量(Y轴显示)")
    private Long num;

	//get ...
    //set ...

}
```

request

```java
public class DateRequest extends BasePageBean {

    @ApiModelProperty("开始日期(yyyy-MM-dd)")
    @JsonFormat(pattern = "yyyy-MM-dd")
    @NotBlank(message = "开始日期不能为空")
    private String startDate;

    @ApiModelProperty("结束日期(yyyy-MM-dd)")
    @JsonFormat(pattern = "yyyy-MM-dd")
    @NotBlank(message = "结束日期不能为空")
    private String endDate;

    @ApiModelProperty("企业id(后端自动获取)")
    private String enterpriseId;

    @ApiModelProperty(value = "供应商名称")
    private String supplierName;

    @ApiModelProperty(value = "客户名称")
    private String ownerName;

    @ApiModelProperty(value = "库单类型(0:手工采购;1:其他采购;2:自动采购;3:采购退货)")
    private Integer purchasedType;

	//get ...
	//set ...
}
```

xml

```sql
<select id="purchaseOrderChartByType" resultType="com.ycxc.immv.controller.bean.vo.StatisticsDto">
        SELECT
        leftDay.date `date`,
        leftDay.NAME `name`,
        IFNULL(rightOut.num, 0) num
        FROM
        <choose>
            <when test="request.type != null and request.type == 1">
                (SELECT
                date_add(subdate(CURDATE(),IF (date_format(CURDATE(), '%w') = 0,7,date_format(CURDATE(), '%w')) -
                1),INTERVAL w.DATEINT - 1 DAY) `date`,
                w.NAME `name`
                FROM sys_week w) leftDay
                LEFT JOIN (
                SELECT
                DATE_FORMAT(pp.create_time, '%Y-%m-%d') createDate,
                SUM(pp.order_number) num
                FROM
                A pp
                <where>
                    <if test="request.purchasedType == 3">
                        and pp.purchased_type = 3
                    </if>
                    <if test="request.purchasedType != 3">
                        and pp.purchased_type != 3
                    </if>
                    AND pp.purchased_status in (0,3)
                    AND YEARWEEK(DATE_FORMAT(pp.create_time, '%Y-%m-%d'),1) = YEARWEEK(NOW(), 1)
                </where>
                GROUP BY
                createDate
                ) rightOut
                ON leftDay.date = rightOut.createDate
            </when>
            <when test="request.type != null and request.type == 2">
                (SELECT
                date_add(curdate(),INTERVAL - DAY (curdate()) + d.DATEINT DAY) `date`,
                d.NAME `name`
                FROM sys_day d) leftDay
                LEFT JOIN (
                SELECT
                DATE_FORMAT(pp.create_time, '%Y-%m-%d') createDate,
                SUM(pp.order_number) num
                FROM
                purchased_product pp
                <where>
                    <if test="request.purchasedType == 3">
                        and pp.purchased_type = 3
                    </if>
                    <if test="request.purchasedType != 3">
                        and pp.purchased_type != 3
                    </if>
                    AND pp.purchased_status in (0,3)
                    AND DATE_FORMAT(pp.create_time, '%Y%m') = DATE_FORMAT(CURDATE(), '%Y%m')
                </where>
                GROUP BY
                createDate
                ) rightOut
                ON leftDay.date = rightOut.createDate
                WHERE
                DATE_FORMAT(`date`, '%Y%m') = DATE_FORMAT(CURDATE(), '%Y%m')
            </when>
            <otherwise>
                (SELECT
                DATE_FORMAT(date_add(curdate(),INTERVAL - MONTH (curdate()) + m.DATEINT MONTH),'%Y-%m') date,
                m.NAME `name`
                FROM
                sys_month m ) leftDay
                LEFT JOIN (
                SELECT
                DATE_FORMAT(pp.create_time, '%Y-%m') createDate,
                SUM(pp.order_number) num
                FROM
                purchased_product pp
                <where>
                    <if test="request.purchasedType == 3">
                        and pp.purchased_type = 3
                    </if>
                    <if test="request.purchasedType != 3">
                        and pp.purchased_type != 3
                    </if>
                    AND pp.purchased_status in (0,3)
                    AND YEAR (pp.create_time) = YEAR (now())
                </where>
                GROUP BY createDate ) rightOut ON leftDay.date = rightOut.createDate
            </otherwise>
        </choose>
        ORDER BY `date`
    </select>
```

### 4.年月日统计

```xml
<select id="purchaseOrderByDay" resultType="com.ycxc.immv.controller.bean.vo.StatisticsDto">
        SELECT
        leftDay.date `date`,
        leftDay. NAME `name`,
        IFNULL(rightOut.num, 0) num
        FROM
        (SELECT
        date_add(#{request.startDate},INTERVAL - DAY (#{request.startDate}) + d.DATEINT DAY) `date`,
        d.NAME `name`
        FROM
        sys_day d
        WHERE
        date_add(
        #{request.startDate},
        INTERVAL - DAY (#{request.startDate}) + d.DATEINT DAY
        ) BETWEEN #{request.startDate}
        AND #{request.endDate}
        ) leftDay
        LEFT JOIN (
        SELECT
        DATE_FORMAT(pp.create_time, '%Y-%m-%d') createDate,
        SUM(pp.order_number) num
        FROM
        purchased_product pp
        <where>
            <if test="request.purchasedType == 3">
                and pp.purchased_type = 3
            </if>
            <if test="request.purchasedType != 3">
                and pp.purchased_type != 3
            </if>
            AND pp.enterprise_id = #{request.enterpriseId}
            AND DATE_FORMAT(pp.create_time, '%Y-%m-%d') between #{request.startDate} and #{request.endDate}
        </where>
        GROUP BY
        createDate )
        rightOut ON leftDay.date = rightOut.createDate
        ORDER BY `date` asc
    </select>
```

