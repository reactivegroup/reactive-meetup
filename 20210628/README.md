## Meetup 信息

时间： 2021年6月28日，下午8点-9点

地点：上海市凌空SOHO

### dubbo收尾

1.对dubbo整体结构有大体了解，对初始化流程和调用流程有清晰的思路

2.对了解过的dubbo的细节实现，再次梳理一下，最好能整理出来

3.对dubbo的设计和优缺点有了解和思考

### netty思路

下周一交流netty的学习思路

接下来，会先从整体入手，分工学习netty的使用和整体架构。

等对整体结构和流程有大体了解之后，分工学习细节实现。

------

# xdubbo设计

## dubbo设计

![dubbo-framework](./slides/dubbo-framework.jpg)

## xdubbo设计

### 嵌入dubbo的思路

通过实现dubbo中不同层次的接口，将xdubbo的实现类嵌入到dubbo流程中。

同时xdubbo的实现类持有自己特有的资源（初始化流程中的资源），在调用流程中使用自己持有的资源做特定逻辑

### 层次设计思路

#### 动静结合

> （与Tomcat的mapper层设计思路相似）

xdubbo通过实现类将自身伪装成invoker等，注入到dubbo的调用流程中，但这些实现类并不直接持有资源。

资源的持有交给同层级的`Manager`，通过静态域管理该层次的资源。

在实现类执行动作的时候，将会从`Manager`静态资源管理器中，获取资源。

#### 资源插件化

`Manager`静态资源管理器，使用插件化的方式，在初始化的时候可以装配不同的插件，从而加载不同的资源