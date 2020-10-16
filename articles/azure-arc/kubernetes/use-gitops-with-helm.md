---
title: Implementar gráficos de leme usando GitOps em Arc ativado cluster Kubernetes (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps com Leme para uma configuração de cluster ativada pelo Arco Azure (Pré-visualização)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: eea81d458ac6631c4a023134b3198e4cdb04526e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541616"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implementar gráficos de leme usando GitOps em Arc ativado cluster Kubernetes (Preview)

Helm é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante aos gestores de pacotes Linux, como APT e Yum, Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra-lhe como configurar e usar Helm com Azure Arc habilitado Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um Azure Arc existente habilitado cluster ligado Kubernetes. Se precisar de um cluster ligado, consulte o [arranque rápido](./connect-cluster.md)do cluster .

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Visão geral da utilização de GitOps e Helm com Arco Azure habilitado Kubernetes

 O operador Helm fornece uma extensão ao Fluxo que automatiza as versões do Helm Chart. Uma versão de Gráfico é descrita através de um recurso personalizado kubernetes chamado HelmRelease. O fluxo sincroniza estes recursos desde o git até ao cluster, e o operador Helm assegura-se de que os gráficos helm são lançados conforme especificado nos recursos.

 O [repositório de exemplo](https://github.com/Azure/arc-helm-demo) utilizado neste documento é estruturado da seguinte forma:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

No repo git temos dois diretórios, um contendo um gráfico Helm e um contendo os lançamentos config. No `releases` diretório, contém `app.yaml` o HelmRelease config apresentado abaixo:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

O config de libertação helm contém os seguintes campos:

- `metadata.name` é obrigatório, e precisa seguir as convenções de nomeação de Kubernetes
- `metadata.namespace` é opcional, e determina onde a libertação é criada
- `spec.releaseName` é opcional, e se não for fornecido o nome de libertação será $namespace-$name
- `spec.chart.path` é o diretório que contém o gráfico, dado em relação à raiz repositória
- `spec.values` são personalizações do utilizador dos valores de parâmetros padrão a partir do próprio gráfico

As opções especificadas na especificação HelmRelealease.values irão sobrepor-se às opções especificadas em values.yaml da fonte do gráfico.

Pode saber mais sobre a HelmRelease na [documentação](https://docs.fluxcd.io/projects/helm-operator/en/stable/) oficial do Operador Helm

## <a name="create-a-configuration"></a>Criar uma configuração

Utilizando a extensão Azure CLI para `k8sconfiguration` , vamos ligar o nosso cluster conectado ao repositório de exemplo git. Daremos a esta configuração um nome `azure-arc-sample` e implantaremos o operador do Fluxo no `arc-k8s-demo` espaço de nomes.

```console
az k8sconfiguration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parâmetros de configuração

Para personalizar a criação de configuração, [saiba sobre parâmetros adicionais que pode utilizar](./use-gitops-connected-cluster.md#additional-parameters).

## <a name="validate-the-configuration"></a>Validar a Configuração

Utilizando o CLI Azure, valide que o `sourceControlConfiguration` foi criado com sucesso.

```console
az k8sconfiguration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O `sourceControlConfiguration` recurso é atualizado com o estado de conformidade, mensagens e informação de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Validar Aplicação

Executar o seguinte comando e navegar `localhost:8080` para o seu navegador para verificar se a aplicação está em execução.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Passos seguintes

- [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
