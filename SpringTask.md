# Spring Task 介绍
Spring Task 是Spring框架提供的任务调度工具，可以按照约定的时间自动执行某个代码逻辑。

定位：定时任务框架。
作用：定时自动执行某段代码。

> 只要是需要定时处理的场景都可以使用Spring Task。应用场景：
> - 信用卡每月还款提醒
> - 银行贷款每月还款提醒
> - 火车票售票系统处理未支付订单
> - 入职纪念日为用户发送通知

# cron 表达式
cron 表达式其实就是一个字符串，通过 cron 表达式可以定义任务触发的时间。
构成规则：分为 6 或 7 个域，由空格分隔开，每个域代表一个含义。每个域的含义分别为：秒、分钟、小时、日、月、周、年(可选)。

2022年10月12日上午9点整对应的 cron 表达式为：0 0 9 12 10 ? 2022 。
![](./pictures/SpringTask/cron.png)

> 对于复杂的 cron 表达式，可以使用 cron 表达式在线生成器：https://cron.qqe2.com/ 。

# 快速开始
Spring Task使用步骤：
- 导入 maven 坐标 spring-context（已存在）；

- 启动类添加注解 @EnableScheduling 开启任务调度；
    ```java
    @SpringBootApplication
    @EnableTransactionManagement //开启注解方式的事务管理
    @Slf4j
    @EnableCaching //开启缓存功能
    @EnableScheduling // 开启定时任务功能
    public class SkyApplication {
        public static void main(String[] args) {
            SpringApplication.run(SkyApplication.class, args);
            log.info("server started");
        }
    }
    ```
- 自定义定时任务类（需要注册为组件，加入 Spring 容器中）。
    ```java
    /**
     * 自定义定时任务类
     */
    @Component
    @Slf4j
    public class MyTask {
        @Scheduled(cron = "0/5 * * * * ?")
        public void executeTask() {
            log.info("定时任务开始执行: {}", System.currentTimeMillis());
        }
    }
    ```
    执行效果：
    ```bash
    2025-09-22 14:23:35.004  INFO 28396 --- [   scheduling-1] com.sky.task.MyTask                      : 定时任务开始执行: 1758522215004
    2025-09-22 14:23:40.002  INFO 28396 --- [   scheduling-1] com.sky.task.MyTask                      : 定时任务开始执行: 1758522220002
    2025-09-22 14:23:45.011  INFO 28396 --- [   scheduling-1] com.sky.task.MyTask                      : 定时任务开始执行: 1758522225011
    2025-09-22 14:23:50.008  INFO 28396 --- [   scheduling-1] com.sky.task.MyTask                      : 定时任务开始执行: 1758522230008
    ......
    ```







