### dispatch queue的类型

  * Serial: 又称为private dispatch queues，同时只执行一个任务，Serial queue通常用于同步访问特定的资源或数据。当你创建多个Serial queue时，虽然它们各自是同步执行的，但Serial queue与Serial queue之间是并发执行的。
  * Concurrent: 又称为global dispatch queue，可以并发地执行多个任务，但是执行完成的顺序是随机的。
  * Main dispatch queue: 它是全局可用的serial queue，它是在应用程序主线程上执行任务的。
  
### 三种队列类型
  
  
  * The main queue: 与主线程功能相同，实际上，提交至main queue的任务会在主线程中执行。main queue可以调用`dispatch_get_main_queue()`来获得。因为main queue是与主线程相关的，所以这是一个串行队列。
  * Global queues: 全局队列是并发队列，并由整个进程共享。进程中存在三个全局队列：高、中（默认）、低三个优先级队列。可以调用`dispatch_get_global_queue`函数传入优先级来访问队列。
  * 用户队列: 用户队列 (GCD并不这样称呼这种队列, 但是没有一个特定的名字来形容这种队列，所以我们称其为用户队列) 是用函数`dispatch_queue_create`创建的队列. 这些队列是串行的。正因为如此，它们可以用来完成同步机制, 有点像传统线程中的mutex。

### 创建队列

  要使用用户队列，我们首先得创建一个。调用函数`dispatch_queue_create`就行了。函数的第一个参数是一个标签，这纯是为了debug。Apple建议我们使用倒置域名来命名队列，比如“com.dreamingwish.subsystem.task”。这些名字会在崩溃日志中被显示出来，也可以被调试器调用，这在调试中会很有用。第二个参数目前还不支持，传入NULL就行了。
  
### 提交 Job
  
  向一个队列提交Job很简单: 调用`dispatch_async`函数，传入一个队列和一个`block`。队列会在轮到这个`block`执行时执行这个`block`的代码。下面的例子是一个在后台执行一个巨长的任务:

    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{ 
      [self goDoSomethingLongAndInvolved]; 
      NSLog(@"Done doing something long and involved"); 
    });

  `dispatch_async`函数会立即返回, block会在后台异步执行。
  还有一个函数叫`dispatch_sync`，它干的事儿和`dispatch_async`相同，但是它会等待`block`中的代码执行完成并返回。结合`__block`类型修饰符，可以用来从执行中的`block`获取一个值。例如，你可能有一段代码在后台执行，而它需要从界面控制层获取一个值。那么你可以使用`dispatch_sync`简单办到:

    __block NSString *stringValue; 
    dispatch_sync(dispatch_get_main_queue(), ^{ 
            // __block variables aren't automatically retained 
            // so we'd better make sure we have a reference we can keep 
            stringValue = [[textField stringValue] copy]; 
    }); 
    [stringValue autorelease]; 
    // use stringValue in the background now   

### 不再使用锁（Lock）

  用户队列可以用于替代锁来完成同步机制。使用GCD，可以使用queue来替代: `dispatch_queue_t queue`要用于同步机制，queue必须是一个用户队列，而非全局队列，所以使用`dispatch_queue_create`初始化一个。然后可以用`dispatch_async` 或者`dispatch_sync`将共享数据的访问代码封装起来:
  
    - (id)something 
    { 
      __block id localSomething; 
      dispatch_sync(queue, ^{ 
        localSomething = [something retain]; 
      }); 
      return [localSomething autorelease]; 
    } 
       
    - (void)setSomething:(id)newSomething 
    { 
        dispatch_async(queue, ^{ 
            if(newSomething != something) 
            { 
                [something release]; 
                something = [newSomething retain]; 
                [self updateSomethingCaches]; 
            } 
        }); 
    }

### dispatch_group

  一个`dispatch group`可以用来将多个`block`组成一组以监测这些`block`全部完成或者等待全部完成时发出的消息。使用函数`dispatch_group_create`来创建，然后使用函数`dispatch_group_async`来将`block`提交至一个`dispatch queue`，同时将它们添加至一个组。所以我们现在可以重新代码:
  
    dispatch_queue_t queue = dispatch_get_global_qeueue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    for(id obj in array)
        dispatch_group_async(group, queue, ^{
            [self doSomethingIntensiveWith:obj];
        });
    dispatch_group_wait(group, DISPATCH_TIME_FOREVER);
    dispatch_release(group); 
    
  `[self doSomethingWith:array];`如果这些工作可以异步执行，那么我们可以更风骚一点，将函数`-doSomethingWith:`放在后台执行。我们使用`dispatch_group_async`函数建立一个`block`在组完成后执行:
  
  
    dispatch_queue_t queue = dispatch_get_global_qeueue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    for(id obj in array)
        dispatch_group_async(group, queue, ^{
            [self doSomethingIntensiveWith:obj];
        });
    //等group里的task都执行完后执行notify方法里的内容,相当于把wait方法及之后要执行的代码合到一起了
    dispatch_group_notify(group, queue, ^{
        [self doSomethingWith:array];
    });
    
  `dispatch_release(group);`不仅所有数组元素都会被平行操作，后续的操作也会异步执行，并且这些异步运算都会将程序的其他部分考虑在内。注意如果`-doSomethingWith:`需要在主线程中执行，比如操作GUI，那么我们只要将`main queue`而非全局队列传给`dispatch_group_notify`函数就行了。

### dispatch_apply的使用

  对于同步执行，GCD提供了一个简化方法叫做`dispatch_apply`。这个函数调用单一`block`多次，并平行运算，然后等待所有运算结束，就像我们想要的那样:
  
    dispatch_queue_t queue = dispatch_get_global_qeueue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);  
    dispatch_apply([array count], queue, ^(size_t index){
      [self doSomethingIntensiveWith:[array objectAtIndex:index]];  
    }); 
    [self doSomethingWith:array];  
  
### dispatch_barrier_async的使用
  
  `dispatch_barrier_async`是在前面的任务执行结束后它才执行，而且它后面的任务等它执行完成之后才会执行。
  
### 信号量  
  
  GCD中快速的控制并发呢，信号量是一个整形值并且具有一个初始计数值，并且支持两个操作：信号通知和等待。当一个信号量被信号通知，其计数会被增加。当一个线程在一个信号量上等待时，线程会被阻塞（如果有必要的话），直至计数器大于零，然后线程会减少这个计数。
　在GCD中有三个函数是semaphore的操作，分别是：
　* `dispatch_semaphore_create`　　　创建一个semaphore
　* `dispatch_semaphore_signal`　　　发送一个信号
　* `dispatch_semaphore_wait`　　　　等待信号
　简单的介绍一下这三个函数，第一个函数有一个整形的参数，我们可以理解为信号的总量，`dispatch_semaphore_signal`是发送一个信号，自然会让信号总量加1，`dispatch_semaphore_wait`等待信号，当信号总量少于0的时候就会一直等待，否则就可以正常的执行，并让信号总量-1，根据这样的原理，我们便可以快速的创建一个并发控制来同步任务和有限资源访问控制。

### 何为Dispatch Sources

  简单来说，dispatch source是一个监视某些类型事件的对象。当这些事件发生时，它自动将一个block放入一个dispatch queue的执行例程中。
  GCD 10.6.0版本支持的事件：
  
  * 1. Mach port send right state changes.    
  * 2. Mach port receive right state changes.    
  * 3. External process state change.    
  * 4. File descriptor ready for read.    
  * 5. File descriptor ready for write.    
  * 6. Filesystem node event.    
  * 7. POSIX signal.    
  * 8. Custom timer.    
  * 9. Custom event. 

### 用户事件

  这些事件里面多数都可以从名字中看出含义，但是你可能想知道啥叫用户事件。简单地说，这种事件是由你调用`dispatch_source_merge_data`函数来向自己发出的信号。
  这个名字对于一个发出事件信号的函数来说，太怪异了。这个名字的来由是GCD会在事件句柄被执行之前自动将多个事件进行联结。你可以将数据“拼接”至dispatch source中任意次，并且如果`dispatch queue`在这期间繁忙的话，GCD只会调用该句柄一次（不要觉得这样会有问题，看完下面的内容你就明白了）。
  用户事件有两种: `DISPATCH_SOURCE_TYPE_DATA_ADD`和`DISPATCH_SOURCE_TYPE_DATA_OR`.用户事件源有个`unsigned long data`属性，我们将一个`unsigned long`传入`dispatch_source_merge_data`。当使用`_ADD`版本时，事件在联结时会把这些数字相加。当使用`_OR`版本时，事件在联结时会把这些数字逻辑与运算。当事件句柄执行时，我们可以使用`dispatch_source_get_data`函数访问当前值，然后这个值会被重置为0。
  让我假设一种情况。假设一些异步执行的代码会更新一个进度条。因为主线程只不过是GCD的另一个`dispatch queue`而已，所以我们可以将GUI更新工作push到主线程中。然而，这些事件可能会有一大堆，我们不想对GUI进行频繁而累赘的更新，理想的情况是当主线程繁忙时将所有的改变联结起来。
  用`dispatch source`就完美了，使用`DISPATCH_SOURCE_TYPE_DATA_ADD`，我们可以将工作拼接起来，然后主线程可以知道从上一次处理完事件到现在一共发生了多少改变，然后将这一整段改变一次更新至进度条。

    dispatch_source_t source = dispatch_source_create(DISPATCH_SOURCE_TYPE_DATA_ADD, 0, 0, dispatch_get_main_queue());   
    dispatch_source_set_event_handler(source, ^{   
      [progressIndicator incrementBy:dispatch_source_get_data(source)];   
    });   
    dispatch_resume(source);   
      
    dispatch_apply([array count], globalQueue, ^(size_t index) {   
      // do some work on data at index   
      dispatch_source_merge_data(source, 1);   
    });

  数据会被并发处理。当每一段数据完成后，会通知`dispatch source`并将`dispatch source data`加1，这样我们就认为一个单元的工作完成了。事件句柄根据已完成的工作单元来更新进度条。若主线程比较空闲并且这些工作单元进行的比较慢，那么事件句柄会在每个工作单元完成的时候被调用，实时更新。如果主线程忙于其他工作，或者工作单元完成速度很快，那么完成事件会被联结起来，导致进度条只在主线程变得可用时才被更新，并且一次将积累的改变更新至GUI。
  
### 计时器
  
  计时器事件稍有不同。它们不使用handle/mask参数，计时器事件使用另外一个函数`dispatch_source_set_timer`来配置计时器。这个函数使用三个参数来控制计时器触发: `start`参数控制计时器第一次触发的时刻。参数类型是`dispatch_time_t`，这是一个opaque类型，我们不能直接操作它。我们得需要`dispatch_time`和`dispatch_walltime`函数来创建它们。另外，常量`DISPATCH_TIME_NOW`和`DISPATCH_TIME_FOREVER`通常很有用。interval参数没什么好解释的。leeway参数比较有意思。这个参数告诉系统我们需要计时器触发的精准程度。所有的计时器都不会保证100%精准，这个参数用来告诉系统你希望系统保证精准的努力程度。如果你希望一个计时器没五秒触发一次，并且越准越好，那么你传递0为参数。另外，如果是一个周期性任务，比如检查email，那么你会希望每十分钟检查一次，但是不用那么精准。所以你可以传入60，告诉系统60秒的误差是可接受的
  
  这样有什么意义呢？简单来说，就是降低资源消耗。如果系统可以让cpu休息足够长的时间，并在每次醒来的时候执行一个任务集合，而不是不断的醒来睡去以执行任务，那么系统会更高效。如果传入一个比较大的leeway给你的计时器，意味着你允许系统拖延你的计时器来将计时器任务与其他任务联合起来一起执行。
  
### dispatch_once  

  GCD还提供单次初始化支持，这个与`pthread`中的函数`pthread_once`很相似。GCD提供的方式的优点在于它使用`block`而非函数指针，这就允许更自然的代码方式。
  这个特性的主要用途是惰性单例初始化或者其他的线程安全数据共享。典型的单例初始化技术看起来像这样（线程安全的）:
  
    + (id)sharedWhatever
    {  
      staticdispatch_once_t pred;
      staticWhatever *whatever = nil; 
      dispatch_once(&pred, ^{ 
        whatever = [[Whatever alloc] init];
      });  
      returnwhatever;
    });
    
### dispatch_queue的挂起和恢复

  `dispatch queue`可以被挂起和恢复。使用`dispatch_suspend`函数来挂起，使用`dispatch_resume`函数来恢复。这两个函数的行为是如你所愿的。另外，这两个还是也可以用于`dispatch source`。
　一个要注意的地方是，`dispatch queue`的挂起是`block`粒度的。换句话说，挂起一个queue并不会将当前正在执行的`block`挂起。它会允许当前执行的`block`执行完毕，然后后续的`block`不再会被执行，直至queue被恢复。
　还有一个注意点：从man页上得来的：如果你挂起了一个queue或者source，那么销毁它之前，必须先对其进行恢复。

### 不要阻塞太多后台线程

  如果我们要在后台线程中请求一系列的数据，然后将它们显示到界面上，你可能写出下面的代码：
  
    //Main Thread
    dispatch_queue_t queue;
    queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    for (NSURL *url in [self.imageStore URLs]) {
        dispatch_async(queue, ^{
            NSData *data = [NSData dataWithContentsOfURL:url];
    
            dispatch_async(dispatch_get_main_queue(), ^{
                [self.imageStore setImageData:data forURL:url];
            });
        });
    }

  这段代码肯定是有问题的，因为获取数据`NSData *data = [NSData dataWithContentsOfURL:url];`是同步的，台线程被这段代码阻塞调，系统会自动创建新的线程去执行下一个循环，最终结果会是获取多少次数据将创建了多少个后台线程。而创建线程本身是有成本的，所以如果创建太多的后台线程会占用大量的系统资源，这时应该用dispatch I/O来解决：

    //Main Thread
    for (NSURL *url in [self.imageStore URLs]) {
        dispatch_io_t io = dispatch_io_create_with_path(DISPATCH_IO_RANDOM, [[url path] fileSystemRepresentation], 0_RDONLY, 0, NULL, NULL);
        dispatch_io_set_low_water(io, SIZE_MAX);
    
        dispatch_io_read(io, 0, SIZE_MAX, dispatch_get_main_queue(), ^(bool done, dispatch_data_t data, int error) {
            [self.imageStore setImageData:data forURL:url];
        });
    }

### 通过读写访问提升效率
  
  我们在设计读写时通常允许并发同步的的读(read)，串行异步的写(write)，并且读写不能同时进行。
  
    self.concurrentQuene = dispatch_queue_create("com.example.current", DISPATCH_QUEUE_CONCURRENT);

    - (id)objectAtIndex:(NSUInteger)index {
        __block id obj;
        dispatch_sync(self.concurrentQueue, ^{
           obj = [self.array objectAtIndex:index];
        });
        return obj;
    }
    
    - (void)insertObject:(id)obj atIndex:(NSUInteger)index {
        dispatch_barrier_async(self.concurrentQueue, ^{
            [self.array insertObject:obj atIndex:index];
        });
    }

### 异步的更新状态

  有时候我们先知道队列中操作执行的进度，并通过状态显示出来，如通过progress view显示当前图片渲染的进度，我们可以使用GCD的dispatch source。
  
    //先设置接受到数据的处理（类似监听）
    self.source = dispatch_source_create(DISPATCH_SOURCE_TYPE_ADD, 0, 0, dispatch_get_main_queue());
    
    dispatch_source_set_event_handler(self.source, ^{
        self.progress += dispatch_source_get_data(self.source);
        [self.progressView setProgress:(self.progress/self.total) animated:YES];
    });
    dispatch_resume(self.source);
    //在渲染的时候将数据传递给dispatch source
    dispatch_async(self.renderQueue, ^{
        //...
        dispatch_source_merge_data(self.source, 1);
    });
    //可以取消掉dispatch source的处理
    dispatch_source_cancel(self.source);
    


@end
  
