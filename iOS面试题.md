## iOS面试题

web：

> [2021- iOS开发者一份你一定会被问到的面试题（附参考答案）_ITPUB博客](http://blog.itpub.net/69971523/viewspace-2783334/)
>
> [2020年面试：整理出一份高级iOS面试题_ITPUB博客](http://blog.itpub.net/69971523/viewspace-2690100/)
>
> [史上最全的iOS面试题及答案_笔经面经_牛客网 (nowcoder.com)](https://www.nowcoder.com/discuss/362)



1. NSArray与NSSet的区别？

- NSArray内存中存储地址连续，而NSSet不连续

2.  属性关键字assign、retain、weak、copy

- assign：用于基本数据类型和结构体。如果修饰对象的话，当销毁时，属性值不会自动置nil，可能造成野指针。
- weak：对象引用计数为0时，属性值也会自动置nil
- retain：强引用类型，ARC下相当于strong，但block不能用retain修饰，因为等同于assign不安全。
- strong：强引用类型，修饰block时相当于copy。

3. weak属性如何自动置nil的？

- Runtime会对weak属性进行内存布局，构建hash表：以weak属性对象内存地址为key，weak属性值(weak自身地址)为value。当对象引用计数为0 dealloc时，会将weak属性值自动置nil。

4. Block的循环引用、内部修改外部变量、三种block

- block强引用self，self强引用block
- 内部修改外部变量：block不允许修改外部变量的值，这里的外部变量指的是栈中指针的内存地址。__block的作用是只要观察到变量被block使用，就将外部变量在栈中的内存地址放到堆中。
- 三种block：NSGlobalBlack(全局)、NSStackBlock(栈block)、NSMallocBlock(堆block

5. KVO底层实现原理？手动触发KVO？swift如何实现KVO？

- KVO原理：当观察一个对象时，runtime会动态创建继承自该对象的类，并重写被观察对象的setter方法，重写的setter方法会负责在调用原setter方法前后通知所有观察对象值得更改，最后会把该对象的isa指针指向这个创建的子类，对象就变成子类的实例。
- 如何手动触发KVO：在setter方法里，手动实现NSObject两个方法：willChangeValueForKey、didChangeValueForKey
- swift的kvo：继承自NSObject的类，或者直接willset/didset实现。

6. Runloop与线程的关系？Runloop的mode? Runloop的作用？内部机制？

- 每一个线程都有一个runloop，主线程的runloop默认启动。
- mode：主要用来指定事件在运行时循环的优先级
- 作用：保持程序的持续运行、随时处理各种事件、节省cpu资源(没事件休息释放资源)、渲染屏幕UI

7. iOS中使用的锁、死锁的发生与避免

- @synchronized、信号量、NSLock等
- 死锁：多个线程同时访问同一资源，造成循环等待。GCD使用异步线程、并行队列

8. NSOperation和GCD的区别

- GCD底层使用C语言编写高效、NSOperation是对GCD的面向对象的封装。对于特殊需求，如取消任务、设置任务优先级、任务状态监听，NSOperation使用起来更加方便。
- NSOperation可以设置依赖关系，而GCD只能通过dispatch_barrier_async实现
- NSOperation可以通过KVO观察当前operation执行状态(执行/取消)
- NSOperation可以设置自身优先级(queuePriority)。GCD只能设置队列优先级(DISPATCH_QUEUE_PRIORITY_DEFAULT)，无法在执行的block中设置优先级
- NSOperation可以自定义operation如NSInvationOperation/NSBlockOperation，而GCD执行任务可以自定义封装但没有那么高的代码复用度
- GCD高效，NSOperation开销相对高

9. swift相比OC有什么优势？

* 可选类型
* 自动做类型判断

10. struct、Class的区别

- class可以继承，struct不可以
- class是引用类型，struct是值类型
- struct在function里修改property时需要mutating关键字修饰

11. 访问控制关键字(public、open、private、filePrivate、internal)

- public与open：public在module内部中，class和func都可以被访问/重载/继承，外部只能访问；而open都可以
- private与filePrivate：private修饰class/func，表示只能在当前class源文件/func内部使用，外部不可以被继承和访问；而filePrivate表示只能在当前swift源文件内访问
- internal：在整个模块或者app内都可以访问，默认访问级别，可写可不写

12. OC与Swift混编

- OC调用swift：import "工程名-swift.h” @objc
- swift调用oc：桥接文件

13. map、filter、reduce？map与flapmap的区别？

- map：数组中每个元素都经过某个方法转换，最后返回新的数组（xx.map({ ![0 *](http://blog.itpub.net/69971523/viewspace-2690100/%20*)0})）
- flatmap：同map类似， 区别在flatmap返回的数组不存在nil，并且会把optional解包；而且还可以把嵌套的数组打开变成一个（[[1,2],[2,3,4],[5,6]] ->[1,2,2,3,4,5,6]）
- filter：用户筛选元素（xxx.filter({$0 > 25})，筛选出大于25的元素组成新数组）
- reduce：把数组元素组合计算为一个值，并接收初始值（）

14. guard与defer

- guard用于提前处理错误数据，else退出程序，提高代码可读性
- defer延迟执行，回收资源。多个defer反序执行，嵌套defer先执行外层，后执行内层

15. try、try?与try!

- try：手动捕捉异常
- try?：系统帮我们处理，出现异常返回nil；没有异常返回对应的对象
- try!：直接告诉系统，该方法没有异常。如果出现异常程序会crash

16. throws与rethrows：throws另一个throws时，将前者改为rethrows

17. crash防护？

- unrecognized selector crash
- KVO crash
- NSNotification crash
- NSTimer crash
- Container crash（数组越界，插nil等）
- NSString crash （字符串操作的crash）
- Bad Access crash （野指针）
- UI not on Main Thread Crash (非主线程刷UI (机制待改善))

18. 内存泄露问题？

* 主要集中在循环引用问题中，如block、NSTime、perform selector引用计数问题。

19. 数据结构&算法

- 快速排序、归并排序
- 二维数组查找(每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数)
- 二叉树的遍历：判断二叉树的层数
- 单链表判断环

20. 计算机基础

* http与https？socket编程？tcp、udp？get与post？

* tcp三次握手与四次握手

* 进程与线程的区别

21. Swift 的主要特点和优点或缺点是什么？

* 主要的 Swift 特性 - 静态类型、协议、引用类型、值类型、选项、泛型。

22. iOS 中的内存管理是如何处理的？

* Swift 使用自动引用计数 ( [ARC](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html) )。这在 Swift 和 Objective-C 中在概念上是一样的。ARC 跟踪对类实例的强引用，并在您将类（引用类型）的实例分配或取消分配给常量、属性和变量时相应地增加或减少它们的引用计数。它释放引用计数降至零的对象使用的内存。ARC 不会增加或减少值类型的引用计数，因为在赋值时，这些被复制。默认情况下，如果您不另行指定，则所有引用都将是强引用。

23. 什么是MVC？

* 模型和视图从不直接相互通信，而是依靠控制器来协调通信。
* 大规模视图控制器是代码库的状态，其中许多逻辑和责任被推入不属于那里的视图控制器。这种做法会使您的代码僵化、臃肿且难以更改。还有其他设计模式可以帮助您解决这个问题，例如 [MVVM](https://www.objc.io/issues/13-architecture/mvvm/)、 [MVP](https://martinfowler.com/eaaDev/uiArchs.html#Model-view-presentermvp)和 [Coordinator](https://www.raywenderlich.com/158-coordinator-tutorial-for-ios-getting-started)。

24. Delegate和 KVO 之间有什么区别？

* 两者都是在对象之间建立关系的方法。[Delegate](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html)是一种一对一的关系，其中一个对象实现委托协议；另一个使用协议定义的方法向它发送消息。[KVO](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/KVO.html)是一种多对多关系，其中一个对象广播一条消息，一个或多个其他对象侦听它并做出反应。KVO 不依赖于协议。KVO 是反应式编程（[RxSwift](https://github.com/ReactiveX/RxSwift)、[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)等）的 第一步和基本块.

25. iOS 应用中常用的设计模式有哪些？

* 构建 iOS 应用程序时典型的常用模式是 Apple 在其 Cocoa、Cocoa Touch、Objective-C 和 Swift 文档中提倡的模式。这些是每个 iOS 开发人员都学习的模式。Apple 将它们称为“核心竞争力”设计模式。它们包括[MVC](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)、[Singleton](https://developer.apple.com/documentation/swift/cocoa_design_patterns/managing_a_shared_resource_using_a_singleton)、[Delegate](https://developer.apple.com/documentation/swift/cocoa_design_patterns/using_delegates_to_customize_object_behavior)和[Observer](https://developer.apple.com/documentation/swift/cocoa_design_patterns/using_key-value_observing_in_swift)。

26. 除了常见的 Cocoa 模式，你还知道哪些设计模式？

* 除了常用的 MVC、Singleton、Delegate 和 Observer 模式之外，还有许多其他模式非常适用于 iOS 应用程序：[Factory Method](https://www.oodesign.com/factory-method-pattern.html)、[Adapter](https://www.oodesign.com/adapter-pattern.html)、[Decorator](https://www.oodesign.com/decorator-pattern.html)、[Command](https://www.oodesign.com/command-pattern.html)和[Template](https://www.sm-cloud.com/template-method/)。

  **工厂方法**用于替换类构造函数，抽象和隐藏对象初始化以便在运行时确定类型，以及隐藏和包含`switch/if`确定要实例化的对象类型的语句。

  **适配器**是一种设计模式，顾名思义，它可以帮助您使一个对象的接口适应另一个对象的接口。当您尝试调整无法更改为您的代码的第三方代码时，或者当您需要使用具有不方便或不兼容的 API 的东西时，通常会使用此模式。

  **装饰器**是另一个类的包装器，可增强其功能。它环绕着你想要装饰的东西，实现它的接口，并将发送给它的消息委托给底层对象，或者增强它们或提供它自己的实现。

  **命令**是一种设计模式，您可以在其中实现一个对象，该对象表示您想要执行的操作。该操作可以有自己的状态和逻辑来执行它所做的任务。这种设计模式的主要优点是您可以对用户隐藏操作的内部实现，您可以为其添加撤消/重做功能，并且您可以在稍后的时间点（或根本不）执行操作而不是立即创建操作。

  **模板**是一种设计模式，其主要概念是拥有一个基类，该基类概述了需要完成的工作的算法。基类有几个抽象方法，需要由它的具体子类来实现。这些方法称为钩子方法。模板方法类的用户仅使用实现算法步骤的基类进行交互；这些步骤的具体实现由子类提供。

27. 你对Singleton了解多少？你会在哪里使用，你不会在哪里？

* [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern)是一个类，无论您请求多少次，它都只返回一个相同的实例。

  单例有时被认为是一种反模式。使用单例有多个缺点。主要是全局状态、对象生命周期和依赖注入。当您只有一个实例时，很容易直接在任何地方引用和使用它，而不是将它注入到您的对象中。这会导致代码中不必要的具体实现耦合，而不是使用接口抽象。

  “Singleton”的另一个副作用是全局状态。单例通常可以实现全局状态共享，并扮演每个对象用来存储状态的“公共包”的角色。当这种不受控制的状态被某人覆盖或删除时，这会导致不可预测的结果和错误或崩溃。

28. 在 iOS 上实现存储和持久化有哪些选择？

* 通常有以下几种方式来存储数据，从简单到复杂的顺序：

  - 内存中的数组、字典、集合和其他数据结构
  - NSUserDefaults/钥匙串
  - 文件/磁盘存储
  - 核心数据，领域
  - SQLite

  **内存中的数组、字典、集合和其他数据结构**非常适合中间存储数据，或者如果不需要持久化数据。

  **NSUserDefaults/Keychain**是简单的键值存储。一个是不安全的，另一个是安全的。

  **文件/磁盘存储**是一种使用[NSFileManager](https://developer.apple.com/documentation/foundation/nsfilemanager)向/从磁盘写入数据（序列化或非序列化）的[方式](https://developer.apple.com/documentation/foundation/nsfilemanager)。

  **Core Data**和**Realm**是简化数据库工作的框架。

  **SQLite**是一个关系型数据库，当你需要实现复杂的查询机制而 Core Data 或 Realm 不会削减它时，它是很好的。

29. 哪些选项可用于 iOS 上的网络和 HTTP？

* 在 iOS 中有几种实现[HTTP](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol)网络的选项。您可以使用旧的[NSURLSession](https://developer.apple.com/documentation/foundation/urlsession)，但除非您将其抽象得足够好，否则使用它可能会令人生畏。另一种选择是在它周围使用包装器库。iOS 上最流行的解决方案是[Alamofire](https://github.com/Alamofire/Alamofire)。

  一般来说，如果您有一个小团队，您可能希望依赖开源解决方案，例如 Alamofire，它为您抽象出大量样板代码；但是如果你在一个大团队中并且可以节省资源，你会希望对数据如何传入/传出服务器有更多的控制权，并使用 NSURLSession 自己实现它。

  高级开发人员应该记住，在 iOS 应用程序中构建网络层不仅意味着处理 HTTP 请求，还意味着实现您的代码所做的与此相关的整套任务：HTTP 网络、数据序列化和数据映射。

30. 如何以及何时在 iOS 上序列化和映射数据？

* 有两种最常见的场景需要在 iOS 应用程序中序列化和映射数据：在网络层接收或发送数据（例如 JSON 或 XML 或其他东西），以及在存储层（NSData）中持久化或检索模型，NSManagedObject）。

  每次您从后端 API 接收 JSON 或 XML 或任何其他响应类型的响应时，您很可能会以 JSON 或二进制或其他“不方便”的格式获得它。要处理收到的数据，您需要做的第一件事是将其序列化为您的应用程序可以理解的内容。在最简单和基本的层面上，这将是一个字典或一个包含来自该响应的其他字典、数组和基元的对象数组。NSJSONSerialization 负责解决这个问题。下一步是将该数据映射到应用程序的域模型中。这些将是应用程序其余部分要使用的模型对象或结构。您可以手动完成，也可以使用`Codable`Apple 提供的协议或使用 Mantle 或 SwiftyJSON 等库。数据和序列化/映射的流程是：`binary data`->`json`-> `NSDictionary/NSArray`-> `your domain model objects`。

  同样，在存储层中，您需要将数据序列化并映射到您的自定义域模型对象和从您的自定义域模型对象映射到您的存储理解的格式。读取数据的“映射”链：`db`-> `raw data format`-> `custom domain models`; 和写作：`custom domain models`-> `raw data format`-> `db`。您将在这里使用 NSManagedObject 或 NSCoding 或 Codable 协议来实现这一点。

31. 在 iOS 上布局 UI 的选项有哪些？

* 在屏幕上布置视图的首选选项是旧的 CGRect Frames 和 AutoLayout。框架以及自动调整大小的遮罩，过去在 iOS 6 之前使用过，现在不是首选选项。框架太容易出错且难以使用，因为很难计算各种设备的精确坐标和视图大小。

  从 iOS 6 开始，我们有了 AutoLayout，这是当今的首选解决方案，也是 Apple 更喜欢的。AutoLayout 是一种技术，可帮助您以声明方式定义视图之间的关系（称为约束），让框架计算 UI 元素的精确框架和位置。

  在 iOS 13 中，Apple 引入了一种布局视图的新方法 - SwiftUI，这是一种声明式方法，支持使用 Combine 进行 FRP（功能反应式编程）数据绑定。FRP 和声明式 UI 并不是新概念，但 SwiftUI 和 Combine 是 Apple 支持它的新框架。SwiftUI 的声明性质允许您非常简洁地声明您的 UI 元素，然后通过数据绑定声明 UI 的哪些部分，例如文本标签，以更新哪些数据模型更改。实际上，它允许你做以前用 RxSwift 已经可以做到的事情，但现在用 Apple 框架。

  布局视图还有其他选项，例如 ASDK（Texture）、ComponentKit 和 LayoutKit，其中一些或多或少受到 React 的启发，而另一些则以不同的方式解决布局异步性。这些替代方案在某些情况下非常有用，例如，您需要构建高度动态且快速的表视图和集合视图。AutoLayout 并不总是完美的，知道有其他选择总是好的。

32. 如何优化动态大小的表或集合视图的滚动性能？

* 滚动性能是 UITableViews 的一个大问题，而且通常很难做到正确。主要难点是单元高度计算。当用户滚动时，每个下一个单元格都需要计算其内容和高度才能显示。如果您进行手动框架视图布局，那么它的性能会更高，但挑战在于正确计算高度和大小。如果您使用 AutoLayout，那么挑战是正确设置所有约束。但即使是 AutoLayout 本身也可能需要一些时间来计算单元格高度，并且您的滚动性能会受到影响。

  滚动性能问题的潜在解决方案可能是：

  - 自己计算单元格高度
  - 保留一个用内容填充的原型单元格并使用它来计算单元格高度

  或者，您可以采取完全激进的方法，即使用不同的技术，例如 ASDK（纹理）。ASDK (Texture) 专为具有动态内容大小的列表视图而设计，并针对在后台线程中计算单元格高度进行了优化，使其具有超强的性能。

33. 你将如何在 iOS 上执行异步任务？

* 现在在 iOS 上，异步任务的首选解决方案是 NSOperations 和 GCD 块。Grand Central Dispatch 是一种与多个后台队列一起工作的技术，这些队列反过来确定哪个后台线程处理工作。主要的是，这是从你那里抽象出来的，这样你就不必担心了。NSOperation 是 GCD 之上的 OOP 抽象，它允许您执行更复杂的异步操作，但是您可以使用 NSOperations 实现的所有操作都可以使用 GCD 完成。许多 Cocoa 框架在底层使用 GCD 和/或 NSOperations（例如 NSURLSession）。

  有使用第三方库的帮助处理异步工作的替代方法。最著名的是 Promises (PromiseKit)、RxSwift 和 ReactiveCocoa。RxSwift 和 ReactiveCocoa 尤其擅长建模需要在后台完成并在线程之间协调的时间和工作的异步性质。

34. 你如何管理依赖关系？

* 几年前，我们在 iOS 上没有任何依赖管理器，不得不将第三方代码复制粘贴和拖放到我们的项目中或使用 Git 子模块。随着我们的代码库和依赖项的增长，这些方法被证明是难以管理的。

  现在我们有其他依赖管理器可供选择：CocoaPods、Carthage 和 Swift Package Manager。

  到目前为止，最具统治力和最强大的是[CocoaPods](https://cocoapods.org/)。我是本着 Ruby [Bundler gem](https://bundler.io/)的精神构建的，它本身就是一个 Ruby gem。它的工作方式是安装 gem，`Podfile`在项目的根目录中创建，声明要使用的 pod（库），然后运行`pod install`. 而已。

  使用[Carthage](https://github.com/Carthage/Carthage)，您可以创建一个名为的依赖声明文件，`Cartfile`但与 Cocoapods 不同的是，您需要进行 Xcode 项目设置才能使其工作。

  [Swift Package Manager](https://swift.org/package-manager/)是任何 Swift 项目依赖管理的未来，但它只支持库和框架，不能用于生成 iOS 目标，尽管 iOS 目标可以依赖于 SPM 构建的模块。

35. 你如何在 iOS 上调试和分析代码？

* 在 iOS 应用程序中 总是有`NSLog`ging 和`print`ing。您可以使用 Xcode 设置断点。对于单个代码段的性能，您可以使用 XCTest 的 measureBlock。

  您可以使用`Instruments`. Instruments 是一种分析工具，可帮助您分析应用程序并在运行时查找内存泄漏和性能问题。

36. 你有TDD经验吗？你如何在 iOS 上进行单元和 UI 测试？

* TDD 是一种技术和学科，您首先编写失败的测试，然后再编写使它们通过的生产代码。测试驱动生产代码的实现和设计，帮助您只编写通过测试实现所需的代码，不多也不少。该学科起初可能令人生畏，您不会立即看到这种方法的回报，但如果您坚持下去，从长远来看，它会帮助您更快地前进。它在帮助您进行重构和代码更改方面特别有效，因为在任何给定时间，您都有测试的安全网来告诉您是否有问题发生，或者在您更改时一切是否仍然正常。

  最近，Apple 对 XCTest 框架进行了改进，使我们的测试更容易。他们还对 Xcode (XCUITest) 中的 UI 测试进行了大量改进，因此现在我们有一个很好的编程界面来与我们的应用程序交互并查询我们在屏幕上看到的内容。或者，您可以使用 KIF、iOSSnapshotTestCase、EarlGrey 等框架。

  关于单元测试，也有多种选择，但最流行的两个是 XCTest 和 Quick 和 Nimble。

  **XCTest**是由 Apple 构建的类似 xUnit 的测试框架。这是他们推荐使用的，它与 Xcode 的集成度最高。

  **Quick**是一个类似于 RSpec 的 BDD 框架，可帮助您根据行为而非“测试”来描述规范/测试。RSpec 的粉丝非常喜欢它。

  **Nimble**是一个匹配器库，可与 XCTest 或 Quick 一起使用以断言您的测试/规范中的期望。

