1. IOC
控制反转，IOC是spring管理bean的容器，他可以实现bean的管理，注册、依赖注入、实例化等功能

2. bean的生命周期
a) 首先spring会找到加有@ComponentScan的类或者是spring配置文件里面的信息
b) 获取上面的bean的一些定义，并将其注册到IOC容器里面，在具体进行使用的时候还会进行实例化
c) 首先需要调用BeanNameAware接口的setBeanName
d) 然后需要调用BeanFactoryAware接口的setBeanFactory方法
e) 之后就是调用ApplicationContextAware接口的setApplicationContext方法
g) BeanpostProcessor的postProcessorBeforeInitializition
h) 自定义的初始化方法（@PostConstruct）
i) InitialingBean接口里面的afterPropertiesSet
j) BeanPostProcessor接口里面的postProcessorAfterInitializition
//到这里就实现了初始化工作的完成
k) 自定义销毁方法（@PreDestory）
l) DisposableBean接口的destory方法

3. 如果IOC里面的bean出现了相互依赖怎么解决
第一种是setter对象的依赖问题：
初始化A类时把A类的初始化Bean放到缓存中，然后set B类，再把B类的初始化Bean放到缓存中，
然后set  C类，初始化C类需要A类和B类的Bean，这时不需要初始化，只需要从缓存中取出即可.
该种仅对single作用的Bean起作用，因为prototype作用的Bean，Spring不对其做缓存
第二种，解决构造器中对其它类的依赖：
创建A类需要构造器中初始化B类，创建B类需要构造器中初始化C类，创建C类需要构造器中又要初始
化A类，因而形成一个死循环，Spring的解决方案是，把创建中的Bean放入到一个“当前创建Bean池”
中，在初始化类的过程中，如果发现Bean类已存在，就抛出一个“BeanCurrentInCreationException”
的异常
