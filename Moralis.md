https://docs.moralis.io/moralis-server/getting-started/quick-start

## 安装

```
npm install moralis
```

```
//  browser
const Moralis = require('moralis');
```

```
// In a node environment
const Moralis  = require('moralis/node');
```

## Initialize Moralis

您将需要服务器 URL 和 APP ID。可以通过按下上面创建的服务器实例上的“查看详细信息”按钮来找到它们。

```
Moralis.initialize("YOUR_APP_ID");

Moralis.serverURL = 'https://YOUR_MORALIS_SERVER:1337/server'
```

请注意: 万能钥匙只能在安全的环境中使用，而不能在客户端使用！如果需要提供万能钥匙，请使用以下方法:

```
Moralis.initialize("YOUR_APP_ID", "", "YOUR_MASTERKEY");

Moralis.serverURL = 'https://YOUR_MORALIS_SERVER:1337/server'
```

## Authentication

用 Web3来验证新用户的身份非常简单:

```
Moralis.authenticate().then(function (user) {
    console.log(user.get('ethAddress'))
})
```

这将提示用户连接一个 Web3钱包，如 MetaMask，并请求签名。一旦验证了签名，承诺将与 User 对象一起解析。

### Logout

```
await Moralis.User.logOut();
```

如何通过 MetaMask 认证，Web3开发-Ivan on Tech

https://docs.moralis.io/moralis-server/getting-started/quick-start#how-to-authenticate-with-metamask-web3-development-ivan-on-tech

如何合并 MetaMask 地址? Web3编程教程-技术上的 Ivan

https://docs.moralis.io/moralis-server/getting-started/quick-start#how-to-merge-metamask-addresses-web3-programming-tutorial-ivan-on-tech

## User

### Current

一旦用户经过身份验证，用户对象可以从任何地方检索到:



```
const user = Moralis.User.current();
```

当前用户总是经过身份验证。如果没有登录用户 Moralis。 User.current ()将返回 null。

### Getting Attributes

用户对象本身看起来如下所示:

```
{
  className: "_User",
  id: "EFurGjRPhl",
  _objCount: 0,
  attributes: {
    ACL: ParseACL {permissionsById: {…}},
    accounts: ["0xda...cb83"],
    authData: {moralisEth: {…}},
    createdAt: Date,
    ethAddress: "0xda...cb83",
    sessionToken: "r:3be8c91cb4db8fe2f154021f96496f49",
    updatedAt: Date,
    username: "Y9jPGr0H1TcMFHUpYZPjj9xhE",
    email: "satoshin@gmx.com"
  }
}
```

There are two methods for getting user attributes:

获取用户属性有两种方法:

```
const user = Moralis.User.current();

const userAddress = user.get("ethAddress");
const username = user.attributes.username;
```

### **Setting Attributes**

总是使用 set ()方法来更改用户属性。然后调用 save ()。任何新的属性都会自动创建。

```
user.set("phone", "555-555-1234");
user.set("birthDate", "2000-01-01");
await user.save();
```

For more details see the ["Users" section](https://docs.moralis.io/moralis-server/users).

有关详细信息，请参阅“用户”部分。

[Users 用户](https://docs.moralis.io/moralis-server/users)

## Queries

### Creating

一旦你有了存储在 Moralis 的数据，你可能会想要使用它！检索数据的基础是使用 Query 函数。查询描述要检索哪些数据以及从哪里检索数据。

```
// set the collection to query
const collectionName = "EthTokenBalance";
const query = new Moralis.Query(collectionName);
```

### Constraints

要过滤查询返回的数据，可以使用所有常见的约束，如 equalTo、 notEqualTo、 greaterThan、 lessthanorequestin、 containedIn 和 limit。查询对象使用类似于 MongoDB 查询的语法。



```
// users with DOGE tokens
query.equalTo("symbol", "DOGE");

// where the balance > 0
query.greaterThan("balance", 0);

// at most 10 results
query.limit(10);

const results = await query.find();
```

### **Sorting**

分类

The data can also be sorted according to specific attributes using the `ascending` and `descending` functions.

还可以使用升序和降序函数根据特定属性对数据进行排序。



```
// highest to lowest balance
query.descending("balance");
```

### **Results**

结果

Once the query is constructed the results can be obtained by using `find`.

一旦构造了查询，就可以使用 find 获得结果。

```
const results = await query.find();
// [EthTokenBalance, EthTokenBalance, ...]
```

Or only the `first`.

或者只是第一次。

```
// get a specific user by email
const query = new Moralis.Query(Moralis.User);
query.equalTo("email", "vbuterin@ethereum.org");

const user = await query.first();
// {id: "23r2r2qawsdf", attributes: {...} }
```

For more details see the "[Queries](https://docs.moralis.io/moralis-server/database/queries)" page.

有关更多详细信息，请参阅“查询”页。

[Queries 查询](https://docs.moralis.io/moralis-server/database/queries)

### **Historical Transaction Data**

历史交易数据

Whenever a new user is authenticated via Web3, all of their transactions are synced by Moralis into the `EthTransactions` collection. This data can then be queried.

每当一个新用户通过 Web3认证，他们所有的交易都会被 Moralis 同步到 EthTransactions 集合中。然后可以查询这些数据。



```
// get all ETH transactions made by the user
const user = Moralis.User.current();
const transQuery = new Moralis.Query("EthTransactions");
transQuery.equalTo("from_address", user.get("ethAddress"));

const results = await transQuery.find();
console.log(results);
// [EthTransaction, EthTransaction, ...]
```

Note that querying user-related data such as transactions requires permissions governed by the Access Control List (ACL). This means any data private to a specific user, such as transactions, can only be queried by that user. Querying data from other users (like finding someone's profile) requires using the `MasterKey`. For security reasons, the master key should only be used on the server inside [cloud functions](https://docs.moralis.io/moralis-server/cloud-code/cloud-functions).

请注意，查询用户相关数据(如事务)需要由访问控制列表(ACL)控制的权限。这意味着对于特定用户的任何私有数据(例如事务)只能由该用户查询。查询来自其他用户的数据(如查找某人的配置文件)需要使用 MasterKey。出于安全原因，主密钥应该只在云功能内的服务器上使用。

[Cloud Functions 云功能](https://docs.moralis.io/moralis-server/cloud-code/cloud-functions)

### **Real-Time Data**

实时数据

Historical data is great, but what if you're building a blog site and want to display a special message whenever a new user buys your `BLOG` token? This is where [live queries](https://docs.moralis.io/moralis-server/database/live-queries) come in. In addition to grabbing the historical results with `find()`, a query can be subscribed to!

历史数据很好，但是如果您正在构建一个博客站点，并且希望在新用户购买您的 BLOG 令牌时显示一条特殊消息，该怎么办？这就是实时查询的来源。除了用 find ()获取历史结果之外，还可以订阅查询！



```
// BLOG token holders
const query = new Moralis.Query("EthTokenBalance");
query.equalTo("symbol", "BLOG");
query.greaterThan("balance", 0);

// subscribe for real-time updates
const subscription = await query.subscribe();

// handle events
subscription.on("create", function(data) {
  console.log(data.attributes.address, " has joined the BLOG army!");
});
```

This will cause an event to be fired whenever the dataset defined by the query changes.

这将导致在查询所定义的数据集发生更改时触发事件。

- **create**: When a new object is created and fulfills the query.

  创建: 当创建一个新对象并完成查询时。

- **update**: When an existing object in the query is updated and both the old and new values satisfy the query.

  Update: 更新查询中的现有对象并且新旧值都满足查询时。

- **enter**: When an existing object's old values did not satisfy the query but the new values do.

  Enter: 当现有对象的旧值不满足查询，但新值满足查询时。

- **leave**: When an existing object's old values satisfied the query but new values do not.

  Leave: 当现有对象的旧值满足查询，而新值不满足查询时。

- **delete**: When an existing object is deleted.

  Delete: 删除现有对象时。

> [Ethereum 实时历史事务 -Web3编程](https://youtu.be/Z8Ik3TyubvU)

Live queries can be created for any other collection as well. See the "[Live Query](https://docs.moralis.io/moralis-server/database/live-queries)" page for more details. You can get more information on how to receive real-time updates on smart contract events on the "[Real-Time Transactions](https://docs.moralis.io/moralis-server/automatic-transaction-sync/historical-transactions)" page under the "[Transactions and Balances](https://docs.moralis.io/moralis-server/automatic-transaction-sync/historical-transactions)" section.

还可以为任何其他集合创建实时查询。更多详细信息请参阅“ Live Query”页面。您可以在“事务和余额”部分的“实时事务”页面上获得更多关于如何接收智能合同事件的实时更新的信息。

[Live Queries 实时查询/moralis-server/database/live-queries](https://docs.moralis.io/moralis-server/database/live-queries)

[Web3 Provider 3提供商/moralis-server/web3/web3](https://docs.moralis.io/moralis-server/web3/web3)

[Deploy and Track ERC-20 Events 部署和跟踪 ERC-20赛事](https://docs.moralis.io/guides/deploy-and-track-erc20-events)

## Demos

> # 什么是 Moralis 服务器？
>
> 在每一个核心的 dApp 建立与莫拉里斯是一个莫拉里斯服务器。和 Moralis SDK 一起，它可以让你快速建立一个 dApp 与用户认证和区块链数据，如用户令牌余额，nft，交易和事件。
>
> 让我们快速总结一下您将要使用的 Moralis 服务器的不同组件。
>
> ## 数据库
>
> 下面是所有数据的存储位置。例如，当用户使用加密钱包身份验证登录到 dApp 时，钱包地址将自动与您配置的任何数据一起保存到您的数据库中，例如令牌余额、历史交易或事件。
>
> 然后你可以在 dApp 前端即时使用这些数据。
>
> 你可以阅读更多的部分: Moralis 服务器数据库和用户认证。
>
> ## 云代码
>
> 如果需要在 dApp 中执行后端代码，可以使用 Moralis 的 Cloud Code 特性。也许您需要对需要在后端进行计算的数据进行聚合或过滤。通过使用云代码，你可以用 JavaScript 编写函数，然后当某些事件发生或由预定的作业触发时，可以通过从 dApp 调用它来触发这些函数。
>
> 您可以在云代码部分阅读更多关于这方面的内容。
>
> ## Moralis SDK
>
> Moralis 的 SDK 是我们如何把这一切联系在一起的。我们的 JavaScript SDK 是你的 dApp 如何与你的 Moralis 服务器交互的。通过 SDK，您可以通过用户名和密码或像 MetaMask 这样的加密钱包对用户进行身份验证。您还可以使用 SDK 获取和设置用户数据，获取余额、 nft、事件或事务。
>
> 你可以点击这里阅读更多关于 SDK 的信息。
>
> # 创建 Moralis 服务器实例
>
> 首先，在 moralis.io 注册一个免费账户。登录后，点击“创建一个新的应用程序”
>
> ## 主网络 vs Testnet vs Local
>
> 创建新实例时有三种环境选择。Moralis 可以同步来自多个块环链的交易，然而，同一组中的每个块环链，无论是来自主网络还是测试网络，只有一个可以同步。
>
> 要同时使用 mainnet 和 testnet，需要创建两个独立的服务器实例。
>
> 选择主网络将连接你的 Moralis 服务器到生产“永久”区块链。在主网络上的交易有真正的财务后果。
>
> 对于 dApps，只从封锁链条阅读-像一个投资组合应用程序-它是完全安全的创建一个服务器直接在主网络。
>
> 就像 mainnet 一样，只不过是为了测试。Testnets 上的所有标记都是毫无价值的。事实上，有一些水龙头可以让开发人员免费测试令牌。Testnet 是一个很好的“临时”环境，可以在部署到 mainnet 之前进行最终测试。
>
> ### 本地 Devchain 服务器
>
> 这是一个区块链，运行在你的计算机本地。当建立你的智能合同时，你可能会想要从一个本地的开发链开始，比如 Ganache 或者 Hardhat，因为他们真的很快。您可以完全控制本地链，这对于创建单元测试非常方便。
>
> ## 选择锁链
>
> 选择一个名字，选择你想要的位置，并选择哪个网络(区块链)你的应用程序需要连接(或所有!).
>
> 服务器需要几分钟才能启动。一旦完成，它将看起来像这样:
>
> 通过按“ ...”按钮展开服务器实例以查看更多详细信息。
>
> ## 设置本地开发链(可选)
>
> 在构建新的智能合同时，您需要一个快速且可控的测试环境。这就是托管你自己的本地区块链是非常有用的。如果您是 Web3开发的新手，可以跳过这一部分，使用 mainnet 或 testnet，因为您可以通过使用现有的智能契约、事务和身份验证对 Moralis 进行大量操作。
>
> 要连接莫拉里斯到甘纳许或安全帽，请查看视频教程的“甘纳许 & 安装”部分链接到下面。
>
> ## 电子邮件配置(可选)
>
> 支持向用户发送电子邮件。点击服务器实例上的“查看详细信息”按钮，然后点击“电子邮件配置”标签。您需要注册一个 SendGrid 帐户，并提供以下内容:
>
> 详情请参阅「发送电邮」网页。
>
> # 安装 SDK
>
> ## 通过 CDN
>
> 将 Moralis SDK 加入 JavaScript 项目的最快方法是通过 npmdn，可以在 https://unpkg.com/Moralis/dist/Moralis.js 中找到。
>
> 不要在生产中使用“最新”，一定要注明版本
>
> 在生产中，请确保指定特定的版本，以防范恶意软件包更新。
>
> ## 通过 NPM
>
> 对于较大的项目，使用 npm 模块。
>
> ## 浏览器
>
> 然后像往常一样把它包括进去。
>
> 对于服务器端应用程序或 node.js 命令行工具，包括:
>
> ## 莫里斯小片段
>
> 为了帮助您快速编码，您可以使用 Moralis 代码段作为 Visual Studio Code 的扩展。
>
> 安装好扩展后，首先在 JavaScript 文件中的任何地方输入: “ moralis”。
>
> 您将为可用的片段获得智能感知。
>
> ## 初始化 Moralis
>
> 为了使用 Moralis 在你的代码，它需要被初始化。您将需要服务器 URL 和 APP ID。可以通过按下上面创建的服务器实例上的“查看详细信息”按钮来找到它们。
>
> 请注意: 万能钥匙只能在安全的环境中使用，而不能在客户端使用！如果需要提供万能钥匙，请使用以下方法:
>
> JavaScript SDK 最初是基于流行的 Backbone.JS 框架，但它提供了灵活的 api，允许它与您喜爱的 JS 库配对。我们的目标是最小化配置，让你快速开始在 Moralis 上构建 JavaScript 和 HTML5应用程序。
>
> 支持 Firefox 23 + ，Chrome 17 + ，Safari 5 + 和 IE 10。9只支持 HTTPS 托管的应用程序。
>
> # 认证
>
> 用 Web3来验证新用户的身份非常简单:
>
> 这将提示用户连接一个 Web3钱包，如 MetaMask，并请求签名。一旦验证了签名，承诺将与 User 对象一起解析。
>
> ## 登出
>
> ### 如何通过 MetaMask 认证，Web3开发-Ivan on Tech
>
> ### 如何合并 MetaMask 地址? Web3编程教程-技术上的 Ivan
>
> # 使用者
>
> ## 目前
>
> 一旦用户经过身份验证，用户对象可以从任何地方检索到:
>
> 当前用户总是经过身份验证。如果没有登录用户 Moralis。 User.current ()将返回 null。
>
> ## 获得属性
>
> 用户对象本身看起来如下所示:
>
> 获取用户属性有两种方法:
>
> ## 设置属性
>
> 总是使用 set ()方法来更改用户属性。然后调用 save ()。任何新的属性都会自动创建。
>
> 有关详细信息，请参阅“用户”部分。
>
> # 查询
>
> ## 创造
>
> 一旦你有了存储在 Moralis 的数据，你可能会想要使用它！检索数据的基础是使用 Query 函数。查询描述要检索哪些数据以及从哪里检索数据。
>
> ## 约束
>
> 要过滤查询返回的数据，可以使用所有常见的约束，如 equalTo、 notEqualTo、 greaterThan、 lessthanorequestin、 containedIn 和 limit。查询对象使用类似于 MongoDB 查询的语法。
>
> ## 分类
>
> 还可以使用升序和降序函数根据特定属性对数据进行排序。
>
> ## 结果
>
> 一旦构造了查询，就可以使用 find 获得结果。
>
> 或者只是第一次。
>
> 有关更多详细信息，请参阅“查询”页。
>
> ## 历史交易数据
>
> 每当一个新用户通过 Web3认证，他们所有的交易都会被 Moralis 同步到 EthTransactions 集合中。然后可以查询这些数据。
>
> 请注意，查询用户相关数据(如事务)需要由访问控制列表(ACL)控制的权限。这意味着对于特定用户的任何私有数据(例如事务)只能由该用户查询。查询来自其他用户的数据(如查找某人的配置文件)需要使用 MasterKey。出于安全原因，主密钥应该只在云功能内的服务器上使用。
>
> ## 实时数据
>
> 历史数据很好，但是如果您正在构建一个博客站点，并且希望在新用户购买您的 BLOG 令牌时显示一条特殊消息，该怎么办？这就是实时查询的来源。除了用 find ()获取历史结果之外，还可以订阅查询！
>
> 这将导致在查询所定义的数据集发生更改时触发事件。
>
> 还可以为任何其他集合创建实时查询。更多详细信息请参阅“ Live Query”页面。您可以在“事务和余额”部分的“实时事务”页面上获得更多关于如何接收智能合同事件的实时更新的信息。
>
> ### Ethereum 实时历史事务 -Web3编程
>
> # 创建 Moralis 服务器实例
>
> 首先，在 moralis.io 注册一个免费账户。登录后，点击“创建一个新的应用程序”
>
> ## 主网络 vs Testnet vs Local
>
> 创建新实例时有三种环境选择。Moralis 可以同步来自多个块环链的交易，然而，同一组中的每个块环链，无论是来自主网络还是测试网络，只有一个可以同步。
>
> 要同时使用 mainnet 和 testnet，需要创建两个独立的服务器实例。
>
> 选择主网络将连接你的 Moralis 服务器到生产“永久”区块链。在主网络上的交易有真正的财务后果。
>
> 对于 dApps，只从封锁链条阅读-像一个投资组合应用程序-它是完全安全的创建一个服务器直接在主网络。
>
> 就像 mainnet 一样，只不过是为了测试。Testnets 上的所有标记都是毫无价值的。事实上，有一些水龙头可以让开发人员免费测试令牌。Testnet 是一个很好的“临时”环境，可以在部署到 mainnet 之前进行最终测试。
>
> ### 本地 Devchain 服务器
>
> 这是一个区块链，运行在你的计算机本地。当建立你的智能合同时，你可能会想要从一个本地的开发链开始，比如 Ganache 或者 Hardhat，因为他们真的很快。您可以完全控制本地链，这对于创建单元测试非常方便。
>
> ## 选择锁链
>
> 选择一个名字，选择你想要的位置，并选择哪个网络(区块链)你的应用程序需要连接(或所有!).
>
> 服务器需要几分钟才能启动。一旦完成，它将看起来像这样:
>
> 通过按“ ...”按钮展开服务器实例以查看更多详细信息。
>
> ## 设置本地开发链(可选)
>
> 在构建新的智能合同时，您需要一个快速且可控的测试环境。这就是托管你自己的本地区块链是非常有用的。如果您是 Web3开发的新手，可以跳过这一部分，使用 mainnet 或 testnet，因为您可以通过使用现有的智能契约、事务和身份验证对 Moralis 进行大量操作。
>
> 要连接莫拉里斯到甘纳许或安全帽，请查看视频教程的“甘纳许 & 安装”部分链接到下面。
>
> ## 电子邮件配置(可选)
>
> 支持向用户发送电子邮件。点击服务器实例上的“查看详细信息”按钮，然后点击“电子邮件配置”标签。您需要注册一个 SendGrid 帐户，并提供以下内容:
>
> 详情请参阅「发送电邮」网页。
>
> # 安装 SDK
>
> ## 通过 CDN
>
> 将 Moralis SDK 加入 JavaScript 项目的最快方法是通过 npmdn，可以在 https://unpkg.com/Moralis/dist/Moralis.js 中找到。
>
> 不要在生产中使用“最新”，一定要注明版本
>
> 在生产中，请确保指定特定的版本，以防范恶意软件包更新。
>
> ## 通过 NPM
>
> 对于较大的项目，使用 npm 模块。
>
> ## 浏览器
>
> 然后像往常一样把它包括进去。
>
> 对于服务器端应用程序或 node.js 命令行工具，包括:
>
> ## 莫里斯小片段
>
> 为了帮助您快速编码，您可以使用 Moralis 代码段作为 Visual Studio Code 的扩展。
>
> 安装好扩展后，首先在 JavaScript 文件中的任何地方输入: “ moralis”。
>
> 您将为可用的片段获得智能感知。
>
> ## 初始化 Moralis
>
> 为了使用 Moralis 在你的代码，它需要被初始化。您将需要服务器 URL 和 APP ID。可以通过按下上面创建的服务器实例上的“查看详细信息”按钮来找到它们。
>
> 请注意: 万能钥匙只能在安全的环境中使用，而不能在客户端使用！如果需要提供万能钥匙，请使用以下方法:
>
> JavaScript SDK 最初是基于流行的 Backbone.JS 框架，但它提供了灵活的 api，允许它与您喜爱的 JS 库配对。我们的目标是最小化配置，让你快速开始在 Moralis 上构建 JavaScript 和 HTML5应用程序。
>
> 支持 Firefox 23 + ，Chrome 17 + ，Safari 5 + 和 IE 10。9只支持 HTTPS 托管的应用程序。
>
> # 认证
>
> 用 Web3来验证新用户的身份非常简单:
>
> 这将提示用户连接一个 Web3钱包，如 MetaMask，并请求签名。一旦验证了签名，承诺将与 User 对象一起解析。
>
> ## 登出
>
> ### 如何通过 MetaMask 认证，Web3开发-Ivan on Tech
>
> ### 如何合并 MetaMask 地址? Web3编程教程-技术上的 Ivan
>
> # 使用者
>
> ## 目前
>
> 一旦用户经过身份验证，用户对象可以从任何地方检索到:
>
> 当前用户总是经过身份验证。如果没有登录用户 Moralis。 User.current ()将返回 null。
>
> ## 获得属性
>
> 用户对象本身看起来如下所示:
>
> 获取用户属性有两种方法:
>
> ## 设置属性
>
> 总是使用 set ()方法来更改用户属性。然后调用 save ()。任何新的属性都会自动创建。
>
> 有关详细信息，请参阅“用户”部分。
>
> # 查询
>
> ## 创造
>
> 一旦你有了存储在 Moralis 的数据，你可能会想要使用它！检索数据的基础是使用 Query 函数。查询描述要检索哪些数据以及从哪里检索数据。
>
> ## 约束
>
> 要过滤查询返回的数据，可以使用所有常见的约束，如 equalTo、 notEqualTo、 greaterThan、 lessthanorequestin、 containedIn 和 limit。查询对象使用类似于 MongoDB 查询的语法。
>
> ## 分类
>
> 还可以使用升序和降序函数根据特定属性对数据进行排序。
>
> ## 结果
>
> 一旦构造了查询，就可以使用 find 获得结果。
>
> 或者只是第一次。
>
> 有关更多详细信息，请参阅“查询”页。
>
> ## 历史交易数据
>
> 每当一个新用户通过 Web3认证，他们所有的交易都会被 Moralis 同步到 EthTransactions 集合中。然后可以查询这些数据。
>
> 请注意，查询用户相关数据(如事务)需要由访问控制列表(ACL)控制的权限。这意味着对于特定用户的任何私有数据(例如事务)只能由该用户查询。查询来自其他用户的数据(如查找某人的配置文件)需要使用 MasterKey。出于安全原因，主密钥应该只在云功能内的服务器上使用。
>
> ## 实时数据
>
> 历史数据很好，但是如果您正在构建一个博客站点，并且希望在新用户购买您的 BLOG 令牌时显示一条特殊消息，该怎么办？这就是实时查询的来源。除了用 find ()获取历史结果之外，还可以订阅查询！
>
> 这将导致在查询所定义的数据集发生更改时触发事件。
>
> 还可以为任何其他集合创建实时查询。更多详细信息请参阅“ Live Query”页面。您可以在“事务和余额”部分的“实时事务”页面上获得更多关于如何接收智能合同事件的实时更新的信息。
>
> ### Ethereum 实时历史事务 -Web3编程
>
> There are several demo projects on our [GitHub](https://github.com/MoralisWeb3/demo-apps) that highlights the major features of Moralis. Most of the demos are written in plain JavaScript and HTML to keep them as small as possible.
>
> 在我们的 GitHub 上有几个演示项目，突出了 Moralis 的主要特性。大多数的演示都是用纯 JavaScript 和 HTML 编写的，以使它们尽可能的小。
>
> - ****[**Sign-In Boilerplate**](https://github.com/MoralisWeb3/demo-apps/tree/main/moralis-sign-in-boilerplate): The base for the "[Serverless dApps](https://www.youtube.com/watch?v=rd0TTLjQLy4)" tutorial.
>
>   登录样板: “无服务器 dApps”教程的基础。
>
>   - ****[**Casino dApp**](https://github.com/MoralisWeb3/demo-apps/tree/main/casino-dapp) is the finished dApp for this tutorial.
>
>     卡西诺 dApp 是本教程完成的 dApp。
>
> - ****[**React-App**](https://github.com/MoralisWeb3/demo-apps/tree/main/moralis-react-app): A React template starter app.
>
>   React-App: React template starter app.
>
> - ****[**Gas Demo**](https://github.com/MoralisWeb3/demo-apps/tree/main/moralis-gas-demo): Authentication, historical transactions, and cloud functions.
>
>   Gas Demo: 身份验证、历史事务和云功能。
>
>   - Displays average gas fees for the top 10 users that paid the most in gas.
>
>     显示支付最多汽油的前10位用户的平均汽油费用。
>
> - ****[**Tether Printer**](https://github.com/MoralisWeb3/demo-apps/tree/main/tether-printer): Smart contract events, and live queries.
>
>   Tether Printer: 智能合同事件和实时查询。
>
>   - Shows the most recent ETH USDT printed by Tether.
>
>     显示由 Tether 打印的最新 ETH USDT。
>
> - [**Swap Monitor**](https://github.com/MoralisWeb3/demo-apps/tree/main/swap-monitor): Smart contracts events, triggers, and live queries.
>
>   交换监视器: 智能契约事件、触发器和实时查询。
>
>   - Real-time swaps on the Uniswap DAI/wETH pair.
>
>     Uniswap DAI/wETH 对的实时交换。
>
>   - Stats on net volume over the last 60 minutes.
>
>     过去60分钟的净体积统计。
>
> - ****[**User Profiles**](https://github.com/MoralisWeb3/demo-apps/tree/main/user-profiles): Authentication with both username and MetaMask, address linking, changing password, and saving additional info to the user object.
>
>   用户配置文件: 同时使用用户名和 MetaMask 进行身份验证，地址链接，更改密码，并将其他信息保存到用户对象。

# Crypto Login

允许你用一行代码认证任何区块链上的用户。您的用户的所有资产、令牌和 nft 都会自动同步到您的 Moralis 数据库，并随着您的用户进行连锁交易而实时更新。

## Ethereum, BSC, and Polygon Login

### MetaMask

Authenticating users is simple:

认证用户非常简单:



```
Moralis.authenticate().then(function (user) {
    console.log(user.get('ethAddress'))
})
```

This will connect [MetaMask](https://metamask.io/) and request a signature (no gas required!).

这将连接 MetaMask 并请求签名(不需要气体!)。

对于所有与 Ethereum 虚拟机(EVM)兼容的链，如 Binance 智能链和 Polygon (Matic) ，它的工作方式相同，因为它们都共享相同的 Ethereum 地址。

[How to Authenticate With Metamask, Web3 Development ](https://www.youtube.com/watch?v=6BfOtYfwFBI)

### WalletConnect

Moralis also supports authentication using WalletConnect. First add the provider by adding the script (make sure to use the latest version, see https://github.com/WalletConnect/walletconnect-monorepo/releases):

Moralis 也支持使用 WalletConnect 进行身份验证。首先通过添加脚本添加提供程序(确保使用最新版本，请参阅 https://github.com/walletconnect/walletconnect-monorepo/releases 文档) :



```
<script src="https://github.com/WalletConnect/walletconnect-monorepo/releases/download/1.6.2/web3-provider.min.js"></script>
```

Or install the `@walletconnect/web3-provider` package when you use npm (or another package manager):

或者在使用 npm (或另一个包管理器)时安装@walletconnect/web3-provider 包:



```
npm install @walletconnect/web3-provider
```

Then call authenticate like above, but with a provider option:

然后像上面一样调用 authenticate，但是有一个 provider 选项:



```
const user = await Moralis.authenticate({ provider: "walletconnect" })
```

### Specify the `chainId`.

指定 chainId

You might want to specify the chain id that WalletConnect will use by default. You can do this by providing the `chainId` as an extra option: 

您可能希望指定 WalletConnect 默认使用的链标识。你可以通过提供 chainId 作为一个额外的选项来实现:



```
const user = await Moralis.authenticate({ provider: "walletconnect", chainId: 56 })
```

### Filter Mobile Linking Options 

过滤手机链接选项

If you would like to reduce the number of mobile linking options or customize its order, you can provide an array of wallet names to the `mobileLinks` option . 

如果您想减少移动链接选项的数量或自定义其顺序，您可以提供一个钱包名称阵列的 mobileinks 选项。



```
const user = await Moralis.authenticate({ 
    provider: "walletconnect", 
    mobileLinks: [
      "rainbow",
      "metamask",
      "argent",
      "trust",
      "imtoken",
      "pillar",
    ] 
})
```

### Custom Wallet Login

自定义钱包登录

Although Moralis offers native support for MetaMask and WalletConnect, it's possible to use any Web3 provider. The scope of this guide is to demonstrate how to supply any provider.

虽然 Moralis 提供了对 MetaMask 和 WalletConnect 的本地支持，但是使用任何 Web3提供者都是可能的。本指南的范围是演示如何提供任何供应商。

This guide will use `Tourus` and Binance Smart Chain. The `Tourus` documentation is available at this url: https://docs.tor.us/.

本指南将使用 Tourus 和 Binance 智能链，文档可以通过以下链接获得:  https://docs.tor.us/。

The `Moralis` class has a method called `enable`. The first step is to overwrite this method in order to use a custom `Moralis wallet provider` class.

Moralis 类有一个名为 enable 的方法。第一步是覆盖这个方法，以便使用自定义 Moralis 钱包提供程序类。

**Import the Provider**

导入提供程序



```
<script src="https://cdn.jsdelivr.net/npm/@toruslabs/torus-embed"></script>
```

**Overwrite the `enable` Method**

覆盖‘ enable’方法



```
Moralis.enable = async () => {
    const web3Provider = new MoralisTorusProvider();
    const web3 = await web3Provider.activate();
    return web3;
}
```

**Create the Torus Provider**

创建环面供应商



```
class MoralisTorusProvider {
    torus = new Torus({})        async activate() {
        this.provider = await this.torus.init(            {                enableLogging: true,                network: {                    host: "<YOUR BINANCE SPEEDY NODE>",                    networkName: "Smart Chain - Testnet",                    chainId: 97,                    blockExplorer: "https://testnet.bscscan.com",                    ticker: 'BNB',                    tickerName: 'BNB',                },            })        await this.torus.login();                const MWeb3 = typeof Web3 === 'function' ? Web3 : window.Web3;        this.web3 = new MWeb3(this.torus.provider);        this.isActivated = true;
        return this.web3;    }}
```

You are good to go, you can now enable Moralis and connect to Web3 by:

你可以去，你现在可以启用 Moralis 和连接到 Web3通过:



```
window.web3 = await Moralis.enable();
```

## Non-EVM Chain Login

与非以太虚拟机(EVM)兼容的链认证需要更多的设置。

### Elrond

**Prerequisites**: In order to log in with Elrond, you'll need a ledger that can be purchased [here](https://www.ledger.com/). Once you have a ledger, follow the steps in the [Elrond Ledger documentation](https://docs.elrond.com/wallet/ledger/) to add the Elrond app to the device.

先决条件: 要使用 Elrond 登录，您需要在这里购买分类账。一旦你有了一个分类账，按照 Elrond Ledger 文档中的步骤将 Elrond 应用添加到设备中。

Once the above steps are completed, we can continue with the actual authentication process.

完成上述步骤后，我们可以继续实际的身份验证过程。

If using the CDN, you must add the "[erdjs](https://docs.elrond.com/)" script to your `<head>`.

如果使用 CDN，则必须将“ erdjs”脚本添加到 < head > 。



```
<script src="https://npmcdn.com/@elrondnetwork/erdjs@4.0.3/out-browser/erdjs.js"></script>
```

Now that all dependencies are in place we can authenticate:

现在所有的依赖都已经就位，我们可以进行身份验证:



```
Moralis.authenticate({ type: 'erd' }).then(function (user) {
    console.log(user.get('erdAddress'))
})
```

**Important**: Notice the prefix of the address has changed from `eth` to `erd`.

重要: 注意地址的前缀已经从 eth 改为 erd。

[Elrond Login Programming - Moralis Web3 Development](https://www.youtube.com/watch?v=0dLNIbx4GbY)

## User Object

签名完成后，承诺将与 User 对象一起解析。创建用户的形状如下:



```
{
  className: "_User"
  id: String
  _objCount: 0
  attributes: {
    accounts: Array,
    ACL: Object,
    authData: Object,
    createdAt: Date,
    email: String, // empty
    ethAddress: String,
    sessionToken: String, 
    updatedAT: Date,
    username: String, // random value
  }
}
```

For new users, the `username` will be a randomly generated alphanumeric string and the `email` property will not exist. This can be set or changed by the app. See the "[Objects](https://docs.moralis.io/moralis-server/database/objects#saving-objects)" and "[Intro](https://docs.moralis.io/users/intro)" docs.

对于新用户，用户名将是一个随机生成的字母数字字符串，email 属性将不存在。这可以通过应用程序设置或更改。参见“ object”和“ Intro”文档。

It will also create a new entry in the `Moralis._EthAddress` class, corresponding to the Ethereum address used for this user. The schema of the created entry in the `Moralis._EthAddress` object looks like this: 

它还将在 Moralis._ ethaddress 类中创建一个新的条目，与此用户使用的以太网地址相对应。在 Moralis.ethaddress 对象中创建条目的模式如下:



```
{
    "objectId": String,
    "transactions_synced": Number,
    "ACL": ACL,
    "last_eth_sync": Date,
    "data": String,
    "user": Pointer <_User>,
    "updatedAt": Date,
    "is_syncing": Boolean,
    "last_eth_sync_completed": Date,
    "signature": String,
    "last_eth_sync_block": Number,
    "transactions_total": Number,
    "createdAt": Date,
    "last_eth_sync_error": String
}
```

### **Chain Specific User Properties**

特定于链的用户属性

If you enable syncing data from chains other than Ethereum, additional user properties will be created. These will be prefixed with the chain. As an example, for Binance Smart Chain, the prefix is `bsc`. There will be an additional set of properties for each chain.

如果您启用从 Ethereum 以外的链同步数据，将创建其他用户属性。这些将以链条作为前缀。作为一个例子，对于 Binance 智能链，前缀是 bsc。每个链都有一组额外的属性。

- `User.bscAddress` (String).

  Bscaddress (String).

- `User.bscAccounts` (Array).

  Bscaccounts (Array).

It's the same for chain-specific class tables on the Moralis Server. An additional set of classes will be created with the corresponding chain prefixes.

对于 Moralis 服务器上特定于链的类表也是如此。将使用相应的链前缀创建另一组类。

- BscTransactions

- BscTokenBalance

- BscTokenTransfers

  Bsctokentransferers

- BscNFTOwners

  城镇居民

- BscNFTTransfers

  Bscnfttransferers

### **Chain Prefixes**

链前缀

| Chain链条                                                    | Prefix前缀      |
| ------------------------------------------------------------ | --------------- |
| Ethereum Mainnet, Ropsten, Goerli, Rinkeby, Kovan伊斯利恩 · 梅内特，罗普斯滕，Goerli，Rinkeby，科万 | `Eth`伊斯       |
| Binance Smart Chain Mainnet, Testnet智能链主网               | `Bsc`女名女子名 |
| Polygon (Matic) Mainnet, Mumbai TestnetPolygon (Matic) Mainnet，Mumbai Testnet | `Polygon`多边形 |
| Elrond埃尔隆德                                               | `Erd`           |

### **Manually Deleting Users**

手动删除用户

During testing, if you manually delete a user, then make sure to delete the corresponding rows in the following collections (where "xxx" is the chain prefix). Not doing so may cause unexpected behavior when authenticating via Web3 from the address that was just deleted.

在测试期间，如果您手动删除一个用户，那么请确保删除以下集合中的相应行(其中“ xxx”是链前缀)。不这样做可能会导致意外的行为时，身份验证通过 Web3从地址，刚才删除。

- Session

  第二节

- xxxBalance

- _xxxAddress

  地址

### **Changing Sign-In Message**

更改登入信息

The message the user sees when signing in with Web3 can be changed by doing the following in the browser:

用户在使用 Web3登录时看到的消息可以通过在浏览器中执行以下操作来更改:



```
Moralis.authenticate({signingMessage:"hello"})
```

![img](https://gblobscdn.gitbook.com/assets%2F-MVStbACGLCycg7J5WQ2%2F-MaZi546kPuwDx16dqqN%2F-MaZieNgb9i_earj2Jzr%2Fimage.png?alt=media&token=422057d9-97ef-4840-8a53-c44528c797b6)

## **Changing App Icon in MetaMask**

更改 MetaMask 中的应用程序图标

It's possible to change the icon a user sees when interacting with your smart contract. To accomplish this, you'll have to add a favicon to your dApp. Follow the instructions in the [MetaMask docs](https://docs.metamask.io/guide/defining-your-icon.html).

当用户与智能合同进行交互时，可以更改用户看到的图标。要做到这一点，你需要在 dApp 中添加一个图标。按照 MetaMask 文档中的说明操作。