
这篇文章对您有用吗？ 我希望听到您的反馈，所以请不要退缩！ 如果您对Kafka，Kubernetes，微服务或事件流感兴趣，或者有任何疑问，请在Twitter上关注我。

可以将服务网格想象为在后台工作的小精灵，它们毫不客气且未被承认，以帮助进行流量管理，检测和可观察性，安全策略实施以及其他平凡但必不可少的任务，而这些尝试会让您无所适从 在您的应用程序代码中实现。 尤其是在信息安全方面，即使在客户端库的帮助下，在应用程序层实现这些问题也从未如此轻松，这也说明了商业API网关供应商的兴旺发展。

本文仅涉及Istio的表面。 我们没有讨论速率限制，断路器，退避和重试等弹性模式； 我们也没有涵盖故障注入或混乱测试-可靠性工程的整个领域又节省了一天。 Istio的高级负载平衡功能，证书管理和授权功能都被忽略了。 在文章变得霸道之前，只能容纳这么多文章。 尽管如此，您现在应该已经具备了在应用程序生态系统中开始使用Istio的必要知识和工具。

精通Istio无疑需要大量的实践，但是与Kubernetes陡峭的学习曲线相比，这是一种缓慢的提升。 此外，与正确地隔离实现每个功能并长期保持这些功能所需的努力相比，回报确实使投资显得非常可行。
# 开始之前

本文旨在提供对Istio的“温和”介绍。 所谓“温柔”，不是指快速，愚蠢或拥挤。 我们将从基本概念入手，然后浏览一下如何将它们与增量示例结合在一起。 期待长时间阅读。 放水壶。 尝试按顺序执行示例，因为某些示例将取决于前面的示例。 到最后，您应该了解Istio是什么，可以在哪里使用它，并有信心自己驱动它。

本文介绍的材料在Kubernetes知识范围内将被分类为中级或高级。 Kubernetes和Docker的强大背景至关重要：您应该对Kubernetes核心概念有透彻的实践了解，具有部署和管理容器化工作负载的能力，并舒适地使用kubectl导航和更改Kubernetes集群的状态。
# 介绍Istio

Istio是一个服务网格—一种应用程序感知的基础结构层，用于促进服务到服务的通信。 “应用程序感知”是指服务网格在某种程度上了解服务通信的性质，并且可以以增值的方式进行干预。 例如，服务网格可以实现弹性模式（重试，断路器），更改流量（调整流量，影响路由行为，促进金丝雀释放），并添加大量的全面安全控制。 意识到服务之间传递的流量，Istio还可以提供细粒度的仪表和遥测洞察力，从而为不透明的分布式系统提供一定程度的可观察性。

注意：服务网格是指OSI参考模型中的第7层协议，但也可以配置为在第3层和第4层运行。

Istio得到了Google，IBM和Lyft的支持，并且是目前使用最广泛的服务网格体系结构。 Kubernetes最初由Google设计，也很好地融入了Istio。 将Istio标记为“ Kubernetes原生服务网格”是公平的。
## 怎么运行的

像大多数服务网格实现一样，Istio通过称为“边车”的代理容器来补充现有的应用程序容器。 Sidecar代理是经过特殊配置的Envoy实例，可拦截进入和离开服务容器的网络流量，并通过专用网络重新路由流量，如下所示。
![](0!bWQio3rKD0XpE8Kl)

Sidecar代理是针对延迟和吞吐量进行了优化的轻型组件，仅具有最小的配置和路由智能。 路由决策是基于由单独的控制平面（服务网格的隐喻“大脑”）托管的策略制定的。 控制平面包括部署在Kubernetes集群中的一组专用组件，就像其他任何容器化应用程序一样，它们驻留在专用的istio-system名称空间中。 控制平面的组件有意与数据平面分离，这些元素执行网络流量的实际路由并直接与应用程序容器接口。 下图说明了这种分离。
![](0!0t8pXEEjVrGnubPt)

注意：Sidecar代理模式不是服务网格实现的唯一方法。 由Netflix技术套件首创的另一种方法是使用客户端库（Ribbon，Hysterix，Eureka和Archaius）来填充数据平面的角色，并从集中控制平面获取路由提示。 这种方法的好处是，与容器编排引擎（或者实际上完全是容器）无关，可以将其以嵌入式形式部署在传统的无容器应用程序中。 Netflix库的好处也是它的缺点-它们需要对应用程序进行侵入式更改，并为有限的编程语言（主要针对JVM）提供支持。 相比之下，更现代的基于sidecar的服务网格设计与语言无关，可与现有应用程序透明部署，无需花费太多时间就可以代表应用程序开发人员。

毫不奇怪，基于Sidecar的服务网格在运营和DevOps团队中得到了广泛采用-在现代服务网格设计中，业务逻辑和基础结构之间的高度可取的分离得以实现。
# 核心概念

Istio扩展了具有多种Istio特定资源类型的“香草” Kubernetes设置的命名法。 作为Kubernetes原生服务网格，Istio设计已使用自定义资源定义（CRD）来实现这些概念。 CRD只不过是YAML中指定并使用kubectl管理的声明性配置片段，类似于诸如Pod，Service，Deployment等内置类型。
## 虚拟服务

虚拟服务指定入站到特定虚拟主机的请求如何路由到基础目标。

虚拟服务将服务使用者与服务提供商之间的耦合程度降到了传统的Kubernetes服务所无法达到的程度，而传统的Kubernetes服务是基本的负载平衡器。 虚拟服务的典型用例包括将请求流量分区到服务的不同版本，这些版本指定为服务子集。 客户端在不了解底层提供程序实现的情况下将请求发送到虚拟服务，然后Envoy根据虚拟服务配置中定义的规则将流量转发到不同版本。 例如，“ X％的呼叫转到新版本”或“这些用户的呼叫转到Y版本”。 精明的读者会将这些用例视为“金丝雀”的部署，其中逐渐增加了对新服务版本的访问量，以缓解大规模爆炸的风险。

除了将流量分配给多个基础提供商实现之外，虚拟服务还允许您做相反的工作-将多个完全不同的服务组合为一个虚拟服务。 换句话说，虚拟服务可以充当基本的聚合层，从而消除了对专用反向代理（如NGINX）的需求。

精心计划的虚拟服务还可以促进Strangler模式。 假设服务合同不变，细粒度的服务路由规则可以针对各个端点，一旦原始端点在整体上已被弃用，就可以将请求流量转移到微服务风格的实现中。 将匹配规则与基于百分比的流量策略结合起来，可以透明地在提供商端透明地安排逐步迁移，而服务使用者则毫无明智之举。

下面的代码片段提供了虚拟服务的简单示例。
```
apiVersion: networking.istio.io/v1alpha3kind: VirtualServicemetadata:  name: reviewsspec:  hosts:  - shoppingcart  http:  - match:    - headers:        end-user:          exact: emil.koutanov    route:    - destination:        host: shoppingcart        subset: v3  - route:    - destination:        host: shoppingcart        subset: v3      weight: 25    - destination:        host: shoppingcart        subset: v2      weight: 75
```

主机部分将虚拟服务绑定到用户可寻址的目的地。 换句话说，它充当调用虚拟服务的触发器。 这些目标可以是固定的IP地址，DNS名称或服务名称-后者可以是Kubernetes的短名称或FQDN（完全合格的域名）。 还支持通配符前缀（用*字符表示），从而在子域上触发虚拟服务。 指定的主机实际上不需要解析为任何现有服务名称； 您可以指定要匹配的任意主机。

注意：主机匹配模型的灵活性是一把双刃剑。 由于主机名可以是任意的，因此Istio不会进行任何形式的健全性检查。 例如，如果上例中的主机名拼写错误为“ shopingcart”，则Istio将很乐意应用该配置。 稍后，当客户端尝试调用正确命名的shoppingcart主机时，请求将直接路由到服务-我们的虚拟服务规则将不会产生任何效果。

http部分描述了路由规则-匹配条件和用于路由发送到主机字段中指定的目标的HTTP / 1.1，HTTP / 2和gRPC通信的条件和操作。 （您也可以使用tcp和tls部分为TCP和未终止的TLS通信配置路由规则。）

路由规则由要转发流量的目的地以及零个或多个匹配条件组成。 在上面的示例中，第一个规则仅匹配来自用户emil.koutanov的请求。 路由规则以严格的自上而下的顺序进行评估：第一个匹配的规则将被执行，如果不匹配，则落入下一个规则。 因此，在我们的示例中，用户emil.koutanov将始终被定向到shoppingcart服务的v3。 在25％的情况下，将为所有其他用户提供v3版本。 因为带有匹配谓词的规则可能不会被解雇，所以最好的做法是保留最后一个没有匹配谓词的规则-有效地冒充“全部捕获”。

与主机部分中指定的虚拟名称不同，目标主机必须可解析为真实地址。 为目标主机提供简称时，Istio将根据虚拟服务的名称空间愉快地添加域后缀。 如果目标位于其他名称空间中，则主机应指定标准服务名称。 Istio维护人员建议在生产中使用标准名称，因为这样可以避免潜在的歧义和配置错误。

到目前为止，我们没有看到目的地和子集，而没有十分注意它们的定义位置。 目标规则是一种可选的细粒度策略，用于控制特定目标的流量。 在评估了虚拟服务路由规则之后，便应用了目标规则，换句话说，它们适用于流量的“真实”目标。

目标规则定义为DestinationRule类型的CRD。 以下是目标规则的示例。
```
apiVersion: networking.istio.io/v1alpha3kind: DestinationRulemetadata:  name: shoppingcart-destinationrulespec:  host: shoppingcart  trafficPolicy:    loadBalancer:      simple: RANDOM  subsets:  - name: v2    labels:      version: v2    trafficPolicy:      loadBalancer:        simple: ROUND_ROBIN  - name: v3    labels:      version: v3
```

既然实际上已经完成了，这个例子应该会变得更有意义。 我们在VirtualService定义中使用的v2和v3子集仅是对shoppingcart服务的标记版本的引用。 标签是标准的Kubernetes概念-可用于注释Kubernetes资源的自由格式键值对。 版本标签可能会出现在服务的“部署”资源定义的“元数据”部分中，并且在调用适当的目标规则子集时会在运行时匹配。
## 网关

网关控制流入和流出服务网格的流量。 在幕后，网关是一个Envoy代理实例，它以独立配置（未附加到应用程序容器）部署在数据平面的概念边界上。

网关的绝大多数用例都围绕入站流量的管理。 以这种能力，网关的行为类似于常规的Kubernetes入口资源。 网关和Kubernetes入口之间的主要区别在于，前者被设计为专门与Istio一起使用，而后者是被设计为处理外部流量的标准API。 入口和入口控制器通常独立于服务网格，并且无需一个即可运行。 从理论上讲，可以部署入口控制器并配置入口以在流量到达Istio网关之前对其进行路由。 此策略对于聚合服务可能有用，其中某些服务可能使用服务网格进行有线连接，而其他服务可能会按常规部署。

以下代码段是网关定义的示例。
```
apiVersion: networking.istio.io/v1alpha3kind: Gatewaymetadata:  name: shoppingcart-gatewayspec:  selector:    istio: ingressgateway  servers:  - port:      number: 443      name: https      protocol: HTTPS    hosts:    - shoppingcart.example.com    tls:      mode: SIMPLE      serverCertificate: /tmp/tls.crt      privateKey: /tmp/tls.key
```

这个简单的示例在端口443上接受发往shoppingcart.example.com的HTTPS流量。除非首先将网关绑定到虚拟服务，否则该流量无法流入服务网格。 扩展我们之前的虚拟服务示例：
```
apiVersion: networking.istio.io/v1alpha3kind: VirtualServicemetadata:  name: reviewsspec:  hosts:  - shoppingcart  gateways:  - shoppingcart-gateway  http:  - match:    - headers:        end-user:          exact: emil.koutanov    route:    - destination:        host: shoppingcart        subset: v3  - route:    - destination:        host: shoppingcart        subset: v3      weight: 25    - destination:        host: shoppingcart        subset: v2      weight: 75
```

我们在上一个示例中添加的全部是gateways部分，通过其名称指定我们的网关。 现在，shoppingcart-gateway网关上的入口流量将被允许流入shoppingcart虚拟服务。

除了处理入口流量外，网关还可以充当离开网状网络的流量的受控出口点。 网关将使您限制哪些服务可以访问外部网络并监视允许离开的流量。 有几个原因可能导致人们想要在基础结构级别上限制出口，而与底层应用程序无关。 其中主要是合规性-例如，PCI DSS标准要求将来自持卡人数据环境（CDE）的出站流量限制为经过授权的通信，要求采用默认拒绝规则来阻止未指定的流量。 另一个常见的原因是减轻攻击，从而使系统中的一个或多个组件受到威胁，并可能试图泄漏敏感数据。
## 服务条目

服务条目是Istio在其专用服务注册表中维护的服务的内部定义。 服务条目并不是经常遇到的东西； 您可以在Istio中部署完整的分布式系统，而无需触碰这个概念。 尽管如此，它仍被视为Istio的核心概念，至少应该意识到这一点。

服务条目的主要用例集分为以下大类：
+ 传统应用程序集成：与未部署在Kubernetes中或无法从Istio数据平面直接访问的服务进行通信。
+ 多集群合成：来自多个物理Kubernetes集群的服务的逻辑聚合。
+ 将网格扩展到Kubernetes之外：将部署在物理硬件和VM上的工作负载添加到现有服务网格。
+ 检测外部服务：例如，对外部服务的呼叫的重试，路由百分比，跟踪等。

注意：您不需要仅配置服务条目即可访问外部服务，例如maps.googleapis.com。 默认情况下，Istio egress Envoy代理配置为将请求传递给未知服务。 但是，未注册的目的地将无法从适用于Istio增强服务的细粒度流量策略中受益。

对于服务进入场景的实际示例，请考虑由someprovider.com托管的外部服务，该服务需要相互TLS（mTLS）进行身份验证。 一种选择是将X.509证书和签名的PEM密钥部署到我们Kubernetes集群中部署的每个使用者。 这带来了后勤方面的挑战：可能会有几个这样的使用者，并且每个使用者都需要对应用程序代码进行潜在的侵入性更改以支持mTLS的使用。 最重要的是，我们必须分发和旋转关键材料。 通过配置Istio作为前向代理，将来自我们数据平面的流量透明封装到TLS隧道（充当mTLS终结器）中，可以解决此难题。 请参阅下面的资源定义。
```
apiVersion: networking.istio.io/v1alpha3kind: ServiceEntrymetadata:  name: external-serviceentryspec:  hosts:  - someprovider.com  ports:  - number: 443    name: https    protocol: HTTPS  location: MESH_EXTERNAL  resolution: DNS---apiVersion: networking.istio.io/v1alpha3kind: DestinationRulemetadata:  name: external-destinationrulespec:  host: someprovider.com  trafficPolicy:    tls:      mode: MUTUAL      clientCertificate: /etc/certs/myclientcert.pem      privateKey: /etc/certs/client_private_key.pem      caCertificates: /etc/certs/rootcacerts.pem
```

已经定义了两个资源：服务条目和相应的目标规则。 前者做的很少，它注册someprovider.com作为服务条目，并将其置于Istio的范围内。 后者承担了繁重的工作-指定身份验证模式，用于验证提供程序的CA证书以及用于验证客户端的私钥和证书。
## 边车

之前，我们已经将Sidecar视为Istio服务网格体系结构的定义元素。 确实，不起眼的Envoy代理服务器是Istio生态系统中众所周知的主力军，它在控制平面的决定下在数据平面中移动流量。

边车与服务入口在同一条船上，因为您可以在构建过程中获得公平的方式，而无需直接处理它们。 常规将Sidecar注入到应用程序Pod中，而用户的参与很少，并将继承默认配置，该配置可以直接使用。 默认情况下，Istio将配置网格中的所有Sidecar代理以到达每个工作负载实例，并接受与工作负载关联的所有端口上的流量。

出于以下原因，可以明确修改Sidecar配置：
+ 限制Envoy代理接受的端口和协议集。
+ 限制Envoy代理可以访问的服务集。

通过使用工作负载选择器，可以将Sidecar配置应用于整个名称空间或特定的工作负载。 这产生了范围界定规则。 当确定要应用到特定工作负载实例的Sidecar配置时，将优先选择带有工作负载选择器的Sidecar资源，而不是没有工作负载选择器的配置。 此外，一个名称空间最多可以具有一个sidecar配置，而不需要工作负载选择器。 如果本地名称空间中没有任何sidecar配置匹配，则istio-system名称空间（或配置的根Istio名称空间）中定义的全局默认配置将生效。 为了使事情变得更加复杂，如果两个或多个带有工作负载选择器的Sidecar配置选择相同的工作负载实例，则系统的行为是不确定的。

全局默认配置是相当宽松的。 您可以通过运行以下命令查看默认值：
```
kubectl get sidecar default -n istio-system -o yaml
```

导致：
```
apiVersion: networking.istio.io/v1alpha3kind: Sidecarmetadata:  annotations:    kubectl.kubernetes.io/last-applied-configuration: |      *** omitted for brevity ***  creationTimestamp: "2019-12-25T01:32:02Z"  generation: 1  labels:    operator.istio.io/component: IngressGateway    operator.istio.io/managed: Reconcile    operator.istio.io/version: 1.4.0    release: istio  name: default  namespace: istio-system  resourceVersion: "4527"  selfLink: /apis/networking.istio.io/v1alpha3/namespaces/istio-system/sidecars/default  uid: 5cee1cb3-1d48-11ea-84b7-025000000001spec:  egress:  - hosts:    - '*/*'
```

以下对全局默认配置的示例修订将出口流量仅限制为同一名称空间中的其他工作负载以及istio-system名称空间中的服务。
```
apiVersion: networking.istio.io/v1alpha3kind: Sidecarmetadata:  name: default  namespace: istio-systemspec:  egress:  - hosts:    - "./*"    - "istio-system/*"
```
# 入门

现在，我们已经对Istio的核心概念有了深刻的了解，现在就开始使用它。 我们将部署Istio发行版中包含的示例Bookinfo应用程序，稍后我们将为它添加一些Istio特色。 具体来说，我们将演示使用Zipkin进行分布式跟踪以及使用JWT进行强制性用户身份验证。
## 要求

这些示例将使用Istio 1.4。 此版本的Istio已在Kubernetes 1.13、1.14和1.15版本中进行了测试。 这些示例已通过Kubernetes 1.14.8进行了验证。

假定您有权访问Kubernetes集群，并且在Kubernetes中具有坚实的背景。 Docker Desktop随附的Kubernetes集群可以正常工作。
## 安装

首先下载Istio发行版：
```
curl -L https://istio.io/downloadIstio | sh -
```

移至Istio软件包目录。 在撰写本文时，最新的稳定Istio版本是1.4.2。 您可能有较新的版本，因此请相应地更改路径。
```
cd istio-1.4.2
```

安装目录包含以下内容：
+ Istio资源定义-将Istio安装到Kubernetes集群所需。 回想一下，Istio只是部署到Kubernetes中的另一个应用程序。
+ sample /目录中的示例应用程序。
+ bin /目录中的istioctl客户端二进制文件。

下一步是可选的，但强烈建议您这样做。 在您的路径中添加istioctl：
```
export PATH=$PWD/bin:$PATH
```

Istio提供了几个配置文件。 这些配置文件为Istio控制平面和Istio数据平面的边车提供了固定的定制。 您可以从Istio的内置配置文件之一开始，然后根据您的特定需求定制配置。 有五个内置配置文件。 我们将简要介绍三个较常见的三个。
+ 默认值：建议用于生产部署的配置文件。 具有最少的附加组件，并使用生产级默认值。
+ 演示：用于展示Istio功能的广度。 具有完整的附加组件集和配置，可优化以最小化资源使用。 它还包含大量的跟踪和访问日志记录，因此通常不建议将其用于对性能敏感的部署。
+ 最少：足以充分利用其流量管理功能的Istio的简约部署。

让我们继续安装演示配置文件：
```
istioctl manifest apply --set profile=demo \    --set values.tracing.enabled=true \    --set values.tracing.provider=zipkin
```

首次运行此操作可能需要一段时间。 Kubernetes将部署十几个服务，并引入大量Docker映像。 此外，我们启用了Zipkin插件，我们希望稍后再展示。 如果您以前安装了Istio，但没有安装此插件，请放心-您可以随时重新运行istioctl清单应用，并指定备用配置文件或一组不同的插件。

部署Istio后，通过查询istio-system名称空间来验证是否存在所有组件。 （这是默认的Istio根名称空间，如有必要，可以将其重新配置为备用名称空间。）
```
kubectl get svc -n istio-system
```

输出：
```
NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                                                                      AGEgrafana                  ClusterIP      10.105.190.242   <none>        3000/TCP                                                                                                                     3d3histio-citadel            ClusterIP      10.100.144.109   <none>        8060/TCP,15014/TCP                                                                                                           3d3histio-egressgateway      ClusterIP      10.101.65.30     <none>        80/TCP,443/TCP,15443/TCP                                                                                                     3d3histio-galley             ClusterIP      10.100.233.70    <none>        443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         3d3histio-ingressgateway     LoadBalancer   10.106.108.201   localhost     15020:30912/TCP,80:30218/TCP,443:30059/TCP,15029:31861/TCP,15030:32620/TCP,15031:30363/TCP,15032:30868/TCP,15443:30359/TCP   3d3histio-pilot              ClusterIP      10.108.102.237   <none>        15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       3d3histio-policy             ClusterIP      10.111.28.108    <none>        9091/TCP,15004/TCP,15014/TCP                                                                                                 3d3histio-sidecar-injector   ClusterIP      10.103.185.22    <none>        443/TCP                                                                                                                      3d3histio-telemetry          ClusterIP      10.111.73.89     <none>        9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       3d3hkiali                    ClusterIP      10.109.207.229   <none>        20001/TCP                                                                                                                    3d3hprometheus               ClusterIP      10.96.164.188    <none>        9090/TCP                                                                                                                     3d3htracing                  ClusterIP      10.108.205.179   <none>        80/TCP                                                                                                                       3d3hzipkin                   ClusterIP      10.107.7.90      <none>        9411/TCP                                                                                                                     3d3h
```

确保相应的Istio Pod也已部署并且状态为“正在运行”。 首次部署时，每个Pod的READY状态可能需要一段时间才能从0/1转换为1/1：
```
kubectl get pods -n istio-system
```

输出：
```
NAME                                      READY   STATUS    RESTARTS   AGEgrafana-5f798469fd-qmzqd                  1/1     Running   1          3d3histio-citadel-6dc789bc4c-qcpq6            1/1     Running   2          3d3histio-egressgateway-75cb89bd7f-96xbw      1/1     Running   1          3d3histio-galley-5bcd89bd9c-6z9hm             1/1     Running   1          3d3histio-ingressgateway-7d6b9b5ffc-n9vrp     1/1     Running   1          3d3histio-pilot-678b45584b-h2pxv              1/1     Running   1          3d3histio-policy-9f78db4cb-bjk54              1/1     Running   5          3d3histio-sidecar-injector-7d65c79dd5-th5ts   1/1     Running   2          3d3histio-telemetry-fc488f958-9np6n           1/1     Running   5          3d3histio-tracing-6bc9795bd7-4gqrr            1/1     Running   1          3d2hkiali-7964898d8c-bsxvd                    1/1     Running   1          3d3hprometheus-586d4445c7-qcjxk               1/1     Running   1          3d3h
```
## 启用边车注入

Istio将自动将sidecar容器注入以istio-injection = enabled标记的任何命名空间中启动的应用程序容器中。

由于我们的Bookinfo示例将托管在默认名称空间中，因此让我们为进行侧向车注入做好准备：
```
kubectl label namespace default istio-injection=enabled
```

Istio不会在您的应用程序上强制进行自动边车注入。 您可以使用istioctl kube-inject来丰富任何现有资源定义，在部署它们之前将Envoy容器显式注入到应用程序pod中：
```
istioctl kube-inject -f <resource-definitions>.yaml | kubectl apply -f -
```
## 关于Bookinfo示例

Bookinfo应用程序包含在Istio发行版的samples / bookinfo /目录中。 这是一个多语言应用程序，用于显示有关图书的信息，类似于在线图书商店的目录条目。 它包含以有向无环图（DAG）安排通信的四个微服务：

productpage：调用详细信息并查看服务以填充页面。

详细信息：包含书籍信息。

评论：包含书评。 根据实现版本的不同，审阅服务将调用评级服务以获得书评。 部署了三种版本的评论：
+ v1不会致电评分服务。
+ v2呼叫分级服务，将每个分级显示为1到5个黑色星体。
+ v3呼叫分级服务，将每个分级显示为1到5个红色星标。

评分：包含书评随附的书评信息。

Bookinfo的高级体系结构如下所示。
## 部署Bookinfo

使用kubectl部署应用程序：
```
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

注意：如果您选择不启用自动边车注入，则使用istioctl可以实现等效功能：
```
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)
```

运行kubectl get svc和kubectl get po验证应用程序已部署：
```
NAME          TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGEdetails       ClusterIP   10.104.247.109   <none>        9080/TCP   3d4hkubernetes    ClusterIP   10.96.0.1        <none>        443/TCP    3d5hproductpage   ClusterIP   10.102.89.169    <none>        9080/TCP   3d4hratings       ClusterIP   10.103.85.175    <none>        9080/TCP   3d4hreviews       ClusterIP   10.100.227.109   <none>        9080/TCP   3d4hNAME                             READY   STATUS    RESTARTS   AGEdetails-v1-c5b5f496d-zz5dh       2/2     Running   2          3d4hproductpage-v1-c7765c886-zdqv4   2/2     Running   0          2m17sratings-v1-f745cf57b-lln4d       2/2     Running   2          3d4hreviews-v1-75b979578c-t4ns7      2/2     Running   0          2m17sreviews-v2-597bf96c8f-jrs2c      2/2     Running   0          2m17sreviews-v3-54c6c64795-zxzk2      2/2     Running   0          2m17s
```

在Istio中部署后，Bookinfo的体系结构将进行少许修改以反映Sidecar代理的存在：

让我们花点时间环顾四周。 第一步是确定我们是否有虚拟服务：
```
kubectl get vs
```

输出：
```
NAME       GATEWAYS             HOSTS   AGEbookinfo   [bookinfo-gateway]   [*]     3d4h
```

将虚拟服务配置转储到YAML：
```
kubectl get vs bookinfo -o yaml
```

输出：
```
apiVersion: networking.istio.io/v1alpha3kind: VirtualServicemetadata:  annotations:    kubectl.kubernetes.io/last-applied-configuration: |      *** omitted for brevity ***  creationTimestamp: "2019-12-25T01:54:39Z"  generation: 1  name: bookinfo  namespace: default  resourceVersion: "6896"  selfLink: /apis/networking.istio.io/v1alpha3/namespaces/default/virtualservices/bookinfo  uid: 85c222bd-1d4b-11ea-84b7-025000000001spec:  gateways:  - bookinfo-gateway  hosts:  - '*'  http:  - match:    - uri:        exact: /productpage    - uri:        prefix: /static    - uri:        exact: /login    - uri:        exact: /logout    - uri:        prefix: /api/v1/products    route:    - destination:        host: productpage        port:          number: 9080
```

这告诉我们什么？ 首先，我们有一个名为bookinfo的虚拟服务，可将请求捕获到任何（*）主机。 虚拟服务将接受与spec.http.match中指定的路径匹配的请求，并将其转发到端口9080上的productpage目标。

我们还可以看到bookinfo虚拟服务已绑定到bookinfo-gateway网关。 让我们深入了解一下：
```
kubectl get gw bookinfo-gateway -o yaml
```

输出：
```
apiVersion: networking.istio.io/v1alpha3kind: Gatewaymetadata:  annotations:    kubectl.kubernetes.io/last-applied-configuration: |      *** omitted for brevity ***  creationTimestamp: "2019-12-25T01:54:39Z"  generation: 1  name: bookinfo-gateway  namespace: default  resourceVersion: "6895"  selfLink: /apis/networking.istio.io/v1alpha3/namespaces/default/gateways/bookinfo-gateway  uid: 85bf7194-1d4b-11ea-84b7-025000000001spec:  selector:    istio: ingressgateway  servers:  - hosts:    - '*'    port:      name: http      number: 80      protocol: HTTP
```

通知我们，Istio网关正在端口80上为所有主机提供HTTP通信。 我们应该只已经调用Bookinfo服务，但是首先我们需要知道从群集外部可以访问网关的地址和端口号。 答案取决于如何公开底层的Istio入口网关服务。 （请记住，Istio由常规的Kubernetes组件组成-必须公开它们才能从群集外部访问。）
```
kubectl get svc istio-ingressgateway -n istio-system
```

输出：
```
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                                                                                      AGEistio-ingressgateway   LoadBalancer   10.106.108.201   localhost     15020:30912/TCP,80:30218/TCP,443:30059/TCP,15029:31861/TCP,15030:32620/TCP,15031:30363/TCP,15032:30868/TCP,15443:30359/TCP   3d20h
```

我碰巧在Docker Desktop随附的Kubernetes集群上运行此示例，因此在我的情况下，EXTERNAL-IP设置为localhost。 您可能正在AWS或GCP中运行Kubernetes集群，在这种情况下，将使用云本地负载均衡器（例如ELB或NLB）来实现LoadBalancer服务类型。 或者，您可能会发现自己处于不支持LoadBalancer服务的环境中（例如，不带隧道的MiniKube），在这种情况下，EXTERNAL-IP将显示为<none>或永久显示为<pending>。 在这种情况下，您可以通过检查PORT（S）列中端口80的端口映射来使用其节点端口访问入口网关服务。

让我们调用我们的应用程序。 浏览到localhost / productpage。 （适当替换主机和端口。）
![](0!rlEW7rFeFrTNNZon)

刷新屏幕几次。 您会注意到，每次提供的视图都略有不同。 这是因为评论服务正在选择带有标签应用“评论”的所有广告连播。 我们稍后会处理。

恭喜你！ 您已经使用Istio成功部署了第一个应用程序。
## 使用Zipkin进行分布式跟踪

在对Bookinfo部署进行任何重大更改之前，让我们首先使用Zipkin来了解流量如何在整个微服务架构中流动。

无法直接访问Zipkin。 要使用它，必须设置临时端口转发：
```
istioctl dashboard zipkin
```

这将在新的浏览器选项卡中启动Zipkin UI。 点击“查找轨迹”按钮以查看最近的轨迹：
![](0!L74G8YG261l21uB9)

Zipkin将呈现遍历所选服务的跟踪。 您可以展开跟踪，以显示基础跨度：
![](0!__n5ZD5-FJ6LLJOT)

Zipkin还使我们可以可视化服务之间的高级依赖关系。 点击屏幕顶部的“依赖关系”链接：
![](0!h96woC2K1V26fSN-)

还不错吧？ 我们不费吹灰之力就得到了所有这些。
## 量身定制的路由

目前，到处都有评论服务的路由：随机向客户提供三种版本的评论。 让我们围绕如何向消费者提供服务的方式进行一些调整。 运行以下命令，这将通过标准输入（heredoc样式）将一对资源定义注入到kubectl中。
```
cat <<EOF | kubectl apply -f -apiVersion: networking.istio.io/v1alpha3kind: VirtualServicemetadata:  name: reviewsspec:  hosts:  - reviews  http:  - match:    - headers:        end-user:          exact: bob    route:    - destination:        host: reviews        subset: v1  - route:    - destination:        host: reviews        subset: v3      weight: 25    - destination:        host: reviews        subset: v2      weight: 75---apiVersion: networking.istio.io/v1alpha3kind: DestinationRulemetadata:  name: reviewsspec:  host: reviews  subsets:  - name: v1    labels:      version: v1  - name: v2    labels:      version: v2  - name: v3    labels:      version: v3EOF
```

现在，我们向25％的普通访问者提供了v3的金丝雀版本，其余版本仍在v2上。 在此示例中，我们有一个特别挑剔的用户— bob —可能对我们来说非常重要。 登录后将始终为该用户提供v1服务。

立即尝试Bookinfo网站。 刷新页面主要用于v2，偶尔会通过v3。 与另一个用户（例如charlie）登录，其行为仍然相同。 （顺便说一句，密码字段纯粹是装饰性的-任何密码都可以使用。）现在注销，然后以鲍勃用户身份重新登录。 屏幕将呈现v1视图，而不显示您按下该刷新按钮的次数。

注意：如果您想知道，Istio对用户身份没有任何特殊的了解。 最终用户标头仅由productpage服务附加到所有已认证的会话。
# 启用原始身份验证

身份验证是服务网格的另一个常见用例。 就像现成的API网关一样，服务网格可以在现有服务之上提供透明的身份验证和授权控制。 我们的下一个示例将使用JSON Web令牌来验证用户对服务的身份。 与验证服务的传输身份验证相反，Istio将此称为源身份验证。

为了试验JWT身份验证，我们需要一个有效的JWT和一个JWKS（JSON Web密钥集）端点。 后者是一组签名的公共密钥，可用于验证JWT。 Istio的好伙伴为我们提供了一个示例JWKS端点。 接下来，我们将创建一个策略资源，该资源强制将JWT用于productpage服务，但仅用于以/ productpage或/ api / v1路径开头的资源。 当同一应用程序还提供需要向未经身份验证的用户开放的静态内容时，选择性身份验证很有用。
```
cat <<EOF | kubectl apply -f -apiVersion: "authentication.istio.io/v1alpha1"kind: "Policy"metadata:  name: "productpage-policy"  namespace: defaultspec:  targets:  - name: productpage  origins:  - jwt:      issuer: "testing@secure.istio.io"      jwksUri: "https://raw.githubusercontent.com/istio/istio/release-1.4/security/tools/jwt/samples/jwks.json"      trigger_rules:      - included_paths:        - prefix: /productpage        - prefix: /api/v1  principalBinding: USE_ORIGINEOF
```

给Istio几秒钟时间传播新策略。 然后使用curl调用productpage服务，仅输出状态代码：
```
curl -s http://localhost/productpage -o /dev/null -w "%{http_code}\n"
```

导致：
```
401
```

未经授权，符合预期。 让我们再试一次，这次传入一个有效的JWT：
```
JWT=$(curl https://raw.githubusercontent.com/istio/istio/release-1.4/security/tools/jwt/samples/demo.jwt -s)curl -H "Authorization: Bearer $JWT" -s http://localhost/productpage -o /dev/null -w "%{http_code}\n"
```

导致：
```
200
```

我们已经成功启用了承载令牌认证。 这意味着我们现在可以通过简单地插入备用JWKS文件，用外部身份提供程序（例如AWS Cognito）替换粗略的身份验证表单。
## 用Grafana可视化

采用服务网格的当下满足之一是开箱即用的遥测数量。 我们已经研究了使用Zipkin进行分布式跟踪。 现在让我们看一下Istio提供的服务和网格级别指标。

Istio的演示配置文件随附了Prometheus和Grafana。 我们可以通过istio仪表板命令代理对Grafana的访问：
```
istioctl dashboard grafana
```

这将打开一个浏览器标签到Grafana主页。 针对Istio行为的各个方面，有几十个预装的仪表板。 在左上角，选择“仪表板”，然后选择“ Istio服务仪表板”。

最初的印象可能令人印象深刻：空白图表和显示“无数据”的忧郁小部件。 那不应该令我们感到惊讶； 毕竟，没有人正在使用我们的Bookinfo服务。 让我们模拟一些流量：
```
for i in `seq 1 1000`; do curl -s -o /dev/null http://localhost/productpage; done
```

返回信息中心，然后从“服务”下拉菜单中选择productpage.default.svc.cluster.local。 如果下拉菜单为空，请刷新屏幕，然后重新填充屏幕。 仪表板将像圣诞树一样点亮。 我们将看到我们的请求吞吐量（每秒查询），错误率，周转时间（请求持续时间）以及更细粒度的指标。
![](0!RUEfMpuux9EWWb9t)

花时间浏览其他仪表板。 Istio公开了其对基础控制平面组件（混音器，城堡，飞行员和厨房）的关键指标。 此外，Istio还提供了一个方便的摘要“ Istio Performance Dashboard”，它将关键组件合并到一个视图中。
![](0!dEO7mCjKWWGg9XHR)
# Istio入门
## 了解什么是服务网格以及如何在微服务体系结构中有效地使用它。

最近几年在软件体系结构领域带来了巨大的变化。我们都见证了一个重大转变，即将大型的整体应用程序和粗粒度应用程序分解为称为微服务的细粒度部署单元，主要通过同步REST和gRPC接口以及异步事件和消息传递进行通信。这种体系结构的好处很多，但缺点同样明显。过去在“旧世界”中直截了当的软件开发方面，例如调试，概要分析和性能管理，现在已经复杂了一个数量级。此外，微服务架构也带来了自己独特的挑战。服务更具流动性和弹性，并且跟踪它们的实例，它们的版本和依赖关系是一项艰巨的挑战，随着服务领域的发展其复杂性迅速增加。最重要的是，服务将孤立地失败，并且由于不可靠的网络而进一步加剧。给定足够大的系统，在任何给定的时间点，系统的某些部分可能会遭受轻微故障，从而可能会影响一部分用户，而这往往是操作员无法察觉的。拥有如此众多的“活动部件”，如何应对这些挑战并确保系统平稳运行而又不影响客户并驱使开发人员精疲力尽？

随着微服务的日益普及，该行业一直在稳步提出各种模式和最佳实践，这些模式和最佳实践使整个体验变得更加可口。 弹性模式，服务发现，容器编排，Canary版本，可观察性模式，BFF，API网关……这些是从业人员将用来构建更健壮和可持续的分布式系统的概念。 但是这些概念仅仅是抽象概念和模式，它们需要有人在系统中的某个地方实现它们。 通常，“某人”是您，而“某处”无处不在。
```
(本文翻译自Emil Koutanov的文章《Getting Started With Istio》，参考：https://medium.com/swlh/getting-started-with-istio-524628c025)
```
