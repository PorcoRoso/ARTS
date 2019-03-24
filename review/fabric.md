

### v1.4 新功能

#### Hyperledger Fabric第一个长期支持版本

自v1.0版本开始，Hyperledger Fabric及其团队都日趋成熟。Fabric开发者一直在努力通过网络协作，以便Fabric v1.4版本能以聚焦的稳定性和可操作性交付使用。因此，Fabric v1.4将是第一个长期支持版本。

官方迄今为止的策略是为最近的主要或次要版本提供错误修复（补丁）版本，直到下一个主要或次要版本发布。官方计划在后续版本中继续使用此策略。但是，对Fabric v1.4版本，Fabric维护者承若在发布之日其的一年内提供错误修复，这可能会导致一系列的补丁版本（如v1.4.1,v1.4.2,...），其中多个bug修复会绑定到补丁版本中。

如果您使用的是Fabric v1.4，则可以放心能够安全的升级到后续任何补丁版本。在需要一些升级过程来修复缺陷时，官方会为该过程提供补丁版本。

####可维护性和操作改进

随着越来越多（公司应用）的Fabric网络部署并进入生产状态，Fabirc可维护性和操作变得至关重要。 Fabric v1.4通过日志记录改进，运行状况检查和运营指标实现了巨大飞跃。 因此，Fabric v1.4是生产操作的推荐版本。

**[运营服务](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html)**：新的RESTful运营服务为实施方提供三种服务来监控和管理peer和orderer节点操作：

- /logspec端点允许实施人员动态获取和设置peer和orderer节点的日志记录级别。

- /healthz端点允许实施人员和容器编排服务检查peer和orderer节点的活跃度和健康状况。

- /metrics端点允许实施人员利用Prometheus从peer和orderer节点拉取运营指标，运营指标也可以被推送到StatsD。

#### 用于开发应用的改进编程模型

编写Dapp程序已经变得更加简单了。 Node.js SDK和Node.js链代码中的编程模型改进使得Dapp程序的开发更加直观，因此您可以专注于应用程序逻辑。 现有的npm软件包仍然可供使用，而新的npm软件包提供了一层抽象，以提高开发人员的工作效率和易用性。

新文档使用一个商业票据网络案例，帮助您了解使用Hyperledger Fabric创建Dapp程序的各个方面：

- *[场景](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/scenario.html)*：描述一个假设的业务网络，它涉及六个希望构建一个应用程序进行相互交易的组织，这将作为描述编程模型的用例。

- *[分析](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/analysis.html)*：描述商业票据的结构以及交易如何影响它。证明使用状态和事务进行建模能够提供一种精确的方法来理解和建模分布式业务流程。

- *[流程和数据设计](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/architecture.html)*：展示如何设计商业票据业务流程及其相关数据结构。

- *[智能合约处理](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/smartcontract.html)*：展示智能合约中如何设计管理发行，购买和赎回商业票据的分布式业务流程。

- *[应用程序](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/application.html)*：概念性地描述了客户端应用程序在上述智能合约中的杠杆作用。

- *[应用程序设计元素](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/designelements.html)*：描述合约命名空间，事务上下文，事务处理程序，连接配置文件，连接选项，钱包和网关的详细信息。

  最后，一个教程和示例将商业票据场景变为现实：

- *[商业票据教程](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html)*

####新教程

- [编写您的第一个应用程序](https://hyperledger-fabric.readthedocs.io/en/release-1.4/write_first_app.html)：本教程已更新，以利用改进的Node.js SDK和链代码编程模型。 本教程包含客户端应用程序和链码的JavaScript和Typescript示例。
- [商业票据](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html)：教程如上所述，这是新开发应用程序文档附带的教程。
- [升级到最新版本的Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.4/upgrade_to_newest_version.html)：利用[Building Your First Network](https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html)脚本构建的网络演示从v1.3到v1.4的升级。 包括脚本（可用作升级模板）以及各个命令，以便您可以了解升级的每个步骤。

####私有数据增强功能

- [私有数据](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data-arch.html)：私有数据功能自v1.2开始成为Fabric的一部分，此版本推出了两个新的增强功能：
  - 协调，允许添加到私有数据集合的组织的peer节点，检索其现在有权使用的先前事务的私有数据。
  - 客户端访问控制，可根据客户端组织集合成员资格在链码中自动实施访问控制，而无需编写特定的链码逻辑。

####发行说明
本发行说明为迁移到新版本的用户提供了更多详细信息，以及指向完整版本变更- 日志的链接。

- [Fabric发布说明](https://github.com/hyperledger/fabric/releases/tag/v1.4.0)。
- [Fabric CA发行说明](https://github.com/hyperledger/fabric-ca/releases/tag/v1.4.0)。

译者注：

Fabric v1.4关注点在于运营服务及私有数据增强两部分，运营服务可对接fabric explorer实现智能化运维，私有数据增强帮助新加入机构可检索以往私有数据集合里的数据及简化链码控制逻辑。