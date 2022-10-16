# SpringBatch

## 第一章 SpringBatch入门

### 第一节 SpringBatch概述

​		Spring Batch是个轻量级的、 完善的批处理框架,旨在帮助企业建立健壮、高效的批处理应用。Spring Batch是Spring的一个子项目,使用Java语言并基于Spring框架为基础开发,使得已经使用Spring框架的开发者或者企业更容易访问和利用企业服务。

​		Spring Batch提供了大量可重用的组件,包括了日志追踪、事务、任务作业统计、任务重启、跳过、重复资源管理。对于大数据量和高性能的批处理任
务,Spring Batch同样提供了高级功能和特性来支持比如分区功能、远程功能。总之,通过Spring Batch能够支持简单的、复杂的和大数据量的批处理作业。

​		Spring Batch是一个批处理应用框架,不是调度框架，但需要和调度框架合作来构建完成的批处理任务。它只关注批处理任务相关的问题,如事务、并发、监控、执行等,并不提供相应的调度功能。如果需要使用调用框架，在商业软件和开源软件中已经有很多优秀的企业级调度框架(如Quartz. Tivoli、 Control-M、 Cron等)可以使用。

**框架主要有以下功能:**
Transaction management (事务管理)
Chunk based processing (基于块的处理)
Declarative 1/0 (声明式的输入输出)
Start/Stop/Restart (启动/停止/再启动)
Retry/Skip (重试/跳过)

![输入图片说明](https://images.gitee.com/uploads/images/2020/0604/160744_ee3431e6_2169232.png "image-20200529133059728.png")

框架一共有**4个主要角色**: 

**JobLauncher**是任务启动器，通过它来启动任务，可以看做是程序的入口。

**Job**代表着一个具体的任务。 

**Step**代表着一个具体的步骤，一个Job可以包含多个Step (想象把大象放进冰箱这个任务需要多少个步骤你就明白了) .

 **JobRepository**是存储数据的地方，**可以看做是一个数据库的接口**，在任务执行的时候需要通过它来记录任务状态等等信息。

### 第二节 搭建SpringBatch项目

网站，idea。。

### 第三节 SpringBatch入门程序

```java
@Configuration
@EnableBatchProcessing
public class JobConfiguration {
    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    /**
     * @Description: 创建任务对象
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    public Job helloWorldJob() {
        //job名字//开始step
        return jobBuilderFactory.get("helloWorldJob")
                .start(step1())
                .build();
    }

    @Bean
    public Step step1() {
        /**
         * @Description: step1 step的名字，tasklet执行任务 可以用chunk
         * @Param: []
         * @Return: org.springframework.batch.core.Step
         */
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    /**
                     * @Description: RepeatStatus 状态值，一步步来，一个step结束开始下一个
                     * @Param: [stepContribution, chunkContext]
                     * @Return: org.springframework.batch.repeat.RepeatStatus
                     */
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("He11o World!");
                        return RepeatStatus.FINISHED;
                    }

                }).build();
    }
}
```

### 第四节 替换为MySQL数据库

```java
<dependency>
	<groupId>mysq1</groupId>
	<artifactId>mysql-connector-java</artifactId> 
</dependency> 
<dependency>
	<groupId>ore.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

**application.properties:**

```java
spring.datasource.url=jdbc:mysql://localhost:3306/batch
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.schema=classpath:/org/springframework/batch/core/schema-mysql.sql
spring.batch.initialize-schema=always
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
```



### 第五节 核心API

, sheode tate = 207085
JooPrsates
JolEauutin
**JobInstance:**该领域概念和ob的关系'jJava中实何和类的关系样，Job定义了个工.作流程。JobInstance就是该 工作流程的一个具体实例。**一个Job可以有多 个JobInstance**

**JobParameters:**是组可以贯穿整Job的运行时**配置参数**。不同的配置将产生不同的JobInstance. 如果你是使用**JobParameters**运行间Job. 那么这次运行会重用 上

**JobExecution:** 该领域概念 表示**JobInstance**的运行。 JobInstance运行时可能会成功或者失败。每次jobInstance的运行都会产生 1个JobExecution. 运行时间，开始结束，状态，成功与否。。

**StepExecution:**类似于 JobExecution.该领域对象表示Step的运行。Step足Job的部分。 因此个StepExecution公关联到 个Jobexecution. 另外。浅对象还会存储很
**ExecutionContext:**上下文，从前面的JobExecution. StepExecution的属性介绍中已经提到I该领域概念。说穿了。该领域概念就是个容器。该容器Batch框架控制。框架对该容器持久化。

## 第二章 作业流

### 第一节 Job的创建和使用

**Job**:作业。批处理中的核心概念，是Batch操作的基础单元。
每个作业Job有1个或者多个作业步Step:

```java
@Configuration
@EnableBatchProcessing
public class JobDemo {
    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    /**
     * @Description: 让step1,step2,step3依次执行
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job jobDemoJob(){
        return jobBuilderFactory.get("jobDemoJob")
                .start(step1())
                .on("COMPLETED").to(step1())
                .from(step2()).on("COMPLETED").to(step3())
                .from(step3()).end()
                .build();
        
      
             // .from(step2()).on("COMPLETED").fail()
        	 // .from(step2()).on("COMPLETED").stopAndRe
  

               // .start(step1())
               // .next(step2())
               // .next(step3())
               // .build();


    }

    public Step step3() {
        return stepBuilderFactory.get("step3")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("step3");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }

    public Step step2() {
        return stepBuilderFactory.get("step2")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("step2");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }

    public Step step1() {
        return stepBuilderFactory.get("step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("step1");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }
}
```

### 第二节 Flow的创建和使用

**1.Flow是多个Step的集合**
**2.可以被多个Job复用**
**3.使用FlowBuilder来创建**

```java
@Configuration
@EnableBatchProcessing
public class FlowDemo {
    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;


    /**
     * @Description: 创建Flow对象,指明F1ow对象包含哪些step
     * @Param: []
     * @Return: org.springframework.batch.core.job.flow.Flow
     */
    @Bean
    public Flow flowDemoFlow(){
        return new FlowBuilder<Flow>("flowDemoFlow")
                .start(flowDemoStep1())
                .next(flowDemoStep2())
                .build();
    }


    /**
     * @Description: fowDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job fowDemoJob(){
        return jobBuilderFactory.get("fowDemoJob0")
                .start(flowDemoFlow())
                .next(flowDemoStep3())
                .end()
                .build();
    }
   
... ...
```





### 第三节 split实现并发执行

**实现任务中的多个step或多个flow并发执行**
1:创建若干个step
2:创建两个flow
3:创建-个任务包含以上两个flow,井让这两个flow并发执行

```java
@Configuration
@EnableBatchProcessing
public class SplitDemo {

    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;


    /**
     * @Description: 创建Flow对象,指明F1ow对象包含哪些step
     * @Param: []
     * @Return: org.springframework.batch.core.job.flow.Flow
     */
    @Bean
    public Flow splitDemoFlow1(){
        return new FlowBuilder<Flow>("splitDemoFlow1")
                .start(splitDemoStep1())
                .build();
    }

    @Bean
    public Flow splitDemoFlow2(){
        return new FlowBuilder<Flow>("splitDemoFlow2")
                .start(splitDemoStep2())
                .next(splitDemoStep3())
                .build();
    }

    /**
     * @Description: fowDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job splitDemoJob(){
        return jobBuilderFactory.get("splitDemoJob")
                .start(splitDemoFlow1())
                .split(new SimpleAsyncTaskExecutor())
                .add(splitDemoFlow2())
                .end()
                .build();

    }
```

### 第四节 决策器的使用

接口: JobExecutionDecider 

```java
public class MyDecider implements JobExecutionDecider {

    private int count;


    /**
     * @Description: decide 决策器，先执行odd奇数
     * @Param: [jobExecution, stepExecution]
     * @Return: org.springframework.batch.core.job.flow.FlowExecutionStatus
     */
    @Override
    public FlowExecutionStatus decide(JobExecution jobExecution, StepExecution stepExecution) {
        count++;
        if(count%2==0){
            return new FlowExecutionStatus("even偶数");
        }else {
            return new FlowExecutionStatus("odd奇数");
        }
    }
}
```



```java
@Configuration
@EnableBatchProcessing
public class DeciderDemo {
    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;


    /**
     * @Description: myDecider 决策器
     * @Param: []
     * @Return: org.springframework.batch.core.job.flow.JobExecutionDecider
     */
    @Bean
    public JobExecutionDecider myDecider(){
        return new MyDecider();
    }

    @Bean
    public Job deciderDemoJob(){
        return jobBuilderFactory.get("deciderDemoJob")
                .start(deciderDemoStep1())
                .next(myDecider())
                .from(myDecider()).on("even偶数").to(deciderDemoStep2())
                .from(myDecider())
                .on("odd奇数").to(deciderDemoStep3())
                .from(deciderDemoStep3())
                .on("*").to(myDecider())//无论返回什么，回到决策器 ↑next(myDecider())
                .end()
                .build();
    }



    @Bean
    public Step deciderDemoStep1(){
        return stepBuilderFactory.get("deciderDemoStep1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("deciderDemoStep1!");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }
```

### 第五节 Job的嵌套

一个Job可以嵌套在另一个Job中，被嵌赛的Job称为子Job,外部Job称为父Jb。子job不能单独执行， 賽要由父Job来启动
案例:创建两个Job，作为子Job,再创建一个Job作为父Job

```java
@Configuration
public class NestedDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    private JobLauncher launcher;
    @Autowired
    private Job childJobOne;
    @Autowired
    private Job childJobTwo;

    @Bean
    public Job parentJob(JobRepository jobRepository, PlatformTransactionManager transactionManager){
        return jobBuilderFactory.get("parentJobs")
                .start(childJob1(jobRepository,transactionManager))
                .next(childJob2(jobRepository,transactionManager))
                .build();
    }

    /**
     * @Description: childJob2返回Job类型的Step，特殊Step
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    private Step childJob2(JobRepository jobRepository, PlatformTransactionManager transactionManager) {
        return new JobStepBuilder(new StepBuilder("childJob2"))
                .job(childJobTwo)
                .launcher(launcher)
                .repository(jobRepository)
                .transactionManager(transactionManager)
                .build();

    }

    private Step childJob1(JobRepository jobRepository,PlatformTransactionManager transactionManager) {
        return new JobStepBuilder(new StepBuilder("childJob1"))
                .job(childJobOne)
                .launcher(launcher)
                .repository(jobRepository)
                .transactionManager(transactionManager)
                .build();
    }
}
```



```java
@Configuration
@EnableBatchProcessing
public class ChildJob2 {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Step childJob2Step1(){
        return stepBuilderFactory.get("childJob2Step1")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("childJob2Step1!");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }

    @Bean
    public Step childJob2Step2(){
        return stepBuilderFactory.get("childJob2Step2")
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        System.out.println("childJob2Step2!");
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }

    @Bean
    public Job childJobTwo(){
        return jobBuilderFactory.get("childJobTwo!!")
                .start(childJob2Step1())
                .next(childJob2Step2())
                .build();
    }

}
```

```java
spring.batch.job.names=parentJobs  //选择执行的Job，名字是自己设置的Job名称
```



### 第六节 监听器的使用

用来监听批处理作业的执行情况
创建监听可以通过实现接口或使用注解

```java
//不同的监听，以及触发时机
JobExecutionListener(before,after)
StepExecutionListener(before,after)
ChunkListener(before,after,error)
ItemReadListener,ItemProcessListener,ItemWriteListener(before,after,error)
public class MyJobListener implements JobExecutionListener{
```

**示例代码：**

```java
public class MyChunkListener {

    @BeforeChunk
    public void beforeChunk(ChunkContext chunkContext){
        System.out.println(chunkContext.getStepContext().getStepName()+"before...");
    }

    @AfterChunk
    public void afterChunk(ChunkContext chunkContext){
        System.out.println(chunkContext.getStepContext().getStepName()+"after...");
    }

}
```

```java
public class MyJobListener implements JobExecutionListener {

    @Override
    public void beforeJob(JobExecution jobExecution) {
        System.out.println(jobExecution.getJobInstance().getJobName()+"before...");
    }

    @Override
    public void afterJob(JobExecution jobExecution) {
        System.out.println(jobExecution.getJobInstance().getJobName()+"after...");
    }
}
```



```java
@Configuration
@EnableBatchProcessing
public class ListenerDemo {

    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job listenerJob() {
        return jobBuilderFactory.get("listenerJob")
                .start(step1())
                .listener(new MyJobListener())
                .build();


    }

    /**
     * @Description: step1 Chunk使用方式
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step step1() {
        return stepBuilderFactory.get("listenerStep1")
                //数据的读取，<String,String>规定读取和输出的数据类型每读完2个数据进行一次输出处理
                .<String,String>chunk(2)
                //容错
                .faultTolerant()
                .listener(new MyChunkListener())
                //数据的读取
                .reader(read())
                //数据的写入/输出
                .writer(write())
                .build();
    }


    /**
     * @Description: write
     * @Param: []
     * @Return: org.springframework.batch.item.ItemWriter<java.lang.String>
     */
    @Bean
    public ItemWriter<String> write() {
        return new ItemWriter<String>() {
            @Override
            public void write(List<? extends String> list) throws Exception {
                for(String item:list){
                    System.out.println(item);
                }
            }
        };
    }


    /**
     * @Description: read
     * @Param: []
     * @Return: org.springframework.batch.item.ItemReader<java.lang.String>
     */
    @Bean
    public ItemReader<String> read() {
        return new ListItemReader<>(Arrays.asList("java","spring","mybatis"));
    }
}
```



### 第七节 Job参数

在Job运行时可以以key=value形式传递参数

```java
/**
 * @ClassName ParametersDemo 实现Step监听器
 * @Description ParametersDemo
 * @Author ZX
 * @Date 2020/5/30
 */
@Configuration
@EnableBatchProcessing
public class ParametersDemo implements StepExecutionListener {
    /**
     * @Description: 注入创建任务对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    /**
     * @Description: //任务的执行由Step决定,注入创建Step对象的对象
     * @Param:
     * @Return:
     */
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    private Map<String,JobParameter> parameters;

    @Bean
    public Job parameterJob() {
        return jobBuilderFactory.get("parameterJob")
                .start(parameterStep())
                .listener(new MyJobListener())
                .build();


    }


    /**
     * @Description: parameterStep
     * Job执行的是Step，Job使用的数据肯定是在step中使用，只需给Step传递数据。
     * 使用监听，使用Step级别的监听来传递数据
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step parameterStep() {
        return stepBuilderFactory.get("parameterStep")
                .listener(this)
                .tasklet(new Tasklet() {
                    @Override
                    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception {
                        //输出接收到的参数的值
                        System.out.println(parameters.get("info"));
                        return RepeatStatus.FINISHED;
                    }
                }).build();
    }

    @Override
    public void beforeStep(StepExecution stepExecution) {
        parameters = stepExecution.getJobParameters().getParameters();
    }

    @Override
    public ExitStatus afterStep(StepExecution stepExecution) {
        return null;
    }
}

```

**输入参数:**

![image-20200603134912191](F:\TyporaPhotos\image-20200603134912191.png)



## 第三章 数据输入

### 第一节 ItemReader概述

```java
@Configuration
@EnableBatchProcessing
public class ItemReaderDemo {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job itemReaderDemoJob() {
        return jobBuilderFactory.get("itemReaderDemoJob")
                .start(itemReaderDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step itemReaderDemoStep() {
        return stepBuilderFactory.get("itemReaderDemoStep")
                .chunk(2)
                .reader(itemReaderDemoRead())
                .writer(list->{
                    for (Object item:list){
                        System.out.println(item+"...");
                    }
                }).build();
    }


    /**
     * @Description: itemReaderDemoRead 自定义itemReader
     * @Param: []
     * @Return: MyReader
     */
    @Bean
    public MyReader itemReaderDemoRead() {
        List<String> data = Arrays.asList("鼠","牛","虎","兔");
        return new MyReader(data);
    }

}
```

```java
/**
 * @ClassName MyReader
 * 自定义ItemReader
 * @Description MyReader
 * @Author ZX
 * @Date 2020/5/30
 */
public class MyReader implements ItemReader<String> {

    private Iterator<String> iterator;

    //构造函数
    public MyReader(List<String> list){
        this.iterator=list.iterator();
    }

    @Override
    public String read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException {
        //默认一个一个读数据
        if(iterator.hasNext()){
            return this.iterator.next();
        }else {
            return null;
        }
    }
}
```



### 第二节 从数据库中读取数据

用**JdbcPagingltemReader**类实现

```java
@Configuration
@EnableBatchProcessing
public class ItemReaderDbDemo {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    private DataSource dataSource;

    @Autowired
    @Qualifier("dbJdbcWriter")
    private ItemWriter<? super User> dbJdbcWriter;

    @Bean
    public Job ItemReaderDbDemoJob(){
        return jobBuilderFactory.get("itemReaderDbDemoJob")
                .start(itemReaderDbStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step itemReaderDbStep() {
        return stepBuilderFactory.get("itemReaderDemoStep")
                .<User, User>chunk(2)
                .reader(dbJdbcReader())
                .writer(dbJdbcWriter)
                .build();
    }

    @Bean
    @StepScope
    public JdbcPagingItemReader<User> dbJdbcReader() {
        JdbcPagingItemReader<User> reader = new JdbcPagingItemReader<User>();
        reader.setDataSource(dataSource);
        reader.setFetchSize(2);
        //把读取到的记录转换成User对象
        reader.setRowMapper(new RowMapper<User>() {
            /**
             * @Description: 结果集的映射
             * @Param: [resultSet, i]
             * @Return: com.example.springbootjdbc.pojo.Users
             */
            @Override
            public User mapRow(ResultSet resultSet, int rowNum) throws SQLException {
                User users = new User();
                users.setId(resultSet.getInt("id"));
                users.setAge(resultSet.getInt("age"));
                users.setPhone(resultSet.getInt("phone"));
                users.setUsername(resultSet.getString("username"));
                users.setEmail(resultSet.getString("email"));
                return users;
            }
        });
        //指定sq1语句
        MySqlPagingQueryProvider provider =new MySqlPagingQueryProvider () ;
        provider.setSelectClause ("id,username,age,phone,email") ;
        provider.setFromClause ("from users") ;
        //指定根据哪个字段进行排序
        Map<String, Order> sort = new HashMap<>(1) ;
        sort.put("id", Order.ASCENDING);
        provider.setSortKeys(sort);
        reader.setQueryProvider(provider);
        return reader ;
    }

}
```

```java
/**
 * @ClassName DbJdbcWriter 自建ItemWriter
 * @Description DbJdbcWriter
 * @Author ZX
 * @Date 2020/5/30
 */
@Component("dbJdbcWriter")
public class DbJdbcWriter implements ItemWriter<User>{


    @Override
    public void write(List<? extends User> list) throws Exception {
        for (User user:list){
            System.out.println(user);
        }
    }
}
```

### 第三节 从普通文件中读取数据

**用FlatFileItemReader类**

customer.txt内容如下：

```java
id,firstName,lastName,birthday
1,Stone,Barrett,1964-10-19 14:11:03
2,Raymond,Pace,1977-12-11 21 :44:30
3,Armando,Logan,1986-12-25 11:54:28
4,Latifah,Barnett,1959-07-24 06:00:16
5,Cassandra,Moses,1956-09-14 06:49:28
6,Audra,Hopkins,1984-08-30 04:18:10
7,Upton,Morrow,1973-82-04 05:26:05
8,Melodie,Velasquez,1953-04-26 11:16:26
9,Sybill,Nolan,1951-06-24 14:56:51
10,Glenna,Little,1953-08-27 13:15:08
11,Ingrid,Jackson,1957-09-05 21:36:47
12,Duncan,Castaneda,1979-01 21 18:31:27
13,Xaviera,Gillespie,1965-07-18 15:05:22
14,Rhoda,Lancaster,1990-09-11 15:52:54
15,Fatima,Combs,1979-06-01 06: 58: 54
```

示例代码

```java
@Configuration
@EnableBatchProcessing
public class FileItemReaderDemo {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    @Qualifier("fileFileItemWriter")
    private ItemWriter<? super Customer> fileFileItemWriter;

    @Bean
    public Job FileItemReaderDemo() {
        return jobBuilderFactory.get("FileItemReaderDemo")
                .start(FileItemReaderDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step FileItemReaderDemoStep() {

        return stepBuilderFactory.get("FileItemReaderDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(fileItemReaderDemoReader())
                .writer(fileFileItemWriter)
                .build();
    }



    /**
     * @Description: fileItemReaderDemoReader 文件读取
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public FlatFileItemReader<Customer> fileItemReaderDemoReader() {
        FlatFileItemReader<Customer> reader = new FlatFileItemReader<Customer>();
        reader.setResource(new ClassPathResource("customer.txt"));
        //跳过第一行
        reader.setLinesToSkip(1);
        //数据解析
        DelimitedLineTokenizer tokenizer = new DelimitedLineTokenizer();
        tokenizer.setNames(new String[]{"id","fistName","lastName","birthday"});
        //把解析出的一个数据映射为Customer对象
        DefaultLineMapper<Customer> mapper = new DefaultLineMapper<>();
        mapper.setLineTokenizer(tokenizer);
        mapper.setFieldSetMapper(new FieldSetMapper<Customer>() {

            /**
             * @Description: mapFieldSet 映射
             * @Param: [fieldSet]
             * @Return: com.example.springbatchdemo.pojo.Customer
             */
            @Override
            public Customer mapFieldSet(FieldSet fieldSet) throws BindException {
                Customer customer = new Customer();
                customer.setId(fieldSet.readLong("id"));
                customer.setFirstName(fieldSet.readString("fistName"));
                customer.setLastName(fieldSet.readString("lastName"));
                customer.setBirthday(fieldSet.readString("birthday"));
                return customer;
            }
        });
        mapper.afterPropertiesSet();
        reader.setLineMapper(mapper);
        return reader;
    }
}
```

自建ItemWriter：

```java
@Component("fileFileItemWriter")
public class FileFileItemWriter implements ItemWriter<Customer> {
    @Override
    public void write(List<? extends Customer> list) throws Exception {
        for (Customer customer:list){
            System.out.println(customer);
        }
    }
}

```



### 第四节 从XML文件中读取数据

**使用StaxEventtemReader类**

xml文件：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<customers>
    <customer>
        <id>1</id>
        <firstName>Mufutau</firstName>
        <lastName>Maddox</lastName>
        <birthday>2017-06-05 19:43:51PM</birthday>
    </customer>
    <customer>
        <id>2</id>
        <firstName>Brenden</firstName>
        <lastName>Cobb</lastName>
        <birthday>2017-01-06 13:18:17PM</birthday>
    </customer>
    <customer>
        <id>3</id>
        <firstName>Kerry</firstName>
        <lastName>Joseph</lastName>
        <birthday>2016-09-15 18:32:33PM</birthday>
    </customer>
    <customer>
        <id>4</id>
        <firstName>asdasd</firstName>
        <lastName>Joseph</lastName>
        <birthday>2016-09-15 18:32:33PM</birthday>
    </customer>
    <customer>
        <id>5</id>
        <firstName>JOJO5</firstName>
        <lastName>Jobana</lastName>
        <birthday>2016-09-15 18:32:33PM</birthday>
    </customer>
    <customer>
        <id>6</id>
        <firstName>XuLun</firstName>
        <lastName>JoTaiLang</lastName>
        <birthday>2046-09-15 18:32:33PM</birthday>
    </customer>
    <customer>
        <id>7</id>
        <firstName>JaiLuo</firstName>
        <lastName>JieBeiLing</lastName>
        <birthday>2077-09-15 18:32:33PM</birthday>
    </customer>
</customers>
```

配置pom文件

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-oxm</artifactId>
</dependency>
<dependency>
    <groupId>com.thoughtworks.xstream</groupId>
    <artifactId>xstream</artifactId>
    <version>1.4.7</version>
</dependency>
```

示例代码：

```java
@Configuration
@EnableBatchProcessing
public class XmlItemReaderDemo {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    @Qualifier("xmlFileWriter")
    private ItemWriter<? super Customer> xmlFileWriter;

    @Bean
    public Job xmlItemReaderDemoJob() {
        return jobBuilderFactory.get("xmlItemReaderDemoJob")
                .start(xmlItemReaderDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step xmlItemReaderDemoStep() {

        return stepBuilderFactory.get("xmlItemReaderDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(xmlFileReader())
                .writer(xmlFileWriter)
                .build();
    }



    /**
     * @Description: xmlFileReader 文件读取
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    @StepScope
    public StaxEventItemReader<Customer> xmlFileReader() {
        StaxEventItemReader<Customer> reader = new StaxEventItemReader<Customer>();
        //指定文件位置
        reader.setResource(new ClassPathResource("customer.xml"));
        //跳过第一行
        reader.setFragmentRootElementName("customer");
        //把xml转成对象
        XStreamMarshaller unmarshaller = new XStreamMarshaller();
        //告诉unmarshaller把xml转成什么类型
        Map<String,Class> map = new HashMap<>();
        map.put("customer",Customer.class);
        unmarshaller.setAliases(map);

        reader.setUnmarshaller(unmarshaller);
        return reader;

    }
}
```

自建Itemwriter

```java
@Component("xmlFileWriter")
public class XmlItemWriter implements ItemWriter<Customer> {
    @Override
    public void write(List<? extends Customer> list) throws Exception {
        for (Customer customer:list){
            System.out.println(customer);
        }
    }
}
```

### 第五节 从多个文件中读取数据

**使用MultiResourceItemReader类**

file1文件如下，file2,file3省略

```txt
1,Stone, Barrett, 1964-10-19 14:11:03
2,Raymond, Pace,1977-12-11 21:44:30
3,Armando, Logan,1986-12-25 11:54:28
4,Latifah, Barnett,1959-07-24 06:00:16
5,Cassandra, Moses,1956-09-14 06:49:28
6,Audra, Hopkins,1984-08-30 04:18:10
7,Upton, Morrow,1973-02-04 05:26:05
8,Melodie, Velasquez,1953-04-26 11:16:26
9,sybill, Nolan,1951-06-24 14:56:51
10,Glenna, Little, 1953-08-27 13:15:08
11,Ingrid, Jackson,1957-09-05 21:36:47.
12,Duncan, Castaneda,1979-01-21 18:31:27
13,Xaviera, Gillespie,1965-07-18 15:05:22
14,Rhoda, Lancaster,1990-09-11 15:52:54
15,Fatima, Combs,1979-06-01 06:58:54
16,Merri1l, Hopkins ,1990-07-02 17:36:35
17,Felicia, Vinson,1959-12-19 20:23:12
18,Hanae , Harvey, 1984-12-27 10:36:49
19,Ramona, Acosta,1962-06-23 20:03:40
20,Katelyn, Hammond ,1988-11-12 19:05:13
```

示例代码：

```java
@Configuration
@EnableBatchProcessing
public class MultiFileItemReaderDemo {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    @Qualifier("multiFileWriter")
    private ItemWriter<? super Customer> multiFileWriter;

    @Value("classpath:/file*.txt")
    private Resource[] fileResources;

    @Bean
    public Job multiFileItemReaderDemoJob() {
        return jobBuilderFactory.get("multiFileItemReaderDemoJob")
                .start(multiFileItemReaderDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step multiFileItemReaderDemoStep() {

        return stepBuilderFactory.get("multiFileItemReaderDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(multiFileReader())
                .writer(multiFileWriter)
                .build();
    }

   /**
     * @Description: multiFileReader 虽说是多文件读取，但其实是逐个读取单个文件
     * @Param: []
     * @Return: org.springframework.batch.item.file.MultiResourceItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    @StepScope
    public MultiResourceItemReader<Customer> multiFileReader() {
        MultiResourceItemReader <Customer> reader = new MultiResourceItemReader<>();
        reader.setDelegate(fileItemReaderDemoReader());
        reader.setResources(fileResources);
        return reader;
    }

    /**
     * @Description: fileItemReaderDemoReader 单个文件读取
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public FlatFileItemReader<Customer> fileItemReaderDemoReader() {
        FlatFileItemReader<Customer> reader = new FlatFileItemReader<Customer>();
        reader.setResource(new ClassPathResource("customer.txt"));
        //跳过第一行
        //reader.setLinesToSkip(1);
        //数据解析
        DelimitedLineTokenizer tokenizer = new DelimitedLineTokenizer();
        tokenizer.setNames(new String[]{"id","fistName","lastName","birthday"});
        //把解析出的一个数据映射为Customer对象
        DefaultLineMapper<Customer> mapper = new DefaultLineMapper<>();
        mapper.setLineTokenizer(tokenizer);
        mapper.setFieldSetMapper(new FieldSetMapper<Customer>() {

            /**
             * @Description: mapFieldSet 映射
             * @Param: [fieldSet]
             * @Return: com.example.springbatchdemo.pojo.Customer
             */
            @Override
            public Customer mapFieldSet(FieldSet fieldSet) throws BindException {
                Customer customer = new Customer();
                customer.setId(fieldSet.readLong("id"));
                customer.setFirstName(fieldSet.readString("fistName"));
                customer.setLastName(fieldSet.readString("lastName"));
                customer.setBirthday(fieldSet.readString("birthday"));
                return customer;
            }
        });
        mapper.afterPropertiesSet();
        reader.setLineMapper(mapper);
        return reader;
    }
}
```

自定义ItemWriter

```java
@Component("multiFileWriter")
public class MultiFileWriter implements ItemWriter<Customer> {

    @Override
    public void write(List<? extends Customer> list) throws Exception {
        for (Customer customer:list){
            System.out.println(customer);
        }
    }
}
```

### 第六节 ItemReader异常处理及重启



## 第四章 数据输出

### 第一节 ItemWriter概述

ItemReader是一个数据一个数据的读， 而ItemWriter是一批一批的输出

```java
@Configuration
@EnableBatchProcessing
public class ItemWriterDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    @Qualifier("myWriter")
    private ItemWriter<? super String> myWriter;

    @Bean
    public Job ItemWriterDemoJob() {
        return jobBuilderFactory.get("ItemWriterDemoJob")
                .start(ItemWriterDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step ItemWriterDemoStep() {
        return stepBuilderFactory.get("ItemWriterDemoStep")
                .<String, String>chunk(2)
                .reader(myReader())
                .writer(myWriter).build();
    }


    @Bean
    public ItemReader<String> myReader() {
        List<String> items = new ArrayList<>();
        for (int i = 1; i <= 50; i++) {
            items.add("java" + i);
        }
        return new ListItemReader<String>(items);
    }
}
```

```java
/**
 * @ClassName MyWriter 
 * @Description MyWriter
 * @Author ZX
 * @Date 2020/6/1
 */
@Component("myWriter")
public class MyWriter implements ItemWriter<String> {
    @Override
    public void write(List<? extends String> list) throws Exception {
        //输出一批的数量，chunk的值
        System.out.println(list.size());
        for (String str:list){
            System.out.println(str);
        }
    }
}
```

### 第二节 数据输出到数据库

实现的各种类：

**Neo4jltemWriter**
**MongoltemWriter**
**RepositoryltemWriter**
**HibernateltemWriter**
**JdbcBatchltemWriter**
**JpaltemWriter**
**GemfireltemWriter**

Mysql这里使用**JdbcBatchltemWriter**

示例代码

```java
@Configuration
@EnableBatchProcessing
public class ItemWriterDbDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;
    @Autowired
    @Qualifier("itemWriterDb")
    private ItemWriter<? super Customer> itemWriterDb;

    @Autowired
    @Qualifier("fileItemReaderDemoReader")
    private ItemReader<? extends Customer> flatFileReader;



    @Bean
    public Job ItemWriterDbDemoJob() {
        return jobBuilderFactory.get("ItemWriterDbDemoJob")
                .start(ItemWriterDbDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    @Bean
    public Step ItemWriterDbDemoStep() {

        return stepBuilderFactory.get("ItemWriterDbDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(flatFileReader)
                .writer(itemWriterDb)
                .build();
    }

}
```

从文件中读取：

```java
@Configuration
public class FlatFileReaderConfig {
    /**
     * @Description: FlatItemReaderDemoReader 文件读取
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public FlatFileItemReader<Customer> fileItemReaderDemoReader() {
        FlatFileItemReader<Customer> reader = new FlatFileItemReader<Customer>();
        reader.setResource(new ClassPathResource("customer.txt"));
        //跳过第一行
        reader.setLinesToSkip(1);
        //数据解析
        DelimitedLineTokenizer tokenizer = new DelimitedLineTokenizer();
        tokenizer.setNames(new String[]{"id","fistName","lastName","birthday"});
        //把解析出的一个数据映射为Customer对象
        DefaultLineMapper<Customer> mapper = new DefaultLineMapper<>();
        mapper.setLineTokenizer(tokenizer);
        mapper.setFieldSetMapper(new FieldSetMapper<Customer>() {

            /**
             * @Description: mapFieldSet 映射
             * @Param: [fieldSet]
             * @Return: com.example.springbatchdemo.pojo.Customer
             */
            @Override
            public Customer mapFieldSet(FieldSet fieldSet) throws BindException {
                Customer customer = new Customer();
                customer.setId(fieldSet.readLong("id"));
                customer.setFirstName(fieldSet.readString("fistName"));
                customer.setLastName(fieldSet.readString("lastName"));
                customer.setBirthday(fieldSet.readString("birthday"));
                return customer;
            }
        });
        mapper.afterPropertiesSet();
        reader.setLineMapper(mapper);
        return reader;
    }
}
```

写入数据库：

```java
@Configuration
public class ItemWriterDbConfig {
    @Autowired
    private DataSource dataSource;

    @Bean
    public JdbcBatchItemWriter<Customer> itemWriterDb(){
        JdbcBatchItemWriter writer = new JdbcBatchItemWriter<Customer>();
        writer.setDataSource(dataSource);
        writer.setSql("insert into customer(id,firstName,lastName,birthday) values" +
                    "(:id,:firstName,:lastName,:birthday)");
        //将Customer中对应属性的值与Sql语句中的四个值进行替换
        writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Customer>());
        return writer;
    }
}
```



### 第三节 数据输出到普通文件

**FlatFileltemWriter**类

示例代码：

```java
@Configuration
@EnableBatchProcessing
public class FileItemWriterDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    @Qualifier("dbJdbcReader")
    private ItemReader<? extends Customer> dbJdbcReader;
    @Autowired
    @Qualifier("fileItemWriter")
    private ItemWriter<? super Customer> fileItemWriter;


  
    /**
     * @Description: fileItemWriterDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job fileItemWriterDemoJob() {
        return jobBuilderFactory.get("fileItemWriterDemoJob")
                .start(fileItemWriterDemoStep())
                .listener(new MyJobListener())
                .build();
    }
    
    /**
     * @Description: fileItemWriterDemoStep
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step fileItemWriterDemoStep() {
        return stepBuilderFactory.get("fileItemWriterDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(dbJdbcReader)
                .writer(fileItemWriter)
                .build();
    }
}
```

写入文件：

```java
@Configuration
public class FIleItemWriter {

    @Bean
    /**
     * @Description: fileItemWriter 向文件输出数据，覆盖原先数据
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    public FlatFileItemWriter<Customer> fileItemWriter(){
        //把Customer对象转成字符串输出到文件
        FlatFileItemWriter<Customer> writer = new FlatFileItemWriter<Customer>();
        String path="F:\\test/customer.txt";
        writer.setResource(new FileSystemResource(path));
        //把Customer对象转成字符串
        writer.setLineAggregator(new LineAggregator<Customer>() {
            ObjectMapper mapper = new ObjectMapper();
            @Override
            public String aggregate(Customer customer) {
               String str = null;
                try {
                    str=mapper.writeValueAsString(customer);
                } catch (JsonProcessingException e) {
                    e.printStackTrace();
                }

                return str;
            }
        });
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }
}
```

从数据库中读取：

```java
@Configuration
public class DbJdbcReaderConfig {
    @Autowired
    private DataSource dataSource;

    /**
     * @Description: dbJdbcReader 从数据库中读取数据
     * @Param: []
     * @Return: org.springframework.batch.item.database.JdbcPagingItemReader<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public JdbcPagingItemReader<Customer> dbJdbcReader() {
        JdbcPagingItemReader<Customer> reader = new JdbcPagingItemReader<Customer>();
        reader.setDataSource(dataSource);
        //设置读取缓存,每次取5个
        reader.setFetchSize(5);
        //把读取到的记录转换成User对象
        reader.setRowMapper(new RowMapper<Customer>() {
            /**
             * @Description: 结果集的映射
             * @Param: [resultSet, rowNum]
             * @Return: com.example.springbootjdbc.pojo.Customer
             */
            @Override
            public Customer mapRow(ResultSet resultSet, int rowNum) throws SQLException {
                Customer customer = new Customer();
                customer.setId(resultSet.getLong(1));
                customer.setFirstName(resultSet.getString(2));
                customer.setLastName(resultSet.getString(3));
                customer.setBirthday(resultSet.getString(4));
                return customer;
            }
        });
        //指定sq1语句
        MySqlPagingQueryProvider provider =new MySqlPagingQueryProvider () ;
        provider.setSelectClause ("id,firstName,lastName,birthday") ;
        provider.setFromClause ("from customer") ;
        //指定根据哪个字段进行排序
        Map<String, Order> sort = new HashMap<>(1) ;
        sort.put("id", Order.ASCENDING);
        provider.setSortKeys(sort);
        reader.setQueryProvider(provider);
        return reader ;
    }
}
```



### 第四节 数据输出到xml文件

**StaxEvenltemWriter**

代码：

```java
@Configuration
@EnableBatchProcessing
public class XmlItemWriterDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    @Qualifier("dbJdbcReader")
    private ItemReader<? extends Customer> dbJdbcReader;
    @Autowired
    @Qualifier("xmlItemWriter")
    private ItemWriter<? super Customer> xmlItemWriter;



    /**
     * @Description: xmlItemWriterDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job xmlItemWriterDemoJob() {
        return jobBuilderFactory.get("xmlItemWriterDemoJob")
                .start(xmlItemWriterDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    /**
     * @Description: xmlItemWriterDemoStep
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step xmlItemWriterDemoStep() {

        return stepBuilderFactory.get("xmlItemWriterDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(dbJdbcReader)
                .writer(xmlItemWriter)
                .build();
    }
    
}
```

写入xml文件：

```java
@Configuration
public class XmlItemWriterConfig {

    @Bean
    public StaxEventItemWriter<Customer> xmlItemWriter(){
        StaxEventItemWriter writer = new StaxEventItemWriter<Customer>();
        XStreamMarshaller marshaller = new XStreamMarshaller();
        //告诉marshaller把数据转成什么类型
        Map<String,Class> map = new HashMap<>();
        map.put("customer",Customer.class);
        marshaller.setAliases(map);

        writer.setRootTagName("customers");
        writer.setMarshaller(marshaller);

        String path = "F:\\test/cus.xml";
        writer.setResource(new FileSystemResource(path));
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }


}
```



### 第五节 数据输出到多个文件

**CompositeltemWriter**
**CassifireCompositeltemWriter** 根据分类写入不同文件

代码：

```java
@Configuration
@EnableBatchProcessing
public class MultiFileItemWriterDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    @Qualifier("dbJdbcReader")
    private ItemReader<? extends Customer> dbJdbcReader;
    @Autowired
    @Qualifier("multiFileItemWriter")
    private ItemWriter<? super Customer> multiFileItemWriter;



    /**
     * @Description: multiFileItemWriterDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job multiFileItemWriterDemoJob() {
        return jobBuilderFactory.get("multiFileItemWriterDemoJob")
                .start(multiFileItemWriterDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    /**
     * @Description: multiFileItemWriterDemoStep
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step multiFileItemWriterDemoStep() {
        return stepBuilderFactory.get("multiFileItemWriterDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(dbJdbcReader)
                .writer(multiFileItemWriter)
                .build();
    }


}
```

只是给多个文件传递数据，不分类：

```java
@Configuration
public class MultiFIleWriterConfig {

    /**
     * @Description: fileWriter 输出数据到txt文件
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public FlatFileItemWriter<Customer> fileWriter(){
        //把Customer对象转成字符串输出到文件
        FlatFileItemWriter<Customer> writer = new FlatFileItemWriter<Customer>();
        String path="F:\\test/customer.txt";
        writer.setResource(new FileSystemResource(path));
        //把Customer对象转成字符串
        writer.setLineAggregator(new LineAggregator<Customer>() {
            ObjectMapper mapper = new ObjectMapper();
            @Override
            public String aggregate(Customer customer) {
                String str = null;
                try {
                    str=mapper.writeValueAsString(customer);
                } catch (JsonProcessingException e) {
                    e.printStackTrace();
                }

                return str;
            }
        });
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }

    /**
     * @Description: xmlWriter 输出数据到xml文件
     * @Param: []
     * @Return: org.springframework.batch.item.xml.StaxEventItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public StaxEventItemWriter<Customer> xmlWriter(){
        StaxEventItemWriter writer = new StaxEventItemWriter<Customer>();
        XStreamMarshaller marshaller = new XStreamMarshaller();
        //告诉marshaller把数据转成什么类型
        Map<String,Class> map = new HashMap<>();
        map.put("customer",Customer.class);
        marshaller.setAliases(map);

        writer.setRootTagName("customers");
        writer.setMarshaller(marshaller);

        String path = "F:\\test/cus.xml";
        writer.setResource(new FileSystemResource(path));
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }


    /**
     * @Description: multiFileItemWriter 调用输出到单个文件操作来实现输出数据到多个文件
     * @Param: []
     * @Return: org.springframework.batch.item.support.CompositeItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public CompositeItemWriter<Customer> multiFileItemWriter(){
        CompositeItemWriter<Customer> writer = new CompositeItemWriter<Customer>();
        //输出到两个不同的文件中
        writer.setDelegates(Arrays.asList(fileWriter(),xmlWriter()));
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;
    }

}
```

示例代码：**CassifireCompositeltemWriter** 根据分类写入不同文件

```java
@Configuration
@EnableBatchProcessing
public class MultiFileItemWriterDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;
    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    @Qualifier("dbJdbcReader")
    private ItemReader<? extends Customer> dbJdbcReader;
    @Autowired
    @Qualifier("multiFileItemWriter")
    private ItemWriter<? super Customer> multiFileItemWriter;


    //分类传数据给文件的ClassifierCompositeItemWriter没有实现ItemStream
    @Autowired
    @Qualifier("jsonFileWriter")
    private ItemStreamWriter<? extends Customer> jsonFileWriter;
    @Autowired
    @Qualifier("xmlWriter")
    private ItemStreamWriter<? extends Customer> xmlWriter;



    /**
     * @Description: multiFileItemWriterDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job multiFileItemWriterDemoJob2() {
        return jobBuilderFactory.get("multiFileItemWriterDemoJob2")
                .start(multiFileItemWriterDemoStep())
                .listener(new MyJobListener())
                .build();
    }

    /**
     * @Description: multiFileItemWriterDemoStep
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step multiFileItemWriterDemoStep() {
        return stepBuilderFactory.get("multiFileItemWriterDemoStep")
                .<Customer,Customer>chunk(5)
                .reader(dbJdbcReader)
                .writer(multiFileItemWriter)
                //ClassifierCompositeItemWriter没有实现ItemStream
                .stream(jsonFileWriter)
                .stream(xmlWriter)
                .build();
    }
}
```

分类输出：

```java
@Configuration
public class MultiFIleWriterConfig {

    /**
     * @Description: fileWriter 输出数据到txt文件
     * @Param: []
     * @Return: org.springframework.batch.item.file.FlatFileItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public FlatFileItemWriter<Customer> jsonFileWriter(){
        //把Customer对象转成字符串输出到文件
        FlatFileItemWriter<Customer> writer = new FlatFileItemWriter<Customer>();
        String path="F:\\test/customer.txt";
        writer.setResource(new FileSystemResource(path));
        //把Customer对象转成字符串
        writer.setLineAggregator(new LineAggregator<Customer>() {
            ObjectMapper mapper = new ObjectMapper();
            @Override
            public String aggregate(Customer customer) {
                String str = null;
                try {
                    str=mapper.writeValueAsString(customer);
                } catch (JsonProcessingException e) {
                    e.printStackTrace();
                }

                return str;
            }
        });
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }

    /**
     * @Description: xmlWriter 输出数据到xml文件
     * @Param: []
     * @Return: org.springframework.batch.item.xml.StaxEventItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public StaxEventItemWriter<Customer> xmlWriter(){
        StaxEventItemWriter writer = new StaxEventItemWriter<Customer>();
        XStreamMarshaller marshaller = new XStreamMarshaller();
        //告诉marshaller把数据转成什么类型
        Map<String,Class> map = new HashMap<>();
        map.put("customer",Customer.class);
        marshaller.setAliases(map);

        writer.setRootTagName("customers");
        writer.setMarshaller(marshaller);

        String path = "F:\\test/cus.xml";
        writer.setResource(new FileSystemResource(path));
        try {
            writer.afterPropertiesSet();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return writer;

    }


    /**
     * @Description: multiFileItemWriter 调用输出到单个文件操作来实现输出数据到多个文件
     * @Param: []
     * @Return: org.springframework.batch.item.support.CompositeItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
//    @Bean
//    public CompositeItemWriter<Customer> multiFileItemWriter(){
//        CompositeItemWriter<Customer> writer = new CompositeItemWriter<Customer>();
//        //输出到两个不同的文件中
//        writer.setDelegates(Arrays.asList(jsonFileWriter(),xmlWriter()));
//        try {
//            writer.afterPropertiesSet();
//        } catch (Exception e) {
//            e.printStackTrace();
//        }
//        return writer;
//    }


    /**
     * @Description: multiFileItemWriter  按照某种条件对数据进行分类存储不同文件
     * 注意：ClassifierCompositeItemWriter没有实现ItemStream
     * @Param: []
     * @Return: org.springframework.batch.item.support.ClassifierCompositeItemWriter<com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public ClassifierCompositeItemWriter<Customer> multiFileItemWriter(){
        ClassifierCompositeItemWriter<Customer> writer = new ClassifierCompositeItemWriter<Customer>();
        writer.setClassifier(new Classifier<Customer, ItemWriter<? super Customer>>() {
            @Override
            /**
             * @Description: classify 分类，比如按照年龄分成两个文件
             * @Param: [customer]
             * @Return: org.springframework.batch.item.ItemWriter<? super com.example.springbatchdemo.pojo.Customer>
             */
            public ItemWriter<? super Customer> classify(Customer customer) {
                //按照Customer的id进行分类
                ItemWriter<Customer> writer1 = customer.getId()%2==0?jsonFileWriter():xmlWriter();
                return writer1;
            }
        });
        return writer;

    }


}
```



### 第六节 ItemProcessor的使用

**ItemProcessor<I,O>**用于处理业务逻辑，验证，过滤等功能
**CompositeltemProcessor**类，处理多种**ItemProcessor**处理方式
案例:从数据库中读取数据，然对数据进行处理，最后输出到普通文件

代码：

```java
@Configuration
@EnableBatchProcessing
public class ItemProcessorDemo {
    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Autowired
    @Qualifier("dbJdbcReader")
    private ItemReader<? extends Customer> dbJdbcReader;
    @Autowired
    @Qualifier("fileItemWriter")
    private ItemWriter<? super Customer> fileItemWriter;

    @Autowired
    private ItemProcessor<Customer,Customer> firstNameUpperProcessor;
    @Autowired
    private ItemProcessor<Customer, Customer> idFilterProcessor;


    /**
     * @Description: itemProcessorDemoJob
     * @Param: []
     * @Return: org.springframework.batch.core.Job
     */
    @Bean
    public Job itemProcessorDemoJob() {
        return jobBuilderFactory.get("itemProcessorDemoJob")
                .start(itemProcessorDemoStep())
                .listener(new MyJobListener())
                .build();
    }


    /**
     * @Description: itemProcessorDemoStep
     * @Param: []
     * @Return: org.springframework.batch.core.Step
     */
    @Bean
    public Step itemProcessorDemoStep() {
        return stepBuilderFactory.get("itemProcessorDemoStep")
                .<Customer, Customer>chunk(5)
                .reader(dbJdbcReader)
                //.processor(firstNameUpperProcessor)  只一种处理方式
                .processor(process())
                .writer(fileItemWriter)
                .build();
    }


    /**
     * @Description: process 同时用多种方式处理方式
     * @Param: []
     * @Return: org.springframework.batch.item.support.CompositeItemProcessor<com.example.springbatchdemo.pojo.Customer,com.example.springbatchdemo.pojo.Customer>
     */
    @Bean
    public CompositeItemProcessor<Customer,Customer> process(){
        CompositeItemProcessor<Customer,Customer> processor = new CompositeItemProcessor<Customer,Customer>();
        List<ItemProcessor<Customer,Customer>> delegates = new ArrayList<>();
        delegates.add(firstNameUpperProcessor);
        delegates.add(idFilterProcessor);

        processor.setDelegates(delegates);
        return processor;
    }

}
```

ItemProcessor使用

```java
@Component
public class FirstNameUpperProcessor implements ItemProcessor<Customer, Customer> {
    @Override
    public Customer process(Customer customer) throws Exception {
        Customer customer1 = new Customer();
        customer1.setId(customer.getId());
        customer1.setLastName(customer.getLastName());
        customer1.setFirstName(customer.getFirstName().toUpperCase());
        customer1.setBirthday(customer.getBirthday());
        return customer1;
    }
}
```

```java
@Component
public class IdFilterProcessor implements ItemProcessor<Customer, Customer> {
    @Override
    public Customer process(Customer customer) throws Exception {
        if(customer.getId()%2==0)
            return customer;
        else
            return null;
    }
}
```

