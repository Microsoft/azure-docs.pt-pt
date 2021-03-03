---
title: 'Quickstart: Ligue um cluster Kubernetes existente ao Arco de Azure'
description: Neste arranque rápido, aprenda a ligar um arco Azure habilitado para o cluster Kubernetes.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 02/24/2021
ms.custom: template-quickstart
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 8eb177f0c80d7ed2df70c75ca476a1dfe33c8425
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665596"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Quickstart: Ligue um cluster Kubernetes existente ao Arco de Azure 

Neste arranque rápido, colheremos os benefícios do Azure Arc ativado pela Kubernetes e ligaremos um cluster Kubernetes existente ao Arco de Azure. Para uma abordagem conceptual sobre os clusters de ligação ao Arco de Azure, consulte o artigo de [Arquitetura do Agente Kubernetes habilitado para o Arco de Azure](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Verifique se tem:
    * Um aglomerado kubernetes em funcionamento.
    * Um `kubeconfig` ficheiro.
    * Permissões de 'Ler' e 'Escrever' para o utilizador ou principal de serviço que liga a criação do tipo de recurso de recurso Azure Arc ativado por Kubernetes ( `Microsoft.Kubernetes/connectedClusters` ).
* Instale o [último lançamento do Helm 3](https://helm.sh/docs/intro/install).
* Instale as seguintes extensões CLI ativadas por Kubernetes de versões >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Para atualizar estas extensões para a versão mais recente, execute os seguintes comandos:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8s-configuration
  ```

>[!NOTE]
>**Regiões apoiadas:**
>* E.U.A. Leste
>* Europa Ocidental
>* E.U.A. Centro-Oeste
>* E.U.A. Centro-Sul
>* Sudeste Asiático
>* Sul do Reino Unido
>* E.U.A. Oeste 2
>* Leste da Austrália
>* E.U.A. Leste 2
>* Europa do Norte

## <a name="meet-network-requirements"></a>Cumprir os requisitos da rede

>[!IMPORTANT]
>Os agentes da Azure Arc exigem que funcionem os seguintes protocolos/portas/URLs de saída:
>* TCP na porta 443: `https://:443`
>* TCP no porto 9418: `git://:9418`
  
| Ponto final (DNS) | Descrição |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | É necessário que o agente se conecte ao Azure e registe o cluster.                                                        |  
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com`, `https://westcentralus.dp.kubernetesconfiguration.azure.com`, `https://southcentralus.dp.kubernetesconfiguration.azure.com`, `https://southeastasia.dp.kubernetesconfiguration.azure.com`, `https://uksouth.dp.kubernetesconfiguration.azure.com`, `https://westus2.dp.kubernetesconfiguration.azure.com`, `https://australiaeast.dp.kubernetesconfiguration.azure.com`, `https://eastus2.dp.kubernetesconfiguration.azure.com`, `https://northeurope.dp.kubernetesconfiguration.azure.com` | Ponto final do plano de dados para o agente empurrar o estado e obter informações de configuração.                                      |  
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar fichas do Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Obrigado a retirar imagens de contentores para agentes do Azure Arc.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Necessário para retirar certificados de identidade de serviço gerido (MSI) atribuídos pelo sistema.                                                                  |


## <a name="install-the-azure-arc-enabled-kubernetes-cli-extensions"></a>Instale as extensões CLI ativadas pelo Arco Azure

Introduza os seguintes comandos:  

    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-configuration
    ```

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registar os dois fornecedores da Azure Arc habilitado a Kubernetes

1. Introduza os seguintes comandos:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    ```
2. Monitorize o processo de registo. A inscrição pode demorar até 10 minutos.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table    
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Ligue um cluster Kubernetes existente

1. Ligue o seu cluster Kubernetes ao Arco Azure utilizando o seguinte comando:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

## <a name="verify-cluster-connection"></a>Verifique a ligação do cluster

Veja uma lista dos seus agrupamentos ligados com o seguinte comando:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> Depois de embarcar no cluster, leva cerca de 5 a 10 minutos para que os metadados do cluster (versão de cluster, versão do agente, número de nós, etc.) surjam na página geral do Arco Azure habilitado o recurso Kubernetes no portal Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Conecte-se usando um servidor de procuração de saída

Se o seu cluster estiver por trás de um servidor de procuração de saída, a Azure CLI e o Arco Azure ativaram que os agentes kubernetes precisam de encaminhar os seus pedidos através do servidor de procuração de saída. 


1. Desaprova as variáveis ambientais necessárias para que o Azure CLI utilize o servidor de procuração de saída:

    * Se estiver a utilizar a bash, executar o seguinte comando com valores apropriados:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Se estiver a utilizar o PowerShell, executar o seguinte comando com valores apropriados:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Executar o comando de ligação com parâmetros de procuração especificados:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Especificar `excludedCIDR` para garantir que a `--proxy-skip-range` comunicação no cluster não é quebrada para os agentes.
> * `--proxy-http`, `--proxy-https` e `--proxy-skip-range` são esperados para a maioria dos ambientes de procuração de saída. `--proxy-cert`*só* é necessário se precisar de injetar certificados fidedignos esperados por procuração na loja de certificados fidedignas de cápsulas de agente.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Ver agentes do Azure Arc para Kubernetes

Azure Arc habilitado a Kubernetes implanta alguns operadores no `azure-arc` espaço de nomes. 

1. Veja estas implementações e cápsulas utilizando:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Verifique se todas as cápsulas estão em `Running` estado.

    ```output
    NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
    deployment.apps/cluster-metadata-operator     1/1             1        1      16h
    deployment.apps/clusteridentityoperator       1/1             1        1      16h
    deployment.apps/config-agent                  1/1             1        1      16h
    deployment.apps/controller-manager            1/1             1        1      16h
    deployment.apps/flux-logs-agent               1/1             1        1      16h
    deployment.apps/metrics-agent                 1/1             1        1      16h
    deployment.apps/resource-sync-agent           1/1             1        1      16h

    NAME                                           READY    STATUS   RESTART AGE
    pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
    pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
    pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
    pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
    pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
    pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
    pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Pode eliminar o recurso Kubernetes ativado pelo Arco Azure, quaisquer recursos de configuração associados *e* quaisquer agentes que corram no cluster utilizando o Azure CLI utilizando o seguinte comando:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>A eliminação do Arco Azure permitiu que o recurso Kubernetes utilizando o portal Azure remove quaisquer recursos de configuração associados, mas *não* remove quaisquer agentes em execução no cluster. A melhor prática é eliminar o recurso Azure Arc ativado pela Kubernetes usando `az connectedk8s delete` em vez do portal Azure.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a implementar configurações para o seu cluster Kubernetes conectado usando GitOps.
> [!div class="nextstepaction"]
> [Implementar configurações usando Gitops](use-gitops-connected-cluster.md)
