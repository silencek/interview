# 基础面试题三  
####简述OC中内存管理机制
* 内存管理机制:使用引用计数管理,分为ARC和MRC
* MRC需要程序员自己管理内存,ARC则不需要.但是并不是所有对象在ARC环境下均不需要管理内存,子线程和循环引用并不是这样
* 与retain配对使用的是release,retain代表引用计数+1,release代表引用计数-1,当引用计数减为0时,对象则被系统自动销毁.与alloc配对使用的是dealloc,alloc代表为对象开辟内存空间,dealloc则代表销毁对象的内存空间

####readwrite,readonly,assign,retain,copy,nonatomic,atomic,strong,weak的作用
* 读写属性:readonly和readwrite;  语义属性:assign/retain/copy;   原子性:nonatomic/atomic
* readwrite代表可读,可写,即有setter和getter方法,是默认属性.readonly代表只可读,即只有get方法,因为不会生成setter方法,所以它不可以和copy/retain/assign组合使用
* weak和assign均是弱引用,assign修饰基本数据类型,weak修饰对象类型.strong和weak用于ARC下(ARC下的代理使用weak,block块使用copy).strong相当于retain.weak相当于assign;assign/retain/copy这些属性用于指定set访问器的语义,也就是说,这些属性决定了以何种方式对数据成员赋值.
* assign,直接赋值,引用计数不改变,适用于基本数据类型
* retain,浅拷贝,使用的是原来的内存空间,只能适用于Objective-C对象类型,而不能适用于Core Foundation对象(retain会增加对象的引用计数,而基本数据和Core Foundation对象都没有引用计数).
* copy:对象的拷贝,新申请一块内存空间,并把原始内容复制到那片空间.新对象的引用计数为1,此属性只对那些遵循了NSCopy协议的对象类型有效.
* nonatomic,非原子性访问,不加同步,是异步操作.默认为atomic,原子操作,atomic是Objc使用的一种线程保护技术,基本上来讲,是防止在写未完成的时候被另外一个线程读取,造成数据错误,而这种机制是消耗系统内存资源的,所以在移动端,都选择nonatomic.

####请简述内存的5个区
* 栈区：由编译器自动分配释放，不需要管理内存
* 堆区：一般由程序员分配释放
* 全局区：存放全局变量和静态变量
* 文字常量区：存放常量字符创
* 程序代码区：存放二进制代码

####类变量的@protected,@private,@public,@package的含义
* @protected 受保护的，本类以及子类可见
* @private 私有的，类内可见
* @public 公有的，类内，子类外部均可访问
* @package 可见度@protected和@public之间，这个类型最常用于矿建的实例变量

####浅谈对线程和进程的理解
* 进程：正在运行的程序，负责程序的内存分配
* 线程：线程是进程中一个独立执行的控制单元，一个进行至少包含一个线程，即主线程
* 创建线程的目的：开辟一个新的执行路径，运行指定的代码，与主线程的代码实现同时执行

####iOS中多线程的开发
* 多线程的使用场景：防止卡顿，可以同时完成多个任务，并且不影响主线程，把耗时操作放在子线程中执行但是会消耗内存
* iOS实现多线程的方式：（1）NSThread，内存需要自己管理（2）GCD （3）NSOperationQueue，不必关注线程

####详解iOS多线程的实现方法
* GCD:GCD里面包含串行队列、并行队列、助对象、全局队列  
> Dispatch_queue_t q = dispatch_queue_create(“qqq”,DISPATCH_QUEUE_SERIAL);//创建一个串行队列  
> Dispatch_sync(q,^{  
      
});//开启同步任务  
Dispatch_async(q,^{  
      
})；//开启异步任务  
> 并行队列：DISPATCH_QUEUE_CONCURRENT  
> 主队列：dispatch_queue_t q = dispatch_get_main_queue();  
> 全局队列：dispatch_queue_t q = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);  
* NSThread
> 获取当前线程：NSThread * current = [NSThread currentThread];
> 获取主线程：NSThread * main = [NSThread mainThread];
> 使用NSThread创建线程的两种方式：  
> - (id)initWithTarget:(id)target selector:(SEL)selector object:(id)argument;  
> + (void)detachNewThreadSelector:(SEL)selector toTarget:(id)target withObject:(id)argument;  
> 暂停当前线程：[NSThread sleepForTimeInterval:2];
* NSOperation
> 创建一个操作队列：NSOperationQueue * queue = [[NSOperationQueue alloc]init];  
> 添加NSOperation到NSOperationQueue中：[queue addOperation:operation];  
> 添加一组operation：[queue addOperations:operations waitUntilFinished:NO];  
> 添加一个block形式的operation：[queue addOperationWithBlock:^()     
> }];  
> 添加NSOperation的依赖对象：[operation2 addDependency:operation1];  
> 设置队列的最大操作数：[queue setMaxConcurrentOperationCount:1];  
> 等待options完成：[operation waitUntilFinished];  
> 暂停、继续queue:[queue setSuspended:YES] [queue setSuspend:NO]  


####线程同步和异步的区别?ios中如何实现线程的同步?
* 同步：任务顺序执行，下一个任务以来上一个任务的完成
* 异步：任务执行顺序不一定，一起执行
* 实现：设置依赖NSOpreationQueue、GCD中的串行队列.

####iOS类是否可以多继承,如果没有,怎么实现
* 不可以多继承.
* 可以通过类目,延展,协议实现多继承
* 类目:类目也叫分类,英文category,在没有原类.m文件的基础上,给该类添加方法.类目里不能添加实例变量,不能添加和原始类方法名相同的方法,否则会发生覆盖.一个类可以添加多个类目,类目中的方法可以成为原始类的一部分,和原始类方法级别相同,可以被子类继承.
* 延展:Extension,是一种特殊形式的类目,主要是在一个类的.m里面声明与实现.作用:就是给某类添加私有方法或者私有变量.
* 虽然延展是给一个类定义私有方法,但是OC没有绝对的私有方法,其实还是可以调用的,延展里面声明的变量只能在该类内部使用,外界访问不了.如果是新建文件建的的某类延展.h文件,则不能添加实例变量,如果括号里没有类目名,则认为延展里面的方法为全都必须实现,如果有,则可选实现.
* 类目写的方法必须实现,延展写的方法非必须.

####堆和栈的区别
* 栈：内存系统管理，系统凯里，系统释放，先进后出
* 堆：内存自己管理，自己开辟，自己释放，先进先出

####iOS本地数据存储都有几种方式
* Write写入方式:永久保存在磁盘中.具体:a.获得文件保存的路径.b.生成该路径下的文件,c,往文件中写入数据.d.从文件中读出数据.
* NSUserDefaults:用来保存应用程序设置和属性,用户保存的数据.用户再次打开程序或者开机后这些数据仍然存在.NSUserDefaults可以存储的数据类型包括:NSData,NSString,NSNumber,NSDate,NSArray.NSDictionary,其他类型的数据需要先行转换.
* NSkeyedArchiver:采用归档的形式来保存数据,该数据对象需要遵守NSCoding协议,并且该对象对应的类必须提供encodeWithCoder:和initWithCoder:方法.前一个方法告诉系统怎么对对象进行编码,而后一个方法则是告诉系统怎么对对象进行解码.
* SQLite:采用SQLite数据库来存储数据,SQLite作为一种轻量级数据库.具体:a.添加SQLite相关的库以及头文件,b.使用数据库存数数据:打开数据库,编写数据库语句,执行,关闭数据库.另:写入数据库,字符串可以采用char方式,而从数据库中取出char类型,当char类型有表示中文字符时,会出现乱码,这是因为数据库默认使用ascII编码方式,所以想要正确从数据库中取出中文,需要使用NSString来接受从数据库取出的字符串.
* CoreData:原理是对SQLite的封装,开发者不需要接触sql语句,就可以对数据库进行操作.

####iOS动态类型和动态绑定
* 多态：父类指针指向子类对象
* 动态类型：只有在运行期，才能确定其真正类型
* 动态加载：根据不同的条件，加载不同的资源

####深拷贝和浅拷贝的理解
* 深拷贝：拷贝内容
* 浅拷贝：拷贝指针

####怎么实现一个单例类
* 单例是一种设计模式，对象只有一个。
* 缺点：对象不会被释放，如果创建很多的话会占用很多内存
* 优点：可以当做工具类使用
> static Single *single = nil ;  
> + (Single *)shareInstance{  
>      @synchronized(self){  
>          if(!single){   
>              single = [[Single alloc]init] ;  
>           }  
>       }
>       return single ;
> }

####什么是安全释放
先释放再置空

####RunLoop是什么
* 事件循环,是线程里面的一个组件.主线程的RunLoop是自动开启的.分为:计时源(timer source),事件源(输入源):input source.防止CPU中断(保证程序执行的线程不会被系统终止).
* Runloop提供了一种异步执行代码的机制,并不能并行执行任务,是事件接收和分发机制的一个实现.每一个线程都有其对应的RunLoop,但是默认非主线程的RunLoop是没有运行的,需要为RunLoop添加至少一个事件源,然后run它.
* 一般情况下我们是没有必要去启动线程的RunLoop的,除非你在一个单独的线程中需要长时间的检测某个事件.
* RunLoop,正如其名所示,是线程进入和被线程用来响应事件以及调用事件处理函数的地方.
* input source传递异步事件,通常是来自其他线程和不同程序的消息.
* timer source传递同步事件.
* 当有事件发生时,RunLoop会根据具体的事件类型通知应用程序作出响应.
* 当没有事件发生时,RunLoop会进入休眠状态,从而到达省电的目的.
* 当事件再次发生时,RunLoop会被重新唤醒,处理事件.
* 一般在开发中很少会主动创建RunLoop,而通常会把事件添加到RunLoop中.

####什么是序列化和反序列化,可以用来做什么?如何在OC中实现复杂对象的存储.
* 序列化和反序列化:归档和反归档,进行本地化,进行数据存储.
* CoreData:数据托管.有四种存储方式:xml,sqlite,二进制,内存.
* 遵循NSCoding协议之后,进行归档即可实现复杂对象的存储.







