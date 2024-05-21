# 第六章：上下文地图-整体情况

地牢管理应用程序目前只包含管理囚犯运输的功能，但随着我们应用程序的增长，组织代码的需求也在增加。能够同时在一款软件上工作的开发人员数量有限。亚马逊创始人兼首席执行官杰夫·贝索斯曾经说过，一个团队的规模不应该超过两个披萨所能满足的人数。这个想法是，任何比这更大的团队在沟通方面都会遇到麻烦，因为团队内部的联系数量会迅速增长。随着我们增加更多的人，保持每个人都了解最新情况所需的沟通量也会增加，团队迟早会因为不断需要开会而变慢。

这个事实造成了一种困境，因为正如我们之前所描述的，完美的应用程序应该是每个人都了解开发过程以及围绕变化做出决策的应用程序。这给我们留下了很少的选择：我们可以决定不扩大团队，构建应用程序，但选择一个更慢的开发周期，这个团队可以独立处理（以及整个应用程序上的较小功能集），或者我们可以尝试让多个团队共同开发同一个应用程序。这两种策略在商业上都取得了成功。保持小规模并自然增长，虽然可能不会导致爆发式增长，但可以导致一个运行良好且成功的公司，就像 Basecamp Inc.和其他独立软件开发者所证明的那样。另一方面，这对于固有复杂性并且目标范围更广的应用程序来说是行不通的，因此亚马逊和 Netflix 等公司开始围绕创建由较小部分组成的更大应用程序的理念来扩大他们的团队。

假设我们选择领域驱动设计的理念，我们更有可能拥有一个属于固有复杂领域的应用程序，因此接下来的章节将介绍处理这种情况的一些常见方法。在设计这样的应用程序时不容忽视的一个重要点是，我们应该始终努力尽可能减少复杂性。你将学到：

+   如何在技术上组织不断增长的应用程序

+   如何测试系统中应用程序的集成

+   如何组织应用程序中不断扩展的上下文

# 不要害怕单体应用

近来，人们开始更加倾向于将应用程序拆分并设计成一组通过消息进行通信的服务。这对于大规模应用程序来说是一个成熟的概念；问题在于找到正确的时间来拆分应用程序，以及决定是否拆分是正确的做法。当我们将一个应用程序拆分成多个服务时，这一点会增加复杂性，因为现在我们必须处理跨多个服务的通信问题。我们必须考虑服务的弹性以及每个服务提供其功能所需的依赖关系。

另一方面，当我们在后期拆分应用程序时，从应用程序中提取逻辑会出现问题。没有理由一个单体应用程序不能被很好地因素化，并且在很长一段时间内保持易于维护。拆分应用程序总会带来问题，而长期保持一个良好因素化的应用程序是可行的。问题在于，一个代码库庞大且有很多开发人员在上面工作的情况更容易恶化。

我们如何避免这样的问题？最好的方法是以尽可能简单的方式设计应用程序，但尽可能长时间地避免子系统之间的通信问题。这正是强大的领域模型擅长的；领域将使我们能够与底层框架强烈分离，但也清楚地指出了在必须分解应用程序时应该如何分解。

在领域模型中，我们已经确定了可以稍后分离的区域，因为我们将它们设计为单独的部分。一个很好的例子是囚犯运输，它被隐藏在一个接口后面，以后可以被提取出来。可以有一个团队专门负责囚犯运输功能，只要公共接口没有改变，他们的工作就可以进行，而不必担心其他改变。

更进一步，从纯逻辑角度来看，实际逻辑执行的位置并不重要。囚犯转移可能只是一个调用单独后端的幌子，或者可能在一个进程中运行。这就是一个良好分解的应用程序的全部意义——它提供了一个接口子域功能，并以足够抽象的方式暴露出来，使底层系统易于更改。

只有在必要的情况下，如果分离出一个服务，并且这样做有明显的好处，减少部署或开发依赖的复杂性，使流程的开发能够尽可能地扩展，最好是有一个团队来负责服务的进一步发展。

# 面向服务的架构和微服务

在极端情况下，**面向服务的架构**（**SOA**）最终会变成微服务；每个服务只负责非常有限的功能集，因此很少有改变的理由，易于维护。在领域驱动设计方面，这意味着为应用程序中的每个有界上下文建立一个服务。上下文最终可以被分解为每个聚合由单独的服务管理。管理聚合的服务可以确保内部一致性，服务作为接口意味着访问点非常清晰地定义。大部分问题都转移到了通信层，必须处理弹性。这可能是应用程序中通信层的一个重大挑战，也是服务本身的挑战，因为它们现在必须处理更多的故障模式，由于依赖方之间的通信失败。微服务在某些场景中取得了巨大成功，但整体概念仍然年轻，需要在更广泛的用例中证明自己。

微服务架构或多或少是演员模型的延伸，只有当将演员变成自给自足的服务时，才是对此的延伸。这增加了更好隔离的通信开销；在领域驱动设计中，这可能意味着围绕实体构建服务，因为它们是应用程序部分生命周期的管理者。

总的来说，无论最终选择哪种架构，都有必要考虑如何准备应用程序以便以后可以分解。精心打造灵活的领域模型并利用有界上下文是以这种方式发展应用程序设计的关键。即使应用程序从未真正分解成部分，有清晰的分离也使每个部分更容易处理，并且由于更易理解，因此更容易修改，组件组合应用程序更少出错。

关键点在于要很好地确定核心领域，并最好将其与系统的其他部分隔离开来进行演化。并不是软件的每一部分都会被很好地设计，但是将核心领域及其子域隔离和定义好，使得整个应用程序都准备好进行演化，因为它们是应用程序的核心部分。

# 将所有这些记在脑中

每次我们打开我们选择的编辑器来编写代码时，都需要一些开销来知道从哪里开始以及我们实际需要修改哪个部分。了解从哪里开始修改以朝着特定目标前进，通常是一个应用程序能否愉快地工作的关键区别，而不是一个没有人愿意碰的应用程序。

当我们开始处理一段代码时，我们在任何给定时间内能够在脑海中保留的上下文量是有限的。尽管不可能给出确切的限制，但当代码的某个部分超出了这个限制时很容易注意到。这通常是重构变得更加困难的时候，测试开始变得脆弱，单元测试似乎失去了其价值，因为它们的通过不再确保系统功能。在开源世界中，这通常是项目的一个破坏点，并且由于其开放性质，这一点非常明显。在这一点上，一个库或应用程序如果能够让人们投入时间真正理解内部工作并继续朝着更模块化的设计取得进展，那么它就变得非常有价值，或者开发就会停止。企业应用程序也遭受同样的命运，只是人们更加不愿意放弃提供收入来源或其他重要业务方面的项目。

当项目变得复杂时，人们经常害怕进行任何修改，而且没有人真正理解发生了什么。当痛苦和不确定性开始增长时，重要的是要认识到这一点，并开始分离应用程序的上下文，以保持其规模可管理。

## 识别上下文

当我们绘制应用程序时，我们已经认识到应用程序的某些部分以及它们之间的通信方式。我们现在可以利用这些知识来确保我们对应用程序的上下文有一个概念：

![识别上下文](img/B03704_06_01.jpg)

在第一章中，*典型的 JavaScript 项目*，我们处理了领域中大约有六个上下文。通过最近章节所获得的理解，这有些变化，但基本原理仍在。这些上下文被确定为它们之间的通信是通过交换消息而不是修改内部状态来进行的。在构建 API 的情况下，我们不能依赖于我们处于可以修改内部状态的情况，也不应该有一种方法可以进入上下文并修改其内部，因为这是上下文之间非常紧密的耦合，这将使上下文的有用性变得不明显。

消息是易于建模 API 的关键基础；如果我们以消息为思考方式，很容易想象拆分应用程序和消息不再在本地发送，而是通过网络发送。当然，拆分应用程序仍然不容易，因为突然之间需要处理更多的复杂性，但有能力处理消息传递是摆脱复杂性的一大部分。

### 提示

函数式编程语言** Erlang**将这个概念发挥到了极致。Erlang 使得将应用程序拆分成所谓的进程变得容易，这些进程只能通过发送消息进行通信。这使得 Erlang 能够将进程重新定位到不同的机器上，并抽象出多处理器机器或多机系统的一系列问题。

拥有明确定义的 API 使我们能够在不破坏外部应用程序的情况下对上下文内部进行重构更改。上下文成为我们系统中可以视为黑匣子的角色，并且我们可以使用它们封装的知识来建模其他部分。在上下文内部，应用程序是一个连贯的整体，并且以一种抽象的方式向外部表示其数据。当我们将域和子域公开为接口时，我们生成了一个可塑性系统的构建块。当它们需要共享数据时，有一种明确的方法可以做到这一点，目标应该始终是共享底层数据并在这些数据上公开不同的视图。

## 在上下文中测试

当我们确定可以视为黑匣子的上下文时，我们也应该在测试中使用这些知识。我们已经看到模拟允许我们根据不同的角色进行分离，而上下文在这种方式上是进行单元测试时的一个完美候选。当我们将应用程序分解为上下文时，当然也可以在不同的上下文中开始使用不同的测试风格，使开发过程随着我们的理解和应用程序的变化而发展。在这样做时，我们需要记住整个应用程序需要继续运行，因此还需要测试应用程序的集成。

### 跨边界的集成

在上下文的边界处，从上下文开发者的角度，有多个需要测试的事情：

1.  我们这一方的上下文需要遵守其合同，也就是 API。

1.  两个上下文的集成需要正常工作，因此需要进行跨边界测试。

对于第一点，我们可以将我们的测试视为 API 的使用者。例如，当我们考虑我们的消息 API 时，我们希望有一个测试来确认我们的 API 是否实现了它承诺的功能。这最好通过一个外部测试来覆盖上下文一侧的合同。假设有一个虚构的`Notifier`，它的工作方式如下，就像我们之前使用通知器通过`message`函数发送消息一样：

```js
function Notifier(backend) {
  this.backend = backend
}

function createMessageFromSubject(subject) {
  return {} // Not relevant for the example here.
}

Notifier.prototype.message = function (target, subject, cb) {
  var message = createMessageFromSubject(subject)
  backend.connectTo(target, function (err, connection) {
    connection.send(message)
    connection.close()
    cb()
  })
}
```

当调用通知器时，我们需要测试后端是否以正确的方式被调用：

```js
var sinon = require("sinon")

var connection = {
  send: function (message) {
    // NOOP
  },
  close: function () {
    // NOOP
  }

}

var backend = {
  connectTo: function (target, cb) {
    cb(null, connection)
  }
}

describe("Notifier", function () {
  it("calls the backend and sends a message", function () {
    var backendMock = sinon.mock(backend)
    mock.expects("connectTo").once()

    var notifier = new Notifier(backendMock)
    var dungeon = {}
    var transport = {}
    notifier.message(dungeon, transport, function (err) {
      mock.verify()
    })
  })
})
```

这不会是一个详尽的测试，但基本的断言是我们使用的后端是否被调用了。为了使其更有价值，我们还需要断言正确的调用方式，以及进一步调用依赖项。

第二点需要建立一个集成测试，以覆盖两个或更多上下文之间的交互，而不涉及模拟或存根。当然，这意味着测试很可能会比允许使用模拟和存根来严格控制环境的测试更复杂，因此通常限于相当简单的测试，以确保基本交互正常工作。在这种情况下，集成测试不应该过于详细，因为这可能比预期的更加僵化 API。以下代码测试了系统中囚犯转移系统的集成，使用了地牢等不同的子系统作为集成点：

```js
var prisonerTransfer = require("../../lib/prisoner_transfer")
var dungeon = require("../../lib/dungeon")
var inmates = require("../../lib/inmates")
var messages = require("../../lib/messages")
var assert = require("assert")

describe("Prisoner transfer to other dungeons", function () {

  it("prisoner is moved to remote dungeon", function (done) {
    var prisoner = new inmates.Prisoner()
    var remoteDongeon = new dungeon.remoteDungeon()
    var localDungeon = new dungeon.localDungeon()
    localDungeon.imprison(prisoner)
    var channel = new messages.Channel(localDungeon, remoteDungeon)

    assert(localDungeon.hasPrioner(prisoner))
    prisonerTransfer(prisoner, localDungeon, remoteDungeon, channel, function (err) {
      assert.ifError(err)
      assert(remoteDungeon.hasPrioner(prisoner))
      assert(!localDungeon.hasPrioner(prisoner))
      done()
    })
  })
})
```

前面的代码显示了确保囚犯转移的端到端测试可以涉及多么复杂。由于这种复杂性，只有测试简单交互才有意义，否则端到端测试很快就会随着小的变化而变得难以维护，并且它们应该只覆盖更高级别的交互。

端到端或系统边界的集成测试的目标是确保基本交互正常工作。单元测试的目标是模块本身的行为符合我们的期望。这留下了一个开放的层次，在生产环境中运行服务时会变得明显。

### TDD 和生产测试

测试驱动开发使我们能够设计一个易于更改和发展的系统；相反，它并不能确保完美的功能。我们首先编写一个“有问题”的测试，一个测试，其中基本功能仍然缺失，然后编写代码来满足它。我们不会编写测试以完全避免生产错误，因为我们永远无法预料到可能出现的所有可能的复杂情况。我们编写测试是为了使我们的系统灵活，并且使其准备好投入生产，以便我们可以审视其行为，并且上下文相对独立，以处理故障。

将代码移至生产环境时，我们以一种新的方式来运行系统，为此我们需要准备好进行监视和审视。这种审视和监视也允许由于注入日志模块和其他模块而进行简单的集成测试断言。

我们已经看到了上下文系统如何帮助我们创建一个更稳定、更易于维护的系统。在接下来的部分中，我们将重点关注如何在应用程序中实际维护上下文，以抵制抽象泄漏和上下文泄漏，并且这与组织应用程序的不同方式有关。

# 管理上下文的不同方式

到目前为止，我们应用程序中上下文的主要目的是通过抽象 API 来分离不同的模块，并使整个应用程序的复杂性更易管理。分离上下文的另一个重要好处是，我们可以开始探索在这些解耦部分中管理应用程序开发的不同方式。

应用程序的开发方式随着软件周围的行业快速发展而发展。几年前还是最先进的开发原则现在受到了指责，开发人员希望转向新的方式，使其更具生产力，同时承诺无错误，更易管理的应用程序。当然，更换开发原则并不是免费的，而且往往新的方式并不一定与完整组织的工作方式相匹配。通过分离应用程序的上下文，我们可以开始探索这些新的方式，同时保持团队与他们维护的应用程序一起发展和进步。

管理上下文的第一步是绘制它们之间的关系地图，并开始清晰地分离，使用我们建立的语言。有了这张地图，我们可以开始考虑如何划分应用程序，并将其分解为不同的方式，以便在团队内实现最大的生产力。

## 绘制上下文地图

到目前为止，我们一直在跟踪的囚犯运输应用程序涉及多个上下文。每个上下文都可以通过清晰的 API 进行抽象，并聚合多个合作者，以使囚犯运输作为一个整体运行。我们可以在之前看到的集成测试中跟踪这些合作者，并在项目中为每个人绘制出它们的关系地图。以下图表显示了囚犯运输中涉及的不同上下文的概述，包括它们的角色：

![绘制上下文地图](img/B03704_06_02.jpg)

目前，地图涉及四个主要上下文，正如我们在前面的集成测试中看到的：

+   囚犯管理

+   - 地牢

+   消息系统

+   - 运输

每个上下文负责提供所需的合作者，以使地牢之间的实际传输发生，并且只要 API 保持一致，就可以用不同的实现替换它。

对上下文的调查显示了随着应用程序的发展而增加的差异，这意味着需要不同的策略来管理上下文。地牢作为应用程序的主要入口点，管理大部分原始资源。地牢将成为地牢管理太阳系中的太阳。它提供对资源的访问，然后可以用来完成不同的任务。因此，地牢是应用程序的共享核心。

另一方面，有不同的子域使用地牢提供的资源聚集在一起。例如，消息系统以一种大部分解耦的方式为不同的系统提供基础设施，以增强它们完成的任务。我们所看到的囚犯转移就是将这些其他子域联系在一起的一个子域。我们使用地牢提供的资源来构建囚犯转移，并使用解耦的消息功能来增强转移任务。

这三个系统展示了我们如何让不同的上下文共同工作，并提供资源来完成系统要构建的任务。在构建它们时，我们需要考虑这些子域应该如何相关。根据正在构建的不同类型的子系统，不同形式的上下文关系是有用的，并且最好支持开发。需要记住的一件事是，只要应用程序足够简单，大多数情况下，这些都会给开发增加更多的开销，而不是增加灵活性，因为应用程序的共享方面将会变得比以前更复杂。

## 单体架构

在开始开发时，开发应用程序的团队很可能很小，应用程序的上下文本身还不是很大。在这个阶段，将应用程序域的上下文分离出来可能没有意义，因为它们仍然是灵活的，还没有发展到需要一个单独的团队来管理它们的程度。此外，在这个阶段，API 还不够稳定，无论事先进行了多少规划，都无法实现一个坚实的抽象。

### 提示

Martin Fowler 也在谈论这个问题，并建议首先构建一个单体，然后根据需要将其拆分。您可以在他的博客上找到更多信息[`martinfowler.com/bliki/MonolithFirst.html`](http://martinfowler.com/bliki/MonolithFirst.html)。

在这个阶段，应用程序开发将最好使用提供对模型的共享访问的单体架构。当然，这并不意味着一切都应该是一大堆代码，但特别是在单体中，很容易将对象拆分出来，因为每个人都可以访问它们。这将使以后拆分应用程序变得更容易，因为边界在开发过程中往往会发展。

这也是我们迄今为止开发应用程序的方式；即使我们认识到存在上下文，这些上下文不一定意味着分离成不同的应用程序或领域，但目前它们是开发者头脑中的地图，用于指导代码的位置和交互的流程。看看囚犯转移，它可能看起来像这样：

```js
prisonerTransfer = function (prisoner, otherDungeon, ourDungeon, notifier, callback) {
  var keeper = ourDungeon.getOrc()
  var carriage = ourDungeon.getCarriage()
  var transfer = prepareTransfer(carriage, keeper, prisoner)
  if (transfer) {
    notifier.message(dungeon, transfer)
    callback()
  } else {
    callback(new Error("Transfer initiation failed."))
  }
}

function prepareTransfer(carriage, keeper, prisoner) {
  return {} // as a placeholder for now
}
```

现在，代码直接访问应用程序的每个部分。即使通信被包装成控制流的对象，囚犯转移中发生了大量的交互，如果应用程序被拆分，这些交互将需要通过网络访问。这种组织形式对于单体应用程序是典型的，当它被分解成不同的部分时会发生变化，但整体上下文将保持不变。

## 共享内核

我们已经看到，地牢就像我们的兽人地牢管理宇宙中的太阳，因此将其功能跨应用程序共享是有意义的。

这种开发方式是一种**共享内核**。地牢本身提供的功能需要在许多不同的地方进行复制，除非以某种方式进行共享，而且由于功能是如此关键的一部分，它与供应链的慢接口并不相容，例如。

地牢为使用它的不同部分提供了许多有用的接口，因此功能需要与使用者一起开发。回到囚犯运输，代码将如下所示：

```js
var PrisonerTransfer = function (prisoner, ourDungeon) {
  this.prisoner = prisoner
  this.ourDungeon = ourDungeon
  this.assignDungeonRessources()
}

PrisonerTransfer.prototype.assignDungeonRessources = function () {
  var resources = this.ourDungeon.getTransferResources()
  this.carriage = resources.getCarriage()
  this.keeper = resources.getKeeper()
}

PrisonerTransfer.prototype.prepare = function () {
  // Make the transfer preparations
  return true;
}

PrisonerTransfer.init = function (prisoner, otherDungeon, ourDungeon, notifier, callback) {
  var transfer = new PrisonerTransfer(prisoner, ourDungeon)
  if (transfer.prepare()) {
    notifier.message(otherDungeon, transfer)
    callback()
  } else {
    callback(new Error("Transfer initiation failed."))
  }
}
```

在前面的代码中，我们使用了一个常见的模式，它使用`init`方法来封装一些初始化地牢所需的逻辑。这通常对于使外部创建变得容易很有用，而不是在复杂的构造函数中处理它，我们将其移到一个单独的工厂方法中。优点是，简单方法的返回值比使用复杂构造函数更容易处理，因为失败的构造函数可能会导致一个半初始化的对象。

这里的重要一点是，地牢现在支持一个特定的端点，以提供转移所需的资源。这很可能会锁定给定的资源并为其初始化一个事务，以便它们在物理世界中不会被重复使用。

由于我们的共享内核特性，这种变化可以同时发生在囚犯转移和应用程序的地牢部分。共享内核当然并非没有问题，因为它在部分之间创建了强耦合。牢记这一点并仔细考虑，是否真的需要在共享内核中使用这些部分，或者它们是否属于应用程序的另一部分，这总是有用的。共享数据并不意味着有理由共享代码。对于应用程序中囚犯转移的视图可能会有所不同：虽然转移本身可能更关心细节，但消息服务共享转移数据以创建要发送的消息只关心目标和来源，以及参与转移的囚犯。因此，在两个上下文之间共享代码会使每个领域混淆不必要和无关的知识。

这样的共享上下文架构意味着在共享上下文内工作的团队必须紧密合作，这部分应用程序必须进行大力重构和审查，以免失控。可以说，这是单体架构的直接演变，但它使应用程序更进一步地分割成多个部分。

对于许多应用程序来说，将一些基本元素分离出来并进行大量变更就足够了，应用程序可以通过共享内核更快地演进，开发团队进行协调。当然，这迫使团队在一般情况下信任彼此的决定，并且工程师之间的沟通开销可能会随着共享内核的演变而增加，此时应用程序已经稳定到一个阶段，团队可以接管应用程序部分的责任，并将其整合到自己的部分中。

## API

构建不同的应用程序需要一组可靠的 API。有了这样的 API，可以从主域和应用程序中提取某些子域，这些子域可以开始完全独立于主应用程序演进，只要它们继续遵守之前的相同 API。

首先要识别一个子域，以便为其提供一个清晰的 API 层来构建。查看上下文映射将显示子域的交互，而这些交互是 API 模型应该基于的。首先以更单片式的方式构建，然后在其子域中巩固时分解出部分，将自然地朝着这个方向发展。

### 提示

与以前相同的 API 一致通常只被视为接受相同的输入并产生相同的输出，但实际上还有更多内容，以便提供一个可替换的组件。新应用程序需要提供类似的保证，以确保响应时间和其他服务水平，例如数据持久性。在大多数情况下，实现一个可替换的组件并不像表面上那么容易，但将应用程序发展到更好的服务水平通常在孤立环境中更容易。

随着我们开发应用程序，我们现在可以自由地分支出去，同时保持对应用程序使命的忠诚。我们为其他需要遵循我们做事方式的应用程序提供服务，但仅限于应用程序的调用。

### 顾客和供应商

提供服务的应用程序是某种服务的供应商。我们可以将消息系统视为这样的服务。它为其他应用程序提供了一个发送消息到特定端点的入口点。如果它们想要接收消息，这些端点需要提供必要的调用，而消息系统则负责传递消息。使用消息系统的应用程序需要以某种方式调用系统。

这种互动方式非常抽象，而且像这样的一个好应用程序并不提供很多端点，而是提供了非常高级的入口点，以便尽可能地使使用变得容易。

### 开发客户端

像这样使用内部应用程序可以有多种方式。接口可以非常简单，例如，像这样通过 HTTP 进行非常基本的调用：

```js
**$ curl –X POST --date ' {"receiver":1,"sender":2,"message":"new transfer of one prisoner"'  http://api.messaging.orc**

```

像这样的调用对大多数语言来说并不需要单独的客户端，因为它非常容易进行交互，并且将被捆绑到客户应用程序中，以任何被认为最佳的方式。

当然，并非每个应用程序都能提供如此简单的接口，因此在这个阶段需要提供一个客户端，最好是在应用程序的不同客户之间共享，以避免重复工作。这可以由开发应用程序提供，例如在复杂客户端的情况下，也可以由其中一个客户应用程序发起，然后以与共享内核相同的方式共享。在大多数更大的系统中，客户端往往是由应用程序开发团队提供的，但这并不一定是最好的方式，因为他们并不总是了解使用他们应用程序的复杂性，因此邀请每个消费者的封装客户端与内部客户端一起发展可能更好。

## 墨守成规

将应用程序分割为 API 供应商和消费者是一个非常明显的分割，即使有提供的客户端，这意味着应用程序现在由多个部分组成，不再作为一个单元进行开发。这种分割通常被认为可以增加开发速度，因为团队可以更小，不再需要如此强烈的沟通。然而，当两个独立的应用程序需要共同提供新功能时，这是需要付出代价的。

当我们需要跨界通信时，这是昂贵的，不仅在网络和方法调用速度方面，而且在整体团队沟通方面也是如此。提供应用程序不同部分的团队并不是为了相互合作而设立的，建立这种结构所需的时间是我们每次开发合作功能时都要付出的额外开销。

| | *设计系统的组织...受限于产生与这些组织沟通结构相同的设计...* | |
| --- | --- | --- |
| | --*M. 康威* |

这种发展是康威定律的一种反向效应，因为组织将会产生受其结构限制的系统，强制使用不同的结构将无意中减慢团队的速度，因为它不适合开发这样的应用程序。

当面对一个不断增长的应用程序时，我们需要做出选择：我们可以决定拆分应用程序，或者处理增长痛苦的结果。处理遗留应用程序的痛苦，并且只是顺应其发展路线，根据整体系统的预期走向，这可能是一个不错的选择。例如，如果应用程序在一段时间内处于维护模式，并且不太可能很快增加功能，决定继续这条路线，即使模型不完美，代码库看起来遗留，可能是最好的选择。

成为顺从者是不受欢迎的选择，但它遵循“永远不要重写”的规则，毕竟，开发一个实际有用的应用程序比开发一个可能工程精良但没有价值并因此迟早被忽视的应用程序更有意义。

## 反腐层

在应用程序的生命周期中，有一个特定的阶段，只是添加更多功能并顺应已有设计不再具有生产力。在这个阶段，从主应用程序中分离出来并开始摆脱软件中不断增加的复杂性很有意义。在这个阶段，重新构建领域语言也是一个好主意，并且看看遗留代码库如何适应模型，因为这可以让您创建坚实的抽象，并在其上设计一个良好的 API。这种开发提供了对代码的外观，我们指的是提供一个层来保护应用程序免受可能泄漏进来的旧术语和问题的影响。

### 提示

反腐层是在改进已经投入生产的应用程序时非常重要的模式。隔离新功能不仅更容易测试，还可以增加可靠性，并且可以更容易地引入新模式。

### 隔离方法论

当我们构建这样的层时，我们完全隔离自己不受底层技术的影响；当然，这意味着我们也应该隔离自己不受下面的软件构建方式的影响，并且可以开始使用自原应用程序开始以来开发的所有新方式。

这有一个非常不好的副作用，即旧应用程序立即成为不多人愿意再去工作的遗留应用程序，而且可能会受到很多责备。确保出于这个原因有必要进行如此强烈的分割。

反腐层在集成外部应用程序进入系统的情况下也可能是有意义的，例如，外部银行系统的信用卡处理。最好将外部依赖项与核心应用程序隔离开来，即使只是因为外部 API 可能会发生变化，调整所有调用者很可能比调整内部抽象更复杂。这正是反腐层擅长的，因此很快你的内部依赖项最好像外部依赖项一样处理。

## 分道扬镳

类似于反腐层，以更分离的方式，试图解决应用程序在域中分离的问题。当我们在系统中开发一个共同的语言并将应用程序拆分时，语言将变得更加精细，某些模型的复杂性将增加在某些应用程序中，但不一定在其他应用程序中。这可能会导致问题，因为共享的核心被使用，因为这个核心需要合并每个子域所需的最大复杂性，因此在我们宁愿保持它小的同时继续增长。

问题在于何时决定某个应用程序需要在域模型层面进行拆分，因为对于一个部分的增加复杂性不再增强另一个部分的可用性。在我们的应用程序中，可能的候选者是共享在其他应用程序中的地牢模型。我们希望尽可能地保持它小，但应用程序的部分将对它有不同的需求。消息子系统将需要专注于将消息传递给地牢并增加这部分的复杂性，而处理囚犯运输前提条件的系统将关心其他资源管理部分。

### 无关的应用程序

由于不同的应用程序对核心域有如此不同的要求，因此不共享模型而为需要它的应用程序构建一个特定的模型是有意义的，只共享数据存储或其他一些共享状态的手段。目标是减少依赖关系，这可能意味着只共享实际需要共享的内容，即使名称可能暗示相反。在共享数据存储时，重要的是要记住，只有拥有数据的子域应该能够修改它，而其他所有子域应该使用 API 来访问数据，或者只有只读访问权限。这取决于 API 的开销是否可持续，或者是否需要直接集成以提高性能。

当应用程序开始以不同的方式使用模型，而它们共享模型的唯一原因是模型的名称相同时，我们可以开始寻找更适合目的的更具体的名称，甚至在某个时候完全摆脱主要模型。在我们的地牢示例中，情况可能是，随着时间的推移，地牢本身只能被减少到只作为应用程序的入口点，充当管理子域应用程序的路由器。

将更多功能移出应用程序最初共享的上下文，意味着我们减少了共享子域的表面，并且最初误解了这个域的角色。这并不是坏事，因为每个应用程序都应该被建立为进化，随着上下文变得更加清晰，这反过来可以澄清先前不清晰的子域边界。

### 提示

不要过于执着于你对域和子域边界的理解。从业务专家那里获得经验可以改善你对子域的理解，因此有界上下文的精炼反过来会影响域。

## 一个开放的协议

使应用程序真正独立的最后一步是将它们发布为开放协议。关键是使应用程序的核心功能从外部公开访问，作为一个公开的标准。这很少是情况，因为它需要大量的维护和初始设置。开放协议的最佳候选者是用于与应用程序通信的特殊通信层，以允许外部客户端。

当应用程序邀请外部用户，甚至可能是外部客户端时，应用程序的 API 可以被视为一个开放协议。在我们的地牢应用程序中，我们可能希望将消息子系统在某个时候变成一个开放协议，以允许其他地牢通过它们自己的本地应用程序插入，并因此在 Dungeon Management™中建立标准。

在这个阶段，当考虑到开放协议时，我们需要关注的是如何有效地分享协议的知识。

# 分享知识

我们将应用程序拆分为多个子域的子应用程序，我们这样做是为了增加团队的规模，并促进它们之间更好的合作。这也意味着团队需要找到一种方式来与新开发人员分享关于应用程序及其使用的信息，以及与进入子域以实现特定目标的开发人员分享信息。

领域语言是我们设计的重要部分，我们已经花了一些时间来构建它。我们可以利用这一点，使这种语言对其他开发人员可用。正如我们所看到的，每个模块的语言都略有调整，是一个需要保持更新的工作文档，这意味着我们需要找到一种方式来保持其发布。

## 发布语言

我们一直在开发的语言是一个不断发展的文档，因此我们必须考虑如何分享其中蕴含的知识。让我们首先定义在一个完美的世界里我们会做什么，然后看看我们如何可以接近这种情况。

在一个完美的世界里，开始开发应用程序的团队会一直保持在一起，直到应用程序的整个生命周期，并继续成长，但核心开发人员会一直在那里。这样的团队会有一个重大优势，即项目的术语和假设被团队共享，因为他们一直在应用程序的整个生命周期中跟踪，并且新的开发人员会加入并通过渗透学习核心团队。他们会慢慢适应团队并遵循规则，必要时打破规则，如果团队一致同意的话。

然而，我们并不生活在一个完美的世界，团队可能会有一些变动，核心开发人员因某种原因离开，并被新面孔取代。当这种情况发生时，应用程序的核心原则可能会丢失，围绕项目的语言可能不再遵循最初的规则，以及许多其他不好的事情。幸运的是，与古代相比，我们不必依赖口口相传，而是可以为他人记录我们的发现。

### 创建文档

文档通常不是软件开发中最受欢迎的部分，但这是因为许多项目中的文档并不实用。当我们创建文档时，重要的是不要陈述显而易见的事实，而是实际上记录在开发过程中出现的问题和想法。

通常，项目中找到的文档是方法的概要，它们接受什么参数，以及它们返回什么。这是一个很好的开始，但并不是所有必要的文档的结束。当不同的人使用项目时，他们需要理解其背后的意图以正确使用 API。当我们创建一个应用程序并决定我们想要什么样的功能以及它们如何工作时，这是重要的文档。到目前为止，在这本书中，我们一直在专注于如何思考应用程序开发，以及如何确保它以一种可理解的形式供他人遵循。所有这些都是需要保留的文档。我们希望确保下一个人能够理解开发过程中的思维，知道术语的含义以及它们之间的关系。

一个很好的开始是保持一个中心文档，其中包含这种信息，靠近应用程序并且对所有感兴趣的人都可访问。使文档尽可能简短，并且有一种方式可以随着项目的发展而看到它的演变是关键的，因此具有某种版本控制是一个非常有用的功能。回溯源代码中的时间是非常常见的，以找出某段代码是如何改变的，能够将正确的文档与之相关联是非常有帮助的。

### 提示

将一个简单的文本文件作为项目的 README 是一个很好的开始。这个 README 可以存在于应用程序存储库中，使文档和应用程序之间的关系非常紧密。

接下来我们通过一个罐头假 API 服务器的示例来看这一点，可在[`github.com/sideshowcoder/canned`](https://github.com/sideshowcoder/canned)找到：

![创建文档](img/B03704_06_03.jpg)

文档的重要点是：

+   项目目标的简短陈述

+   贯穿整个项目的设计理念，以指导新开发人员。

+   功能的示例用法

+   非常重要的代码实现说明

+   主要变更的更改历史

+   设置说明

### 更新文档

将文档保持在应用程序附近具有一些重要的优势；在某个需要特殊权限访问的 wiki 中忽视一些文档太容易了，而在项目上工作时每天查看某些东西更可能保持最新。

文档是整个项目的一个活生生的部分，因此它需要成为项目本身的一部分。特别是在现代、开源激发的开发世界中，每个人都应该能够快速地为项目做出贡献的想法已经根植于开发者文化中，这是一件好事。代码比千言万语的架构规范更有说服力，因此将文档限制在核心设计理念，同时让代码解释具体实现，使文档在长期内更有用，并保持开发人员参与更新过程。

### 测试不是唯一的文档

关于测试的一个侧面说明：通常 TDD 被认为具有提供测试作为文档的好处，因为它们毕竟是如何使用代码的示例。这经常成为不费力地在外部编写任何示例和不记录整体设计的借口，因为阅读测试可以说明设计。

这种方法的问题在于对于测试来说，所有方法都同等重要。很难传达一个辅助决策，因为它似乎在这一刻没有影响，从项目的核心设计理念来看，这使得重构变得困难，并且容易使项目偏离轨道并维护从未打算在一开始就有的功能。对于加入项目的开发人员，文档应该指定核心功能是什么，如果他或她发现核心之外的某个晦涩函数有用，这很好，也是阅读测试的好地方，但有一种方法可以区分主应用程序支持的功能与辅助功能。

尝试使这更加互动的一种方法是 README 驱动开发，我们首先编写 README 并使示例可执行，试图使我们的代码通过我们指定的示例作为第一层测试。

### 提示

您可以在 Tom Preston-Werner 的博客上阅读更多关于 README 驱动开发的内容，[`tom.preston-werner.com/2010/08/23/readme-driven-development.html`](http://tom.preston-werner.com/2010/08/23/readme-driven-development.html)。

# 总结

在本章中，我们重点关注了不同子项目形成子域并通过不同方式相互合作的互动。这种合作可以采取许多形式，取决于应用程序整体的上下文和状态，有些形式可能比其他形式更有价值。

当然，合作的正确选择总是值得讨论的，并且随着项目的发展很可能会改变模式。我想要传达的一个重要观点是，这些合作理念并不是一成不变的；它是一个可变的尺度，每个团队都应该决定什么对他们最有效，以及如何保持应用程序和团队工作的实际复杂性低。

在上一部分中，本章重点关注了在为项目创建文档时的重要事项，以及我们如何使其在不深入创建精心制定的规范的情况下变得有用，因为一旦离开最初创建它们的架构师的手，很少有人会接触或理解它们。

在下一章中，我们将探讨其他开发方法如何适应领域驱动设计，良好的面向对象结构如何支持总体设计，以及领域驱动设计如何受到许多其他技术的影响。