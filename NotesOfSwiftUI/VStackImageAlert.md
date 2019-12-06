# 1. 堆栈、图片、Alert弹窗等

<!-- TOC -->

- [1. 堆栈、图片、Alert弹窗等](#1-堆栈图片alert弹窗等)
    - [1.1. 使用堆栈排列视图](#11-使用堆栈排列视图)
    - [1.2. 颜色Colors 与 框架frames](#12-颜色colors-与-框架frames)
    - [1.3. 渐变](#13-渐变)
    - [1.4. 按钮和图片](#14-按钮和图片)
    - [1.5. 显示警报消息弹窗Alert](#15-显示警报消息弹窗alert)

<!-- /TOC -->

## 1.1. 使用堆栈排列视图

当我们为身体返回`some View`时，我们的意思是“符合View协议的一种特定类型。 可能是导航视图，表单，文本视图，选择器或其他全部内容，但始终完全是符合View协议的一件事。

如果我们想返回多个东西，我们有多种选择，但是三个特别有用。 它们是HStack，VStack和ZStack，它们分别是水平，垂直和垂直堆栈。

让我们现在尝试一下。 我们的默认模板如下所示：
```SWIFT
var body: some View {
    Text("Hello World")
}
```

这恰好返回一种视图，即文本视图。 如果我们想返回两个文本视图，这种代码完全不允许：
```swift
var body: some View {
    Text("Hello World")
    Text("This is another text view")
}
```

取而代之的是，我们需要确保SwiftUI恰好获得一种视图，而这正是堆栈的用武之地：它允许我们说“这里有两个文本视图，我希望它们的位置是这样的……”

因此，对于VStack –一个垂直的视图堆栈–两个文本视图将一个放在另一个之上，如下所示：
```swift
var body: some View {
    VStack {
        Text("Hello World")
        Text("This is inside a stack")
    }
}
```
默认情况下，VStack在两个视图之间放置很少或不放置间距，但是我们可以在创建堆栈时通过提供一个参数来控制间距，如下所示:

```swift
VStack(spacing: 20) {
    Text("Hello World")
    Text("This is inside a stack")
}
```

就像SwiftUI的其他视图一样，VStack最多可以有10个子级-如果要添加更多子级，则应将它们包装在一个组中。

默认情况下，VStack对齐其视图以使其居中，但您可以使用其alignment属性对其进行控制。 例如，这使文本视图与它们的前边缘对齐，这在从左到右的语言（例如英语）中将导致它们向左对齐：
```swift
VStack(alignment: .leading) {
    Text("Hello World")
    Text("This is inside a stack")
}
```
除了VStack，我们还有HStack用于水平排列。 这具有与VStack相同的语法，包括添加间距和对齐的功能：
```swift
HStack(spacing: 20) {
    Text("Hello World")
    Text("This is inside a stack")
}
```

垂直和水平堆栈会自动适合其内容，并且倾向于将其与可用空间的中心对齐。 如果要更改，可以使用一个或多个`Spacer()`视图将堆栈中的内容推送到一侧。 这些将自动占用所有剩余空间，因此，如果在VStack末尾添加一个，它将把所有视图推到屏幕顶部：
```swift
VStack {
    Text("First")
    Text("Second")
    Text("Third")
    Spacer()
}
```
如果添加多个Spacer()，它们会自己分配可用空间。

我们还有ZStack可以按深度排列内容-它使视图重叠。 对于我们的两个文本视图，这将使事情变得难以阅读：
```swift
Stack {
    Text("Hello World")
    Text("This is inside a stack")
}
```
`ZStack`没有间距的概念，因为视图是重叠的，但是它有对齐。因此，如果您的`ZStack`中有一个大的子视图和一个小的子视图，您可以让两个视图像这样对齐到顶部:`ZStack(alignment:.top){`.

ZStack从上到下，从后到前绘制它的内容。这意味着，如果您有一个图像，那么一些文本ZStack将按照这个顺序绘制它们，将文本放在图像的顶部。

试着把几个水平的堆叠放在一个垂直的堆叠里——你能做出一个3 * 3的网格吗?

## 1.2. 颜色Colors 与 框架frames
SwiftUI为我们提供了多种渲染颜色的功能，并且既简单又强大，这是一个很难的挑战，但是他们确实做到了。

要尝试此操作，我们创建一个带有单个文本标签的`ZStack`：
```swift
ZStack {
    Text("Your content")
}
```
如果我们想在文本后面放置一些内容，则需要在ZStack中将其放置在文本上方。但是，如果我们想在那儿放一些红色怎么办—我们将如何做呢？

一种选择是使用background（）修饰器，可以给它一种颜色进行绘制，如下所示：
```swift
ZStack {
    Text("Your content")
}
.background(Color.red)
```
这可能已经完成了您所期望的工作，但也有能在意料之外:为什么只有文本视图有一个背景颜色，即使我们要求整个`ZStack`都用这个颜色。

实际上，该代码与以下代码没有区别:
```swift
ZStack {
    Text("Your content")
        .background(Color.red)
}
```
如果要在文本后面的整个区域填充红色，则应将颜色放入`ZStack`中-单独将其视为一个整体视图：
```swift
 ZStack {
        Color.red
        Text("Your content")
    }
```
实际上，`Color.red`本身就是一个视图，因此可以像形状和文本一样使用它。 它会自动占用所有可用空间，但是您也可以使用`frame（）`修饰器询问特定的大小：
```swift
Color.red.frame(width: 200, height: 200)
```
SwiftUI为我们提供了许多内置的颜色，例如`Color.blue`，`Color.green`等。 我们还提供了一些语义颜色：这些颜色不会说出它们所包含的色调，而只是描述它们的用途。

例如，`Color.primary`是SwiftUI中文本的默认颜色，根据用户设备是在亮模式还是暗模式运行，颜色将为黑色或白色。 还有`Color.secondary`，根据设备的不同，颜色也可以是黑色或白色，但是现在具有轻微的透明性，因此其后面的一些颜色会发光。

如果您需要特定的颜色，则可以通过在0和1之间传递RGB的值来创建自定义颜色，如下所示：
```swift
Color(red: 1, green: 0.8, blue: 0)
```
即使在全屏显示时，您也会看到使用`Color.red`会留一些空白。

白色的空间多少取决于您的设备，但是在iPhone X设计（iPhone X，XS和11）上，您会发现状态栏（顶部的时钟区域）和底部没有颜色。

这是有意留为空白的，因为Apple不想让重要的内容被其他UI功能或设备上的任何圆角遮盖。 因此，剩下的部分-整个中间空间-称为安全区域。

如果希望您的内容进入安全区域，则可以使用edgeIgnoringSafeArea（）修饰器指定要运行到哪些屏幕边缘。 例如，这将创建一个ZStack，用红色将屏幕边缘用红色填充，然后在顶部绘制一些文本：
```swift
ZStack {
    Color.red.edgesIgnoringSafeArea(.all)
    Text("Your content")
}
```
至关重要的是，不要把重要的内容放在安全区域之外，因为用户很难看到这些内容。有些视图，如List，允许内容在安全区域之外滚动，但是随后添加额外的insets，以便用户可以将内容滚动到视图中。

如果你的内容只是装饰性的——就像我们这里的背景色——那么把它扩展到安全区域之外是可以的。

## 1.3. 渐变

SwiftUI提供了三种渐变，和颜色一样，它们也是可以在UI中绘制的视图。

梯度是由几个组成部分:
- 要显示的颜色数组
- 尺寸和方向信息
- 要使用的渐变类型

例如，一个线性梯度是一个方向，所以我们提供了一个起点和终点，就像这样:
```swift
LinearGradient(gradient: Gradient(colors: [.white, .black]), startPoint: .top, endPoint: .bottom)
```
如果你的内容只是装饰性的——就像我们这里的背景色——那么把它扩展到安全区域之外是可以的。

这里使用的内部渐变类型也可以提供渐变停止，这可以让你指定一个颜色和沿着渐变的颜色应该使用多远。
```swift
RadialGradient(gradient: Gradient(colors: [.blue, .black]), center: .center, startRadius: 20, endRadius: 200)
```
最后一种渐变类型称为角度渐变，尽管您可能听说过其他地方将其称为圆锥形或圆锥形渐变。 这使颜色围绕一个圆圈循环而不是向外辐射，并且可以创建一些美丽的效果。

例如，这将以渐变的中心为中心在单个渐变中循环显示一系列颜色：
```swift
AngularGradient(gradient: Gradient(colors: [.red, .yellow, .green, .blue, .purple, .red]), center: .center)
```
所有这些渐变都可以单独作为视图站立，也可以用作修饰器的一部分–例如，您可以将它们用作文本视图的背景。

## 1.4. 按钮和图片

SwiftUI中的按钮有两种显示方式，具体取决于所需要的外观。

制作按钮的最简单方法是当它仅包含一些文本时：传递按钮的标题，以及点击按钮时应运行的闭包：
```swift
Button("Tap me!") {
    print("Button was tapped")
}
```
如果您想要更多的内容，例如图像或视图的组合，您可以使用这种替代形式
```swift
Button(action: {
    print("Button was tapped")
}) { 
    Text("Tap me!")
}
```
当您要将图像合并到按钮中时，此功能尤其常见。

SwiftUI具有专用的图像类型来处理应用程序中的图片，您可以通过三种主要方式创建它们：
- `Image（“pencil”）`将加载您已添加到项目中的名为“ Pencil”的图像。
- `Image(decorative: "pencil")` 将加载相同的图像，但不会被启用了屏幕阅读器的用户读出。这对于不传达其他重要信息的图像很有用。
- `Image（systemName：“ pencil”）`将加载内置在iOS中的铅笔图标。它使用Apple的SF Symbols图标集，您可以搜索所需的图标-从网络上下载Apple的免费SF Symbols应用程序以查看完整的图标集。

默认情况下，如果启用了屏幕阅读器，它将读取您的图像名称，因此，如果要避免混淆用户，请确保为图像提供清晰的名称。或者，如果他们实际上并未添加屏幕上其他地方尚未存在的信息，请使用`Image（decorative :)`初始化程序。

由于较长形式的按钮内部可以具有任何视图，因此可以使用如下图像：
```swift
Button(action: {
    print("Edit button was tapped")
}) { 
    Image(systemName: "pencil")
}
```
当然，您可以将这些与堆栈结合起来以制作更高级的按钮布局：
```swift
Button(action: {
    print("Edit button was tapped")
}) {
    HStack(spacing: 10) { 
        Image(systemName: "pencil")
        Text("Edit")
    }
}
```
提示：如果你发现你的照片已成为填充有颜色，例如显示为纯蓝色，而不是实际的图片，这可能是SwiftUI对按钮着色了，以便它看上去是可点击的。 要解决此问题，请使用`renderingMode（.original）`修饰器强制SwiftUI显示原始图像，而不是重新着色的版本。

## 1.5. 显示警报消息弹窗Alert

如果发生重要事件，通知用户的一种常用方法是使用警报Alert-弹出窗口，其中包含标题，消息和一到两个按钮（取决于您的需要）。

但是请考虑一下：何时应该显示警报以及如何显示警报？ 视图是我们程序状态的函数，警报也不例外。 因此，我们不用说“显示警报”，而是创建警报并设置显示警报的条件。

基本的SwiftUI警报具有标题，消息和一个关闭按钮，如下所示：
```swift
Alert(title: Text("Hello SwiftUI!"), message: Text("This is some detail message"), dismissButton: .default(Text("OK")))
```
您可以根据需要添加更多代码来更详细地配置按钮，但现在这样已经足够了。 更有趣的是我们如何显示警报：我们不将警报分配给变量，而是编写诸如`myAlert.show（）`之类的代码，因为这将回到旧的“事件系列（命令式编程）”思维方式。

相反，我们创建一些状态来跟踪我们的警报是否正在显示，如下所示：
```swift
@State private var showingAlert = false
```
然后，我们将警报附加到用户界面的某处，告诉它使用该状态来确定是否显示警报。 SwiftUI将观察`showingAlert`，并在它变为`true`时显示警报。

将所有这些放在一起，下面是一些示例代码，当点击按钮时显示警报：
```swift
struct ContentView: View {
    @State private var showingAlert = false

    var body: some View {
        Button("Show Alert") {
            self.showingAlert = true
        }
        .alert(isPresented: $showingAlert) {
            Alert(title: Text("Hello SwiftUI!"), message: Text("This is some detail message"), dismissButton: .default(Text("OK")))
        }
    }
}
```
这会将警报附加到按钮上，但是说实话，在哪里使用`alert（）`修饰器都没关系–我们要做的只是说警报存在，并且在`showalert`为`true`时显示。

仔细看看`alert（）`修饰符：
```swift
.alert(isPresented: $showingAlert)
```
这是另一种双向数据绑定，这是因为SwiftUI会在点击弹窗的确定按钮后自动将`showingAlert`设置为`false`。

这是该项目概述的最后一部分，因此几乎是开始使用真实代码的时候了。 如果要保存已编程的示例，则应将项目目录复制到其他位置。

准备就绪后，将 `ContentView.swift`放回您最初创建项目时的开始方式，这样我们就可以从头开始。
