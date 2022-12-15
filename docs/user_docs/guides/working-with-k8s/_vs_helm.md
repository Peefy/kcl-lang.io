
# KCL 与其他 Kubernetes 配置管理工具的异同 - Helm 篇

在[上一节](/docs/user_docs/guides/working-with-k8s/generate_k8s_manifests)中，我们介绍了如何使用 KCL 编写并管理 Kubernetes 配置并将配置下发到集群，这一节我们通过与其他 Kubernetes 配置管理工具的对比如 Kustomize 介绍 KCL 在 Kubernetes 配置管理场景更丰富的介绍。

因此，KCL 期望在 Kubernetes YAML 资源管理层面解决如下问题：

1. 用**生产级高性能编程语言以编写代码**的方式提升配置的灵活度，比如条件语句、循环、函数、包管理等特性提升配置重用的能力
2. 在代码层面提升**配置语义验证的能力**，比如字段可选/必选、类型、范围等配置检查能力
3. 提供**配置分块编写、组合和抽象的能力**，比如结构定义、结构继承、约束定义等能力

本篇文章是 KCL 可以做什么系列文章第二篇，重点讲述 KCL 语言一些进阶用法以及与 Kustomize 工具区别，后续会持续更新和分享 KCL 的一系列特点和使用场景，大家敬请期待！

## KCL 和 Helm 的区别

[Helm](https://helm.sh/) 是 Kubernetes 资源的包管理工具，通过配置模版管理 Kubernetes 资源配置，它可以在 `.tpl` 文件中定义可复用的模版，而在 KCL 中均为高级语言的编程方式，不需要额外的语法去指定模版，不需要过多的 `{{ * }}` 来标记代码块，信息密度更高，并且可以通过常规编程语言变量定义和条件语句的方式，比较自然。而 Helm 中有大量的 `{{- include }}` 和 `nindent` 等和实际逻辑无关的标记字符，需要在每一次引用的地方计算空格和缩进，语法噪音较高。

+ Helm 工程结构

+ Helm

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "foo.deploymentName" . }}
  labels:
    {{- include "foo.labels" . | nindent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      {{- include "foo.selectorLabels" . | nindent 6 }}
  template:
    metadata:
    {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
    {{- end }}
      labels:
        {{- include "foo.labels" . | nindent 8 }}
```

+ KCL

```python
apiVersion = "apps/v1"
kind = "Deployment"
metadata = {
    name = option("deploymentName")
    labels = option("labels")
}
spec = {
    replicas = option("replicaCount")
    selector.matchLabels = option("selectorLabels")
    template.metadata = {
        labels = option("labels")
        annotations = option("annotations")
    }
}
```

通过 kcl 和 kubectl 命令行可以将配置下发到集群

接下来，会通过 KCL 结构定义以及包管理的方式为大家讲解如何一键生成所需的 K8s 资源

## 下一期

本期内容大概简单介绍了用 KCL 编写复杂 Kubernetes 配置的快速入门和与 Kustomize 和 Helm 进行 Kubernetes 配置管理的对比

如果您喜欢这篇文章，一定记得收藏 + 关注！！更多精彩内容请访问: 

+ KCL 仓库地址：https://github.com/KusionStack/KCLVM
+ Kusion 仓库地址：https://github.com/KusionStack/kusion
+ Konfig 仓库地址：https://github.com/KusionStack/Konfig

如果您喜欢这些项目，欢迎点个 star 鼓励一下，同时欢迎加入我们的社区进行交流 👏👏👏：

https://github.com/KusionStack/community
