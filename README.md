# iOS 组件化/模块化，BeeHive/CTMediator/MGJRouter 


## 简介
### 一、BeeHive
[BeeHive源码](https://github.com/alibaba/BeeHive)
#### 通过 protocol 定义服务接口，组件通过实现该接口来提供接口定义的服务，具体实现就是把 protocol 和 class 做一个映射，同时在内存中保存一张映射表，使用的时就通过 protocol 找到对应的 class 来获取需要的服务
#### 优点：
* 1、可传递非常规参数的问题（相比路由）
* 2、组件间的调用更为方便
* 3、解耦代码量少，实现方便，以后维护也方便
* 4、协议方法改变后，编译就会报错，避免代码修改遗漏
* 5、协议方法未实现的话，会报编译警告
* 6、方法查找容易，调用高效
* 7、没有硬编码
#### 缺点：
* 1、组件的方法调用分散
* 2、内存中维护映射表
* 3、协议方法有可能未实现
* 4、对组件协议需要注册，不注册就无法调用



### 二、CTMediator
[CTMediator源码](https://github.com/casatwy/CTMediator)
#### Target-Action这个方案是基于ObjC 的Runtime、Category 特性动态获取模块，例如通过NSClassFromString 获取类并创建实例，通过 performSelector + NSInvocation动态调用方法。
    首先每个模块需要配置Target和Category，其中Target是每个组件对应一个或者多个Target，Category是中间层Mediator的分类，使用分类的目的是为了让Mediator的业务代码分离，从而降低Mediator中的依赖和耦合性。
    利用了runtime的反射机制，在Category中找到对应Target以及调用Target对应的Action。
#### 优点：
* 1、解耦，只存在组件依赖中间层（单向依赖）
* 2、利用 Category 可以明确声明的接口，进行编译检查
* 3、统一处理了所有组件间调用入口，调用不对编译就会报错，避免代码修改遗漏，方便管理
* 4、无需注册
#### 缺点：
* 1、每个组件的Category对应一个Target，Category中的Action对应Target中的Action。（此类的代码量很多）
* 2、有硬编码的问题，各种定义的string



### 三、MGJRouter
[MGJRouter源码](https://github.com/lyujunwei/MGJRouter)
#### 在启动时，注册组件提供的服务（注册URL以及关联服务Block），然后在使用时，通过URL直接调用（openURL）
#### 优点：
* 1、极高的动态性，适合经常开展运营活动的App
* 2、方便地统一管理多平台的路由规则
* 3、易于适配URL Scheme，可以下发
#### 缺点：
* 1、传参方式有限，并且无法利用编译器进行参数类型检查，因此所有的参数都是通过字符串转换而来
* 2、参数的格式不明确，是个灵活的 dictionary，也需要有个地方可以查参数格式
* 3、依赖于字符串硬编码，难以管理，需有专门的文档查阅
* 4、无法保证所使用的的模块一定存在
* 5、解耦能力有限，url 的”注册”、”实现”、”使用”必须用相同的字符规则，一旦任何一方做出修改都会导致其他方的代码失效，并且重构难度大
