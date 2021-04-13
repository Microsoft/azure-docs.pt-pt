---
title: Azure Arc permitiu extensões de cluster Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Implementar e gerir o ciclo de vida das extensões em Azure Arc permitiu que Kubernetes
ms.openlocfilehash: 854d7418515d7927a3c0b4b8790ed4770af555ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312625"
---
# <a name="kubernetes-cluster-extensions"></a>Extensões de cluster kubernetes

A funcionalidade de extensões Kubernetes permite o seguinte em Azure Arc ativado clusters Kubernetes:

* Implementação baseada em Azure Resource Manager da extensão do cluster.
* Gestão do ciclo de vida das cartas helm de extensão.

Uma visão geral conceptual desta funcionalidade está disponível em [extensões cluster - Azure Arc ativou o artigo de Kubernetes.](conceptual-extensions.md)

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Instale ou atualize o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a versão >= 2.16.0.
- `connectedk8s` (versão >= 1.1.0) e `k8s-extension` (versão >= 0.2.0) Extensões CLI Azure. Instale estas extensões Azure CLI executando os seguintes comandos:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Se a `connectedk8s` `k8s-extension` extensão já estiver instalada, pode atualizá-las para a versão mais recente utilizando o seguinte comando:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Um arco Azure existente permitiu o agrupamento de kubernetes conectado.
    - Se ainda não ligou um cluster, use o nosso [quickstart](quickstart-connect-cluster.md).
    - [Atualize os seus agentes](agent-upgrade.md#manually-upgrade-agents) para a versão >= 1.1.0.

## <a name="currently-available-extensions"></a>Extensões atualmente disponíveis

| Extensão | Description |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | Proporciona visibilidade ao desempenho das cargas de trabalho implementadas no cluster Kubernetes. Recolhe métricas de utilização de memória e CPU de controladores, nós e contentores. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Recolhe informações relacionadas com segurança, como dados de registo de auditoria do cluster Kubernetes. Fornece recomendações e alertas de ameaças com base em dados recolhidos. |

## <a name="usage-of-cluster-extensions"></a>Utilização de extensões de cluster

### <a name="create-extensions-instance"></a>Criar extensões instância

Criar uma nova instância de extensão `k8s-extension create` com, passando em valores para os parâmetros obrigatórios. O comando abaixo cria um Monitor Azure para a extensão de contentores no seu aglomerado de Kubernetes habilitado a Kubernetes:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Saída:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * O serviço não consegue reter informação sensível durante mais de 48 horas. Se o Azure Arc ativou os agentes kubernetes não têm conectividade de rede por mais de 48 horas e não conseguem determinar se criam uma extensão no cluster, então a extensão passa a `Failed` ser estatal. Uma vez no `Failed` estado, terá de correr `k8s-extension create` novamente para criar um novo recurso Azure de extensão.
> * * O Monitor Azure para contentores é uma extensão singleton (apenas uma necessária por cluster). Terá de limpar quaisquer instalações anteriores do Azure Monitor para contentores (sem extensões) antes de instalar o mesmo através de extensões. Siga as instruções para [eliminar a `az k8s-extension create` tabela Helm antes de funcionar ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid).

**Parâmetros necessários**

| Nome do parâmetro | Descrição |
|----------------|------------|
| `--name` | Nome da instância de extensão |
| `--extension-type` | O tipo de extensão que pretende instalar no cluster. Por exemplo: Microsoft.AzureMonitor.Containers, microsoft.azuredefender.kubernetes | 
| `--scope` | Âmbito de instalação para a extensão - `cluster` ou `namespace` |
| `--cluster-name` | O nome do Arco Azure permitiu o recurso Kubernetes no qual a instância de extensão tem de ser criada |
| `--resource-group` | O grupo de recursos que contém o Arco Azure permitiu o recurso Kubernetes |
| `--cluster-type` | O tipo de cluster em que a extensão tem de ser criada. Apenas a corrente `connectedClusters` , que corresponde a Azure Arc habilitado Kubernetes, é um valor aceite |

**Parâmetros opcionais**

| Nome do parâmetro | Descrição |
|--------------|------------|
| `--auto-upgrade-minor-version` | Propriedade Boolean que especifica se a versão menor de extensão será atualizada automaticamente ou não. Predefinição: `true`.  Se este parâmetro for definido como verdadeiro, não é possível definir `version` o parâmetro, uma vez que a versão será atualizada dinamicamente. Se estiver definido, a `false` extensão não será atualizada automaticamente mesmo para versões de patch. |
| `--version` | Versão da extensão a instalar (versão específica para fixar a extensão para). Não deve ser fornecida se a versão de atualização automática-menor estiver definida para `true` . |
| `--configuration-settings` | Configurações que podem ser passadas na extensão para controlar a sua funcionalidade. Devem ser passados como pares separados do espaço `key=value` após o nome do parâmetro. Se este parâmetro for utilizado no comando, não `--configuration-settings-file` pode ser utilizado no mesmo comando. |
| `--configuration-settings-file` | Caminho para o ficheiro JSON com pares de valor chave a serem usados para passar em configurações de configuração para a extensão. Se este parâmetro for utilizado no comando, não `--configuration-settings` pode ser utilizado no mesmo comando. |
| `--configuration-protected-settings` | Estas definições não são recuperáveis usando `GET` chamadas ou `az k8s-extension show` comandos API, e são assim usadas para passar em configurações sensíveis. Devem ser passados como pares separados do espaço `key=value` após o nome do parâmetro. Se este parâmetro for utilizado no comando, não `--configuration-protected-settings-file` pode ser utilizado no mesmo comando. |
| `--configuration-protected-settings-file` | Caminho para o ficheiro JSON com pares de valor chave para serem usados para passar em configurações sensíveis para a extensão. Se este parâmetro for utilizado no comando, não `--configuration-protected-settings` pode ser utilizado no mesmo comando. |
| `--release-namespace` | Este parâmetro indica o espaço de nomes no qual a libertação deve ser criada. Este parâmetro só é relevante se `scope` o parâmetro for definido para `cluster` . |
| `--release-train` |  Os autores de extensões podem publicar versões em diferentes comboios de lançamento, tais `Stable` `Preview` como, etc. Se este parâmetro não for definido explicitamente, `Stable` é utilizado como padrão. Este parâmetro não pode ser utilizado quando `autoUpgradeMinorVersion` o parâmetro é definido para `false` . |
| `--target-namespace` | Este parâmetro indica o espaço de nomes no qual a libertação será criada. A permissão da conta do sistema criada para esta instância de extensão será restrita a este espaço de nome. Este parâmetro só é relevante se o `scope` parâmetro for definido para `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Mostrar detalhes de uma instância de extensão

Ver detalhes de uma instância de extensão atualmente instalada `k8s-extension show` com, passando em valores para os parâmetros obrigatórios:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Saída:**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>Listar todas as extensões instaladas no cluster

Listar todas as extensões instaladas num cluster `k8s-extension list` com, passando em valores para os parâmetros obrigatórios.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Saída:**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="delete-extension-instance"></a>Eliminar instância de extensão

Elimine uma instância de extensão num cluster `k8s-extension delete` com, passando em valores para os parâmetros obrigatórios.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> O recurso Azure que representa esta extensão é eliminado imediatamente. A libertação do Helm no cluster associado a esta extensão só é eliminada quando os agentes que estão em execução no cluster Kubernetes têm conectividade de rede e podem chegar novamente aos serviços Azure para buscar o estado pretendido.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as extensões de cluster atualmente disponíveis para Azure Arc habilitado a Kubernetes:
> [!div class="nextstepaction"]
> Monitor Azure [](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)