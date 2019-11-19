# 步进器Stepper, DatePicker, DateFormatter

## 用步进器输入数值

SwiftUI有两种让用户输入数字的方式，而我们将在这里使用的一种是步进器：一个简单的`-`和`+`按钮，可以点击来选择一个精确的数字。 另一个选择是`Slider`，我们稍后将使用它，它也使我们可以从一系列值中进行选择，但不太精确。

步进器非常聪明，可以处理您喜欢的任何类型的数字类型–您可以将它们绑定到`Int`，`Double`和更多类型，它将自动适应。 例如，我们可以创建如下属性：

```swift
@State private var sleepAmount = 8.0
```

然后我们可以把它绑定到一个步进器上，这样它就会显示当前值，就像这样:

```swift
Stepper(value: $sleepAmount) {
    Text("\(sleepAmount) hours")
}
```

当这段代码运行时，您将看到8.000000小时，您可以点击- 或 +，向下到7、6、5并进入负数，或者向上到9、10、11，等等。

默认情况下，步进器仅受其存储范围的限制。 在此示例中，我们使用Double，这意味着滑块的最大值将为1.7976931348623157e + 308。 这是科学计数法，它的意思是“ 1.79769乘以10的308的幂” 。更简单地说，这确实是一个非常大的数字。

现在，作为两个孩子的父亲，你不知道我有多爱睡觉，尽管我也睡不好。幸运的是，Stepper允许我们通过提供一个`in`范围来限制我们想要接受的值，就像这样，想睡几小时就睡几小时:

```swift
Stepper(value: $sleepAmount, in: 4...12) {
    Text("\(sleepAmount) hours")
}
```

有了这个改动，步进器将从8开始，然后允许用户在4和12之间移动，但不能超过这个范围。这允许我们控制睡眠范围，使用户不能尝试24小时睡眠，也让我们拒绝不可能的值，例如你不能睡-1小时。

还有第三个有用的步进参数`Stetpper`，即步进值—每次—或+移动值的距离`step`。同样，这可以是任何类型的数字，但它需要匹配用于绑定的类型。因此，如果绑定到整数，则不能使用Double作为步骤值。

在这种情况下，我们可能会说，用户可以选择4到12之间的任何睡眠值，以15分钟的增量移动:

```swift
Stepper(value: $sleepAmount, in: 4...12, step: 0.25) {
    Text("\(sleepAmount) hours")
}
```

这开始看起来很有用-我们拥有合理范围的精确值，合理的步长增量，并且用户每次都能准确看到他们选择的内容。

不过，在继续之前，请先解决该问题：现在显示为8.000000，虽然准确，但太长了。之前我们使用过这样的字符串插值说明符:

```swift
Text("\(sleepAmount, specifier: "%.2f") hours")
```

我们可以在这里使用它，但看起来很奇怪：“ 8.00小时”似乎过于学术。 这是“％g”说明符在起作用的一个很好的例子，因为它会自动从数字末尾删除不重要的零。 因此，它将显示8、8.25、8.5、8.75、9，依此类推，用户阅读起来自然得多。

## 使用DatePicker选择日期和时间

SwiftUI为我们提供了一种称为`DatePicker`的专用选择器类型，可以将它绑定到`date`属性。 是的，Swift具有专用于处理日期的类型，它被称为`date`。

因此，要使用它，您首先需要使用`@State`属性，例如：

```swift
@State private var wakeUp = Date()
```

然后，您可以将其绑定到日期选择器，如下所示：

```swift
var body: some View {
    DatePicker("Please enter a date", selection: $wakeUp)
}
```

尝试在模拟器中运行它，以便查看外观。 您应该看到带有日期和时间的滚轮，以及左侧的“Please enter a date”标签。

现在，您可能会认为该标签看起来很丑陋，尝试用以下标签替换它：

```swift
DatePicker("", selection: $wakeUp)
```

但是，如果这样做，您将遇到两个问题：日期选择器即使标签为空也仍然为标签留出空间，并且屏幕阅读器处于活动状态的用户（他们更熟悉VoiceOver）将不知道日期是为什么而选择器。

有两种选择，都可以解决问题。

第一，我们可以将`DatePicker`包装为一个`Form`：

```swift
var body: some View {
    Form {
        DatePicker("Please enter a date", selection: $wakeUp)
    }
}
```

就像常规的选择器一样，这会改变SwiftUI渲染视图的方式。 不过，这次我们没有将新视图推送到`NavigationView`上； 取而代之的是，我们得到一个列表行，当点击它时它会折叠成日期选择器。

这看起来非常好，并且将表单的简洁性与日期选择器的熟悉的，基于转轮的用户界面相结合。 可悲的是，现在这些选择器的显示方式有时会出现一些故障。 我们稍后再讲。

除了使用表单`Form`之外，另一种方法是使用`labelsHidden（）`修饰符，如下所示：

```swift
var body: some View {
    DatePicker("Please enter a date", selection: $wakeUp)
        .labelsHidden()
}
```

该标签仍包含原始标签，因此屏幕阅读器可以将其用于VoiceOver，但是现在它们在屏幕上不再可见-日期选择器将占据屏幕上的所有水平空间。

日期选择器为我们提供了几个配置选项，以控制它们的工作方式。 首先，我们可以使用`displayComponents`来决定用户应该看到的选项类型：

- 如果您不提供此参数，则用户会看到一天，一小时和一分钟。
- 如果使用`.date`，则用户可以查看月，日和年。
- 如果使用`.hourAndMinute`，则用户只会看到小时和分钟部分。

因此，我们可以选择这样的精确时间：

```swift
DatePicker("Please enter a time", selection: $wakeUp, displayedComponents: .hourAndMinute)
```

最后，有一个`in`参数与在`Stepper`中的的工作原理相同：我们可以为它提供一个日期范围，并且日期选择器将确保用户不能选择超出范围。

现在，我们已经使用一段时间了，您已经习惯看到`1 ... 5`或`0 .. <10` 之类的东西，但是我们也可以将Swift日期与范围一起使用。 例如：

```swift
// when you create a new Date instance it will be set to the current date and time
let now = Date()

// create a second Date instance set to one day in seconds from now
let tomorrow = Date().addingTimeInterval(86400)

// create a range from those two
let range = now ... tomorrow
```

 译者注：上述方法需要放在`var body`里面，否则会报错`Cannot use instance member 'now' within property initializer; property initializers run before 'self' is available`

这对于DatePicker确实有用，但还有更好的地方：我们指定起始或结束范围,但不同时指定两个范围。Swift会形成一个单边范围，而另一边由Swift推断。

例如，我们可以这样创建一个日期选择器：

```swift
DatePicker("Please enter a date", selection: $wakeUp, in: Date()...)
```

这将允许将来的所有日期，但不允许过去的所有日期。读作“从当前日期到任何日期”。
