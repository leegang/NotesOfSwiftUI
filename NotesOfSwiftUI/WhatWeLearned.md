# 1. 第一阶段总结

<!-- TOC -->

- [1. 第一阶段总结](#1-第一阶段总结)
    - [1.1. 总结我们学到的](#11-总结我们学到的)
    - [1.2. 关键点](#12-关键点)
        - [1.2.1. 结构体Structs vs 类classes](#121-结构体structs-vs-类classes)
        - [1.2.2. 使用ForEach](#122-使用foreach)
        - [1.2.3. 使用绑定 bindings](#123-使用绑定-bindings)

<!-- /TOC -->

## 1.1. 总结我们学到的

您现在已经完成了前两个SwiftUI项目，并且还完成了一个技术项目–两个应用程序的相同节奏和一个技术项目一直持续到课程结束，这将帮助您在花时间的同时快速地提高自己的知识水平。 返回并完善您所学。

尽管我们只是SwiftUI的三个项目，但您已经学到了一些最重要的概念：视图，修饰符，状态，堆栈布局等等–这些是您将在SwiftUI中反复使用的技能，这就是为什么我想尽早将它们写下来的原因。

当然，您还构建了一些真实的项目并完成了许多编码挑战，为帮助巩固您的学习，因此希望您开始对自己的知识有所了解。

到目前为止，我们已经介绍了：

- 构建将文本与控件（例如`Picker`）混合的滚动表单，SwiftUI会变成漂亮的基于表格的布局，在其中可以以新的选择滑动新屏幕。
- 创建一个`NavigationView`并为其命名。这不仅使我们能够将新视图推送到屏幕上，而且使我们能够设置标题并避免日以继夜的内容出现问题。
- 如何使用`@State`存储变化的数据，以及为什么需要它。请记住，我们所有的SwiftUI视图都是结构体，这意味着如果没有`@State`之类的内容就无法更改。
- 为用户界面控件（例如`TextField`和`Picker`）创建双向绑定，了解如何使用`$ variable`允许我们读取和写入值。
- 使用`ForEach`循环创建视图，这使我们可以一次制作很多视图。
- 使用`VStack`，`HStack`和`ZStack`构建复杂的布局，并将它们组合在一起以构成网格。
- 如何将颜色和渐变用作视图，包括如何为它们指定特定的帧，以便您可以控制它们的大小。
- 如何通过提供一些文本或图像以及点击按钮时应执行的关闭操作来创建按钮。
- 通过定义显示条件来创建弹窗框，然后从其他位置切换该状态。
- SwiftUI如何（以及为什么！）广泛使用不透明的结果类型（`some View`），为什么将其与修饰符顺序如此紧密地联系起来很重要。
- 如何使用三元运算符创建条件修饰符，这些条件修饰符根据您的程序状态应用不同的结果。
- 如何使用视图组件和自定义视图修饰符将代码分解为小部分，从而使我们能够构建更复杂的程序而不会迷失在代码。

我想让您考虑的一件事是，在SwiftUI中成为“视图view”意味着什么。 在开始本课程之前，您可能已经想到`Color.red`不可能是视图，但是它确实是视图。 您还了解了`LinearGradient`是如何使用视图的，这意味着在我们的布局中易于使用。

但是`VStack`呢？ 还是组？ 还是`ForEach`？ 这些是view吗？

是!这些绝对都是SwiftUI中的视图，这就是使框架具有如此显著的可组合性的原因-我们可以将`ForEach`放入`ForEach`内，放入`VStack`内的`Group`中，然后一切都能正常工作。

请记住，为了符合View协议，所有需要做的事情就是拥有一个名为`bod`y的计算属性，该属性返回`some View`。

## 1.2. 关键点

有三个关键点值得详细介绍。

这将在一定程度上复习我们学到的东西--再次用不同的例子复习，以帮助确保它们是清晰的-但我也想借此机会回答一些到目前为止可能已经出现的问题。

### 1.2.1. 结构体Structs vs 类classes

首先，希望在您的记忆中刷新这两个概念：结构和类。 这两种方法都可以使我们使用属性和方法构建复杂的数据类型，但是它们的工作方式（更具体地讲，它们之间的区别）很重要。

如果您还记得的话，结构和类之间有五个主要区别：

- 类不带有成员初始化器。 结构体是可以的。
- 类可以使用继承来构建功能。 结构体不能。
- 如果您复制一个类，则两个副本都指向相同的数据。 结构体的副本始终是唯一的。
- 类可以具有反初始化器； 结构体不能。
- 您可以在常量类中更改变量属性。 常量结构内部的属性是固定的，而不管属性是常量还是变量。

在Apple最初的编程语言`Objective-C`中，我们几乎将所有类都使用了类-我们没有选择的余地，因为它确实融入了工作方式。

在Swift中，我们确实有一个选择，并且该选择应该基于上述因素。 我之所以说“应该做到”，是因为看到人们不在乎这些差异的情况并不少见，因此总是使用`Calss`或 `Struct` 而不考虑选择的后果。

选择结构还是类取决于您和要解决的特定问题。 但是，我要您做的是考虑它如何传达您的意图。 唐纳德·克努斯（Donald Knuth）说：“程序只能由人类读取，并且只能由计算机执行”，这确实引起了我所谈论的重点：当有人读取您的代码时，您的意图是否清晰明了？

如果大多数时候使用结构体，那么在一个特定的地方切换到类可以传达一些意图：这件事与众不同，需要以不同的方式使用。 如果您始终使用类，那么这种区别就会消失–毕竟，您极不可能经常需要它们。

**提示：**SwiftUI的一个迷人细节是它如何完全颠倒我们使用结构体和类的方式。 在UIKit中，我们将使用结构体来存储数据，而将类用于UI，但在SwiftUI中则完全相反，这很好地提醒了学习的重要性，即使您认为它们并没有立即有用。

### 1.2.2. 使用ForEach

我想讨论的第二件事是`ForEach`，我们将其与以下代码一起使用：
```swift
ForEach(0 ..< 100) { number in
    Text("Row \(number)")
}
```

就像SwiftUI中的大多数其他视图一样，`ForEach`是一个视图，但是它允许我们在循环内创建其他视图。 这样，它还允许我们绕过SwiftUI的十个子视图的限制 – ForEach本身成为十个限制之一，而不是循环里面的东西。

现在考虑这样的字符串数组：
```swift
let agents = ["Cyril", "Lana", "Pam", "Sterling"]
```
我们如何遍历这些内容并进行文本查看？

一种选择是使用我们已经拥有的相同构造：
```swift
VStack {
    ForEach(0 ..< agents.count) {
        Text(self.agents[$0])
    }
}
```
但是SwiftUI为我们提供了第二种选择：我们可以直接在数组上循环。 这需要更多的思考，因为SwiftUI想要知道如何识别数组中的每个项目。

想想看：如果我们遍历一个四项数组，我们将创建四个视图，但是如果`body`被重新调用并且我们的数组现在包含五个项，SwiftUI需要知道哪个视图是新视图，以便可以在UI中显示 。 SwiftUI要做的最后一件事是丢弃整个布局，并在每次进行小的更改时从头开始。 相反，它希望完成的工作量最少：它想保留现有的四个视图，而仅添加第五个。

因此，我们回到Swift如何识别数组中的值的方法。 当我们使用`0 .. <5`或`0 .. <agent.count`这样的范围时，Swift肯定知道每个项目都是唯一的，因为它将使用该范围内的数字–每个数字在循环中仅使用一次 ，因此绝对是独一无二的。

在我们不再可能使用的字符串数组中，但是我们可以清楚地看到每个值都是唯一的：`[“ Cyril”，“ Lana”，“ Pam”，“ Sterling”]`中的值不再重复。 因此，我们可以做的就是告诉SwiftUI，字符串本身（“ Cyril”，“ Lana”等）可以用来唯一地标识循环中的每个视图。

在代码中，我们可以这样写：
```swift
VStack {
    ForEach(agents, id: \.self) {
        Text($0)
    }
}
```
因此，我们现在不再直接读取下标并使用它来读取数组，而是直接读取数组中的项目-就像`for`循环一样。


随着你对SwiftUI的学习进展，我们将介绍使用可识别协议识别视图的第三种方法，但这将适时出现。

### 1.2.3. 使用绑定 bindings

当我们使用诸如`Picker`和`TextField`之类的控件时，我们使用`$propertyName`为它们创建到某种`@State`属性的双向绑定。 这对于简单的属性非常有用，但有时您可能需要更高级的功能：如果要运行一些逻辑来计算当前值怎么办？ 或者，如果您不仅想在编写值时将其保存起来，该怎么办？

如果我们想对绑定中的更改做出反应，我们可以尝试利用Swift的`didSet`属性观察器，如下所示：
```swift
@State var selection = 0 {
    didSet {
        print("Selection was changed to \(selection)")
    }
}
```
这允许我们在手工修改值时对更改做出反应，但它不允许我们控制读取值。 这就是定制绑定的用武之地：它们可以像@State绑定一样使用，只是我们可以完全控制它们的工作方式。

绑定并不神奇：`@State`为我们去除了一些乏味的样板代码，但如果您愿意，完全可以手动创建和管理绑定。再说一次，这里向你展示这个，不是因为他很常见，只是想让你明白这这件事不神奇。

SwiftUI为我们做的所有事情都可以手工完成。尽管依靠自动解决方案几乎总会更好，但是偷窥幕后的确很有帮助，这样您就可以了解背后的机制。

首先，让我们看一下自定义绑定的最简单形式，它只是将值存储在另一个`@State`属性中，然后将其读回：
```swift
struct ContentView: View {
    @State var selection = 0

    var body: some View {
        let binding = Binding(
            get: { self.selection },
            set: { self.selection = $0 }
        )

        return VStack {
            Picker("Select a number", selection: binding) {
                ForEach(0 ..< 3) {
                    Text("Item \($0)")
                }
            }.pickerStyle(SegmentedPickerStyle())
        }
    }
}
```
因此，该绑定实际上只是充当传递对象-它本身并不存储或计算任何数据，而只是充当我们的UI和被操纵的基础状态值之间的垫片。

但是，请注意，选择器现在使用`selection: binding`—不需要美元符号。我们不需要显式地请求双向绑定，因为它已经是双向绑定了。

如果我们愿意，我们可以创建一个更高级的绑定，该绑定不仅可以传递单个值，还可以执行更多操作。 例如，假设我们有一个包含三个切换开关的表单：用户是否同意条款和条件，是否同意隐私政策以及是否同意收到有关运输的电子邮件。

我们可以将其表示为三个布尔@State属性：
```swift
@State var agreedToTerms = false
@State var agreedToPrivacyPolicy = false
@State var agreedToEmails = false
```
尽管用户可以手动切换它们，但是我们可以使用自定义绑定一次完全部打开或关闭。 如果所有这三个布尔值都为true，则此绑定为true，但是如果更改了它们，则将全部更新它们，如下所示：
```swift
let agreedToAll = Binding(
    get: {
        self.agreedToTerms && self.agreedToPrivacyPolicy && self.agreedToEmails
    },
    set: {
        self.agreedToTerms = $0
        self.agreedToPrivacyPolicy = $0
        self.agreedToEmails = $0
    }
)
```

现在我们可以创建四个开关：一个用于单个布尔值，另一个用于一次同意或不同意所有三个的控制开关：

```swift
struct ContentView: View {
    @State var agreedToTerms = false
    @State var agreedToPrivacyPolicy = false
    @State var agreedToEmails = false

    var body: some View {
        let agreedToAll = Binding<Bool>(
            get: {
                self.agreedToTerms && self.agreedToPrivacyPolicy && self.agreedToEmails
            },
            set: {
                self.agreedToTerms = $0
                self.agreedToPrivacyPolicy = $0
                self.agreedToEmails = $0
            }
        )

        return VStack {
            Toggle(isOn: $agreedToTerms) {
                Text("Agree to terms")
            }

            Toggle(isOn: $agreedToPrivacyPolicy) {
                Text("Agree to privacy policy")
            }

            Toggle(isOn: $agreedToEmails) {
                Text("Agree to receive shipping emails")
            }

            Toggle(isOn: agreedToAll) {
                Text("Agree to all")
            }
        }
    }
}
```
同样，自定义绑定并不是您经常需要的东西，但是花时间去了解幕后情况并了解发生的情况非常重要。 尽管SwiftUI非常聪明，但它只是一个工具，而不是魔术！
