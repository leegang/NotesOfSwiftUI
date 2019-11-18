# 堆栈、图片、Alert弹窗


## 使用堆栈排列视图

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

## 颜色Colors 与 框架frames




