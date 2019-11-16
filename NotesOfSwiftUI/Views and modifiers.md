# Views 和and modifiers



## 为什么SwiftUI 的views使用结构体structs ?

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

## SwiftUI主视图的背景是什么？

## 为什么修饰符顺序很重要

## 为什么 SwiftUI 的view type使用 “some View” ?

## 条件修饰器 condition modifiers

## 环境修饰器Environment modifiers

## Views作为参数 Views as properties

## Views组建 View composition

## 自定义修饰器 Custom modifiers

## 自定义容器 Custom containers


