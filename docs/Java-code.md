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

#### **3.一对多的xml写法==>mall->SmsCouponHistoryDao**

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

