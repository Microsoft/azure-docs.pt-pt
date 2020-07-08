---
title: Utilize GitOps com Leme para uma configuração de cluster ativada pelo Arco Azure (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps com Leme para uma configuração de cluster ativada pelo Arco Azure (Pré-visualização)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 677c5f2b27794ebea9d38e470b5e1a5ba12bff7e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857218"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>Utilize GitOps com Leme para uma configuração de cluster ativada pelo Arco Azure (Pré-visualização)

Helm é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações Kubernetes. Semelhante aos gestores de pacotes Linux, como APT e Yum, Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra-lhe como configurar e usar Helm com Azure Arc habilitado Kubernetes.

## <a name="before-you-begin"></a>Before you begin

Este artigo pressupõe que você tem um Azure Arc existente habilitado cluster ligado Kubernetes. Se precisar de um cluster ligado, consulte o [arranque rápido](./connect-cluster.md)do cluster .

Vamos definir as variáveis ambientais para usar ao longo deste tutorial. Você precisará do nome do grupo de recursos e nome do cluster para o seu cluster conectado.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Verifique se o seu cluster está ativado com o Arco

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Saída:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Visão geral da utilização do Helm com Arco Azure habilitado a Kubernetes

 O operador Helm fornece uma extensão ao Fluxo que automatiza as versões do Helm Chart. Uma versão de Gráfico é descrita através de um recurso personalizado kubernetes chamado HelmRelease. O fluxo sincroniza estes recursos desde o git até ao cluster, e o operador Helm assegura-se de que os gráficos helm são lançados conforme especificado nos recursos.

 Abaixo está um exemplo de git repo estrutura que vamos usar neste tutorial:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

No repo git temos dois diretórios, um contendo um gráfico Helm e um contendo os lançamentos config. No `releases/prod` diretório `azure-vote-app.yaml` contém o config HelmRelease apresentado abaixo:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

O config de libertação helm contém os seguintes campos:

- `metadata.name`é obrigatório, e precisa seguir as convenções de nomeação de Kubernetes
- `metadata.namespace`é opcional, e determina onde a libertação é criada
- `spec.releaseName`é opcional, e se não for fornecido o nome de libertação será $namespace-$name
- `spec.chart.path`é o diretório que contém o gráfico, dado em relação à raiz repositória
- `spec.values`são personalizações do utilizador dos valores de parâmetros padrão a partir do próprio gráfico

As opções especificadas na especificação HelmRelealease.values irão sobrepor-se às opções especificadas em values.yaml da fonte do gráfico.

Pode saber mais sobre a HelmRelease na [documentação](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html) oficial do Operador Helm

## <a name="create-a-configuration"></a>Criar uma configuração

Utilizando a extensão Azure CLI para `k8sconfiguration` , vamos ligar o nosso cluster conectado ao repositório de exemplo git. Daremos a esta configuração um nome `azure-voting-app` e implantaremos o operador do Fluxo no `prod` espaço de nomes.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Parâmetros de configuração

Para personalizar a criação de configuração, [saiba sobre parâmetros adicionais que pode utilizar](./use-gitops-connected-cluster.md#additional-parameters).


## <a name="validate-the-configuration"></a>Validar a Configuração

Utilizando o CLI Azure, valide que o `sourceControlConfiguration` foi criado com sucesso.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Note que o `sourceControlConfiguration` recurso é atualizado com o estado de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Validar Aplicação

Vamos agora verificar se a aplicação está em funcionamento, recebendo o ip de serviço.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Saída:**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Encontre o endereço IP externo a partir da saída acima e abra-o num browser.

## <a name="next-steps"></a>Próximos passos

- [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
