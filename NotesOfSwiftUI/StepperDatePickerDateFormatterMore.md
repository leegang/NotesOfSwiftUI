# 步进器Stepper, 日期选择器 DatePicker, DateFormatter进阶

## 建立基本布局

这个应用程序将允许用户输入一个日期选择器和两个步进器，两者结合起来会告诉我们他们什么时候醒来，他们通常喜欢多少睡眠，以及喝多少咖啡。

因此，请首先添加三个属性，让我们存储这些控件的信息：

```swift
@State private var wakeUp = Date()
@State private var sleepAmount = 8.0
@State private var coffeeAmount = 1
```

在`body`内，我们将三套在`VStack`和`NavigationView`中的组件放置在一起，所以我们从唤醒时间开始。 替换默认的“ Hello World”文本视图：

```swift
NavigationView {
    VStack {
        Text("When do you want to wake up?")
            .font(.headline)

        DatePicker("Please enter a time", selection: $wakeUp, displayedComponents: .hourAndMinute)
            .labelsHidden()

        // more to come
    }
}
```

因为我们在`VStack`中，所以它将日期选择器呈现为iOS上的旋转齿轮，这很好。 我们要求使用.hourAndMinute配置，因为我们关心的是某人想要醒来的时间，而不是白天，并且使用labelsHidden（）修饰符，我们没有为选择器获得第二个标签–上面的标签足够了 。

接下来，我们将添加一个步进器，让用户大致选择所需的睡眠时间。 通过在4 ... 12的范围内以及0.25的范围内提供此值，我们可以确保它们将输入合理的值，但是我们可以将其与％g字符串插值说明符结合使用，以便看到类似“ 8”和 不是“ 8.000000”。

添加此代码，以代替  `// more to come`:

```swift
Text("Desired amount of sleep")
    .font(.headline)

Stepper(value: $sleepAmount, in: 4...12, step: 0.25) {
    Text("\(sleepAmount, specifier: "%g") hours")
}
```
最后，我们将添加最后一个步进器并贴上标签，以处理他们喝了多少咖啡。 这次，我们将使用1到20的范围（因为确定每天20杯咖啡足以供任何人使用吗？），但我们还将在步进器中显示两个标签之一，以更好地处理多元化问题。 如果用户将coffeeAmount设置为正好1，我们将显示“ 1杯”，否则我们将使用该数量加上“ cups”。

在以前的视图下面，将它们添加到VStack中：

```swift
Text("Daily coffee intake")
    .font(.headline)

Stepper(value: $coffeeAmount, in: 1...20) {
    if coffeeAmount == 1 {
        Text("1 cup")
    } else {
        Text("\(coffeeAmount) cups")
    }
}
```

我们需要的最后一件事是一个按钮，让用户可以计算出他们应该入睡的最佳时间。 我们可以在VStack的末尾使用一个简单的按钮来做到这一点，但是为了给这个项目增添一些趣味，我想尝试一些新的东西：我们将直接在导航栏中添加一个按钮。

首先，我们需要一个用于按钮调用的方法，因此添加一个空的 `calculateBedtime（）`方法，如下所示：

```swift
func calculateBedtime() {
}
```

现在，我们需要使用`navigationBarItems（）`修饰符向导航视图添加尾随按钮。 从左到右的语言（如英语）中的“尾随”表示“在右边”，您可以在此处提供任何视图-如果要几个按钮，则可以使用`HStack`。 在这里时，我们也可以使用`navigationBarTitle（）`在顶部放置一些文本。

因此，将以下修饰符添加到`VStack`中：
```swift
.navigationBarTitle("BetterRest")
.navigationBarItems(trailing:
    // our button here
)
```

在我们的案例中，我们想用“计算”按钮替换该注释。 之前我曾解释过按钮有两种形式：

```swift
Button("Hello") {
    print("Button was tapped")
}

Button(action: {
    print("Button was tapped")
}) {
    Text("Hello")
}
```

如果需要，我们可以在这里使用第一种形式：

```swift
Button("Calculate") {
    self.calculateBedtime()
}
```
可以，但是我希望您重新考虑。 该代码创建了一个新的闭包，闭包的唯一工作就是调用一个方法。 闭包在大多数情况下只是没有名称的函数–我们将它们直接分配给某些对象，而不是将它们作为单独的实体。

因此，我们正在创建一个仅调用另一个函数的函数。 如果我们可以完全跳过中间层，对每个人都更好吗？

好吧，我们可以。 该按钮关心的是它的动作是某种函数，该函数不接受任何参数并且不发送任何内容-只要它们都遵循这些规则，就不在乎这是方法还是闭包。

因此，我们实际上可以直接将`calculateBedtime`发送到按钮的操作，如下所示：

```swift
Button(action: calculateBedtime) {
    Text("Calculate")
}
```

现在，当人们看到他们经常认为我做错了。 他们想改写成这样：

```swift
Button(action: calculateBedtime()) {
    Text("Calculate")
}
```

但是，该代码无效，实际上意思也完全不同。 如果我们在 `calculateBedtime`之后添加括号，则意味着“调用 `calculateBedtime（)`，并在点击按钮时将其发送回正确的函数以使用。”因此，Swift将要求`calculateBedtime（）`返回一个闭包以运行。

通过编写`calculateBedtime`而不是`calculateBedtime（）`，我们告诉Swift在点击按钮时运行该方法，仅此而已； 它不会返回应运行的任何内容。

Swift确实模糊了函数，方法，闭包甚至运算符（+，-等）之间的界线，这使我们可以如此互换地使用它们。

因此，整个修饰符应如下所示：
```swift
.navigationBarItems(trailing:
    Button(action: calculateBedtime) {
        Text("Calculate")
    }
)
```

这不会做任何事情，因为`calculateBedtime()`为空，但至少我们的UI暂时足够好。

