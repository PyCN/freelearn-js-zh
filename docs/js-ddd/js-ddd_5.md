# 第五章：分类和实施

根据 IBM 的一项研究（[`www-935.ibm.com/services/us/gbs/bus/pdf/gbe03100-usen-03-making-change-work.pdf`](http://www-935.ibm.com/services/us/gbs/bus/pdf/gbe03100-usen-03-making-change-work.pdf)），只有 41%的项目能够达到其进度、预算和质量目标。项目的成功或失败在很大程度上并不取决于技术，而是取决于参与其中的人。

想象一个软件项目，每个开发人员始终了解项目各个部分决策制定过程的复杂性。在这个理想的世界中，开发人员可以始终做出明智的决定，只要没有开发人员想要积极损害项目，决策就会是合理的。如果做出了错误的决定，在整体规划中不会造成巨大问题，因为接下来接触该项目部分的开发人员将知道如何修复它，并且也会了解所有涉及的依赖关系。这样的项目从项目角度来看极不可能失败。然而，悲哀的事实是，世界上几乎没有这样的项目，这很可能是因为这样的系统会带来整个团队需要审查每一次变更的额外开销。

这可能适用于非常小的项目，很可能是一个只有少数工程师的初创公司，但随着项目的增长，它根本无法扩展。当功能和复杂性增长时，我们需要分解项目，正如我们已经看到的，小项目比大项目更容易处理。分解并不容易，所以我们需要找到项目中的接缝，我们还需要意识到并决定项目整体的治理模式，以及子项目或子域。

在开源世界中，Linux 内核项目是一个很好的例子，它最初只有少数开发人员，但从那时起不断增长。自从超出了一个人在任何时间点都能掌握的规模后，内核就分裂成了子项目或子域，无论是网络处理、文件系统还是其他方面。每个子项目都建立了自己的领域，项目的增长建立在每个子项目都会做正确的事情的信任基础上。这意味着项目会分裂，因此一个开放的邮件列表可以让人们讨论有关整体架构和项目目标的话题。为了促进这一点，该邮件列表中使用的语言非常专注于社区的需求。否则，每次都详细解释会导致完全偏离重点的大讨论。

在本章中，我们将详细介绍如何在不断增长的项目中利用领域驱动设计，特别是：

+   使用和扩展项目的语言

+   管理领域及其子领域的上下文

+   领域驱动项目的构建块，聚合、实体、值对象和服务

# 建立一个共同的语言

我们无法让每个开发人员始终了解整个项目，但我们可以通过建立项目内部共享的通用语言，使决策非常清晰，结构非常直观。熟悉项目中使用的语言的开发人员应该能够弄清楚陌生代码的作用以及它在整个系统上下文中的位置。即使在一个领域中项目增长并且子领域的语言变得更加突出并开始更专注于子领域的特定部分，保持整体结构也是很重要的。作为一个子领域的开发人员，当我看到另一个子领域时，我不应该感到迷失，因为总体领域的语言为我提供了一个全局上下文。

到目前为止，我们一直在通过从业务领域中获取单词并在应用程序中使用它们来建立一个共同的语言。业务专家能够大致了解每个组件的内容以及组件之间的交互方式。随着我们的发展，构建这种语言也很重要，开发人员可以为业务领域贡献新的单词，以消除元素的歧义。

这些贡献不仅对开发人员有价值，因为他们现在能够清楚地传达某个元素是什么，而且对业务专家也有益，因为他们现在也能更清楚地传达信息。如果一个术语很合适，它将被领域所采纳；如果不合适，那么在大多数情况下最好放弃它。为了做到这一点，我们必须首先意识到我们可以使用哪些模式，并使用已经提供给我们的术语来影响我们在整个过程中使用的语言。

## 对象分类的重要性

开发人员喜欢对事物进行分类，就像我们之前讨论为什么命名类似`SomethingManager`是有害的时候所看到的那样。我们喜欢对事物进行分类，因为这样可以让我们对我们正在处理的对象做出一些假设。描述某个元素的目的不仅在业务领域中存在问题且容易出错，而且在编程领域中也是如此。我们希望能够快速地将代码的某些部分与特定问题关联起来。虽然通用语言解决了业务领域中的这一部分，但我们可以借鉴模式来更好地描述我们的编程问题。让我们来看一个例子：

开发者 1：嗨，让我们谈谈代码，以便在我们的领域对象和持久性之间进行转换。

开发者 2：是的，我认为这里有很大的优化空间。我们有没有看过外部公司提供的东西？

开发者 1：是的，我们有，但我们有非常特殊的需求，而常见的可用替代方案似乎对我们来说性能不够好。

开发者 2：哦，好的。我印象中我们自己开发的版本在处理线程方面有问题，整体性能也不太好。

开发者 1：我认为我们不需要在这里讨论线程，这应该在更低的层次处理。

开发者 2：等等，我们现在不是在讨论数据库连接吗？你想要降到多低的层次？

开发者 1：不，不！我在谈论将领域对象转换为数据库对象，因为我们需要将字段转换为正确的类型和列名等等。

开发者 2：哦，在这种情况下，你找错人了。对这部分我不太熟悉，抱歉。

当糟糕的命名潜入项目时，这种对话很可能会发生。开发者 1 谈论的通常被称为**数据映射器模式**，而开发者 2 谈论的是数据库 API。拥有通常被接受的名称不仅使对话变得更容易，而且还让某些开发人员更容易地表达他们对代码的哪一部分更熟悉或不太熟悉。

模式最常用于命名编程技术，例如，数据映射器模式描述了处理对象与它们对数据库的持久性之间的交互的一种方式。

### 注

数据映射器执行持久数据存储和领域对象或类似它的内存数据表示之间的双向数据传输。它在*企业应用架构模式*，*Martin Fowler*，*Pearson*中被命名。

在领域驱动设计中，我们也有处理某些类型的对象及其关系的*特定*方式。一方面，有组织开发本身的模式，另一方面有为实现特定目的的对象命名。这种分类就是本章的主题。我们通过将领域元素转化为特定领域驱动设计概念的具体实现来建立对某些领域元素的理解。

## 看到更大的图景

在处理大型项目时，最常见的问题是弄清楚设计背后的指导思想是什么。当软件变得庞大时，项目很可能由多个项目组成，实际上被分割成子项目，每个子项目负责自己的 API 和设计。在领域驱动设计方面，有领域和子领域。每个子领域都有自己的上下文；在领域驱动设计中，这就是有界上下文。独立的上下文以及主领域和其子领域之间的关系将知识整合在一起，形成一个完整的整体。

### 提示

在服务器端，有一个朝着面向服务的架构的趋势，这将项目的某些元素分割得非常彻底，将它们分离成不同的组件，分别运行。

在 Java 中，一直有定义自己可见性的包的概念。JavaScript 在这方面有些欠缺，因为所有的代码传统上都在一个线程下在浏览器中运行。当然，这并不意味着一切都完了，因为我们可以通过约定分隔命名空间，而像**npm**和**browserify**这样的工具现在也使我们能够在前端使用类似后端的分离。

支持查找代码的某些部分的过程，以及找出可以在领域的不同部分之间共享的内容，是一个问题，不同的语言已经以多种方式解决了这个问题。由于 JavaScript 非常动态，这意味着语言本身从来没有一种严格的方式来强制执行某些部分的隐私，例如`private`这样的关键字。然而，如果我们选择这样做，隐藏某些细节是可能的。以下代码使用了 JavaScript 模式来定义对象中的私有属性和函数：

```js
function ExaggeratingOrc(name) {
  var that = this
  // public property
  that.name = name

  // private property
  var realKills = 0
  // private method
  function killCount() {
    return realKills + 10
  }

  // public method using private method
  that.greet = function() {
    console.log("I am " + that.name + " and I killed " + killCount())
  }

  // public method using private property
  that.kill = function() { // public
    realKills = realKills + 1
  }
}

var orc = new ExaggeratingOrc("Axeman Axenson")
orc.killCount() // => TypeError: Object #< ExaggeratingOrc> has no method 'killCount'
orc.greet() // => I am Axeman Axenson and I killed 10
```

这种编码风格是可能的，但并不是很惯用。在 JavaScript 中，程序员倾向于相信他们的同行会做正确的事情，并假设如果有人想要访问某个属性，他或她一定有充分的理由。

### 提示

经常提到的面向对象的一个很好的特性是它可以隐藏实现的细节。根据你工作的环境不同，隐藏细节的原因也常常不同。虽然大多数 Java 开发人员都竭尽全力防止他人触及“他们”的实现。大多数 JavaScript 开发人员倾向于将其解释为其他开发人员不需要知道事物是如何工作的，但如果他们想要重用内部部分，他们是自由的，并且必须应对后果。很难说在实践中哪种方式更好。

在更高层次的抽象中也是如此；可能会隐藏很多细节，但一般来说，包往往是相当开放的，暴露内部结构，如果程序员想要访问的话。JavaScript 本身及其文化并不适合有效地隐藏细节。我们可以尽最大努力实现这种效果，但这将违背人们对软件的期望原则。

尽管完全隐藏许多细节很困难，但我们仍然需要在我们的应用程序中保持一致性。这就是我们使用聚合的目的，它封装了一组功能，通过一个连贯的接口来公开。对于我们的领域驱动设计，我们需要意识到这一点；通过使用正确的语言和模式，我们需要引导其他程序员通过我们的代码。我们希望通过一致的命名和通过测试解释某个功能所在的级别来在正确的情况下提供正确的上下文。当我们将软件的某些部分分类为聚合时，我们向下一个开发人员表明，访问功能的安全方式是通过这个聚合。牢记这一点，尽管仍然有可能深入内部并检查内部细节，但只有在有很好的理由的情况下才应该这样做。

# 值对象

在处理各种语言中的对象时，包括 JavaScript，在几乎所有情况下，对象都是通过引用传递和比较的，这意味着传递给方法的对象不会被复制，而是传递了它的指针，当比较两个对象时，比较它们的指针。这不是我们对对象的思考方式，尤其是值对象的思考方式，因为我们认为如果它们的属性相同，那么它们就是相同的。更重要的是，当我们考虑诸如相等性之类的事情时，我们不想考虑内部实现细节。这对使用对象的函数有一些影响；一个重要的影响是修改对象实际上会改变系统中的每个人，例如：

```js
function iChangeThings(obj) {
  obj.thing = "changed"
}

obj = {}
obj.thing // => undefined
iChangeThings(obj)
obj.thing // => "changed"
```

与此相关的是，比较并不总是产生预期的结果，就像在这种情况下：

```js
function Coin(value) {
  this.value = value
}

var fiftyCoin = new Coin(50)
var otherFiftyCoin = new Coin(50)

fiftyCoin == otherFiftyCoin // => false
```

尽管这对我们作为程序员来说可能是显而易见的，但它实际上并没有捕捉到领域中对象的意图。在现实世界中，拥有两个价值为 50 美分的硬币并认为它们不同并不方便，例如，在支付领域。对于商店来说，接受某个特定的 50 美分硬币而拒绝另一个硬币是没有意义的。我们希望通过它们所代表的价值而不是物理形式来比较我们的硬币。另一方面，收藏家对这个问题的看法会大不相同，对他们来说，某个 50 美分硬币可能价值连城，而普通的硬币则不是。对象的比较和它们的标识总是必须考虑到领域的上下文。

如果我们决定通过其属性值而不是其固有标识来比较和识别软件系统中的对象，我们就有了值对象的一个实例。

## 值对象的优势

传递并可以修改的对象可能会导致意外行为，并且根据领域的不同，通过标识比较对象可能会产生误导。在这种情况下，声明某个对象为值对象可以在未来节省大量麻烦。确保对象不被修改反过来使得更容易推理与之交互的任何代码。这是因为我们不必查看下一行的依赖关系，因为我们可以直接使用对象。

JavaScript 内置了对这些类型的对象的支持；使用`Object.freeze`方法将确保对象在被冻结后不会发生任何更改。将这添加到对象的构造中将使我们确信对象始终会按我们期望的方式行事。以下代码使用`freeze`构造了一个不可变的值对象：

```js
"use strict"

function Coin(value) {
  this.value = value
  Object.freeze(this)
}

function changeValue(coin) {
  coin.value = 100
}

var coin = new Coin(50)
changeValue(coin) // => TypeError: Cannot assign to read only property 'value' of #<Coin>
```

### 提示

JavaScript 的一个值得注意的补充是`use strict`指令。如果我们不使用这个指令，对值属性的赋值将会悄悄失败。即使我们仍然可以确保不会发生任何更改，这也会导致代码中出现一些茫然的表情。因此，即使在本书中大部分时间都没有提到，为了使代码示例简短，强烈建议使用`use strict`。例如，您可以使用**JSLint**来强制执行此操作（[`www.jslint.com/`](http://www.jslint.com/)）。

在处理值对象时，还可以提供一个函数来比较它们，无论在当前领域中意味着什么。在硬币的例子中，我们希望通过硬币的价值来比较它们，因此我们提供了一个`equals`函数来实现这一点：

```js
Coin.prototype.equals = function(other) {
  if(!(other instanceof Coin)) {
    return false
  }

  return this.value === other.value
}
}

var notACoin = { value: 50 }
var aCoin = new Coin(50)
var coin = new Coin(50)

coin.equals(aCoin) // => true
coin.equals(notACoin) // => false
```

`equals`函数确保我们正在处理硬币，并且如果是的话，检查它们是否具有相同的价值。这在支付领域是有意义的，但在其他地方可能并不一定成立。重要的是要注意，某些领域中的某些东西是值对象并不意味着这在普遍意义上也是如此。当处理组织内部项目的关系时，这一点变得特别重要。可能需要对类似的事物进行不同的定义，因为它们在不同的应用程序中以不同的方式被看待。

### 提示

前面的代码使用了对象的`__proto__`属性，这是一个内部管理的属性，指向对象的原型，是 JavaScript 的一个最近的补充。尽管这非常方便，但如果必要的话，我们总是可以通过`Object.prototype`（对象）来获取原型，如果`__proto__`不可用的话。

当然，仅仅有一个比较的方法并不意味着每个人都会在所有情况下使用它，而 JavaScript 也没有提供强制执行的方法。这是一个领域语言能够帮助我们的地方。传播关于领域的知识将使其他开发人员清楚地知道什么应该被视为值对象，以及比较它的方法。在使用类并需要向下一个人提供一些细节时，这可能是一个不错的主意。

## 引用透明度

我们一直在使用的`Coin`对象还有另一个有趣的属性，这对我们的系统可能很有用，那就是它们是引用透明的。这是一个非常花哨的说法，意思是无论我们对硬币做什么，它在应用程序的每个部分都会被视为相同。因此，我们可以自由地将其传递给其他函数，并保持它，而不必担心它的变化。我们也不需要跟踪硬币作为依赖项，检查在传递它时可能发生了什么，或者它可能被其他函数改变了。下面的代码说明了构造为值对象的硬币对象的简单用法。即使代码依赖于它，我们也不需要特别小心地与对象交互，因为它被定义为不可变的值对象。

```js
Orc.prototype.receivePayment = function (coin) {
  if (this.checkIfValid(coin)) {
    return this.wallet.add(coin)
  } else {
    return false
  }
}
```

在前面的例子中，只有一个依赖项 - 钱包的保存操作，如果`Coin`是一个值对象，那么情况就会复杂得多。`Coin`对象是一个实体的话，`checkIfValid`函数可能会改变属性，因此我们必须调查内部发生了什么。

值对象不仅使代码流程更容易跟踪，引用透明性在处理应用程序生命周期内的缓存对象时也是一个非常重要的因素。尽管 JavaScript 是单线程的，所以我们不必担心对象被其他线程修改，但我们已经看到对象仍然可以被其他函数修改，它们也可能因其他原因而改变。有了值对象，我们就不必担心这一点，因此我们可以自由地将其保存起来，以后需要时引用它。在函数之间，可能会发生事件导致我们当前正在处理的对象被修改，这可能会使跟踪错误变得非常困难。在下面的代码中，我们看到了`EventEmitter`变量的简单用法，以及如何使用它来监听`"change"`事件：

```js
var events = require("events")
var myEmitter = new events.EventEmitter()

var thing = { count: 0 }

myEmitter.on("change", function () {
  thing.count++
})
function doStuff(thing) {
  thing.count = 10
  process.nextTick(function() {
    doMoreStuff(thing)
  })
}

function doMoreStuff(thing) {
  console.log(thing.count)
}

doStuff(thing)
myEmitter.emit("change")
// => prints 11
```

仅查看`doStuff`和`doMoreStuff`函数，我们期望在控制台上看到 10 被打印出来，但实际上打印出了 11，因为事件`change`是交错的。在前面的例子中这是非常明显的，但是这样的依赖关系可能深藏在代码内部，跨越更多的函数。值对象使相同的错误变得不可能，因为对象的更改将被禁止。当然，这并不是异步编程中所有错误的终点，需要更多的模式来确保这样的预期工作；对于大多数用例，我建议查看**async**([`github.com/caolan/async`](https://github.com/caolan/async))，这是一个帮助处理各种异步编程任务的库。

# 作为实体定义的对象

正如我们所见，通过其属性主要定义对象可以非常有用，并帮助我们处理系统设计中的许多场景。因此，我们经常看到某些对象具有与它们相关的不同生命周期。在这种情况下，对象由其 ID 定义，在领域驱动设计术语中，它被视为实体。这与值对象相反，值对象由其属性定义，当它们的属性匹配时被视为相等。只有当实体具有相同的 ID 时，它才是相等的，即使所有属性匹配；只要 ID 不同，实体就不相同。

实体对象管理应用程序内部的生命周期；这可以是整个应用程序中的生命周期，但也可能是系统中发生的事务。在地牢中，我们处理了许多情况，我们实际上并不关心对象本身的生命周期，而是关心它的属性。以囚犯运输为例，我们知道它包括许多不同的对象，但其中大多数可以实现为值对象。我们并不真的关心随行的兽人守卫的生命周期，只要我们知道有一个守卫，并且他有武器保护我们就可以了。

这可能看起来有点违直觉，因为我们知道我们需要注意分配兽人，因为我们没有无限数量的兽人，但实际上里面隐藏着两个不同的概念，一个是`Orc`作为值对象，另一个是它被分配来守卫运输。下面的代码定义了一个`OrcRepository`函数，它可以用于在受控情况下获取兽人并使用它们。这种模式可以用于控制对共享资源的访问，通常与最有可能封装在其中的数据库访问一起使用：

```js
function OrcRepository(orcs, swords) {
  this.orcs = orcs
  this.swords = swords
}

OrcRepository.prototype.getArmed = function () {
  if (this.orcs > 0 && this.swords > 0) {
    this.orcs -= 1
    this.swords -= 1
    return Orc.withSword();
  }
  return false
}

OrcRepository.prototype.add = function (orc) {
  this.orcs += 1
  if (orc.weapon == "sword") this.swords += 1
}

function Orc(name, weapon) {
  this.name = name
  this.weapon = weapon
}

Orc.withSword = function () {
  return new Orc(randomName(), "sword")
}

repo = new OrcRepository (1, 1)
orc = repo.getArmed() // => { name: "Zuul", weapon: "sword" }
repo.getArmed() // => false
repo.add(orc)
repo.getArmed() // => { name: "Zuul", weapon: "sword"}
```

虽然`Orc`对象本身可能是一个值对象，但分配需要具有生命周期，定义开始、结束和可用性。我们需要从`Orc`对象的存储库中获取一个兽人，满足能够在运输过程中保护并在运输完成后立即归还的需求。在前面的情况下，`Orcs`存储库是一个实体，因此我们需要确保它被正确管理，否则我们可能会得到不正确的兽人数量或未记录的武器，这两者对业务都不利。在这种情况下，兽人可以自由传递，我们与其管理隔离开来。

## 更多关于实体的内容

在构建应用程序时，实体经常出现，很容易陷入使系统中的大多数对象成为实体而不是值对象的陷阱。要牢记的重要事情是值对象可以执行大量工作，并且对值对象的依赖是“便宜”的。

那么，为什么对值对象的依赖比对实体的依赖“更便宜”呢？当处理实体时，我们必须处理状态，因此对实体进行的任何修改都可能对使用该实体的其他子系统产生影响。造成这种情况的原因是每个实体都是一个可以改变的独特事物，而值对象归结为一组属性。当我们传递实体时，我们需要同步实体的状态，可能还需要同步所有依赖实体的状态。这可能会很快失控。以下代码显示了处理多个实体交互时的复杂性。在添加和删除物品时，我们需要控制钱包、库存和兽人本身的多个方面，以保持一致的状态：

```js
function Wallet(coins) {
  this.money = coins
}

Wallet.prototype.pay = function (coin) {
  for(var i = 0; i < this.money.length; i++) {
    if (this.money[i].equals(coin) {
      this.money.splice(i, 1)
      return true
    }
  }
  return false
}

function Orc(wallet) {
  this.wallet = wallet
  this.inventory = []
}

Orc.prototype.buy = function (thing, price) {
  var priceToPay = new Coin(price)
  if (this.wallet.pay(priceToPay)) {
    this.inventory.unshift(thing)
    return true
  }
  return false
}
```

在这种情况下，我们需要确保购买行为不会被中断，因为根据其他实现可能会出现奇怪的行为。如果库存有更多与之相关的行为，比如大小检查，那么我们需要在确保可以无中断地回滚的同时协调这两个检查。我们之前已经看到事件如何在这里给我们带来了很多问题，这会很快变得难以控制。尽管在某个级别上不可避免地需要处理这个问题，但意识到这些问题是很重要的。

实体需要以确保不存在不一致状态的方式来控制它们的生命周期。这使得处理实体更加复杂，也可能会影响性能，因为需要进行锁定和事务控制。

# 管理应用程序的生命周期

实体和聚合都是关于在应用程序的每个级别管理这个周期。我们可以将应用程序本身视为包装在其所有组件周围以管理附加值对象和包含实体的聚合。在我们的囚犯转移级别上，我们将转移本身视为包装所有本地从属者的事务，并管理最终结果，无论是成功的转移还是失败的转移。

始终可以将生命周期管理进一步推到对象链的上方或下方，并找到合适的级别可能很困难。在前面的例子中，分配也可以是一个值对象，由对象链上的聚合管理，以确保满足其约束。在这个阶段，正确的抽象级别是系统开发人员必须做出的决定。将事务控制推得太高，然后使事务跨越更多对象可能会很昂贵，因为锁更粗糙，因此并发操作受到阻碍；将其推得太低可能会导致聚合之间的复杂交互。

### 提示

决定管理生命周期的正确抽象级别对应用程序的影响比一开始看到的要深远得多。由于实体是通过它们的 ID 进行管理的，同时又是可变的，这意味着它们是需要在处理并发时进行同步的对象，因此它影响了整个系统的并发性。

## 聚合

面向对象的编程在很大程度上依赖于将多个协作者的功能组合起来实现某些功能。在构建系统时，经常出现这样的问题，即某些对象吸引了越来越多的功能，从而成为几乎参与系统中每次交互的上帝对象。摆脱这种情况的方法是让多个对象合作实现相同的功能，但是作为小部分的总和，而不是一个大对象。

构建这些相互关联的子系统存在一个不同的问题，即随着对象结构的构建，它往往会暴露大而复杂的接口，因为用户需要更多地了解内部来使用系统。让客户端处理子系统的内部不是对建模这样的系统的好方法，这就是聚合的概念发挥作用的地方。

聚合允许我们向客户端公开一个一致的接口，并让他们只处理他们需要提供的部分，以使系统作为一个整体运行，并让外部入口点处理不同的内部部分。在前一章第四章中，*建模演员*，我们讨论了聚合的例子，即马车由使其作为一个整体运行所需的所有元素组成。相同的概念也适用于其他级别，并且我们构建的每个子系统都是其部分的一种聚合，包括实体和值对象。

## 分组和接口

作为开发人员，我们需要问自己的问题是，在开发过程中，如何分组部分，最好在哪里构建管理这些聚合的接口，以及它们应该是什么样子？尽管当然没有严格的公式，但以下描述的部分可以作为指导。

接口应该只要求客户端提供它实际关心的部分，这通常意味着子系统有多个入口点，通过不同的点触及系统的客户端可能会在途中相互干扰。在这一点上，我们可以借鉴一些经典的技术，提供所谓的“工厂”方法，以便为我们提供所需的对象图的入口点。这使我们能够创建一个易于阅读的语法，而不是试图利用所有动态的方式使对象创建灵活，并接受非常不同的参数来提供相同的功能。以下代码展示了在创建兽人的上下文中使用这种工厂的情况。我们希望对象构造函数尽可能灵活，同时为常见情况提供工厂方法：

```js
var AVAILABLE_WEAPONS = [ "axe", "axe", "sword" ]
var NAMES = [ "Ghazat", "Waruk", "Zaraugug", "Smaghed", "Snugug",
              "Quugug", "Torug", "Zulgha", "Guthug", "Xnath" ]

function Orc(weapon, rank, name) {
  this.weapon = weapon
  this.rank = rank
  this.name = name
}

Orc.anonymusArmedGrunt = function () {
  var randomName = NAMES[Math.floor(Math.random() * NAMES.length)]
  var weapon = AVAILABLE_WEAPONS.pop()
  return new Orc(weapon, "grunt", randomName)
}
```

在这个例子中，我们可以检测到缺少的属性，并重新安排输入参数，以确保生成兽人可以适用于各种组合，但这很快变得难以控制。一旦协作者不再只是简单的字符串，我们需要与更多的对象进行交互并控制更多的交互。通过提供一个工厂函数，我们可以准确表达我们打算提供的内容，而不需要采取非常复杂的处理。

总的来说，将协作者分组成聚合并为访问提供不同的接口的目标是控制上下文，并更深入地将领域语言融入项目中。聚合的作用是提供对其聚合的模型数据的简化视图，以防止不一致的使用。

# 服务

到目前为止，我们一直在表达关于“事物”的概念，但有些概念最好是围绕着做某事的行为来表达，这就是服务的用武之地。服务是领域驱动设计的一流元素，它们的目标是封装领域中涉及许多合作者协调的行为。

|   | *“在 Javaland 中，动词负责所有工作，但由于它们受到所有人的鄙视，因此任何动词都不得自由行动。如果要在公共场合看到动词，必须始终由名词陪同。当然，“陪同”本身也是一个动词，几乎不允许裸奔；必须找到一个动词陪同者来促进陪同。但“促进”和“促进”呢？事实上，促进者和采购者都是相当重要的名词，它们的工作是监护低贱的动词“促进”和“采购”，分别通过促进和采购。”* |   |
| --- | --- | --- |
|   | --*- Steve Yegge - Thursday, March 30, 2006 - Execution in the Kingdom of Nouns* |

服务是一个非常有用但也经常被滥用的概念，它们归根结底是关于命名的。做某事的行为可以用“事物”来表达，也可以用“做事者”的名称来表达。例如，我们可以有一个`Letter`并在其上调用`send`方法，让它决定该做什么，并传递所需的合作者，例如：

```js
function Letter(title, text, to) {
  this.title = title
  this.text = text
  this.to = to
}

Letter.prototype.send = function(postman) {
  postman.deliver(this)
}
```

另一种选择是创建一个处理发送信件的服务，并以无状态的方式调用它，在构造时将所有合作者传递给服务：

```js
function LetterSender(postman, letter) {
  this.postman = postman
  this.letter = letter
}

LetterSender.prototype.send = function() {
  var address = this.letter.to
  postman.deliver(letter, address)
}
```

在一个简单的例子中，很明显第二种方法似乎复杂，并且并没有以任何有意义的方式增加发送信件的领域语言。在更复杂的代码中，这经常被忽视，因为某个动作的复杂性需要存在于某个地方。选择哪种方法取决于服务中可以封装的功能量。如果一个服务存在只是为了将一段代码拆分成一个现在独立但有点无家可归的部分，那么服务可能是一个坏主意。如果我们能够在服务中封装领域知识，那么我们将有一个创建服务的有效理由。

将一个对象命名为其功能，并且只有一个实际执行操作的方法，这对于任何面向对象的程序员来说都应该引起警惕。良好的服务可以增加领域的表达，并表达在领域本身具有坚实基础的概念。这意味着有名称来表达这个概念。服务可以封装那些不直接由“事物”支持的概念，并且它们应该根据领域进行命名。

# 关联

在前一节中，我们看到信件的递送取决于邮递员。信件和递送它的人之间存在一定的关系，但根据领域的不同，这种关系可能并不是非常强大或相关的。例如，对于我们的地牢主来说，知道谁递送了哪封信可能是相关的，因为每个邮递员都会立即被监禁并对他或她递送的邮件内容负责。

兽人的方式可能不像商业规则那样容易理解。在这种情况下，我们希望确保我们给每封信和递送它的邮递员贴上标签，将信件与特定的人联系起来。反之则无关紧要。在我们的领域中对此进行建模时，我们希望传递这一重要知识，并有一种方法将递送过程中的消息与适当的递送人员关联起来。在代码中，这可以更容易地完成；例如，我们可以为该封信创建一个历史记录，其中每个与递送相关的合作者都被关联起来。

领域模型之间的关联概念是领域设计的一个组成部分，因为大多数对象无论以何种形式都不会完全独立。我们希望在关联中尽可能地编码知识。当我们考虑对象之间的关联时，关联本身可能包含我们希望在我们的模型中加入的领域知识。

# 实现过程中的见解

模式的概念在面向对象语言以及其他类型的语言中都已经得到了很好的建立。已经有很多书籍对此进行了讨论，也有很多讨论涉及将许多开发人员的知识编码成模式，以用于企业以及其他类型的软件。最终，关键在于在开发过程中在正确的时间使用正确的模式，这不仅适用于领域模式，也适用于其他软件模式。

在他的书《企业应用架构模式》中，Martin Fowler 不仅讨论了通过`DataMapper`插件加领域层、事务脚本、Active Record 等方式处理与数据库通信的可用选项，还讨论了何时使用它们。和大多数事情一样，最终的结论是所有选择都有好坏两面。

在软件开发中，随着我们的前进，我们可以获得多种见解。一个非常有价值的见解是引入了以前不清楚的新概念。要达到这一点并没有明显的方法，我们可以做的是开始对我们当前在软件中拥有的模式进行分类，并尽可能地使它们清晰，以便更有可能发现新概念。有了一组良好分离的部分，发现缺失的部分更有可能发生。当我们考虑领域模式时，特别是我们可以对应用程序的某些元素进行分类的各种方式时，分类的方式并不总是像我们希望的那样清晰。

## 识别领域模式

正如你在处理发送信件的示例中看到的，我们注意到即使所提出的选项使用服务来处理协作，也有其他方法可以处理。这本书中的小例子的问题在于很难传达某个选项何时有益，或者某个设计在一般情况下是否过度复杂；这对于像领域驱动设计这样复杂的架构来说尤其如此，毕竟如果应用程序只有几百行代码，领域驱动设计解决的许多问题就不存在了。

当我们为某个功能编写代码时，我们总是需要意识到组件的设计并不是一成不变的。一个应用程序可能一开始会有很多实体存在，大部分交互都是内联处理的，因为系统还没有发展到足够清晰地看到哪些交互是复杂且重要到足以将它们作为领域概念的阶段。此外，通常我们*使用*软件意味着我们认识到某些概念，并且作为开发人员使用软件，我的意思是触及接口并扩展整个软件。

### 并非一切都是实体

在领域驱动设计中，往往很容易为一切创建实体。实体的概念在开发人员的思维中非常普遍。当我们将对象视为内存中的事物时，对象总是具有固定的标识，大多数语言默认以这种方式进行比较，例如：

```js
Function Thing(name) {
  this.name = name
}

aThing = new Thing("foo")
bThing = new Thing("foo")

aThing === bThing // => false
```

这使得我们很容易期望一切都是一个实体，其 ID 是 JavaScript 认为的任何东西。

当我们考虑领域时，这当然并不总是有意义的，我们很快就会意识到某些事物并不是以这种方式被识别的，这往往会使应用程序的某些部分转向使用值对象。

尽可能简单地开始是件好事，但随着时间的推移，使项目成为一个更好的工作场所的关键是尽可能多地抓住机会来改进事物。即使最终选择的路线可能并非如此，尝试它本身也会使代码变得更好。

### 提示

**原始偏执**反模式是在不及早和经常进行重构时经常陷入的陷阱。问题在于很少引入新对象，许多概念由原语表示，比如将电子邮件表示为字符串，或者将货币值表示为纯整数。问题在于原语并未封装所有知识，而只是纯属性，这导致知识在各处重复，而命名概念，如电子邮件或货币对象，本可以共享。

## 始终朝着可塑代码进行重构

当我们开始朝着让代码以不同的方式引导我们的设计的方向努力时，我们会注意到那些不断变化的地方，以及那些给我们带来最多新功能或甚至重构的地方。这些就是我们需要解决的痛点。

|  | *在面向对象编程中，单一责任原则指出每个类应对软件提供的功能的一个部分负责，并且该责任应完全由类封装。它的所有服务应与该责任紧密对齐* |  |
| --- | --- | --- |
|  | --*– 根据维基百科，单一责任原则最初由 Robert C. Martin 定义* |

我们希望我们的更改是局部的，并且探索某个功能的不同实现路径应该尽可能少地触及代码。这就是由 Robert C. Martin 定义的**单一责任原则**的目的，它将责任定义为变更的原因。与**开闭原则**一起，使代码易于使用，因为已知的接缝和构建块。

领域驱动设计的目标是将面向对象编程的概念提升到更高的水平，因此大多数面向对象的概念也适用于领域驱动设计。在面向对象中，我们希望封装对象，在领域驱动设计中，我们封装领域知识。我们希望我们的每个子系统和领域的每个元素尽可能独立，如果我们实现了这一点，代码将很容易在途中进行更改。

# 实施语言指导

领域驱动设计的核心是封装领域知识，而包含和分发知识的引导力量是语言。我们之前已经谈到，领域驱动设计的目标之一是在项目中创建一个共享的通用语言，该语言在开发人员和项目所有者或利益相关者之间共享，以指导实施。之前已经暗示过，这当然不是单向的。

当发现领域概念时，通常有必要作为团队建立和命名新概念，以使它们成为通信的已建立方式。有时，这些新名称和含义可能会回到业务中，它们将开始用于描述现在命名的模式，并且在很长时间内可能会回到跨业务领域中使用的通用语言中，如果它们被认为是有用的。

在 Eric Evans 的领域驱动设计原著中，他讨论了财务软件的开发以及建立的术语如何回溯到销售和营销部门，用于描述软件的新功能。即使您的业务语言的新添加可能不会如此，如果一个添加是有帮助的，至少业务的核心部分会采纳它们。

## 与业务语言一起工作

根据领域的不同，构建领域语言是非常不同的。很少有领域已经有与之相关的非常具体的语言。例如，如果我们研究会计，就会发现有关一切名称和相互作用方式的书籍。类似的情况也可能存在于成熟的企业，即使可能没有相关的书籍可供参考，但跟随日常进行业务的人可以很快揭示一些概念。

### 提示

跟踪你被要求实施的过程一天可以提供一些非常重要的见解。它还可以暗示业务在非常特定的方式中表现出来的领域，那些我们作为程序员在事后才会遇到的小事情。我们认为不合逻辑的事情很难融入我们的模型。

很少有业务领域是如此幸运的，特别是在年轻企业开发新理念的世界中，缺乏成熟的语言是固有的。那些企业往往是那些大量投资于基于 JavaScript 的应用程序的企业，那么我们该如何处理这种情况呢？

回到兽人地牢，我们正在处理一个对我们非常陌生的世界，这个世界没有一个非常成熟的语言来处理它的过程，因为迄今为止几乎从来没有这样的需要。我们在书中已经处理了这个问题，因为许多术语在上下文中都被严重重载。通知可以是一条消息，通知兽人他被分配到某个囚犯运输任务，或者通知另一个地牢囚犯即将到达，或者监狱要求新的囚犯。我们该如何处理这种情况呢？

让我们以兽人大师向我们解释他如何通知另一个地牢的情况为例：

**开发者：** 当地牢里囚犯满了的时候，你需要什么？

**兽人大师：** 没问题！让萨古克处理？

**开发者：** 据我所知，萨古克是北方地牢的领袖，所以我猜你需要准备一个运输？

**兽人大师：** 是的，我需要通知巴隆克准备运输，还有通知萨古克准备好。

*他写下两封信，然后叫来他的哥布林助手。*

**兽人大师：** 把这个送给巴隆克，这个送给萨古克！

*哥布林开始跑开，但就在他通过南门离开房间之前，主人开始尖叫。*

**兽人大师：** 你在干什么？你需要找一只乌鸦，先把这个送到萨古克那里！

*哥布林看起来很困惑，但现在开始朝另一个方向跑去。*

**兽人大师：** 这种事经常发生——哥布林就是记不住谁是谁，他也不需要记住，但他需要把信送到正确的办公室。

**开发者：** 啊，所以如果你想给另一个地牢发消息，你找一只乌鸦？当你在地牢内部给某人发消息时，它会在本地传递？

**兽人大师：** 没错！

**开发者：** 好的，为了不混淆系统，我只会称另一个地牢的消息为“乌鸦”，而在本地我们继续称其为“消息”。这样做有道理吗？

兽人大师：是的！希望哥布林不再搞砸了，因为这已经引起了一些奇怪的对话。

这是对这种情况可能发展的一个重要简化，但我们作为开发者应该吸收业务提供的语言片段并将其纳入领域。这不仅使我们的生活更轻松，也提高了业务沟通的效率。

需要注意的一点是，我们需要确保不把我们非常特定的语言强加给业务，也就是说，如果某个概念没有被采纳，就准备放弃它。毕竟，未命名的概念比令人困惑的命名概念更糟糕。构建语言需要来自领域，而不应该被强加给领域。一个名字不被接受的概念，要么是命名了一个不重要的概念，要么是不够描述性而无法被接受。

如果给一个不重要的概念命名，通常会不必要地引起对它的关注，这可能会在以后造成麻烦，因为我们可能会不愿意改变它或者适应新的需求，认为它太重要。例如，我们开发了自动囚犯分配的概念，它使用算法来确定我们有多少囚犯时最佳的牢房。这似乎非常重要，因为我们希望地牢尽可能地得到最佳利用。有一天，一个新的囚犯到来，系统开始计算确定他的最佳牢房，而狱卒却说：“为什么这么久？每次都这样！我早就知道把他放在哪里了，我就把他塞进 1 号牢房！”这是有效的用户反馈——尽管我们可能已经找到了最佳利用地牢的方法，但这可能并不重要，因为兽人对每个牢房的囚犯数量看待得比我们轻松得多。自动分配的概念从未真正流行起来；我们从未听说过有人谈论它，所以我们可能会把整个概念都移除掉，这样对我们和用户来说系统会更容易一些。

当然，系统不仅为用户服务；它们也可能在途中为其他系统服务。因此，牢记谁是真正的用户可能会对决策产生重大影响。

# 构建上下文

我们一直在谈论我们使用的语言，系统如何相互作用以及它们由什么组成，但我们还需要触及一个更高的层次：*系统如何与其他系统相互作用？*

在服务器世界中，目前强调的是微服务及其构建和交互。重要的一点是，一个小团队拥有的系统比由一个较大团队构建的系统更容易维护；这只是故事的一半，所以服务毕竟需要相互作用。微服务是领域驱动设计界限上下文的更技术化的方法。

下图显示了微服务世界中的交互是如何进行的。我们有很多小服务相互调用来完成更大的任务：

![构建上下文](img/B03704_05_01.jpg)

交互不仅发生在 API 级别，也发生在开发者级别。

## 分离和共享知识

团队需要意识到在变化发生时如何分享知识并共同合作。埃里克·埃文斯在领域驱动设计中专门讨论了实践中所见的模式。在软件开发中，我们经常看到各种模式，无论是软件模式，比如`DataMapper`、`ActiveRecord`，还是埃里克·埃文斯讨论的关于共同工作过程的模式。

在当前的微服务世界中，我们似乎已经从深度集成转向了更灵活地轻触系统的其他部分的方式。跨团队共享领域，以及了解什么接触了什么的地图变得比以往更加重要。

# 总结

在本章中，我们详细讨论了如何分离系统以及如何处理应用程序中的概念，主要是在项目的较小规模上，以及它如何与较大规模互动。

在构建项目时，我们可以从其他地方使用过的许多想法中汲取经验，无论是面向对象的设计还是软件架构模式；要牢记的重要一点是，没有什么是一成不变的。关于领域驱动设计的一个非常重要的事情是，它不断变化，而这种变化是一件好事。一旦项目变得太固定，就很难改变，这意味着使用它的企业无法随着软件的发展而发展，最终意味着转换到另一个系统和不同的软件，或者重写现有的软件。

下一章将更多地涉及项目整体交织部分的高层视图，详细介绍项目的每个部分所处的上下文。