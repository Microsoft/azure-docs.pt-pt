---
title: Implementar gráficos de leme usando GitOps em Arc ativado cluster Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Use GitOps com leme para uma configuração de cluster ativada pelo Arco Azure
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: df9b40764ec463553659803749f282bbc4587bde
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449548"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Implementar gráficos de leme usando GitOps em um cluster de Kubernetes ativado por arco

O Helm é uma ferramenta open source de empacotamento que o ajuda a instalar e a gerir o ciclo de vida de aplicações do Kubernetes. Semelhante aos gestores de pacotes Linux como APT e Yum, Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos Kubernetes pré-configurados.

Este artigo mostra-lhe como configurar e usar Helm com Azure Arc habilitado Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um arco Azure existente permitiu o agrupamento de kubernetes conectado.
    - Se ainda não ligou um cluster, caminhe pelo nosso [Connect a Azure Arc ativando o arranque rápido do cluster Kubernetes.](quickstart-connect-cluster.md)
- Uma compreensão dos benefícios e arquitetura desta característica. Ler mais em [Configurações e GitOps - Azure Arc habilitado artigo de Kubernetes](conceptual-configurations.md).
- Instale a `k8s-configuration` extensão Azure CLI da versão >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Visão geral da utilização de GitOps e Helm com Arco Azure habilitado Kubernetes

 O operador Helm fornece uma extensão ao Fluxo que automatiza as versões do Helm Chart. Um lançamento helm Chart é descrito através de um recurso personalizado Kubernetes chamado HelmRelease. O fluxo sincroniza estes recursos de Git para o cluster, enquanto o operador Helm garante que os Gráficos helm são lançados conforme especificado nos recursos.

 O [repositório de exemplo](https://github.com/Azure/arc-helm-demo) utilizado neste artigo é estruturado da seguinte forma:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

No repo Git temos dois diretórios: um contendo um Helm Chart e outro contendo os lançamentos config. No `releases` diretório, contém `app.yaml` o HelmRelease config, apresentado abaixo:

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

| Campo | Descrição |
| ------------- | ------------- | 
| `metadata.name` | Campo obrigatório. Precisa seguir as convenções de nomeação de Kubernetes. |
| `metadata.namespace` | Campo opcional. Determina onde a libertação é criada. |
| `spec.releaseName` | Campo opcional. Se não for fornecido, o nome de libertação será `$namespace-$name` . |
| `spec.chart.path` | O diretório que contém o gráfico (relativamente à raiz do repositório). |
| `spec.values` | Personalizações do utilizador dos valores dos parâmetros predefinidos a partir do próprio Gráfico. |

As opções especificadas no HelmRelease `spec.values` irão sobrepor-se às opções especificadas `values.yaml` na fonte do Gráfico.

Pode saber mais sobre a HelmRelease na [documentação](https://docs.fluxcd.io/projects/helm-operator/en/stable/)oficial do Operador Helm .

## <a name="create-a-configuration"></a>Criar uma configuração

Utilizando a extensão Azure CLI para `k8s-configuration` , ligue o seu cluster ligado ao repositório de exemplo Git. Dê a esta configuração o nome `azure-arc-sample` e implante o operador flux no espaço de `arc-k8s-demo` nomes.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parâmetros de configuração

Para personalizar a criação da configuração, [conheça os parâmetros adicionais.](./tutorial-use-gitops-connected-cluster.md#additional-parameters)

## <a name="validate-the-configuration"></a>Validar a configuração

Utilizando o CLI Azure, verifique se a configuração foi criada com sucesso.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O recurso de configuração é atualizado com o estado de conformidade, mensagens e informações de depuração.

```output
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
    "chartVersion": "1.2.0"
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

## <a name="validate-application"></a>Validar aplicação

Executar o seguinte comando e navegar `localhost:8080` para o seu navegador para verificar se a aplicação está em execução.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Passos seguintes

Aplicar configurações de cluster à escala utilizando [a Política Azure](./use-azure-policy.md).
