---
title: 【漫谈】从项目实践走向RxSwift响应式函数编程
date: 2017-08-01 11:44:18
categories:
tags:
---
![](http://upload-images.jianshu.io/upload_images/977602-48ac34a811092840.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<!-- more -->
（一）万年不变的开端
去年大三还在学校的时候就听说过ReactiveCocoa
这一Github
开源的响应式重量级框架，可是对于当时还只埋头狂写OOP的我来说，大概只能用下面的话来形容自己吧。
```
你对力量一无所知。
```

后来为了学习ReactiveCocoa
，看了几乎所有中文的资料以及博客，才感觉自己稍稍入门。相对于OOP，的确FRP
的学习成本高了很多，但是也绝对没有有些人说的那么夸张，可能就有那么一些人，会了一些国外玩了很久的技术，然后沾沾自喜地指着其他人，“看！我多么厉害，这么难的技术我都学会了！”,似乎只有这样才能体现他们卑微的优越感。
```
闻道有先后。
```

我一直相信这句话。
在这里不去讨论FRP多么的优越，每一种技术栈都有自己的优劣势，学习FRP对我来说更多的是开拓自己的视野，扩展技能栈。在早期的创业项目中，过早的引入复杂的架构，“先进”的框架，我想更多的会是得不偿失吧。
（二）MVVM Pattern
![](http://upload-images.jianshu.io/upload_images/977602-af67c9163a0a1ac9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)MVVM.png

其实移动端的技术一直慢于Web端，Web前端事件驱动，数据驱动已经晚了很多年了，而对于iOS平台来说RxSwift，ReSwift都是比较新的框架，但是他们都无一例外 Inspire By XXX。
从 MVC -> MVVM，无非是每一个模块都更加的纯粹，给原来充斥着各种各样业务代码的控制器“瘦身”，ViewModel所提供给View的应该是可以直接展示或者可以直接根据状态来绑定的接口。
一般来说，经典的业务情况将是这样的：
![](http://upload-images.jianshu.io/upload_images/977602-6295373344e63fc2.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)经典业务逻辑.jpeg

首先我们通过本地的网络层获取到服务器通信给我们的MetaData
，一般来说是JSON
或者XML
，然后通过客户端的解析（transform），构造出对应的Model，但是Model对象的值并不能直接展示给用户，所以我们需要对它做加工处理（Process）。例如，UserModel中性别通过Int
类型的1,2
来区分，于是我们通过本地的逻辑代码，将其转化成字符串类型的男，女
,而这些最后的字符串类型的数据就是ViewModel
所暴露出来的数据，View
层就可以通过ViewModel
所暴露出来的数据绑定现实了。
（三）MVVM 实践 Demo：Gank.io客户端
1.初窥
首先感谢代码家
倾情提供的api,当你想通过实际项目来提升自己的时候，别犹豫，先用这个api来撸一个gank客户端吧！
这个gank客户端的首页大概是这样子的。
![](http://upload-images.jianshu.io/upload_images/977602-78e9f010b3126698.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)Simulator Screen Shot 2017年2月24日 上午2.24.10.png

2.关于ViewModel
一个好的ViewModel是怎么样的 ?

[devxoul](https://github.com/devxoul)在[RxTodo](https://github.com/devxoul/RxTodo)的Readme中所写的关于ViewModel的哲学，我深以为然：
View 不应该存在逻辑控制流的逻辑，View 不应该对数据造成操作，View 只能绑定数据，控制显示。
**Bad**
```
viewModel.titleLabelText 
.map { $0 + "!" } // Bad: View 不应该有修改数据的操作 
.bindTo(self.titleLabel)
```
**Good**
```
viewModel.titleLabelText 
.bindTo(self.titleLabel.rx.text)
```
View 并不知道 ViewModel具体做了什么,View 只能通过ViewModel知道需要View做什么.
**Bad**
```
viewModel.login() // Bad: View不应该知道ViewModel的具体动作
```
**Good**
```
self.loginButton.rx.tap 
.bindTo(viewModel.loginButtonDidTap) 

self.usernameInput.rx.controlEvent(.editingDidEndOnExit) 
.bindTo(viewModel.usernameInputDidReturn) // "Hey I tapped the return on username input"
```
Model 应当被ViewModel所隐藏，ViewModel只暴露出View渲染所需要的最少信息。
**Bad**
```
struct ProductViewModel { 
let product: Driver<Product> // Bad: Model不应该暴露给View，也不需要暴露给View. 
}
```
**Good**
```
struct ProductViewModel { 
let productName: Driver<String> 
let formattedPrice: Driver<String> 
let formattedOriginalPrice: Driver<String> 
let isOriginalPriceHidden: Driver<Bool> 
}
```
个人在实践的时候也是完全遵从这几个原则的，最主要的好处是，这样的代码结构使得App层次分明，实践在RxSwift上面大概是这样的:
```
//告诉View当前的分类（iOS，android，后端等等...） 
let category = Variable<Int>(0) 
// RxDataSources所需要的数据模型，用于优雅的绑定
TableView let section: Driver<[HomeSection]> 
// 首页的刷新命令，入参是当前的分类 
let refreshCommand = PublishSubject<Int>() 
// 刷新当前的页面，和下拉操作一起绑定
let refreshTrigger = PublishSubject<Void>()
 // RxDataSource的核心类 
let dataSource = RxTableViewSectionedReloadDataSource<HomeSection>() 
// 被隐藏起来的Model fileprivate 
let bricks = Variable<[Brick]>([])
```
在ViewModel的构造方法中，初始化相关的变量
```
override init() { 
section = bricks.asObservable().map({ (bricks) -> [HomeSection] in 
   return [HomeSection(items: bricks)] 
}) 
.asDriver(onErrorJustReturn: []) 

super.init() 

refreshCommand 
   .flatMapLatest { gankApi.request(.data(type: GankAPI.GankCategory.mapCategory(with: $0), size: 20, index: 0)) } 
   .subscribe({ [weak self] (event) in 
    self?.refreshTrigger.onNext() 
    switch event { 
    case let .next(response): 
do { 
let data = try response.mapArray(Brick.self) 
self?.bricks.value = data 
}catch { 
self?.bricks.value = [] 
} break 
case let .error(error): 
self?.refreshTrigger.onError(error); 
break 
default: 
break 
} 
}) 
.addDisposableTo(rx_disposeBag) 
}
```
到这里，我们Gank客户端的ViewModel就算是构建完成了，遵守前面说到的设计哲学，ViewModel还是很好构建的。
Binding
最后的一步就是将ViewModel中暴露出来的绑定给View（控制器或者是View），在RxSwift的世界中，绑定的操作是通过链式的方法调用来玩成的。值得一说的是，在“竞争对手”ReactiveSwift中，大多数的绑定都可以通过自定义的操作符<~
来实现，不得不说，貌似在这方面，ReactiveSwift更加全面的利用了Swift的语言特性，看起来也更加装逼了呢。回到这个Gank项目，Controller层面大概是这样的:
```
do /** Rx Config */ {
 
        // Input
 
        tableView.refreshControl?.rx.controlEvent(.allEvents)
            .flatMap({ self.homeVM.category.asObservable() })
            .bindTo(homeVM.refreshCommand)
            .addDisposableTo(rx_disposeBag)
 
        NotificationCenter.default.rx.notification(Notification.Name.category)
            .map({ (notification) -> Int in
                let indexPath = (notification.object as? IndexPath) ?? IndexPath(item: 0, section: 0)
                return indexPath.row
            })
            .bindTo(homeVM.category)
            .addDisposableTo(rx_disposeBag)
 
 
        NotificationCenter.default.rx.notification(Notification.Name.category)
            .map({ (notification) -> Int in
                let indexPath = (notification.object as? IndexPath) ?? IndexPath(item: 0, section: 0)
                return indexPath.row
            })
            .observeOn(MainScheduler.asyncInstance)
            .do(onNext: { (idx) in
                SideMenuManager.menuLeftNavigationController?.dismiss(animated: true, completion: {
                    DispatchQueue.main.async(execute: { 
                        self.tableView.refreshControl?.beginRefreshing()
                    })
                })
            }, onError: nil, onCompleted: nil, onSubscribe:nil,onDispose: nil)
            .bindTo(homeVM.refreshCommand)
            .addDisposableTo(rx_disposeBag)
 
        // Output
 
        homeVM.section
            .drive(tableView.rx.items(dataSource: homeVM.dataSource))
            .addDisposableTo(rx_disposeBag)
 
        tableView.rx.setDelegate(self)
            .addDisposableTo(rx_disposeBag)
 
        homeVM.refreshTrigger
            .observeOn(MainScheduler.instance)
            .subscribe { [unowned self] (event) in
                self.tableView.refreshControl?.endRefreshing()
                switch event {
                case .error(_):
                    NoticeBar(title: "Network Disconnect!", defaultType: .error).show(duration: 2.0, completed: nil)
                    break
                case .next(_):
                    self.tableView.reloadData()
                    break
                default:
                    break
                }
            }
            .addDisposableTo(rx_disposeBag)
 
        // Configure
 
        homeVM.dataSource.configureCell = { dataSource, tableView, indexPath, item in
            let cell = tableView.dequeueReusableCell(for: indexPath, cellType: HomeTableViewCell.self)
            cell.gankTitle?.text = item.desc
            cell.gankAuthor.text = item.who
            cell.gankTime.text = item.publishedAt.toString(format: "YYYY/MM/DD")
            return cell
        }
    }
```
2.强大的RxSwift社区开源组件
在Github上的[RxSwift Community](https://github.com/RxSwiftCommunity)项目列表中，我们可以看到大量的一系列RxSwift拓展，并且任然还有大量的开发者为其添砖加瓦，在这里我们可以看到一个朝气蓬勃的社区。
在这里列举一些比较常用的扩展组件，如果想了解更多的组件，可以点击上文的链接关注他们。
(1).[ Action](https://github.com/RxSwiftCommunity/Action)
使用过ReactiveCocoa的人一定不会对RACCommand陌生，RACCommand是一个将用户的操作封装起来的组件。它所发射的信号是一个二阶信号，即发射的信号的值是一个信号。在RxSwift的世界里，Action就充当了类似的功能。
(2). **[NSObject-Rx](https://github.com/RxSwiftCommunity/NSObject-Rx)**
**NSObject-Rx**，在我看来这个扩展对于RxSwift开发者来说是最简单同时也是必备的，为了合理的内存管理，我们总要写上这样的代码:
```
class MyObject: Whatever {
    let disposeBag = DisposeBag()

    ...
}
```

然后重复...重复...再重复的这样写，本来使用RxSwift如此优雅的FRP，着实被这个DisposeBag给煞了风景。但是当引用了NSObject-Rx
之后，通过Swift Extension
的方式，虽然还是需要显示的加上DisposeBag，但是已经相比之前优雅太多。
```
thing
  .bindTo(otherThing)
  .addDisposableTo(rx_disposeBag)
```

(3).**[RxDataSources](https://github.com/RxSwiftCommunity/RxDataSources)**
讲道理，RxDataSources也是我们在开发 RxApp 时候的标配。如果你用了RxSwift，然后还在那里傻傻的写着TableView的Delegate，那我觉得不如回家养猪，还写啥Swift ？通过RxDataSources我们可以很完美的将VM和View绑定起来，做到优雅的分层处理和数据驱动。我们可以来看看我在Gank客户端中RxDataSources的示例用法:
```
/// 首先需要构建一个Section模型，遵守
struct HomeSection {
 
    var items: [Item]
}
 
extension HomeSection: SectionModelType {
 
    typealias Item = Brick
 
    init(original: HomeSection, items: [HomeSection.Item]) {
        self = original
        self.items = items
    }
}
```

然后我们需要一个DataSource的示例驱动对象
```
/// 使用之前我们构造的HomeSection
    let dataSource = RxTableViewSectionedReloadDataSource()
```

最后我们只需要将ViewModel中的数据模型通过RxDataSource与TableView或者CollectionView绑定即可。
```
// Binding with Section Model and UITableView    
    homeVM.section
        .drive(tableView.rx.items(dataSource: homeVM.dataSource))
        .addDisposableTo(rx_disposeBag)
```

完美，忘掉那些一大推的系统原生Delegate吧...

结语
我们可以看到，RxSwift有一个非常活跃和富有创造性的社区，为优雅的使用Rx提供了一系列的组件，虽然RxSwift上手需要一点点的功夫，但是这些一系列的组件又为我们降低了门槛。从Star数来看，似乎使用的人还不是很多，笔者在这里的建议就是:   赶快上车RxSwift。

RxSwift + MVVM + Services + Routing + Moya ，这是笔者比较喜欢的架构方式，本片文章更多的是介绍性质，很多东西都是一略而过，并没有什么太多的技术含量，只是希望更多的人了解未来，面向未来，向未来前进。

这个是本文的Demo地址，仅供参考[Gank客户端](https://github.com/Maru-zhang/Gank)。

（EOF）参考文章
[被误解的MVC和被神化的MVVM](http://www.infoq.com/cn/articles/rethinking-mvc-mvvm)
[Coordinator 与 RxSwift 共同构建的 MVVM 架构](https://realm.io/cn/news/mobilization-lukasz-mroz-mvvm-coordinators-rxswift/)
[iOS 架构模式 - 简述 MVC, MVP, MVVM 和 VIPER](https://blog.coding.net/blog/ios-architecture-patterns)
[MVVM With ReactiveCocoa](http://blog.leichunfeng.com/blog/2016/02/27/mvvm-with-reactivecocoa/)

链接：http://www.jianshu.com/p/de7e90e1c13d

