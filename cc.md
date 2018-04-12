### @Inject 注解

DI中第一个并且是最重要的就是@Inject注解。JSR-330标准中的一部分，标记那些应该被依赖注入框架提供的依赖。在Dagger 2中有3种不同的方式来提供依赖：
###  @Qualifier 注解
Qualifier注解帮助我们去为相同接口的依赖创建“tags”。