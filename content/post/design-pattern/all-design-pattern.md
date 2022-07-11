---
title: "各种设计模式"
date: 2022-07-11T18:27:35+08:00
ShowToc: true
categories: 
- 软件开发
tags: 
- 设计模式
---


## Creational

Object Created Pattern

### Factory Method

Provide the method for creating an instance in the superclass, and allow the subclass to choose the type of the instance.

在父类中提供创建对象的方法，允许子类决定实例化对象的类型。

具备的部分：生产者协议、产品协议，往后就可以根据需要来扩展每一种产品。

![factory](../imgs/factory.png)

具体的生产者比如 `MongoCakeCreator` 的存在是为了实现与产品相关的核心业务逻辑，而不仅仅是创建 `MongoCake` 实例。工厂方法将核心业务逻辑从具体产品类中分离出来。

```swift
    // Creator
    protocol CakeCreator {
        func createCake() -> Cake
        func doSomethingForCake(cake: Cake) -> Cake
    }
    
    // Product
    protocol Cake {
        func doWork()
    }
    
    // ConcreteCreator
    class MongoCakeCreator: CakeCreator {
        var cake: MongoCake?
        func createCake() -> Cake {
            var cake = MongoCake()
            doSomethingForCake(cake: cake)
            return cake
        }
        func doSomethingForCake(cake: Cake) -> Cake{
            cake.doWork()
            cake.doWork()
            return cake
        }
        
    }
    
    // ConcreteCreator
    class ChocolateCakeCreator: CakeCreator {
        func createCake() -> Cake {
            var cake = ChocolateCake()
            doSomethingForCake(cake: cake)
            return cake
        }
        
        func doSomethingForCake(cake: Cake) -> Cake{
            cake.doWork()
            return cake
        }
    }
    
    class MongoCake: Cake {
        func doWork() {
            print("Add some mongo")
        }
    }
    
    class ChocolateCake: Cake {
        func doWork() {
            print("Add some chocolate")
        }
    }
    
    // If we want to add a type of cake call "PinapleCake", just need to 
    // make it conform to Cake and add a creator that conform to the CakeCreator for the "PinapleCake"
    
    let cakeOne = MongoCakeCreator().createCake()
```

### Abstract Factory

Base on the factory method, add an abstract factory. We can call the same abstract factory method to create different mode’s product. If we want to create another mode’s product, we need to change the concrete factory.

基于工厂方法模式，我们添加了一个抽象工厂协议，其它具体的工厂来实现这个工厂协议。从而我们可以调用同一个抽象工厂方法来创建不同模式的相似产品（比如 WinButton 和 MacButton）。如果想要切换模式，我们需要切换到另外一种具体的工厂实现。

![abstractFactory](../imgs/abstractFactory.png)

```swift
    // Abstract factory
    protocol GUIFactory {
        func createButton() -> Button
        func createCheckBox() -> CheckBox
    }
    
    // Concrete factory
    class WinGUIFactory: GUIFactory {
        func createButton() -> Button {
            var btn = WinButton()
            return btn
        }
        func createCheckBox() -> CheckBox {
            var box = WinCheckBox()
            return box
        }
    }
    
    // Concrete factory
    class MacGUIFactory: GUIFactory {
        func createButton() -> Button {
            var btn = MacButton()
            return btn
        }
        func createCheckBox() -> CheckBox {
            var box = MacCheckBox()
            return box
        }
    }
    
    // Abstract product
    protocol Button {
        func click()
    }
    
    // Abstract product
    protocol CheckBox {
        func render()
    }
    
    // Concrete product
    class WinButton: Button {
        func click() {
            print("Win click button")
        }
        
    }
    
    // Concrete product
    class WinCheckBox: CheckBox {
        func render() {
            print("Here the win render checkBox")
        }
    }
    
    // Concrete product
    class MacButton: Button {
        func click() {
            print("Mac click button")
        }
    }
    
    // Concrete product
    class MacCheckBox: CheckBox {
        func render() {
            print("Here the mac render checkBox")
        }
    }
    
    class App {
        var factory: GUIFactory
        init(factory: GUIFactory) {
            self.factory = factory
        }
        
        func someOperation() {
            var checkBox = factory.createCheckBox()
            checkBox.render()
        }
    }
    
    let factory = WinGUIFactory()           
    let app = App(factory: factory)         // The mode is choosen now, and the behavior in the app is specific.
    let button = app.factory.createButton();
    button.click()
    app.someOperation()
```

### Builder

Extracting the instance’s building code from the instance’s class, and put the code into a “Builder” object to do the build work. So we can build different complex instance by using different composition of the builder method. Besides, Add a “director” to manage the calling form client.

将对象的构造代码从对象的产品类代码中抽离出来，将这些代码放置在一个名为 “生成器” 的独立对象中。 所以，我们可以通过在建造方法中使用不同的组合，来创建不同的复杂实例。并且，增加一个 “指挥者” 来管理那些来自外界客户对调用。

![builder](../imgs/builder.png)

```swift
    // Director
    class HouseDirector{
        var builder: HouseBuilder
        init (builder: HouseBuilder) {
            self.builder = builder
        }
        func changeBuilder(bulider: HouseBuilder) {
            self.builder = bulider
        }
        // The function "make" define the house must built by some componets. 
        func make(type: String) {
            builder.reset()
            if type == "simple" {
                builder.buildA()
            } else {
                builder.buildB()
                builder.buildC()
            }
        }
    }
    
    // Builder
    protocol HouseBuilder {
        func reset()
        func buildA()
        func buildB()
        func buildC()
    }
    
    class Builder1: HouseBuilder {
        private var result: Product1?
        func reset() {
            result = Product1()
        }
        func buildA() {
            
        }
        func buildB() {
            
        }
        func buildC() {
            
        }
        func getResult() -> Product1? {
            return result
        }
    }
    
    class Builder2: HouseBuilder {
        private var result: Product2?
        func reset() {
            result = Product2()
        }
        func buildA() {
            
        }
        func buildB() {
            
        }
        func buildC() {
            
        }
        func getResult() -> Product2? {
            return result
        }
    }
    
    class Product1 {
        
    }
    
    class Product2 {
        
    }
    
    let b = Builder1()
    let director = HouseDirector(builder: b)
    director.make(type: "complex")
```

### Singleton

Singleton can solve two problem:

- Keep that one specific class have only one instance
- Provide a global access node for this instance

Implementation:

- Set the default initializer as private to prevent other objects use it.
- Create a static func that will call the default initializer if the single instance is null, then this class will have this only one instance.

单例模式解决了可以解决两个问题：

- 保证某个类只有一个实例
- 为这个实例提供全局的访问节点

实现：

- 将默认的构造函数设置为私有，让其它对象无法访问。
- 新建一个静态函数，在这个函数内进行判断，若单例对象为空，则调用私有的构造函数来创建实例对象，实例只被创建一次。

![singleton](../imgs/signleton.png)

```swift
        class FileAccess {
            private static var obj: FileAccess? = nil
            private init(){
                
            }
            public static func getInstance() {
                if obj == nil {
                    obj = FileAccess()
                }
            }
        }

        let firstAccess = FileAccess.getInstance()

        let secondAccess = FileAccess.getInstance()

        // These two access are reference to a same instance.
```

### Prototype

When we want to clone the instance itself, it may create some dependencies between the client and the original class, it will make the code be more complex. So we use “prototype” to **compound the clone operation into the original class itself**. And the clone operation will be easily.

当我们想要完全复制一个对象它本身时，很可能会在调用者和源类之间产生一些复杂当耦合关系。所以我们使用“原型”模式，将对象克隆操作整合在对象当源类之中。这样一来，克隆操作由特定的类自身决定，调用变得简洁。

![prototype](../imgs/prototype.png)

```swift
    // Prototype
    protocol StorePrototype{
        func clone() -> Store
    }
    
    // Concrete Prototype
    class Store: StorePrototype {
        var area: [Int] = [0, 0]
        init(store: StorePrototype) {
            if let s = store as? Store {
                self.area = s.area
            }
        }
        init() {}
        func clone() -> Store {
            return Store(store: self)
        }
    }
    
    // Sub Concrete Prototype
    class BookStore: Store {
        var bookSelfCount = 0
        init(bookStore: StorePrototype) {
            super.init(store: bookStore)
            if let s = bookStore as? BookStore {
                self.bookSelfCount = s.bookSelfCount
            }
        }
        override init() {
            super.init()
        }
        override func clone() -> Store {
            return BookStore(bookStore: self)
        }
        
    }
    
    // Sub Concrete Prototype
    class FlowerStore: Store {
        var flowers = ["Sun Flower", "Rose", "Lily"]
        init(flowerStore: StorePrototype) {
            super.init(store: flowerStore)
            if let s = flowerStore as? FlowerStore {
                self.flowers = s.flowers
            }
        }
        override init() {
            super.init()
        }
        override func clone() -> Store {
            return FlowerStore(flowerStore: self)
        }
    }
    
    let store = Store()
    let obj = FlowerStore()
    
    let copyStore = store.clone()
    let copyObj = obj.clone()        
    // Now it did the deep copy. It copy the whole instance "store" to "copyStore", and copy the whole instace "obj" to copyObj.
```

## Structural

### Adapter

A class called ‘A’ needs to disguise as another class called ‘B’, create a new adapter to extend the target class B and then insert the class A into the adapter, override the methods from the class B in the adapter. So we can use the adapter which has new features from A, just like we are using class B(The adapter extends from class B).

类 A 想要适配类 B，首先使用一个适配器继承类 B ，把类 A 注入适配器，在适配器里面重写需 要模仿的类 B 的方法。最后，在需要用 A 替换 B 的地方，传递一个适配器即可，因为它既具有 A 的性质，又具有 B 的方法，它继承自 B ，可以当作 B 来调用。

```swift
    class Bike {
        var name = "deafult"
        func go() {
            print("Bike Go")
        }
        func addWheel() {
            print("Add wheel for\(name)")
        }
    }
    
    class WaterBike {
        var weight = 0
        func floating() {
            print("The water bike floating on the water")
        }
    }
    
    // now the water bike adapt the bike, inherit the target(Bike) that be disguised.
    class WaterBikeAdapter: Bike {
        var waterBike: WaterBike?
        init(waterBike: WaterBike) {
            self.waterBike = waterBike  // inject the waterBike(which want to disguise as other)
        }
        override func go() {
            print("Now it's the water bike go")
        }
        
    }
    
    func someThingGo(bike: Bike) {
        bike.go()
    }
    // This mode make the "WaterBike" to disguise as a "Bike"
    let waterBikeAdapter = WaterBikeAdapter(waterBike: WaterBike()) // The adapter has a waterBike and  has the method of bike.
    someThingGo(bike: waterBikeAdapter) // Now it can use as a bike.
```

### Bridge

It depart one class or some closing class into two independent layer, the abstract and the implementation. The concrete class A contains an abstract interface B, and **this interface can be implement in different ways**. The A relies on the interface B not the concrete class. We inject different concrete class（has implemented interface B） for A or subclass of A, so we can get an instance of A or subclass with different functionality.

桥接模式将一个大类或者一系列紧密相关的类拆分为**抽象**和**实现**两个独立的层次结构。实体类 A 包含有接口 B，这个接口可以通过不同的方式来实现。实体类 A 依赖于接口 B 而不是具体的类。 我们使用不同的实现了接口 B 的实体类注入到 A 类实体或者 A类的子类的实体中，从而可以得到具有不同功能的 A 实体 或者 A 子类实体。

![bridge](../imgs/bridge.png)

```swift
    // Abstract interface
    protocol Device {
        func isEnable() -> Bool
        func enable()
        func disable()
        func getVolume() -> Int
        func setVolume(_ volumn: Int)
        func getChannel() -> Int
        func setChannel(_ channel: Int)
    }
    
    // Concrete class that contains the interface
    class Remote {
        var device: Device
        init(_ device: Device) {
            self.device = device
        }
        func togglePower() {
            if device.isEnable() {
                device.disable()
            } else {
                device.enable()
            }
        }
        func volumeUp() {
            device.setVolume(device.getVolume() + 1)
        }
        
        func voluomeDown() {
            device.setVolume(device.getVolume() - 1)
        }
        func channelUp() {
            device.setChannel(device.getChannel() + 1)
        }
        
        func channelDown() {
            device.setChannel(device.getChannel() - 1)
        }
    }
    
    // Sub class
    class AdvanceRemote: Remote {
        
        override init(_ device: Device) {
            super.init(device)
        }
        func mute() {
            device.setVolume(0)
        }
    }
    
    // Specific implementation of the interface
    class TV: Device {
        private var displaySize = (1080, 960)
        private var on: Bool = false
        private var volume = 0
        private var channel = 0
        func isEnable() -> Bool {
            return on
        }
    
        func enable() {
            self.on = true
        }
    
        func disable() {
            self.on = false
        }
    
        func getVolume() -> Int {
            return volume
        }
    
        func setVolume(_ volumn: Int) {
            self.volume = volumn
        }
    
        func getChannel() -> Int {
            return channel
        }
    
        func setChannel(_ channel: Int) {
            self.channel = channel
        }
    }
    
    // Specific implementation of the interface
    class Radio: Device {
        private var on: Bool = false
        private var volume = 0
        private var channel = 0
        func isEnable() -> Bool {
            return on
        }
    
        func enable() {
            on = true
        }
    
        func disable() {
            on = false
        }
    
        func getVolume() -> Int {
            return volume
        }
    
        func setVolume(_ volumn: Int) {
            self.volume = volumn
        }
    
        func getChannel() -> Int {
            return channel
        }
    
        func setChannel(_ channel: Int) {
            self.channel = channel
        }
    }
    
    let tv = TV()                     // create a TV
    let remote = AdvanceRemote(tv)    // inject a concrete Device to the remote.
    remote.togglePower()
    remote.channelUp()
    remote.volumeUp()
    remote.mute()
    
    let radio = Radio()
    let remoteForRadio = Remote(radio)
    remoteForRadio.channelUp()
    remoteForRadio.togglePower()
```

### Composite

We want to make the client using the Container or Leafs in a same calling way, so we set the leaf and the container that implement the same interface (Component). And the Container contains some children. In this way we create a model like tree, the client can treat every node similarly.

为了使客户端能够使用相同的方式调用 “容器” 和 “叶子”，我们让容器和叶子都去实现同一个接口 “组件”。并且容器中包含有一些子的容器。通过这种方式，我们可以创建一个类似于一棵树的模型，客户端对每个结点使用相同的对待方式。

![composite](../imgs/command.png)

```swift
    // interface
    protocol Component {
        func execute()
    }
    
    class Leaf: Component {
        
        func execute() {
            print("I'm leaf")
        }
    }
    
    class SubLeaf: Leaf {
        override func execute() {
            print("I'm sub leaf")
        }
    }
    
    // Composite
    class Containner:Component {
        private var children: [Component] = []
        func add(c: Component) {
            children.append(c)
        }
        func remove(c: Component) {
            
        }
        
        func execute() {
            for item in children {
                item.execute()
            }
        }
    }
    
    // We treat the leaf and the containner as the same type of node.
    
    let leaf = Leaf()
    let leafTwo = Leaf()
    let subLeaf = SubLeaf()
    
    let c = Containner()
    c.add(c: leaf)
    c.add(c: leafTwo)
    
    let root = Containner()
    root.add(c: c)
    root.add(c: subLeaf)
    c.execute()
    root.execute()
```

### Decorator

The components and decorators are departed, we can add new components and decorators freely. And we can make different compositions(with different decorations) to be different products.

组件模块和装饰器是分开的，我们可以自由地添加组件和装饰器。我们也可使用不同的组合形式（基于不同装饰方式），来得到不同的产品。

![decorator](../imgs/decorator.png)

```swift
    // Base interface between classes.
    protocol Component {
        var text: String? { get set}
        func execute()
    }
    
    // Concrete Component
    class Bike: Component {
        var text: String?
        func execute() {
            text = "bike"
        }
    }
    
    // Concrete Component
    class Moto: Component {
        var text: String?
    
        func execute() {
            text = "moto"
        }
    }
    
    // Base Decorator
    class BaseDecorator: Component {
        var text: String?
        
        private var c: Component
        func execute() {
            c.execute()
            
            if let t = c.text {
                text = "<" + t + ">"   // Here the text is the property of the current concrete decorator.
            } else {
                text = "<>"
            }
        }    
        
        func getText() -> String {
            if let s = text {
                return s
            } else {
                return ""
            }
        }
        func setText(_ str: String?) {
            text = str
        }
        func getC() -> Component {
            return c
        }
        // inject an instance
        init(_ c: Component) {
            self.c = c
        }
        
    }
    
    // Concrete Decorator
    class PaintDecorator: BaseDecorator {
        override func execute() {
            super.execute()
            extra()
        }
        func extra() {
            let s = getText()
            setText("(Paint)\(s)(Paint)")
        }
    }
    
    // Concrete Decorator
    class AttachTagDecorator: BaseDecorator {
        override func execute() {
            super.execute()
            extra()
        }
        func extra() {
            let s = getText()
            setText("(Tag)\(s)(Tag)")
        }
    }
    
    let a = Bike()
    let tag = AttachTagDecorator(a)
    let paint = PaintDecorator(tag)
    
    paint.execute()                // (Paint)<(Tag)<bike>(Tag)>(Paint)
    print(paint.getText())
    
    let b = Moto()
    let bpaint = PaintDecorator(b)
    let bTag = AttachTagDecorator(bpaint)
    bTag.execute()                // (Tag)<(Paint)<moto>(Paint)>(Tag)
    print(bTag.getText())
    
```

### Facade

The sub-system has many objects and we want to decoupling them with the client’s calling, so we set a class `Facade` and use its instance to interact with the sub-system. The `Facade` just like an inter-media.

 一个子系统有许多对象，而我们想要将这些对象与客户端调用解耦合，所以我们设置一个 “外观” 类，使用外观类的实例来与子系统交互。这个外观类实例就像一个处于中间的媒介。

![facade](../imgs/facade.png)

```swift
    // Sub-System objects
    class VideoFile {
        
    }
    class BitwiseNot {
        
    }
    
    class CvtColor {
        
    }
    
    class Transformer {
        
    }
    
    // Facade
    class VideoConverter {
        func convert() -> File {
            var videoFile = VideoFile()
            var bitwiseNot = BitwiseNot()
            var cvtColor = CvtColor()
            var transformer = Transformer()
            var file = File()
            return file
        }
    }
    class File {
        
    }
    
    let converter = VideoConverter()
    var file = converter.convert()
```

### Flyweight

The flyweight is to make the memory can load more objects. An object may have some properties that is repeating, and it take many memory space, we move these properties into another class(called Flyweight), and the original class just hold a reference of the Flyweight instance.

享元可以使内存能加载更多的对象。一个对象可能会拥有一些重复的、占用空间大的属性，我们将这些属性移到另外的一个类中（这个类称为享元），然后原来的类就只是持有一个享元实例的引用。

![flyweight](../imgs/flyweight.png)

```swift
    // Client
    class Game {
        var bullets: [Bullet] = []
        var factory: Factory
        func creatBullet(_ count: Int) {
            for i in 0..<count {
                var b = Bullet(id: i, position: [Int.random(in: 0...20), Int.random(in: 0...20)], paint: factory.getBulletPaint(sign: BulletPaint.sign))
                bullets.append(b)
            }
        }
        init() {
            factory = Factory()
        }
    }
    
    // Context
    class Bullet {
        var id: Int
        var position: [Int] = [0, 0]
        var paint: BulletPaint
        init(id: Int, position: [Int], paint: BulletPaint) {
            self.id = id
            self.position = position
            self.paint = paint
        }
    }
    
    // Flyweight
    class BulletPaint {
        var picture: String?
        static var sign = 0 
        func changePicture(picture: String) {
            self.picture = picture
            BulletPaint.sign += 1
        }
    }
    
    // Flyweight Factory
    class Factory {
        private var stuff: [BulletPaint] = []
        func getBulletPaint(sign: Int) -> BulletPaint {
            if stuff.count <= sign || stuff[sign] == nil {
                var paint = BulletPaint()
                stuff.append(paint)
                return paint
            }
            return stuff[sign]
        }
    }
    
    var game = Game()
    game.creatBullet(100)
```

### Proxy

A class `Proxy` and a class `Service` both conform to the same protocol `ServiceInterface`, and `Proxy` want to give some of its method to done by `Service`, so `Proxy` just keep a reference of `Service` and call the `Service` when `Proxy` it want to solve some problem.

一个名为 “代理” 的类和一个名为 “服务” 的类都遵守同一个名为 “服务接口” 的协议， 然后代理想要把它的一些方法交给服务去做，进而代理只是持有服务的一个引用，然后在需要解决问题的时候，调用代理来解决。

![proxy](../imgs/proxy.png)

```swift
    // Service Interface
    protocol Work {
        func manageStuff()
        func dispatchSalary()
    }
    
    // Proxy
    class Boss: Work {
        var manager: Manager?
        init(manager: Manager) {
            
        }
        func manageStuff() {
            manager?.manageStuff()
        }
    
        func dispatchSalary() {
            manager?.dispatchSalary()
        }
    
        
    }
    
    // Service
    class Manager: Work {
        func manageStuff() {
            
        }
        func dispatchSalary() {
            
        }
    }
    
    let manager = Manager()
    let boss = Boss(manager: manager)
    boss.dispatchSalary()   // In fact, it's the manager whom dispatch the salary.
```

### Delegate

We move some detail method from class A to class B (delegation), and the B can do some detail by the inputed A’s reference.

我们 A 中实现具体功能的方法移动到 B 中，B 为受委托对象，B 中的方法都是需要输入一个 A 的引用，从而才能让 B 替 A 做一些具体且复杂的操作。相当于 A 把复杂的操作委派给 B 来完成。

 Class `Game` has a `GameDelegation` in it. Then input the `Game` instance to the `Delegation` instance, and the delegation can do some detailed work that may be previously done by the `Game` instance. So it‘s called delegate model.

类 `game` 拥有一个 `GameDelegation` . 接着输入 `Game`  实例到 `Delegation` 的实例，这个委派者可以做一些本来应该由 `Game` 的实例来完成的细节的事情。这样的模式称为委派模式。

![delegate](../imgs/delegation.png)

```swift
    // Delegation
    protocol Delegation {
        func gameDidStart(_ game: Game)
        func game(_ game: Game)
        func gameDidEnd(_ game: Game)
    }
    
    // Game
    protocol Game {
        var delegation: Delegation? { get set }
        func play()
    }
    // Concrete Deleagtion
    class SnakeTracker: Delegation {
        func gameDidStart(_ game: Game) {
        }
        
        func game(_ game: Game) {
            // do something
        }
        func gameDidEnd(_ game: Game) {
        }
    }
    
    // Concrete Game
    class SnakeGame: Game {
        var delegation: Delegation?
        init(_ delegation: Delegation) {
            self.delegation = delegation
        }
        func play() {
            delegation!.gameDidStart(self)
            delegation!.game(self)
            delegation!.gameDidEnd(self)
        }
    }
    let gameTracker = SnakeTracker()
    let game = SnakeGame(gameTracker)
    game.play()  // It's the func of the game, but the detail work is done by the delegation.
```

## Behavioral

### Chain of Responsibility

We set a chain of responsibility. Then it t allow the solver solve the request or sends the request to the next another solver. As the diagram shown below, the handler is one solver of the chain.

我们设定了一条责任链，然后它允许处理者选择处理请求或者将请求发送给下一个其它的处理者。下图中，handler 是责任链上的一个处理者。

![chain of responsibility](../imgs/chainOfResponsibility.png)

```swift
    // Problem wait to be solve
    class Request {
        var priority: Int
        var s: String
        init(p: Int, s: String) {
            priority = p
            self.s = s
        }
    }
    
    // Handler
    protocol Thought {
        func solve(r: Request)
    }
    
    // Base handler
    class People: Thought {
        
        var next: People?
        func setNext(next: People) {
            self.next = next
        }
        func solve(r: Request) {
            if let n = next {
                n.solve(r: r)
            } else {
                print("No people want to solve")
            }
        }
        
    }
    
    // Concrete handler
    class Guard: People {
        override func solve(r: Request) {
            if r.priority < 2 {
                print("Just be sovled by Guard [\(r.s)]")
                print(r.s)
            } else {
                super.solve(r: r)
            }
        }
        
    }
    
    // Concrete handler
    class Commander: People {
        override func solve(r: Request) {
            if r.priority < 4 {
                print("Commander solve [\(r.s)]")
            } else {
                super.solve(r: r)
            }
        }
    }
    
    // Concrete handler
    class Leader: People {
        override func solve(r: Request) {
            if r.priority < 10 {
                print("Leader come and solve [\(r.s)]")
            } else {
                super.solve(r: r)
            }
        }
    }
    
    let g = Guard()
    let c = Commander()
    let l = Leader()
    g.setNext(next: c)
    c.setNext(next: l)
    
    let r = Request(p: 9, s: "Let us in")
    g.solve(r: r)
```

### Command

Create receiver,  then create command and link it to the receiver if needed, then create sender and link it to the specific command. The command mode extract the command from the business, and set a sender and receiver. The sender send message to receiver by calling the command, but not call the receiver directly.

创建接收者，然后创建命令并且在有需要的时候把它连接到接收者，然后创建发送者并且把它连接到命令。命令模式从业务中抽出命令的部分，然后设置一个发送者和接收者。发送者通过调用命令向接受者发送信息，而不是直接调用接收者。

![command](../imgs/command.png)

```swift
    // Message
    class TableInfo {
        var tableNo: Int?
        init(tableNo: Int) {
            self.tableNo = tableNo
        }
    }
    
    // Receiver
    class Cooker {
        func cookMeal(info: TableInfo) {
            print("Cook meal for: \(info.tableNo!)")
        }
        
        func sayHello(info: TableInfo) {
            print("Say hello to: \(info.tableNo!)")
        }
        
        func reheatFood(info: TableInfo) {
            print("Reheat food for: \(info.tableNo!)")
        }
        
    }
    
    // Command
    protocol Command {
        func execute(info: TableInfo)
    }
    
    // Concrete Command
    class ServeCommand: Command {
        var cooker: Cooker?
        init(cooker: Cooker) {
            self.cooker = cooker
        }
        func execute(info: TableInfo) {
            cooker?.sayHello(info: info)
            cooker?.cookMeal(info: info)
        }
    }
    
    // Sender
    class Waiter {
        var command: Command
        init(command: Command) {
            self.command = command
        }
        func executeCommand(info: TableInfo) {
            self.command.execute(info: info)
        }
    }
    
    let cooker = Cooker()
    let command = ServeCommand(cooker: cooker)
    let waiter = Waiter(command: command)
    
    waiter.executeCommand(info: TableInfo(tableNo: 4))
    
    // Print:
    // Say hello to: 4
    // Cook meal for: 4
```

### Iterator

The idea of the iterator is to extract the traversal behavior into an independent iterator object. The collection has some elements, and we use the iterator to get each element step by step.

迭代器的主要思想是将遍历行为抽取为单独的迭代器对象。

![iterator](../imgs/iterator.png)

```swift
    // Element in the collection
    class Node {
        var info: String = ""
        init(_ s: String) {
            self.info = s
        }
    }
    
    // Iterator
    protocol Iterator {
        func hasNext() -> Bool
        func getNext() -> Node
    }
    
    // Concrete iterator
    class PositiveIterator: Iterator {
        
        var collection: Collection
        var curIndex = 0
    
        init(_ c: Collection) {
            self.collection = c
        }
        
        func hasNext() -> Bool {
            if curIndex < collection.nodes.count {
                return  true
            } else {
                return false
            }
        }
        
        func getNext() -> Node {
            var node = collection.nodes[curIndex]
            curIndex += 1
            return node
        }
        
    }
    
    // Concrete iterator
    class RevertIerator: Iterator {
        var collection: Collection
        var curIndex = 0
        
        init(_ c: Collection) {
            self.collection = c
            self.curIndex = collection.nodes.count - 1
        }
        
        func hasNext() -> Bool {
            if curIndex >= 0{
                return  true
            } else {
                return false
            }
        }
        
        func getNext() -> Node {
            var node = collection.nodes[curIndex]
            curIndex -= 1
            return node
        }
    }
    
    // Collection
    protocol Collection {
        var nodes: [Node] { get set }
        func getIterator() -> Iterator
        func getRevertIterator() -> Iterator
    }
    
    // Concrete collecton
    class NodesCollection: Collection {
        var nodes: [Node]
        init() {
            self.nodes = []
        }
        
        func append(_ n: Node) {
            nodes.append(n)
        }
        
        func getIterator() -> Iterator {
            return PositiveIterator(self)
        }
        
        func getRevertIterator() -> Iterator {
            return RevertIerator(self)
        }
    }
    
    let str = "hello world"
    let collection = NodesCollection()
    for s in str {
        collection.append(Node(String(s)))
    }
    
    var it = collection.getIterator()
    
    while (it.hasNext()) {
        var n = it.getNext()
        print(n.info, terminator: " ")
    }
    
    print()
    
    it = collection.getRevertIterator()
    
    while (it.hasNext()) {
        var n = it.getNext()
        print(n.info, terminator: "_")
    }
```

### Mediator

The mediator constrains the interaction between the component, the components can only notify the mediator and the mediator can manage the action of all the components it contains.

中介者限制了组件之间的交互，组件只能通知中介一些指令，然后中介者管理它内部拥有的所有组件的数据操作与行为。

![mediator](../imgs/mediator.png)

```swift
    // Component basic class
    class Component {
        var m: Mediator
        init(mediator: Mediator) {
            self.m = mediator
            m.notify(component: self)
        }
    }
    
    // Concrete component
    class Button: Component {
        func click() {
            m.notify(component: self)
        }
    }
    // Concrete component
    class CheckBox: Component {
        var check = false
        func isCheck() -> Bool {
            return check
        }
    }
    // Concrete component
    class PasswordText: Component {
        var text: String = ""
        func getText() -> String{
            return text
        }
        
    }
    // Concrete component
    class UserNameText: Component {
        var text: String = ""
        func getText() -> String{
            return text
        }
        
    }
    
    protocol Mediator {
        func notify(component: Component)
    }
    
    // Concrete mediator
    class LoginMediator: Mediator {
        var btnLogin: Button?
        var tickSavePassword: CheckBox?
        var password: PasswordText?
        var userName: UserNameText?
        func notify(component: Component) {
            if component is Button {
                var btn = component as! Button
                if self.btnLogin == nil {
                    // self btn initialize
                    self.btnLogin = btn
                    return 
                }
                
                if (canLogin(userName: userName?.getText(), password: password?.getText())) {
                    print("Login success")
                    if let wantSave = tickSavePassword?.isCheck() {
                        // save the userName and password
                    }
                } else {
                    print("Login failed")
                }
            } else if component is CheckBox {
                var box = component as! CheckBox
                self.tickSavePassword = box
            } else if component is UserNameText {
                var userName = component as! UserNameText
                self.userName = userName
            } else if component is PasswordText {
                var password = component as! PasswordText 
                self.password = password
            }
            
        }
    }
    
    // The function check whether the username and password match
    func canLogin(userName: String?, password: String?) -> Bool {
        if userName == nil || password == nil {
            return false
        }
        if let name = userName, let word = password{
            if name == "hello" && word == "Mike" {
                return true
            } else {
                return false
            }
        } 
        return false
    }
    
    let mediator = LoginMediator()
    let userName = UserNameText(mediator: mediator)
    let password = PasswordText(mediator: mediator)
    let tickSavePassword = CheckBox(mediator: mediator)
    let btnLogin = Button(mediator: mediator)
    
    userName.text = "hello"
    password.text = "Mike"
    tickSavePassword.check = true
    
    btnLogin.click()
```

### Memento

It allow to save or recover the object state before without expose the object’s implemented detail. The originator can create the snapshot of itself and can restore the snapshot when it need. The memento is a value object of the originator’s snapshot state, it’s built by the initializer and pass the information by only one time. The caretaker can only use the originator’s `makeMemento()` and `restore()`.

它允许在不暴露对象实现细节的情况下，保存和恢复对象之前的状态。原发器可以创建它自己的快照并且在需要的时候恢复快照到自身。备忘录是一个用于存放原发器快照状态的值对象，它只通过构造函数传递状态信息。负责人只可以使用原发器的 “创建快照” 和 “恢复状态” 的方法。

![memento](../imgs/memento.png)

```swift
    // Originator
    protocol Animal {
        func getMemento() -> Memento
    }
    
    // Memento
    protocol Memento {
        func restore()
    }
    
    // Concrete originator
    class Dog: Animal {
        private var age: Int
        private var weight: Float
        private var legCount: Int
        
        init(age: Int, weight: Float, legCount: Int) {
            self.age = age
            self.weight = weight
            self.legCount = legCount
        }
        func getMemento() -> Memento {
            return DogData(dog: self, age: age, weight: weight, legCount: legCount)
        }
        func setAge(age: Int) {
            self.age = age
        }
        func setWeight(weight: Float) {
            self.weight = weight
        }
        func setLegCount(legCount: Int) {
            self.legCount = legCount
        }
        
        func info() -> String {
            return "age: \(age) weight: \(weight) legCount: \(legCount)"
        }
    }
    
    // Concrete memento
    class DogData: Memento {
        private var age: Int
        private var weight: Float
        private var legCount: Int
        
        private var dog: Dog?
        
        init(dog: Dog, age: Int, weight: Float, legCount: Int) {
            self.dog = dog
            self.age = age
            self.weight = weight
            self.legCount = legCount
        }
        
        func restore() {
            dog?.setAge(age: age)
            dog?.setWeight(weight: weight)
            dog?.setLegCount(legCount: legCount)
        }
        
    }
    
    // CareTaker
    class Recored {
        var dog: Dog
        var history: [Memento] = []
        init(dog: Dog) {
            self.dog = dog
        }
        func save() {
            var backup = dog.getMemento()
            history.append(backup)
            if (history.count > 10) {
                history.remove(at: 0)
            }
        }
        
        func undo() {
            if history.count <= 0 {
                return 
            }
            var data = history[history.count - 1]
            data.restore()
            history.remove(at: history.count - 1)
        }
        
    }
    
    let dog = Dog(age: 2, weight: 15, legCount: 4)
    let record = Recored(dog: dog)
    record.save()
    print(dog.info())
    
    dog.setAge(age: 4)
    dog.setWeight(weight: 20)
    print(dog.info())
    
    record.undo()
    print(dog.info())
```

### Observer

The publisher has some subscribers reference, it can add subscriber, remove subscriber, and notify all subscriber.    

发布者持有一些订阅者的引用，它可以添加订阅者，也可以移除订阅者，或者是通知所有的订阅者。

![observer](../imgs/observer.png)

```swift
    class Message {
        var text: String = ""
        init(_ m: String) {
            self.text = m
        }
    }
    
    class Event {
        var text: String = ""
        init(_ e: String) {
            self.text = e
        }
    }
    
    // Subscriber
    protocol Listener {
        func update(message: Message)
    }
    
    // Publisher
    protocol Publisher {
        func subscribe(event: Event, listener: Listener)
        func remove(event: Event)
        func notify(event: Event, message: Message)
        func notifyAll(message: Message)
    }
    
    // Concrete listener
    class ListenerA: Listener {
        func update(message: Message) {
            print("A do: \(message.text)")
        }
    }
    
    // Concrete listener
    class ListenerB: Listener {
        func update(message: Message) {
            print("B do: \(message.text)")
        }
    }
    
    // Concrete publisher
    class ViewPublisher: Publisher {
        var listeners: [String: Listener] = [:]
        func subscribe(event: Event, listener: Listener) {
            listeners[event.text] = listener
        }
    
        func remove(event: Event) {
            listeners.removeValue(forKey: event.text)
        }
    
        func notify(event: Event, message: Message) {
            listeners[event.text]?.update(message: message)
        }
        
        func notifyAll(message: Message) {
            for item in listeners.values {
                item.update(message: message)
            }  
        }
     
    }
    
    let publisher = ViewPublisher()
    let a = ListenerA()
    let b = ListenerB()
    publisher.subscribe(event: Event("look"), listener: a)
    publisher.subscribe(event: Event("find"), listener: b)
    
    publisher.notify(event: Event("look"), message: Message("Firstly, welcome."))
    print()
    
    publisher.notifyAll(message: Message("Hello world!"))
    print()
    
    publisher.remove(event: Event("look"))
    
    publisher.notifyAll(message: Message("The end."))
```

### State

 The state mode enable the class to change its behavior when its inner state changed. The `StateContext` only do the switch work, and the detail work is done by `State` object. The context manage the statements in a high level, it can switch some different state when we need. The context inject a reference to the concrete state object to enable the state to delegate the detail work.
 状态模式中，当类的状态改变时，类的行为随着改变。状态上下文类只执行状态切换工作，具体的工作细节交给状态对象去执行。上下文对象在一个较高的层次管理着自身的状态，它可以在我们有需要的时候切换不同的状态。上下文对象注入了一个自己的引用给状态对象，使得状态对象可以代理在切换状态时候的细节工作。

![state](../imgs/state.png)

```swift
    // Abstract state
    class State {
        var context: Context?
        var name: String = ""
        init(context: Context?) {
            self.context = context
        }
        
    }
    
    // Concrete state
    class NewState: State {
        override init(context: Context?) {
            super.init(context: context)
            self.name = "New"
        }
        func start() {
            print("Call start")
            if self.name == "New" {
                context?.setState(state: RunnableState(context: context))
            } else {
                print("Can't go to runnable")
            }
        }
    }
    
    // Concrete state
    class RunnableState: State {
        override init(context: Context?) {
            super.init(context: context)
            self.name = "Runnable"
        }
        func getCpu() {
            if self.name == "Runnable" {
                context?.setState(state: RunningState(context: context))
            } else {
                print("Can't go to running")
            }
        }
    }
    
    // Concrete state
    class RunningState: State {
        override init(context: Context?) {
            super.init(context: context)
            self.name = "Running"
        }
        
        func suspend() {
            if self.name == "Running" {
                context?.setState(state: BlockState(context: context))
            }
        }
        
        func stop() {
            if self.name == "Running" {
                context?.setState(state: DeadState(context: context))
            }
        }
        
    }
    
    // Concrete state
    class  BlockState: State {
        override init(context: Context?) {
            super.init(context: context)
            self.name = "Block"
        }
        
        func resume() {
            if name == "Block" {
                context?.setState(state: RunnableState(context: context))
            }
        }
    }
    
    // Concrete state
    class DeadState: State {
        override init(context: Context?) {
            super.init(context: context)
            self.name = "Dead"
        }
        
        
    }
    
    // Thread Context
    class Context {
        private var state: State?
        init() {
            self.state = NewState(context: self)
        }
        
        func setState(state: State) {
            self.state = state
        }
        
        func getState() -> State? {
            return self.state
        }
        
        func start() {
            if let s = self.state as? NewState {
                s.start()
            }
        }
        
        func suspend() {
            if let s = self.state as?  RunningState {
                s.suspend()
            }
        }
        
        
        func resume() {
            if let s = self.state as? BlockState {
                s.resume()
            }
        }
        
        func getCpu() {
            if let s = self.state as? RunnableState {
                s.getCpu()  // It will switch to the running state.
            }
        }
        func  stop() {
            if let s = self.state as? RunningState {
                s.stop()
            }
        }
        
        
    }
    
    let context = Context()
    context.start()   // It will be runnable then.
    context.getCpu()  // It will be running then.
    context.suspend() // It will be blocked then.
    context.resume()  // It will be runnable then.
    context.getCpu()  // It will be running then.
    context.stop()    // It will be dead then.

```

### Strategy

Strategy mode set some solution class, and each solution can be substituted by other solution easily. When we face different problem, we choose different solution to solve problem. The `Problem` class hold a reference of `Strategy` interface to use different strategy.
策略模式设定了一些算法类，每个算法类可以被其它算法类所替换。当我们面对不同的问题时，我们选择不同的策略来解决问题。“问题” 类持有 “策略”接口， 以便使用不同的策略。

![strategy](../imgs/strategy.png)

```swift
    protocol Strategy {
        func sort(_ nums: inout [Int]) -> [Int]
    }
    
    // Strategy A
    class SelectedSort: Strategy {
        func sort(_ nums: inout [Int]) -> [Int] {
            print("Selected sort")
            var len = nums.count
            if len <= 1 {
                return nums
            }
            for i in 0..<len {
                var minIndex = i
                for j in (i + 1)..<len {
                    if nums[j] < nums[minIndex] {
                        minIndex = j
                    }
                }
                var tmp = nums[i]
                nums[i] = nums[minIndex]
                nums[minIndex] = tmp
            }
            return nums
        }
    }
    
    // Strategy B
    class InsertSort: Strategy {
        func sort(_ nums: inout [Int]) -> [Int] {
            print("Insert sort")
            var len = nums.count
            if len <= 1 {
                return nums
            }
            for i in 1..<len {   
                var left = i - 1
                while true {
                    if left < 0 || nums[i] > nums[left] {
                        var cover = i - 1
                        var t = nums[i]
                        while cover > left {
                            nums[cover + 1] = nums[cover] 
                            cover -= 1
                        } 
                        nums[left + 1] = t
                        break
                    } else {
                        left -= 1
                    }
                }
            }
            return nums
        }
    }
    
    class Problem {
        private var strategy: Strategy
        init(strategy: Strategy) {
            self.strategy = strategy
        }
        func solve(_ nums: inout [Int]) -> [Int] {
            strategy.sort(&nums)
        }
        
        func changeStrategy(strategy: Strategy) {
            self.strategy = strategy
        }
    }
    
    var nums = [2, 6, 1, 5, 0, 3]
    
    var a = SelectedSort()
    var b = InsertSort()
    var problem = Problem(strategy: a)
    problem.solve(&nums)
    print(nums)
    
    nums.shuffle()
    print(nums)
    
    problem.changeStrategy(strategy: b)
    problem.solve(&nums)
    print(nums)
```

### Template Method

The super class define an algorithm structure, and the subclass can override the method without changing the structure. Depart an algorithm into a series of step, and rewrite these step as method, then call these method in the template method.

超类定义了一个算法框架，子类可以在不改动框架的情况下依据需要来重写方法。把算法拆分为一系列的步骤，把这些步骤写成方法，然后可以在模板方法中调用这些方法。

![templateMethod](../imgs/templateMethod.png)

```swift
    // Abstract class
    class House {
    // Use final to prevent the subclass override the template method.
        final func build(){
            makeRoot()
            makeWall()
            makeWindows()
            makeRoof()
        }
        
        func makeRoot() {
            print("Use cement to construct root.")
        }
        
        func makeWall() {
            print("Add white wall")
        }
        
        func makeWindows() {
            print("Add window")
        }
        
        func makeRoof() {
            print("Add smooth roof")
        }
    }
    
    // Concrete class A
    class EastenHouse: House {
        override func makeWall() {
            print("Add red wall.")
        }
        
        override func makeWindows() {
            print("Add wooden windows.")
        }
        
        override func makeRoof() {
            print("Add wooden roof.")
        }
        
    }
    
    // Concrete class B
    class WestenHouse: House {
        
        override func makeWindows() {
            print("Add iron windows")
        }
        
        
        override func makeRoof() {
            print("Add cement roof")
        }
    }
    
    let house = EastenHouse()
    house.build()
    
    let houseB = WestenHouse()
    houseB.build()
```

### Visitor

Visitor mode can isolate the algorithm and the object which algorithm affect. (Depart the server and the visitor). It put the new behaviors into a `Visitor` class, then pass the existed object’s reference to the visitor to do the behaviors. It just visit the data of the object, but not change the original data.

访问者模式可以隔离算法和被这个算法影响的对象（分离服务者和访问者）。它将新的行为放置在一个称为 “访问者” 的对象中，然后传递原有对象的引用来执行一些行为。它只是访问对象数据，而没有改变源数据。

![vistor](../imgs/vistor.png)

```swift
    protocol Visitor {
        func visit(d: Dog)
        func visit(c: Cat)
        func visit(h: Horse)
    }
    
    protocol Element {
        func accept(v: Visitor)
    }
    
    // Element A
    class Dog: Element {
        var name = "dog"
        var age = 0
        func accept(v: Visitor) {
            v.visit(d: self)
        }
    }
    
    // Element B
    class Cat: Element {
        var name = "cat"
        var age = 1
        func accept(v: Visitor) {
            v.visit(c: self)
        }
    }
    
    // Element C
    class Horse: Element {
        var name = "horse"
        var age = 2
        func accept(v: Visitor) {
            v.visit(h: self)
        }
    }
    
    // Visitor 1
    class NameVisitor: Visitor {
        func visit(d: Dog) {
            print(d.name)
        }
    
        func visit(c: Cat) {
            print(c.name)
        }
    
        func visit(h: Horse) {
            print(h.name)
        }
    }
    
    // Visitor 2
    class AgeVisitor: Visitor {
        func visit(d: Dog) {
            print("Dog age: \(d.age)")
        }
    
        func visit(c: Cat) {
            print("Cat age: \(c.age)")
        }
    
        func visit(h: Horse) {
            print("Horse age: \(h.age)")
        }
    }
    
    let dog = Dog()
    let cat = Cat()
    let horse = Horse()
    
    let nameVisitor = NameVisitor()
    let ageVisitor = AgeVisitor()
    
    dog.accept(v: nameVisitor)
    cat.accept(v: nameVisitor)
    horse.accept(v: nameVisitor)
    
    dog.accept(v: ageVisitor)
    cat.accept(v: ageVisitor)
    horse.accept(v: ageVisitor)
```

## 模式的特征与区别

### 结构型

- 适配器模式的目的是为了让类 A 去模仿类 B，从而在需要类 B 的地方可以直接使用类 A 替代，适配器C既具有 B 的属性，又看起来像是 B （因为适配器继承自类 B）
- 桥接模式是具体的类 A 中具有抽象接口 B，构造 A 的时候注入一个 B 的实体对象，那么可以在使用对象 A 的时候，可以**调用接口 B** 来实现对应功能，这样的类 A 是依赖于接口而不是具体的类。
- 桥接模式是两个类 A 与 B 之间，使用一个接口，由 B 实现， 注入 A。
- 组合模式是组件模块与容器（抽象接口）都实现同一接口，容器里面可以持有多个接口实例（由组件模块实例来注入），形成树状结构。
- 装饰器模式是组件与装饰器（抽象接口）都实现同一接口，装饰器里面持有一个接口实例（由组件模块实例来注入）， 装饰器对持有的实例添加附带功能。
- 委托模式（delegate）是让一个对象扮演另外一个对象的行为。委托对象暂时持有到另外对象的引用。在适当时间发消息给另外对象。
- 代理模式（proxy）比委托更为严格，若干对象实现同一接口。

### 行为型

- 责任链模式（chain of responsibility）模式中的每一个对象持有指向另一对象的引用 A（用于转移责任），询问当前对象，当前对象若有能力处理事务则处理，若没有能力处理事务则把事务移交给另外一个对象（引用 A 所指）处理。
- 命令模式（command) 发送者和接收者使用命令进行间接沟通，常用于视图和控制器的分离（视图为发送者，控制器为接收者）。若与责任链结合，则为命令在传递给接收者的时候，是传递到接收者链上，从而寻求潜在的接收者。
- 中介者模式（mediator）设置一个中介者来管理那些来自组件的调用，中介者拥有各个组件的引用，站在更高的层次上对组件进行管理。组件之间通过中介者来发生交互，而不是组件之间进行耦合。
- 备忘录模式（memento），为原发器添加一个备忘录对象用于存放原发器的的状态信息，使用一个负责人对象来管理备忘录的增加与回滚，可以对原发器的版本进行控制。
- 观察者模式（observer），发布者持有多个订阅者的引用，当有需要发布新消息时，准确地向每个订阅者发送信息。

    中介者和观察者很像，但是，中介者主要功能是消除一系列的组件之间的依赖，用于管理组件之间的调用，而观察者强调的是发布者向各个订阅的订阅者分发信息。

- 状态模式（状态模式）与策略模式（strategy）关键区别：

    状态模式中的特定状态知道其它状态的存在，能从一个状态转移到另一状态。

    策略模式中某个策略不知道其它策略的存在。

- 模版方法（template method) 中，超类定义了若干模板框架方法（固定的）和步骤方法，子类不能修改框架方法，只能重写步骤方法。模板方法中可以调用详细的步骤方法。工厂模式是模版方法的特殊形式。
- 访问者（visitor) 将操作交给其它的类（访问者类），允许访问者进行访问。它是命令模式的加强版本。访问者模式中，业务在需要使用访问服务的时候，传递自己的引用给访问者，而代理模式和委托模式是将自身引用注入到受托付到对象中，移交自己的权利。