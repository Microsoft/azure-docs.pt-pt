---
title: Conecte um cluster Kubernetes ativado pelo Arco Azure (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Ligue um cluster Kubernetes ativado pelo Arco Azure com o Arco Azure
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.custom: references_regions
ms.openlocfilehash: 85ef8bb9868784df66199a4aea261e6b752ae7f8
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636262"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Conecte um cluster Kubernetes ativado pelo Arco Azure (Pré-visualização)

Ligue um cluster Kubernetes ao Arco de Azure.

## <a name="before-you-begin"></a>Before you begin

Verifique se tem os seguintes requisitos prontos:

* Um aglomerado de Kubernetes que está a funcionar. Se não tiver um cluster Kubernetes existente, pode utilizar um dos seguintes guias para criar um cluster de teste:
  * Criar um cluster Kubernetes usando [Kubernetes em Docker (tipo)](https://kind.sigs.k8s.io/)
  * Crie um cluster Kubernetes usando Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
* Você precisará de um ficheiro kubeconfig para aceder ao cluster e papel de administração de cluster no cluster para implantação de agentes Kubernetes habilitados pela Arc.
* O utilizador ou o principal de serviço utilizado `az login` e `az connectedk8s connect` os comandos devem ter as permissões de 'Ler' e 'Escrever' no tipo de recurso 'Microsoft.Kubernetes/connectedclusters'. A função "Azure Arc for Kubernetes Onboarding" com estas permissões pode ser usada para atribuições de funções no utilizador ou principal de serviço utilizado com Azure CLI para o embarque.
* O leme 3 é necessário para o embarque do cluster utilizando a extensão connectedk8s. [Instale a mais recente versão do Helm 3](https://helm.sh/docs/intro/install) para satisfazer este requisito.
* A versão 2.3+ do Azure CLI é necessária para instalar as extensões CLI ativadas pelo Arco Azure. [Instale o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou atualize para a versão mais recente para garantir que tem a versão Azure CLI 2.3+.
* Instale as extensões CLI ativadas pelo Arco:
  
  Instale a `connectedk8s` extensão, o que o ajuda a ligar os clusters Kubernetes ao Azure:
  
  ```console
  az extension add --name connectedk8s
  ```
  
  Instalar a `k8sconfiguration` extensão:
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  Se pretender atualizar estas extensões mais tarde, execute os seguintes comandos:
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Regiões suportadas

* E.U.A. Leste
* Europa Ocidental

## <a name="network-requirements"></a>Requisitos de rede

Os agentes da Azure Arc exigem que os seguintes protocolos/portas/URLs de saída funcionem.

* TCP no porto 443 -- >`https://:443`
* TCP no porto 9418 -- >`git://:9418`

| Ponto final (DNS)                                                                                               | Descrição                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Necessário para que o agente se conecte ao Azure e registe o cluster                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Ponto final do plano de dados para o agente empurrar o estado e obter informações de configuração                                      |
| `https://docker.io`                                                                                            | Obrigado a puxar imagens de contentores                                                                                         |
| `https://github.com`git://github.com                                                                         | Exemplo GitOps repos estão hospedados no GitHub. O agente de configuração requer conectividade com qualquer ponto final que especifique. |
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar fichas do Gestor de Recursos Azure                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Obrigado a retirar imagens de contentores para agentes do Azure Arc                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registe os dois fornecedores da Azure Arc habilitado a Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

O registo é um processo assíncronos. A inscrição pode demorar aproximadamente 10 minutos. Pode monitorizar o processo de registo com os seguintes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Criar um Grupo de Recursos

Utilize um grupo de recursos para armazenar metadados para o seu cluster.

Em primeiro lugar, crie um grupo de recursos para manter o recurso de cluster conectado.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Saída:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Ligar um cluster

Em seguida, ligaremos o nosso cluster Kubernetes ao Azure. O fluxo de trabalho `az connectedk8s connect` para é o seguinte:

1. Verifique a conectividade com o seu cluster Kubernetes: `KUBECONFIG` `~/.kube/config` via, ou`--kube-config`
1. Implementar agentes Azure Arc para Kubernetes usando Helm 3, no espaço de `azure-arc` nome

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verifique o cluster conectado

Listar os seus agrupamentos ligados:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Saída:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Também pode ver este recurso no [portal Azure.](https://portal.azure.com/) Assim que tiver o portal aberto no seu navegador, navegue para o grupo de recursos e o Arco Azure ativou o recurso Kubernetes com base no nome de recursos e entradas de nome de grupo de recursos usadas anteriormente no `az connectedk8s connect` comando.

Azure Arc habilitado a Kubernetes implanta alguns operadores no `azure-arc` espaço de nomes. Pode ver estas implementações e cápsulas aqui:

```console
kubectl -n azure-arc get deployments,pods
```

**Saída:**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes do Azure Arc para Kubernetes

Azure Arc habilitado Kubernetes é composto por alguns agentes (operadores) que funcionam no seu cluster implantados no `azure-arc` espaço de nome.

* `deployment.apps/config-agent`: observa o cluster ligado para os recursos de configuração do controlo de origem aplicados no cluster e atualiza o estado de conformidade
* `deployment.apps/controller-manager`: é um operador de operadores e orquestra interações entre componentes do Arco Azure
* `deployment.apps/metrics-agent`: recolhe métricas de outros agentes da Arc para garantir que estes agentes exibem um desempenho ótimo
* `deployment.apps/cluster-metadata-operator`: recolhe metadados de cluster - versão de cluster, contagem de nós e versão do agente Arc
* `deployment.apps/resource-sync-agent`: sincroniza os metadados de cluster acima mencionados para Azure
* `deployment.apps/clusteridentityoperator`: mantém o certificado de identidade de serviço gerido (MSI) utilizado por outros agentes para comunicação com a Azure
* `deployment.apps/flux-logs-agent`: recolhe registos dos operadores de fluxo implantados como parte da configuração do controlo de fontes

## <a name="delete-a-connected-cluster"></a>Eliminar um cluster conectado

Pode eliminar um `Microsoft.Kubernetes/connectedcluster` recurso utilizando o portal Azure CLI ou Azure.


* **Supressão utilizando o Azure CLI**: O seguinte comando Azure CLI pode ser utilizado para iniciar a supressão do recurso Kubernetes ativado pelo Arco Azure.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Isto remove o `Microsoft.Kubernetes/connectedCluster` recurso e quaisquer recursos associados `sourcecontrolconfiguration` em Azure. O CLI Azure usa o helm desinstalar para remover os agentes que estão a trabalhar no cluster também.

* **Eliminação no portal Azure**: A eliminação do Arco Azure permitiu que o recurso Kubernetes no portal Azure elimina o `Microsoft.Kubernetes/connectedcluster` recurso e quaisquer recursos associados `sourcecontrolconfiguration` em Azure, mas não elimina os agentes que estão a executar o cluster. Para eliminar os agentes que funcionam no cluster, executar o seguinte comando.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Próximos passos

* [Utilizar o GitOps num cluster ligado](./use-gitops-connected-cluster.md)
* [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)