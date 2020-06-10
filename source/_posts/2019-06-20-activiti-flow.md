---
title: Activiti工作流简介
comments: true
date: 2019-06-20 16:11:02
tags:
- java
- avtiviti
- 工作流
author: jun.zhou
categories:
- JAVA开发
---

![](https://img.fengjr.com/image/2019/06/20/ae71a151f84d8afe11ed095e4fc88ea2.png)
# Activiti工作流简介

本文根据永则分享的 Activiti工作流简介的ppt输入的博客，方便大家查看和学习。

## 1、工作流的定义与基本概念

### 基本概念

**工作流**是指一类能够完全自动执行的经营过程，根据一系列过程规则，将文档、信息或任务在不同的执行者之间进行传递与执行。通俗的说工作流就是通过计算机控制整体业务流向，将多人的业务串联，实现整体业务的一种方式。

**工作流管理系统**(Workflow Management System, WfMS)是一个软件系统，它完成工作流的定义和管理，并按照在系统中预先定义好的工作流规则进行工作流实例的执行。工作流管理系统不是企业的业务系统，而是为企业的业务系统的运行提供了一个软件的支撑环境。

**工作流管理联盟**(WfMC，Workflow Management Coalition)给出的关于工作流管理系统的定义是：工作流管理系统是一个软件系统，它通过执行经过计算的流程定义去支持一批专门设定的业务流程。工作流管理系统被用来定义、管理、和执行工作流程。

## 主要功能

1. 定义工作流：包括具体的活动、规则等。
2. 执行工作流：按照流程定义的规则执行，并由多个参与者进行控制


## 主要优点

1. 提高系统的柔性，适应业务流程的变化
2. 实现更好的业务过程控制，提高顾客服务质量
3. 降低系统开发和维护成本

## 常用框架
- Activiti
- JBPM
- Shark
- OSWorkflow
- ActiveBPEL
- YAWL


## 2、Activiti数据库表说明

### Activiti5介绍

**Activiti5**是一个针对企业用户、开发人员、系统管理员的轻量级工作流业务管理平台，其核心是使用Java开发的快速、稳定的BPMN2.0流程引擎。Activiti是在ApacheV2许可下发布的，可以运行在任何类型的Java程序中，例如服务器、集群、云服务等。Activiti可以完美地与Spring集成。同时，基于简约思想的设计使得Activiti成为一个非常轻量级框架。它特色是提供了eclipse插件，开发人员可以通过插件直接绘画出业务流程图。

### 数据表说明

Activiti的后台是有数据库的支持，所有的表都以ACT_开头。 第二部分是表示表的用途的两个字母标识。 用途也和服务的API对应。

命名规则：

- ACT_RE_*: 'RE'表示repository。 这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）。
- ACT_RU_*: 'RU'表示runtime。 这些运行时的表，包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据，在流程结束时就会删除这些记录。这样运行时表可以一直很小速度很快。
- ACT_ID_*: 'ID'表示identity。 这些表包含身份信息，比如用户，组等等。
- ACT_HI_*: 'HI'表示history。 这些表包含历史数据，比如历史流程实例， 变量，任务等等。
- ACT_GE_*: 通用数据， 用于不同场景下，如存放资源文件。

下面是数据库具体的表名和作用：

|表分类 | 表名 | 注释|
|---|---|---|
|一般数据 | ACT_GE_BYTEARRAY | 通用的流程定义和流程资源|
| | ACT_GE_PROPERTY | 系统相关属性|
|流程历史记录 | ACT_HI_ACTINST | 历史的流程活动|
| | ACT_HI_COMMENT |历史的说明性信息|
| | ACT_HI_DETAIL |历史的流程运行中的细节信息|
| | ACT_HI_IDENTITYLINK |历史的流程运行过程中用户关系|
| | ACT_HI_PROCINST |历史的流程实例|
| | ACT_HI_TASKINST |历史的任务实例|
| | ACT_HI_VARINST |历史的流程运行中的变量信息|
| 用户用户组表 | ACT_ID_GROUP | 身份信息-组别信息 |
|| ACT_ID_INFO |	身份信息-组信息|
|| ACT_ID_MEMBERSHIP |身份信息-用户和组关系的中间表|
|| ACT_ID_USER |身份信息-用户信息|
|流程定义表|ACT_RE_DEPLOYMENT|部署单元信息|
||ACT_RE_MODEL|模型信息|
||ACT_RE_PROCDEF|已部署的流程定义|
|运行实例表| ACT_RU_EVENT_SUBSCR|运行时事件|
|| ACT_RU_EXECUTION	|运行时流程执行实例|
|| ACT_RU_IDENTITYLINK |运行时用户关系信息|
|| ACT_RU_JOB |运行时作业|
|| ACT_RU_TASK |运行时任务|
|| ACT_RU_VARIABLE |运行时变量表|

## 3、Activiti工作原理

### 框架初始化

![](https://img.fengjr.com/image/2019/06/20/e021871ae3d411953f434bf23821247b.png)

**ProcessEngine**

>  ProcessEngine是Activiti框架的门面，ProcessEngine本身不提供任何功能，通过getXXXService方法可以获取到对应的Service对象执行操作

**ProcessEngineConfiguration**

> ProcessEngineConfiguration负责Activiti框架的属性配置、初始化工作，初始化入口是buildProcessEngine方法，所有Activiti框架运行时需要用到的组件基本都在这里初始化

![](https://img.fengjr.com/image/2019/06/20/4385a62264bcc07e02cc1b4bfcff9e12.png)

### 流程部署

#### 部署方式

部署流程资源有很多种方法，包括classpath、InputStream、字符串、zip格式压缩包

**1、classpath**

![](https://img.fengjr.com/image/2019/06/20/34c39ee6bf51aad33f742c90c29fdf9d.png)

**2、InputStream**

![](https://img.fengjr.com/image/2019/06/20/7f1b3eeceafe7e190dff013787eb9fe1.png)

**2、字符串**

![](https://img.fengjr.com/image/2019/06/20/41c83c52086017cb8c1d70740caf40c0.png)

**3、zip/bar格式压缩包**

![](https://img.fengjr.com/image/2019/06/20/06ccf1d63bcba0f82e430595f6d9211d.png)

createDeployment方法中创建了DeploymentBuilder对象，DeploymentBuilder对象负责读取指定路径的流程图xml文件的内容（byte数组），并缓存在DeploymentEntity对象中，最终DeploymentBuilder的deploy方法会调用RepositoryService的deploy方法，完成流程部署。

### 流程解析

在DeployCmd中，首先调用DeploymentEntityManager持久化存储DeploymentEntity对象，然后调用DeploymentManager部署流程（流程解析）

##### 1、DeploymentEntityManager

> DeploymentEntityManager的deploy方法中循环调用Deployer对象的deploy方法，Activiti默认的Deployer是BpmnDeployer。
> 
> 另外DeploymentEntityManager中还缓存了解析好的流程定义对象和Bpmn模型对象。

![](https://img.fengjr.com/image/2019/06/20/b46a583368f6fcc1615863ba81775cbc.png)

##### 2、BpmnDeployer

1. 通过BpmnParser对象创建BpmnParse。
2. 调用BpmnParse的execute方法，将inputStream中的流程图转化ProcessDefinitionEntity。
3. 持久化ProcessDefinitionEntity对象

![](https://img.fengjr.com/image/2019/06/20/cd5cc9467fe13282859ff6d2365e0dd1.png)

##### 3、BpmnParse

在BpmnParse的execute中完成了xml文件到ProcessDefinitionEntity对象

![](https://img.fengjr.com/image/2019/06/20/c4f5f0468d957b173844bb7fb6cacf94.png)

### 运行流程

#### 创建流程实例

可以通过RuntimeService发起流程实例，在startProcessInstanceByKey方法中执行StartProcessInstanceCmd命令

![](https://img.fengjr.com/image/2019/06/20/864e0ebbf9226f542340beb29edbc121.png)

#### 驱动流程

StartProcessInstanceCmd调用ExecutionEntity的start方法开始驱动流程

![](https://img.fengjr.com/image/2019/06/20/a225c227a481ad4f4c551dde24c3e846.png)

Activiti框架的流程运行于PVM模型之上，在流程运行时主要涉及到PVM中几个对象：ActivityImpl、TransitionImpl和ActivityBehavior

1. ActivityImpl：ActivityImpl是流程节点的抽象，ActivityImpl维护流程图中节点的连线，包括有哪些进线，有哪些出线。另外还包含节点同步／异步执行等信息。
2. TransitionImpl：TransitionImpl包含source和target两个属性，连接了两个流程节点。
3. ActivityBehavior：每一个ActivityImpl对象都拥有一个ActivityBehavior对象，ActivityBehavior代表节点的行为。

ActivityImpl、TransitionImpl和ActivityBehavior只是描述了流程的节点、迁移线和节点行为，真正要让ExecutionEntity流转起来，还需要AtomicOperation的驱动。

![](https://img.fengjr.com/image/2019/06/20/c77300c6f1f7c467c05d47de2434cc74.png)

在ExecutionEntity的start方法中，调用了PROCESS_START，PROCESS_START做了几件事：

- 获取流程定义级别定义的监听start事件的ExecutionListener，调用notify方法。
- 如果开启了事件功能，发布ActivitiEntityWithVariablesEvent和ActivitiProcessStartedEvent。3、调用PROCESS_START_INITIAL。

PROCESS_START_INITIAL也实现了类似的功能

- 获取初始节点上定义的监听start事件的ExecutionListener，调用notify方法。
- 调用ACTIVITY_EXECUTE。

在流程执行中涉及的AtomicOperation的链路主要包括：

1. ACTIVITY_EXECUTE：调用当前activity的behavior。
2. TRANSITION_NOTIFY_LISTENER_END：某个activity节点执行完毕，调用节点上声明的监听end事件的ExecutionListener。
3. TRANSITION_NOTIFY_LISTENER_TAKE：触发线上的ExecutionListener。
4. TRANSITION_NOTIFY_LISTENER_START：某个activity节点即将开始执行，调用节点上的监听start事件的ExecutionListener。

## 4、Activiti组件介绍与参数传递

### 开始事件（startEvent）

开始事件用来指明流程在哪里开始。开始事件的类型（流程在接收事件时启动， 还是在指定时间启动，等等），定义了流程如何启动， 这通过事件中不同的小图表来展示。 在XML中，这些类型是通过声明不同的子元素来区分的。

XML结构：

```
<startEvent id="start" name="my start event" />
```

### 结束事件（endEvent）

End Event 结束事件表示（子）流程（分支）的结束。 结束事件都是触发事件。 这是说当流程达到结束事件，会触发一个结果。 结果的类型是通过事件的内部黑色图标表示的。 在XML内容中，是通过包含的子元素声明的。

XML结构：

```
<endEvent id="end" name="my end event" />
```

### 顺序流（Sequence Flow）

顺序流是连接两个流程节点的连线。 流程执行完一个节点后，会沿着节点的所有外出顺序流继续执行。 就是说，BPMN 2.0默认的行为就是并发的： 两个外出顺序流会创造两个单独的，并发流程分支。

XML结构：

```
<sequenceFlow id="flow1" sourceRef="theStart" targetRef="theTask" /> 
```

### 用户任务（UserTask）

用户任务用来设置必须由人员完成的工作。  当流程执行到用户任务，会创建一个新任务，并把这个新任务加入到分配人或群组的任务列表中。             XML结构：

```
<userTask id="theTask" name="Important task" />
```

#### 任务分配
对于用户任务的分配有4种方法，一是个人任务分配，二是组任务分配，三是角色任务分配，四是JAVA自定义扩展的分配逻辑。

##### 1、Assignee属性

这个自定义扩展可以直接把用户任务分配给指定用户。我们可以在流程设计时直接指定办理人（此时的办理人可以写成定值如张三；也可以写成变量表达式如${userId}，在流程实例启动时再进行办理人的赋值也是可以的；也可以不写，通过JAVA自定义扩展来指定办理人。），也可以手动分配任务从一个人给另外一个人。

XML结构：

```
<userTask id=“theTask” name=“my task” activiti:assignee=“张三” /> 
```
JAVA 代码：

```
Map<String, Object> variables = new HashMap<String,Object>();
variables.put(“userID”, “张三");
ProcessInstance pi = processEngine.getRuntimeService()
		.startProcessInstanceByKey("taskProcess",variables);
processEngine.getTaskService().setAssignee(taskId, userId);
```

##### 2、CandidateUsers 属性

这个自定义扩展可以使用户成为任务的候选者。即将一个任务分配给一组用户，让这组用户成为这个任务的候选者，直到有一个用户认领了该任务成为该任务的参与者为止。

既然是一个组任务，那么在有用户参与之前，可以对该任务的候选者进行添加和删除工作。也可以在用户认领任务后，将任务回退到组任务，前提是该用户认领的这个任务之前必须是组任务。

XML结构：

```
<userTask id=“theTask” name=“my task” activiti:candidateUsers=“张三, 李四” />
```

JAVA 代码：

```
Map<String, Object> variables = new HashMap<String, Object>();
variables.put(“userIDs”, “张三,李四,王五");
ProcessInstance pi = processEngine.getRuntimeService()
	.startProcessInstanceByKey(processDefinitionKey,variables);
processEngine.getTaskService().claim(taskId, userId);
```
##### 3、CandidateGroups属性

这个自定义扩展允许为任务定义一组候选者。即将一个任务分配给一组角色，让每个角色组的用户成为这个任务的候选者，直到有一个用户认领了该任务成为该任务的参与者为止。

既然是一个组任务，那么在有用户参与之前，可以对该任务的候选者进行添加和删除工作。也可以在用户认领任务后，将任务回退到组任务，前提是该用户认领的这个任务之前必须是组任务。

XML结构：

```
<userTask id=“theTask” name=“my task” activiti:candidateGroups=“management, accountancy” />
```

JAVA 代码：

```
IdentityService identityService =processEngine.getIdentityService();
identityService.saveGroup(new GroupEntity("部门经理"));
identityService.saveUser(new UserEntity("张三"));
identityService.saveUser(new UserEntity("李四"))
identityService.createMembership("张三", "部门经理");
identityService.createMembership("李四", "部门经理")
processEngine.getTaskService().claim(taskId, userId);
```

##### 4、定义扩展JAVA类

如果以上方案仍然不够，那么可以实现了任务监听器TaskListener的JAVA类来自定义分配逻辑。

XML结构：

```
<userTask id="task1" name="My task" >
    <extensionElements>
	    <activiti:taskListener event="create"class="org.activiti.MyAssignmentHandler" />
    </extensionElements>
</userTask>
```

JAVA代码：

```
public class MyAssignmentHandler implements TaskListener {
    public void notify(DelegateTask delegateTask) {
	    delegateTask.setAssignee("kermit");
	    delegateTask.addCandidateUser("fozzie");
	    delegateTask.addCandidateGroup("management");
	    ...
	}
}
```

#### 任务完成

Activiti中当前任务节点完成任务时，只需要调用TaskService服务中的complete方法即可

### 服务任务（Service Task）

Java 服务任务用来调用外部 Java 类。

XML结构：

```
<serviceTask id=" serviceTask1“  name="My Java Service Task"activiti:class=“com.demo.MyJavaDelegate1" />
```

### 排他网关（Exclusive Gate Way）

用来对流程中的决定进行建模。流程执行到这种gateway 时，按照输出流定义的顺序对它们进行计算。条件为 true 的顺序流（或没有设置条件，概念上顺序流上定义为’true’）被选取继续执行流程。

##### 模型实例

![](https://img.fengjr.com/image/2019/06/20/2457b668bcb956f50a729c57385f3a5a.png)

### 并行网关（Parallel Gate Way）

并行网关的功能要根据输入和输出的顺序流，它能拆分出多个执行路径，或多个输入执行路径进行合并。

**拆分（fork）** ：并行执行所有的输出顺序流，为每一个顺序流创建一个并行执行路径。

**合并（join）** ：所有到达 parallel gataway 的并发性的执行路径都等待于此，直到每个输入流都执行到。然后，流程经由 joining gateway 继续向下执行。
XML结构：

```
<parallelGateway id="myParallelGateway" />
```

##### 模型实例

![](https://img.fengjr.com/image/2019/06/20/ad2e4433f234fcd31d2480f65b5dd427.png)

## 5、Activiti集成Spring

Activiti的所有Service服务都是通过ProcessEngine来获取的。通过查看源文件，可以发现最终是通过 ProcessEngineConfiguration来创建流程引擎对象的。而Activiti做的更好的地方是：ProcessEngineConfiguration这个抽象类有多种实现方式，这意味着我们可以通过一种实现方式把ProcessEngine作为一个普通的Spring  bean进行配置。同样，在流程引擎配置进来后，它所包含的Service服务也可以当作一个Spring Bean进行配置进来。

XML代码:

```
<?xml version="1.0" encoding="UTF-8"?>  
<beans xmlns="http://www.springframework.org/schema/beans"   
       xmlns:context="http://www.springframework.org/schema/context"  
       xmlns:tx="http://www.springframework.org/schema/tx"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
       xsi:schemaLocation=“
	http://www.springframework.org/schema/beans 
	http://www.springframework.org/schema/beans/spring-beans.xsd  
                 http://www.springframework.org/schema/context
	 http://www.springframework.org/schema/context/spring-context-2.5.xsd  
                 http://www.springframework.org/schema/tx
	http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">  
</bean>
<!-- spring负责创建流程引擎的配置文件 -->
<bean id="processEngineConfiguration" class="org.activiti.spring.SpringProcessEngineConfiguration"> 
    <!-- 数据源 --> 
    <property name="dataSource" ref="dataSource" />  
    <!-- 配置事务管理器，统一事务 -->
    <property name="transactionManager" ref="transactionManager" />  
    <!-- 设置建表策略，如果没有表，自动创建表 -->
    <property name="databaseSchemaUpdate" value="true" />  
    <property name="jobExecutorActivate" value="false" />  
   <!-- 设置记录历史级别 none:activity:audit:full -->
    <property name="history" value="full" />  
    <property name="processDefinitionCacheLimit" value="10" />  
</bean>

```


![](https://img.fengjr.com/image/2019/06/20/864e0ebbf9226f542340beb29edbc121.png)