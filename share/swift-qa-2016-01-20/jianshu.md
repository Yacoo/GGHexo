每周 Swift 社区问答 2016-01-20"


作者：[shanks](http://codebuild.me)

本周共整理了 5 个问题，程序员的问题也是千奇百怪。。大家可以看看本周整理的几个问题。。好好感受下吧

本周整理问题如下：

* [Can i have 2 types on parameter in swift?](#Q1)
* [Swift: why aren't all variables lazy by default?](#Q2)
* [Sorting Dictionarys in Arrays](#Q3)
* [Swift: Factory Pattern, Subclass methods are not visible](#Q4)
* [In Swift, how do I prevent a function from being called on a subclass?](#Q5)




对应的代码都放到了 github 上，有兴趣的同学可以下载下来研究：[点击下载](https://github.com/SwiftGGTeam/SwiftCommunityWeeklyQA/tree/master/20160120)

<!--more-->

<a name="Q1"></a>

## Question1: Can i have 2 types on parameter in swift?

[Q1链接地址](http://stackoverflow.com/questions/34786897/can-i-have-2-types-on-parameter-in-swift)



### 问题描述


楼主想根据传入参数类型，来做不同的处理，见以下的伪代码：

    func (parameter: unknownType){
        if(typeof parameter == Int){
            //do this
        }else if(typeof parameter == String){
            //do that
        }
    }

### 问题解答

初看这个问题，以为用泛型就可以解决。但是想到要根据类型做不同的处理，也就是说，需要判断具体类型，然后做一些操作。而泛型跟类型无关。所以用泛型解决不了这个问题。让我们来看看跟帖们的解决方案吧。


* 方案一：使用重载

    class Example
    {
        func method(a : String) -> String {
            return a;
        }
        func method(a : UInt) -> String {
            return "{\(a)}"
        }
    }
    
    Example().method("Foo") // "Foo"
    Example().method(123) // "{123}"

* 方案二：使用 Any

    func foo(bar: Any){
        switch(bar) {
            case let a as String:
                /* String类型的操作 */
                print("The bar is a String, bar = " + a)
            case let a as Int:
                /* Int 类型操作 */
                print("The bar is an Int, bar = \(a)")
            case _ : print("The bar is neither an Int nor a String, bar = \(bar)")
        }
    }
    
    /* Example */
    var myString = "Hello"
    var myInt = 1
    var myDouble = 1.5
    
    foo(myString) // The bar is a String, bar = Hello
    foo(myInt)    // The bar is an Int, bar = 1
    foo(myDouble) // The bar is neither an Int nor a String, bar = 1.5

* 方案三：使用协议

    protocol MyProtocol {
        func doSomething()
    }
    
    extension Int: MyProtocol {
        func doSomething() {
            print("I am an int")
        }
    }
    
    extension String: MyProtocol {
        func doSomething() {
            print("I am a string")
        }
    }
    
    func doSomething(parameter: MyProtocol) {
        parameter.doSomething()
    }
    
    
    doSomething(1) // I am an int
    doSomething("a") // I am a string
    //doSomething(14.0) // 因为Double类型没有扩展实现 MyProtocol 协议，所以报错

<a name="Q2"></a>

## Question2: Swift: why aren't all variables lazy by default?

[Q2链接地址](http://stackoverflow.com/questions/34841915/swift-why-arent-all-variables-lazy-by-default)


### 问题描述

楼主提问，为什么 lazy 关键字不能作为默认的存在。他列出了自己的分析：

先看看 2 行代码，更直观的进行比较：

    var networkManager = NetworkManager.sharedInstance()
    
    var lazy networkManager = NetworkManager.sharedInstance()

2行代码共有的特性点：

* 可以通过右值的计算得到值
* 可以直接赋值

lazy 的好处：

* 可以引用 self
* 在使用时才计算得到值
* 如果没有使用它，就不会计算它

非 lazy 的好处：

* 无


由此分析可以得出结论：lazy 可以默认的定义存在。




### 问题解答


跟帖中发表了一些看法，值得参考：

* lazy 增加了程序的不确定性，因为并知道，何时会用到 lazy 定义的变量。
* lazy 使用场景是在需要得到一个计算量很大或者占用资源很大的变量的时候使用的，一般情况下，不需要用到 lazy
* 在 Swift 函数式编程中，用到一些带有 lazy 性质的特性，比如 map 和filter 就是后计算的。lazy 性质的特性在 Swift 用的比较多。但是 lazy 不是线程安全的。



<a name="Q3"></a>

## Question3: Sorting Dictionarys in Arrays

[Q3链接地址](http://stackoverflow.com/questions/34838580/sorting-dictionarys-in-arrays)

### 问题描述

楼主的问题是，根据字典中某一个key进行排序，按照顺序输出结果，见下面代码：
PS：帖子中代码是不能运行的，因为product1的类型编译器推断不出来，必须显式指定为：[String: Any]，跟帖中定义为[String: AnyObject] 也是错的。因为value都是值类型的，不能用AnyObject来表示。

    // 输入
    var product1: [String: Any] = ["name": "milk", "price": 3.2]
    var product2: [String: Any] = ["name": "bread", "price": 2.9]
    var product3: [String: Any] = ["name": "meat", "price": 4.1]
    var product4: [String: Any] = ["name": "sweets", "price": 1.0]
    // 输出
    var priceArray = [1.0,2.9,3.2,4.1]
    var nameArray = ["sweets","bread","milk","meat"]

### 问题解答

* 解法一：传统的思路来解决

    let product11: [String: Any] = ["name": "milk","price": 3.2]
    let product21: [String: Any] = ["name": "bread","price": 2.9]
    let product31: [String: Any] = ["name": "meat","price": 4.1]
    let product41: [String: Any] = ["name": "sweets", "price": 1.0]
    
    var tempDictArray = [[String: Any]]()
    tempDictArray.append(product11)
    tempDictArray.append(product21)
    tempDictArray.append(product31)
    tempDictArray.append(product41)
    
    func priceSort(dict1: [String: Any], dict2: [String: Any]) -> Bool {
        let price1 = dict1["price"] as? Float ?? 0
        let price2 = dict2["price"] as? Float ?? 0
        return price1 < price2
    }
    
    
    tempDictArray = tempDictArray.sort(priceSort)
    
    var priceArray1 = [Float]()
    var nameArray1 = [String]()
    
    for item in tempDictArray {
        
        let price = item["price"] as? Float ?? 0
        let name = item["name"] as! String
        
        priceArray1.append(price)
        nameArray1.append(name)
    }
    priceArray1    //[1, 2.9, 3.2, 4.1]
    nameArray1     //["sweets", "bread", "milk", "meat"]

* 解法二：推荐解法

1、使用 struct 替代字典，更加直观和自然

    struct Product {
        let name: String
        let price: Double
        
        init?(dict:[String:Any]) {
            guard
                let name = dict["name"] as? String,
                let price = dict["price"] as? Double else {
                    return nil
            }
            self.name = name
            self.price = price
        }
    }
2、定义输入


    let productsDict = [product11, product21, product3, product4]

3、排序得到结构体数组

    let products = productsDict.flatMap { Product(dict: $0) }.sort{ $0.price < $1.price }

4、输出结果

    let prices2 = products.map { $0.price }
    let names2 = products.map { $0.name }

* 解法三：与第二种大同小异，有一些细节的差异，大家可以参考一下：

    // Define a structure to hold your data.
    // The protocol CustomStringConvertible allows you to format the
    // data as a nice string for printing.
    
    struct Product1: CustomStringConvertible {
        var name: String
        var price: Double
        var description: String { return "name: \(name), price: \(price)" }
        
        init?(info: [String: Any]) {
            guard let name = info["name"] as? String,
                let price = info["price"] as? Double else { return nil }
            self.name = name
            self.price = price
        }
    }
    
    
    var products1 = [Product1(info: product1), Product1(info: product2), Product1(info: product3), Product1(info: product4)].flatMap {$0}
    
    products1.sortInPlace { $0.price < $1.price }
    
    products1.forEach { print($0) }
    
    let priceArray2 = products1.map { $0.price }  // [1.0, 2.9, 3.2, 4.1]
    let nameArray2 = products1.map { $0.name }    // ["sweets", "bread", "milk", "meat"]

<a name="Q4"></a>

## Question4: Swift: Factory Pattern, Subclass methods are not visible

### 问题链接

[Q4链接地址](http://stackoverflow.com/questions/34825828/swift-factory-pattern-subclass-methods-are-not-visible)

### 问题描述

楼主想实现工厂模式，于是写下以下代码，报错：

    protocol IInterface {
        func InterfaceMethod() -> Void
    }
    
    public class SubClass: IInterface {
        init() {}
        
        func InterfaceMethod() { }
        
        public func SubClassMethod() { }
    }
    
    public class Factory {
        class func createFactory() -> IInterface {
            return SubClass()
        }
    }
    
    let mgr = Factory.createFactory()
    
    mgr.InterfaceMethod()
    //mgr.SubClassMethod() // 报错

### 问题解答

楼主对工厂模式看来理解不深刻，跟帖中做了解释，首先楼主实现的的简单工厂模式，简单工厂会根据对工厂类不同的调用产生最终的使用的类，见跟帖中的例子：

    protocol Animal {
        func voice() -> String
    }
    
    class Dog: Animal {
        func voice() -> String {
            return "bark"
        }
        
        func sit() {
        }
    }
    
    class Cat: Animal {
        func voice() -> String {
            return "meow"
        }
    }
    
    class AnimalFactory {
        func getAnimal() -> Animal {
            return Dog()
        }
    }
    
    
    let animalFactory = AnimalFactory()
    animalFactory.getAnimal()

稍微做一下改造，就可以根据传入参数，产生不同的 Animal

    enum AnimalType {
        case DogType
        case CatType
    }
    
    class AnimalFactory1 {
        func getAnimal(type: AnimalType) -> Animal {
            
            switch(type) {
                case .DogType:
                    return Dog()
                case .CatType:
                    return Cat()
            }
        }
    }
    
    let animalFactory1 = AnimalFactory1()
    animalFactory1.getAnimal(AnimalType.CatType)

还有其他 2 种相关的模式：工厂方法模式和抽象工厂模式，具体的模式介绍，请参看[这里](http://www.cnblogs.com/stonehat/archive/2012/04/16/2451891.html) , 大家有兴趣可以自己用 swift 实现。欢迎跟帖！








<a name="Q5"></a>

## Question5: In Swift, how do I prevent a function from being called on a subclass?

### 问题链接

[Q5链接地址](http://stackoverflow.com/questions/34802374/in-swift-how-do-i-prevent-a-function-from-being-called-on-a-subclass)

### 问题描述

楼主的问题是，给了一个子类的实例，如何在编译器层面，阻止子类去调用或者实现父类的方法。
楼主说的应用场景是父类实现了 add 和 move 操作，子类通过另外一个方法 change ，调用了父类的 add 和 move 方法。这时候子类的实例，只需要调用 change 方法就可以了，没必要调用 add 和 remove 了。

    class Parent {
        
        func doThis() { print("Parent Did This") }
        func doThat() { print("Parent Did That") }
        
    }
    
    class Child: Parent {
        
        override func doThis() {
            print("Child Did This")
        }
        
        // I want this to be a compile time error
        override func doThat() {
            print("Not Supported")
            return
        }
        
    }

### 问题解答

跟帖中提到了使用 @available 来声明访问的可用性，见代码：

    class Parent {
        func doThis() { print("Parent Did This") }
        func doThat() { print("Parent Did That") }
    }
    
    class Child: Parent {
        override func doThis() {
            print("Child Did This")
        }
        
        @available(*, unavailable, message="Child can't doThat")
        override func doThat() {
            print("Not Supported")
        }
    }
    
    
    let parent = Parent()
    parent.doThis()
    parent.doThat()
    
    let child = Child()
    child.doThis()
    child.doThat() // 编译报错


### 问题思考

用父类子类来定义这个问题的结构是有问题的。可以另外定义没有继承关系一个类来包裹 add 和 remove 操作。即可以解决楼主的问题。当然如果真的需要用到继承，也是可以的，用上面的方法即可。不过目前的趋势都是少用继承，多用 protocol 来解决问题。。

















