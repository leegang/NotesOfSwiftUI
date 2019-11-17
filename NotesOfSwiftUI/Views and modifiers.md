# 1. Views 和 modifiers

<!-- TOC -->

- [Views 和 modifiers](#views-和-modifiers)
    - [为什么SwiftUI 的views使用结构体structs ?](#为什么swiftui-的views使用结构体structs-)
    - [SwiftUI主视图的后面有什么？](#swiftui主视图的后面有什么)
    - [为什么修饰器顺序很重要](#为什么修饰器顺序很重要)
    - [为什么 SwiftUI 的view type使用 “some View” ?](#为什么-swiftui-的view-type使用-some-view-)
        - [拓展：SwiftUI如何处理Stack？](#拓展swiftui如何处理stack)
    - [条件修饰器 condition modifiers](#条件修饰器-condition-modifiers)
    - [环境修饰器Environment modifiers](#环境修饰器environment-modifiers)
    - [Views作为属性 Views as properties](#views作为属性-views-as-properties)
    - [Views组件 View composition](#views组件-view-composition)
    - [自定义修饰器 Custom modifiers](#自定义修饰器-custom-modifiers)
    - [自定义容器 Custom containers](#自定义容器-custom-containers)
        - [拓展：试图生成器view builders](#拓展试图生成器view-builders)

<!-- /TOC -->

## 1.1. 为什么SwiftUI 的views使用结构体structs ?

如果您曾经用UIKit或AppKit（Apple最初用于iOS和macOS的用户界面框架）编程，您会知道它们使用Class而View来构造视图。 SwiftUI并非如此：我们更喜欢将结构体Struct用于整体视图View，这有两个原因。

首先，性能是一个个原因：结构比类更简单，更快。我之所以说这是一个原因，是因为很多人认为这是SwiftUI使用结构的主要原因，而实际上这只是全局的一部分。

在UIKit中，每个视图都是从UIView类继承的，该类具有许多属性和方法–背景色，确定其放置方式的约束，用于将其内容呈现到其中的图层等等。其中有很多，每个UIView和UIView子类都必须具有它们，因为继承就是这样。

通常这没什么问题，但是有一个名为UIStackView的特定子类，它类似于SwiftUI中的VStack和HStack。在UIKit中，UIStackView是一种非渲染视图类型，旨在简化布局，但这意味着即使由于继承而具有背景色，也​​从未真正使用过背景色。

没有什么继承自父类，或祖类，或曾祖类，等等额外的值-它包含的只有是你能看到的。

得益于现代iphone的强大功能，我毫不犹豫地就能创造出1000个整数，甚至10万个整数——一眨眼的功夫就能实现。1000甚至100000的SwiftUI的Views也是如此;它们的速度如此之快，以至于不用思考。

然而，尽管性能很重要，但作为结构的视图还有更重要的东西：它迫使我们考虑以一种干净的方式隔离状态。Class类能够自由地更改它们的值，这可能导致更混乱的代码——SwiftUI怎么知道何时更改值以更新UI？

生成不随时间变化的视图，SwiftUI鼓励我们转向功能更强的设计方法：我们的视图变得简单、惰性，可以将数据转换为UI，而不是可能失去控制的智能事物。

当你看到这些东西的时候，你就能看到它的作用。 我们已经使用了```Color.red```和```LinearGradient```作为视图–包含很少数据的琐碎类型。 实际上，要使用Color.red作为视图，要简单得多：除了“用红色填充我的空间”之外，它不包含任何信息。

相比之下，Apple的UIView文档列出了UIView拥有的大约200个属性和方法，所有这些都会传递给它的子类，无论它们是否需要它们。

提示：如果您在视图中使用Class类，您可能会发现您的代码要么无法编译，要么在运行时崩溃。在这一点上相信我：使用结构体Struct。

## 1.2. SwiftUI主视图的后面有什么？

当刚开始使用SwiftUI时，我们会得到以下代码：
``` swift
struct ContentView: View {
    var body: some View {
        Text("Hello World")
    }
}
```

然后用背景色修改文本视图，并期望它填充屏幕：

```swift
struct ContentView: View {
    var body: some View {
        Text("Hello World")
            .background(Color.red)
    }
}
```

然而，我们期望的并没有发生。我们在屏幕中央看到一个小的红色文本视图，文字后面是一片白色。

这会让人感到困惑，并且通常会导致问题--“我如何使视图后面的背景也变红？”

对于SwiftUI来说，**视图背后没有任何东西** 。你不应该尝试用奇怪的方法或变通方法使空白区域变成红色，当然也不应该尝试通过SwiftUI外的其他进行操作。

现在，至少现在在我们的内容视图（Content View）后面有一个UIHostingController：它是UIKit（苹果公司最初的iOS UI框架）和SwiftUI之间的桥梁。 但是，如果您开始尝试进行修改，则会发现您的代码不再可以在Apple的其他平台上使用，并且实际上可能在将来的某个时候完全无法在iOS上使用。

你应该试着进入一种心态，认为View背后没有任何东西--你所看到的就是一切。

一旦有了这种想法，正确的解决方案就是使文本视图占用更多空间； 使其充满整个屏幕，而不是精确地围绕其内容调整大小。 我们可以通过使用```frame（）```修饰器来做到这一点，将最大宽度和最大高度都传递给```.infinity```。

```swift
Text("Hello World")
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .background(Color.red)
```
使用```maxWidth```, ```maxHeight``` 与使用 ```width``` , ```height``` 不同。并不是文本视图必须占据所有空间，只是它可以。如果你周围有其他视图，SwiftUI将确保它们都获得足够的空间。

默认情况下，视图不会离开安全区域，但可以使用edgesIgnoringSafeArea()修饰器进行更改，如下所示：

```swift
Text("Hello World")
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .background(Color.red)
    .edgesIgnoringSafeArea(.all)
```



## 1.3. 为什么修饰器顺序很重要

每当我们对一个SwiftUI视图应用一个修改器时，我们实际上是在创建一个新的视图，并应用了相应的修改。我们不只是修改现有的视图。仔细想想，这种行为是有意义的——我们的视图只包含我们提供给它们的属性，所以如果我们设置背景颜色或字体大小，就没有地方存储这些数据。

我们将在下一章讨论为什么会发生这种情况，但首先我想看看这种行为的实际含义。看看这段代码：

```swift
Button("Hello World") {
    // do nothing
}    
.background(Color.red)
.frame(width: 200, height: 200)
```
你猜一下它运行时会是什么样子？

很可能你猜错了：你不会看到一个200x200红色按钮，中间有“Hello World”。相反，您将看到一个200x200的空白正方形，中间有“Hello World”，并且在“Hello World”周围有一个红色矩形。

如果您考虑修饰器的工作方式，就可以理解这里发生的事情:每个修饰器都使用修饰器创建一个新结构，而不只是在视图上设置一个属性。

Swift的```type(of:)```方法打印特定值的精确类型，在这个实例中，它将打印以下内容:```ModifiedContent， _BackgroundModifier>， _FrameLayout>```

你可以在这里看到两件事:


- 我们修改视图SwiftUI通过使用泛型来应用该修改器：ModifiedContent ```<OurThing，OurModifier>```。
- 当我们应用多个修饰器时，它们会堆叠在一起：```ModifiedContent <ModifiedContent <...```

要想知道类型是什么，你可以从最内层的类型开始:
- 最里面的类型是```ModifiedContent <Button <Text>，_BackgroundModifier <Color>```：我们的按钮具有一些带有背景色的文本。
- 围绕它，我们有了```ModifiedContent <…，_FrameLayout>```，它采用了我们的第一个视图（按钮+背景色），并为其提供了更大的框架。

如您所见，我们以**ModifiedContent**类型堆叠结束-每个类型都需要一个视图进行转换，加上要进行的实际更改，而不是直接修改视图。

**这意味着修饰器的顺序很重要**。如果我们重写代码以在帧后应用背景色，那么您可能会得到预期的结果：
```swift
Button("Hello World") {
    print(type(of: self.body))
}
.frame(width: 200, height: 200)
.background(Color.red)
```
现在最好的考虑方法是想象一下，SwiftUI在每个修饰器之后都会呈现您的视图。 因此 .background（Color.red）会将背景颜色变为红色，而不管您给它什么帧。 如果您以后再扩展框架，它不会神奇地重新绘制背景-已经应用了。

当然，这并不是SwiftUI的实际工作方式，因为如果这样做的话，它将成为一个性能噩梦，但是这是一个在您学习时可以使用的简便方法。

使用修饰器的一个重要副作用是，我们可以多次应用相同的效果：每个修饰器都会简单地添加到以前的内容中。

例如，SwiftUI为我们提供了padding（）修饰器，该修饰器在视图周围添加了一些空间，以使其不会与其他视图或屏幕边缘抵触。 如果我们应用填充，然后应用背景色，然后应用更多填充和不同的背景色，则可以为视图提供多个边框，如下所示：

```swift
Text("Hello World")
    .padding()
    .background(Color.red)
    .padding()
    .background(Color.blue)
    .padding()
    .background(Color.green)
    .padding()
    .background(Color.yellow)
```


## 1.4. 为什么 SwiftUI 的view type使用 “some View” ?

SwiftUI非常依赖于一种称为“不透明的返回类型”的Swift功能，您每次编写```some View```时都可以看到它的实际效果。 这意味着“一种符合View协议的特定类型，但是我们不想说什么。”

与仅返回 ```View``` 相比，返回 ```some View``` 有两个重要区别：

1. 我们必须始终返回相同类型的视图。
2. 尽管我们不知道返回的视图类型是什么，但编译器知道。


第一个区别对性能很重要:SwiftUI需要能够查看我们正在显示的视图，并了解它们是如何变化的，这样才能正确地更新用户界面。如果我们被允许随机改变视图，那么SwiftUI要想弄清楚到底是什么改变了，就会变得非常缓慢——它需要抛弃所有东西，在每次小的改变之后重新开始。

第一个区别对性能很重要：SwiftUI需要能够查看我们显示的视图并了解它们如何更改，以便可以正确更新用户界面。 如果允许我们随机更改视图，SwiftUI要准确找出更改的内容确实很慢–几乎需要放弃所有内容，并在每次小的更改后重新开始。

第二个区别很重要，因为SwiftUI使用ModifiedContent构建数据的方式不同。之前我给你看了这个代码:


```swift
Button("Hello World") {
    print(type(of: self.body))
}
.frame(width: 200, height: 200)
.background(Color.red)

```

这就创建了一个简单的按钮，然后让它打印出准确的Swift类型，并给出一些带有几个 ```ModifiedContent``` 实例的长输出。

View协议附加了一个关联的类型，这是Swift所说的View本身并不意味着任何东西的方式-我们需要确切说明它是哪种视图。-我们需要确切地说它是哪种视图。它实际上有一个漏洞，就像 swift 不让我们说“这个变量是一个数组”，而是要求我们说出数组中的内容：“这个变量是一个字符串数组。”

因此，不允许编写这样的View：
```swift
struct ContentView: View {
    var body: View {
        Text("Hello World")
    }
}
```
最好写成这样：

```swift
struct ContentView: View {
    var body: Text {
        Text("Hello World")
    }
}
```

返回视图是没有意义的，因为Swift想要知道视图中的内容–它有一个“大洞”必须填补。 另一方面，返回Text很好，因为我们已经填补了漏洞； Swift知道视图是什么。

现在让我们回到之前的代码：
```swift
Button("Hello World") {
    print(type(of: self.body))
}
.frame(width: 200, height: 200)
.background(Color.red)
```

如果我们想从 ```body``` 属性中返回其中之一，应该写什么？ 尽管您可以尝试找出 ```ModifiedContent``` 泛型的确切组合，但是这令人痛苦，而且简单的事实是我们不在乎：这都是SwiftUI内部的东西。

some View允许我们做的是写出“这将返回一种特定类型的视图，例如Button或Text，但是我不想这样写。”因此，View的这个“大洞”将由真实视图填补， 但是我们不需要写出确切的名字很长的类型。

### 1.4.1. 拓展：SwiftUI如何处理Stack？

现在，如果您好奇的话，您可能想知道SwiftUI如何处理VStack之类的东西-它符合View协议，但是如果它可以包含很多内容，它将如何填补“它具有什么样的内容？”漏洞 里面不同的东西？

好吧，如果您创建一个内部有两个文本视图的VStack，SwiftUI会静默创建一个TupleView来包含这两个视图–一种特殊类型的视图，其中恰好包含两个视图。 因此，VStack用“这是一个包含两个文本视图的TupleView”来填充“这是一种什么样的视图？”。

如果在VStack中有三个文本视图怎么办？ 然后是一个包含三个视图的TupleView。 或四个视图。 或八个视图，甚至十个视图–实际上是TupleView的一个版本，可跟踪十种不同的内容：

```swift
TupleView<(C0, C1, C2, C3, C4, C5, C6, C7, C8, C9)>
```

这就是为什么SwiftUI不允许父级中超过10个视图的原因：他们编写了 ```TupleView``` 的版本，可以处理2个视图到10个视图，但不能超过10个视图。


## 1.5. 条件修饰器 condition modifiers

如果希望修饰器仅在满足特定条件时才适用，在SwiftUI中，最简单的方法是使用三元运算符。

提醒一下，要使用三元运算符，您首先要编写条件，然后是问号，如果条件为true，则应使用什么，如果条件为false，则应加冒号，然后使用什么。

例如，如果您拥有一个可以为true或false的属性，则可以使用该属性来控制按钮的前景色，如下所示：

```swift
struct ContentView: View {
    @State private var useRedText = false

    var body: some View {
        Button("Hello World") {
            // flip the Boolean between true and false
            self.useRedText.toggle()            
        }
        .foregroundColor(useRedText ? .red : .blue)
    }
```
因此，当```useRedText```为true时，修饰器有效地读取```.foregroundColor（.red）```；为false时，修饰器变为```.foregroundColor（.blue）```。 由于SwiftUI会监视```@State```属性的更改并重新调用我们的body属性，因此只要该属性更改，颜色就会立即更新。

有时，您可以使用常规的if条件根据某个状态返回不同的视图，但这仅在少数情况下可行。

例如，这类代码是不允许的：
```swift
var body: some View {
    if self.useRedText {
        return Text("Hello World")
    } else {
        return Text("Hello World")
            .background(Color.red)
    }
}
```
请记住，some View意味着“将返回一种特定类型的View，但是我们不想确切的说。”由于SwiftUI使用通用 ```ModifiedContent``` 包装器 ```Text（…```）和 ```Text（…）.background```创建新视图的方式。 ```（Color.red）```是不同的基础类型，并且与某些View不兼
容。


## 1.6. 环境修饰器Environment modifiers

许多修饰器可以应用于容器，这允许我们同时将相同的修饰器应用于多个视图。

例如，如果我们在一个VStack中有四个 Text() 视图，并且希望为它们提供相同的字体修饰器，我们可以直接将该修饰器应用于VStack，并将该更改应用于所有四个文本视图：
```swift
VStack {
    Text("Gryffindor")
    Text("Hufflepuff")
    Text("Ravenclaw")
    Text("Slytherin")
}
.font(.title)
```
这称为**环境修改器**，与应用于视图的常规修改器不同。

从编码角度来看，这些修饰器的使用方式与常规修饰器完全相同。 但是，它们的行为会有细微的差别，因为如果这些子视图中的任何一个覆盖了相同的修饰器，则子版本优先。

例如，这显示了四个带有标题字体的文本视图，但其中一个具有较大的标题：

```swift
VStack {
    Text("Gryffindor")
        .font(.largeTitle)
    Text("Hufflepuff")
    Text("Ravenclaw")
    Text("Slytherin")
}
.font(.title)
```

font(.title）是一个环境修饰器，这意味着"Gryffindor"文本视图可以使用自定义字体覆盖它。

下面的例子，对 ```VStack``` 加上模糊效果，然后尝试在其中一个文本视图上禁用模糊：

```swift
VStack {
    Text("Gryffindor")
        .blur(radius: 0)
    Text("Hufflepuff")
    Text("Ravenclaw")
    Text("Slytherin")
}
.blur(radius: 5)
```
这样做的效果不同：blur(radius: 5)是常规修饰器，因此应用于子视图的所有模糊都将添加到VStack模糊中，而不是替换它。

没有办法提前知道哪些修改器是环境修饰器，哪些是常规修饰器--您试一下也就知道了。尽管如此，还是尽可能在任何地方应用一个修饰器，这样做比复制并粘贴相同的东西到多个地方要好得多。


## 1.7. Views作为属性 Views as properties

有很多方法可以使在SwiftUI中使用复杂的视图层次结构变得更简单，其中一种选择是使用属性-将视图创建为自己视图的属性，然后在布局中使用该属性。

例如，我们可以像这样创建两个文本视图作为属性，然后在```VStack``` 中使用它们：
```swift
struct ContentView: View {
    let motto1 = Text("Draco dormiens")
    let motto2 = Text("nunquam titillandus")

    var body: some View {
        VStack {
            motto1
            motto2
        }
    }
}
```
甚至还可以在使用这些属性时直接将修饰器应用于这些属性，如下所示：
```swift
VStack {
    motto1
        .foregroundColor(.red)
    motto2
        .foregroundColor(.blue)
}
```
将视图创建为属性可以帮助使您的```body```代码更加清晰–不仅有助于避免重复，而且还可以讲更多复杂的代码放在```body```属性的外面。

Swift不允许我们创建一个引用其他存储属性的存储属性，因为在创建对象时会引起问题。 这意味着尝试创建绑定到本地属性的```TextField``` 将导致问题。

但是，您可以根据需要创建计算属性，如下所示：
```swift
var motto1: some View { Text("Draco dormiens") }
```


## 1.8. Views组件 View composition
SwiftUI使我们可以将复杂的视图分解为较小的视图，而不会产生太多的性能影响。 这意味着我们可以将一个大视图拆分为多个小视图，SwiftUI会为我们重新组装它们。

例如，在这个视图中，我们有一种特殊的样式文本视图的方式-它们有一个大字体，一些填充，前景和背景颜色，加上一个胶囊形状：
```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 10) {
            Text("First")
                .font(.largeTitle)
                .padding()
                .foregroundColor(.white)
                .background(Color.blue)
                .clipShape(Capsule())

            Text("Second")
                .font(.largeTitle)
                .padding()
                .foregroundColor(.white)
                .background(Color.blue)
                .clipShape(Capsule())
        }
    }
}
```

因为这两个文本视图除了它们的文本之外是相同的，所以我们可以将它们封装在一个新的自定义视图中，如下所示：
```swift
struct CapsuleText: View {
    var text: String

    var body: some View {
        Text(text)
            .font(.largeTitle)
            .padding()
            .foregroundColor(.white)
            .background(Color.blue)
            .clipShape(Capsule())
    }
}
```
然后，我们可以在原始视图中使用该```CapsuleText```视图，如下所示：
```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 10) {
            CapsuleText(text: "First")
            CapsuleText(text: "Second")
        }
    }
}
```
当然，我们也可以在视图中存储一些修饰器，并在使用它们时自定义其他修饰器。 例如，如果我们从```CapsuleText```中删除了```.foregroundColor```，则可以在创建该视图的实例时应用自定义颜色，如下所示：

```swift
VStack(spacing: 10) {
    CapsuleText(text: "First")
        .foregroundColor(.white)
    CapsuleText(text: "Second")
        .foregroundColor(.yellow)
}   
```

## 1.9. 自定义修饰器 Custom modifiers

SwiftUI为我们提供了一系列内置修饰器，例如```font（）```，```background（）```和 ```clipShape（）```。 但是，也可以创建自定义修饰器来完成特定的操作。

例如，我们可能会说我们的应用程序中的所有标题都应该具有特定的样式，因此首先我们需要创建一个自定义```ViewModifier struct```来完成我们想要的操作：

```swift
struct Title: ViewModifier {
    func body(content: Content) -> some View {
        content
            .font(.largeTitle)
            .foregroundColor(.white)
            .padding()
            .background(Color.blue)
            .clipShape(RoundedRectangle(cornerRadius: 10))
    }
}
```
现在，我们可以将其与Modify（）修饰器一起使用–是的，它是一个名为“modifier”的修饰器，但是它允许我们将任何种类的修饰器应用于视图，如下所示：
```swift
Text("Hello World")
    .modifier(Title())
```

使用自定义修饰器时，通常最好在View上进行**扩展**程序，以使其易于使用。 例如，我们可以将```Title```修饰器包在如下扩展中：
```swift
extension View {
    func titleStyle() -> some View {
        self.modifier(Title())
    }
}
```
我们现在可以如下使用修饰器：
```swift
Text("Hello World")
    .titleStyle()
```
自定义修改器不仅可以应用其他现有修改器，而且还可以根据需要创建新的View结构体。 记住，修饰器会返回新对象，而不是修改现有对象，因此我们可以创建一个将视图嵌入堆栈并添加另一个视图的对象：
```swift
struct Watermark: ViewModifier {
    var text: String

    func body(content: Content) -> some View {
        ZStack(alignment: .bottomTrailing) {
            content
            Text(text)
                .font(.caption)
                .foregroundColor(.white)
                .padding(5)
                .background(Color.black)
        }
    }
}

extension View {
    func watermarked(with text: String) -> some View {
        self.modifier(Watermark(text: text))
    }
}
```
完成后，我们现在可以向任何视图添加```Watermark```s，如下所示：

```swif
Color.blue
    .frame(width: 300, height: 200)
    .watermarked(with: "Hacking with Swift") 
```
提示：自定义视图修饰器在Xcode的早期版本中无法正常工作-您应确保使用最新版以获得最佳效果。

## 1.10. 自定义容器 Custom containers

尽管您不太可能经常这样做，但我想至少告诉您，完全可以在SwiftUI应用中创建自定义容器。 这需要更高级的Swift知识，因为它利用了Swift的一些强大功能，因此，如果你看不懂的地方太多，可以跳过。

为了进行测试，我们将创建一种新型的名为```GridStack```的堆栈，它将使我们能够在网格内创建任意数量的视图。 我们要说的是，有一个名为```GridStack```的新结构，该结构符合```View```协议并具有一定数量的行和列，并且在网格内部将有许多内容单元格，它们本身必须符合View协议。

在Swift中，我们可以这样写：
```swift
struct GridStack<Content: View>: View {
    let rows: Int
    let columns: Int
    let content: (Int, Int) -> Content

    var body: some View {
        // more to come
    }
}
```
第一行– ```struct GridStack <Content：View>：View ``` –使用Swift的一个更高级的功能，称为泛型。在这种情况下，这意味着“您可以提供所需的任何种类的内容，但是无论内容如何，都必须符合View 在冒号之后，我们再次重复查看以说    ```GridStack```本身也符合View协议。

请特别注意```let content```这一行-它定义了一个闭包，该闭包必须能够接受两个整数并返回我们可以显示的某种内容。

我们需要通过结合多个垂直和水平堆栈以创建所需数量的单元格来完成```body```属性。 我们不需要说每个单元格中的内容，因为我们可以通过使用适当的行和列调用```content```闭包来实现。

因此，我们可以这样填写：
```swift
var body: some View {
    VStack {
        ForEach(0 ..< rows) { row in
            HStack {
                ForEach(0 ..< self.columns) { column in
                    self.content(row, column)
                }
            }
        }
    }
}
```

现在我们有了一个自定义容器，我们可以使用它来编写一个视图，如下所示：

```swift
struct ContentView: View {
    var body: some View {
        GridStack(rows: 4, columns: 4) { row, col in
            Text("R\(row) C\(col)")
        }
    }
}
```

我们的`GridStack`能够接受任何种类的单元格内容，只要它符合View协议。 因此，如果需要，我们可以给单元格一个堆栈：
```swift
GridStack(rows: 4, columns: 4) { row, col in
    HStack {
        Image(systemName: "\(row * 4 + col).circle")
        Text("R\(row) C\(col)")
    }
}
```

### 1.10.1. 拓展：试图生成器view builders

为了获得更大的灵活性，我们可以利用SwiftUI的一种称为视图生成器(view builders)的功能，该功能允许我们发送多个视图并将其形成隐式堆栈。

要使用此功能，我们需要为```GridStack```结构体创建自定义初始化程序，以便可以将`content`闭包作为SwiftUI的视图构建器系统：
```swift
init(rows: Int, columns: Int, @ViewBuilder content: @escaping (Int, Int) -> Content) {
    self.rows = rows
    self.columns = columns
    self.content = content
}
```
多数情况下，这只是将参数直接复制到结构的属性中，但是请注意，这里有`@ViewBuilder`属性。 您还将看到`@escaping`属性，该属性使我们可以存储闭包，以便以后使用。

有了适当的设置，SwiftUI现在将在我们的单元格闭合内部自动创建一个隐式水平堆栈：
```swift
GridStack(rows: 4, columns: 4) { row, col in
    Image(systemName: "\(row * 4 + col).circle")
    Text("R\(row) C\(col)")
}
```

这两个方法均有效，因此无论您喜欢哪个都可以。

