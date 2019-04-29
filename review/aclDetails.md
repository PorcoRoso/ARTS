## 访问控制列表ACL
### 1、什么是ACL?
Fabric使用ACL来实现资源Resource访问控制，通过策略policy来设置。
#### 1.1 资源Resource
资源包括：用户链码、系统链码、事件流
fabric configtx.yaml对这些资源都有默认策略

#### 1.2 策略Policy
策略分两种，并通过type字段区分：
- Signature策略
签名策略的规则支持`AND`、`OR`、`NOutOf`三种，允许构建成类似这种规则：``orgA.Admin`` AND ``两个其他的admin`` OR ``20个之中的11个 admin``
```
Policies:
  MyPolicy:
    Type: Signature
    Rule: “Org1.Peer OR Org2.Peer”
```
上述配置意思：名为`MyPolicy`的策略，只有被`Org1的peer`实体或`Org2的peer`实体签名，才能满足此策略
- ImplicitMeta策略
隐藏元策略，它支持像``大多数的组织admin``这样的规则，一般写法为：
`<ALL|ANY|MAJORITY> <sub_policy>`，比如：`ANY Admins`
这里有三个默认策略配置选项
-- `Admins`: 具有操作权限，指定为Admins（或Admins的子集）的策略可以访问资源，比如实例化通道上的链码
-- `Writers`: 可提出账本更新，如一笔交易，但是通常没有管理权限
-- `Readers`: 角色更为被动，能访问信息，但是既无权限提出账本更新，也无权限执行管理任务
```
Policies:
  AnotherPolicy:
    Type: ImplicitMeta
    Rule: "MAJORITY Admins"
```

#### 1.3 访问控制在哪儿指定？

在`configtx.yaml`文件，`configtxgen`工具会使用此文件构建通道配置（config.json）。
有两种方法更新访问控制，一是通过编辑configtx.yaml文件（可以将ACL的更改传递到新通道）；二是通过更新特定channel的通道配置中的ACL来更新ACL。

### 2、configtx.yaml中如何格式化ACL?
以键值对形式，K为资源函数名，V为字符串，如下所示：
```yaml
# ACL policy for invoking chaincodes on peer
peer/Propose: /Channel/Application/Writers
```

```yaml
# ACL policy for sending block events
event/Block: /Channel/Application/Readers
```
上述ACLs定义了访问`peer/Propose`资源的限制为：满足路径`/Channel/Application/Writers`中定义的策略身份。

#### 2.1 更新configtx.yaml中ACL默认值

在启动区块链网络前，更新configtx.yaml文件。假设需要修改`peer/Propose`的ACL默认配置，让其从`/Channel/Application/Writers`到`MyPolicy`。

首先要在configtx.yaml中添加一个策略`MyPolicy`，它定义在了yaml文件的Application.Policies部分，且指定了

#### 2.2 更新通道配置中的ACL默认值

在已启动的区块链网络中，先更新配置，通过交易更新ACL值。

#### 2.3 满足需要访问多资源的ACL

如果一个成员需要调用多个系统链码，则所有的系统链码的ACLs都要满足。
`peer/Propose`指定通道上的任意提案，如果特定提案需要访问两个系统链码，而这两个系统链码一个需要身份满足`Writers`，另一个需要身份满足`MyPolicy`，当成员提交此提案，必须有同时满足`Writers`和`MyPolicy`的身份。

#### 2.4 对使用实验ACL特征的用户的迁移考虑

1.2之前的版本，对于访问控制列表的管理在`isolated_data`部分，通过`PEER_RESOURCE_UPDATE`交易更新。
从1.1迁移到1.2是可以的，停掉所有节点，更新到1.2，提交通道配置交易（启用v1.2能力并设置ACLs），最终重启更新节点。