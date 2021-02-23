# Java异常

## 异常产生的原理
* JVM针对程序运行监测出的异常创建个异常对象，对象包含了异常产生的：内容、原因、位置。根据程序逻辑将异常对象层层抛出或者处理。

## Throwable体系
### Error
* 错误Error一般是指java虚拟机相关的问题，如系统崩溃、虚拟机出错误、动态链接失败等，这种错误无法恢复或不可能捕获，将导致应用程序中断，通常应用程序无法处理这些错误，因此应用程序不应该捕获Error对象，也无须在其throws子句中声明该方法抛出任何Error或其子类。
* VirtualMachineError
	* OutOfMemoryError
	* StackOverflowError
* LinkageError
	* NoClassDefFoundError
	* NoSuchMethodError
* 。。。
	
### Exception
* 运行时异常 RuntimeException
	* 是指“可以避免的异常”，如 null引用异常，这类异常都是由程序内部原因造成的，是可以避免的，考验开发的严谨性，因此这个问题是我们作为一名开发必须要避免发生的，再大的公司每年总会有几次事故是因为NPE导致的，我们如果因为这种问题被通报了就是很尴尬的事情了。（对生产环境一定要有足够的敬畏心啊）
	* NullPointerException
	* ClassCastExecption
	* ArithmeticException
	* IndexOutOfBoundsException
	* ClassNotFoundException
	* 自定义的RuntimeException
	* 。。。
* 非运行时异常
	* 代表“无法避免的异常” 如io异常，往往这类异常是由于外部原因造成的，程序本身无法保证他们不发生，所以这类异常必须捕获，这就是我们在使用io资源或者操作线程比如sleep的时候必须要捕获或者抛出的原因
	* IOException
	* InterruptedException
	* SQLException
	* 自定义的非RuntimeException
	* 。。。
* ![1](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT1oVaf14e1iahwyqWPD25jROUnUPsia0t0ubB4Ytib0m8Em314HPabbuKTwxh2mcLTUfLFsnxSoa7leg/640?wx_fmt=png)

### Check And UnCheck
* CheckedException
	* 等价于非运行时异常
	要么捕获，要么抛出，否则编译无法通过
	代表“无法避免的异常” 如io异常   往往这类异常是由于外部原因造成的，程序本身无法保证他们不发生
* UnCheckedException
	* 包括：Error&运行时异常

## 方法
* toString
	* 异常类信息+异常信息描述
* getMessage
	* 异常信息描述
* printStackTrace
	* 最常用打印完整的错误堆栈信息

## catch还是throw
* 通常应该捕获那些知道如何处理的异常，而将那些不知道如何处理的异常进行传递。如果想传递一个异常，就必须在方法的声明中添加一个throws说明符，以便告知调用者这个方法可能会抛出异常。

## finally
* 无论是否出现异常都会执行
* 必须与try一起使用
* 注意
	* finally中有return语句永远返回finally中的return，try块中的return不会生效
	* ![2](https://mmbiz.qpic.cn/mmbiz_png/K07fFKibvoT1oVaf14e1iahwyqWPD25jROW0pqvuVfKCFPFUb9gmhkTPT759JDJU7TQH36uMh5Rj1cBCxhjjX48g/640?wx_fmt=png)

## 其他
* 如果父类抛出了多个异常，子类重写父类方法的时候，抛出和父类相同的异常//是父类异常的子类//或者不抛出
* 父类方法没有抛出异常，子类重写父类该方法的时候也不可以抛出异常。此时子类产生该异常，只能捕获处理，不能声明抛出
* 使用习惯
	* 不要丢弃异常，捕获异常后需要进行相关处理。如果用户觉得不能很好地处理该异常，就让它继续传播，传到别的地方去处理，或者把一个低级的异常转换成应用级的异常，重新抛出
	* catch语句应该指定具体的异常类型。不能把不该捕获的异常也捕获了
	* 在finally里面释放资源。如果finally里面也会抛出异常，也一样需要使用try..catch处理
	* 不要把大量的代码塞在try...catch块里面，分离各个可能出现异常的语句并分别捕获异常
