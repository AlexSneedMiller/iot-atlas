---
title: "设备引导"
weight: 15
---


<!-- {{% synopsis-bootstrap %}} -->
未注册设备变为已注册状态并在IoT解决方案中具有完整的功能
<!--more-->


## 挑战

IoT解决方案需要设备执行某种特权升级，以便在该解决方案中从零特权转变为完全注册且可操作的设备。从*零*特权到*完整*特权的转变是需要有独立且有计划的步骤。用于获得特权的每个步骤本身必须遵循最小特权的方法。此外，可能有现有的身份认证与权限系统需要包含在IoT解决方案中。

## 解决方案

通过将这些挑战分解为一个跨越两个不同概念的流程，IoT解决方案可以很好的应对设备引导所带来的挑战。这两个概念是：*注册机构(registration authority)*和*权限提升器(privilege escalator)*

**注册机构**是一个验证从设备接收的证书，令牌或共享凭证的组件。该组件返回供设备使用的解决方案凭证。注册机构至少是一组策略，规定未注册设备可以进行订阅或发布到服务器上定义主题的能力。

**权限提升器**允许具有短期低权限凭据的设备在获得解决方案中的更高权限之前，可以共享更多关于自身的属性或展示正确的行为。如果解决方案需要，权限提升流程可以增加一个单独的权限提升步骤来进行人工批准

虽然有些情况下具体实现可能会将注册与获得完全权限这两者合在一起，但如这个设计所做的将这些挑战分解，每个挑战可以使用使用新系统或遗留系统来明确解决。

下图中显示的"设备引导"设计可以提供此功能.

![Device Bootstrapping](bootstrap.png)

### 步骤说明
1. 设备使用共享凭证或令牌向注册机构注册。
2. 注册机构验证共享凭证或令牌的真实性，注册设备，并将短期凭证返回给设备。
3. 设备使用短期凭据联系权限提升器并共享有关自身的更多信息
4. 权限提升器确定设备的类型，并确定是否应在IoT解决方案中授权设备。如果设备已获得授权，则权限提升器将返回长期凭证，例如IoT解决方案中与设备用途相对应的并与权限相关联的设备证书。
5. 设备使用长期权限并通过服务器的协议端点订阅并发布到设备的[消息主题]({{<ref "/glossary/vocabulary#messaging-topic">}})。

## 考虑点
实施此设计时，请考虑以下问题：

#### 设备的制造过程是否会在设备上创建并放置初始令牌？

如果**不是**，则设备必须具有在设备制造后接收安全令牌或[证书](https://en.wikipedia.org/wiki/Public_key_certificate)的机制。这种机制可能涉及通过移动应用程序进行蓝牙低功耗([BLE](https://zh.wikipedia.org/wiki/%E8%93%9D%E7%89%99%E4%BD%8E%E5%8A%9F%E8%80%97))连接来配置设备。这种机制具有额外的优点，即能够在设备登录到移动应用程序时立即将设备关联用户。

如果**是 - 使用令牌/共享凭证**，在这种情况下，非常重要的一点是使用初始令牌或共享凭证来获取注册到解决方案所需要的最小权限。一旦注册机构验证了初始令牌或共享凭证，此设计的其余步骤需要被遵循。

如果**是 - 使用证书**，则可以以安全的方式制造设备，注册机构也可以弱化甚至不需要了。这说起来容易做起来难，因为许多制造过程都是有意与云断开的。但无论如何，由于制造商引入密钥从而完全去掉注册机构，客户体验和整体系统的简化性都会受益。

#### 用户如何绑定设备?

在几乎所有设备预配置的情况下，我们都需要将设备与用户或已有系统的设备配置文件相关联。这涉及从设备收集附加信息以完成设备注册。收集这些附加信息可以通过以下一种或多种方式完成：

* 通过在制造过程中使用[证书](https://en.wikipedia.org/wiki/Public_key_certificate)可以完成设备的配置，并且可以将这些证书预先映射到设备配置文件。这对于具有大量已知设备的解决方案来说是常见的。
* 设备在与注册机构通信期间报告其型号和序列号，该信息可以预先映射到设备配置文件。
* 设备使用BLE或其他本地通信形式来接收有关其身份的信息，例如用户资料。通过移动应用程序处理是最常见的设备配置形式。本地通信可以与制造期间安装证书两者相结合，使得用户关联和注册过程能够在单个步骤中完成。

#### 是否使用设备证书?
尽管在解决方案中尝试使用证书配置每个设备的想法可能令人生畏，但它是迄今为止配置设备最安全的方式。建立相互身份验证对于防止类似[中间人](https://zh.wikipedia.org/wiki/%E4%B8%AD%E9%97%B4%E4%BA%BA%E6%94%BB%E5%87%BB) 攻击这样的威胁是非常重要的。引导设备时，证书应始终是设备标识的首选。

#### 注册机构是否需要支持与现有客户授权方案的进行定制化集成?
如果**是**，则可以使用应用程序编程接口([API](https://zh.wikipedia.org/wiki/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E6%8E%A5%E5%8F%A3)) 在现有客户授权方案前面实现本设计的注册机构步骤。然后，此API可以在利用客户现有解决方案的同时执行注册机构的相关工作。

## 示例

    <tbd written scenario>
