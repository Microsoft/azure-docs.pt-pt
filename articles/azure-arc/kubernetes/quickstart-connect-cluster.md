---
title: 'Quickstart: Ligue um cluster Kubernetes existente ao Arco de Azure'
description: Neste arranque rápido, aprenda a ligar um arco Azure habilitado para o cluster Kubernetes.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, cluster
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481264"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Quickstart: Ligue um cluster Kubernetes existente ao Arco de Azure 

Neste arranque rápido, colheremos os benefícios do Azure Arc ativado pela Kubernetes e ligaremos um cluster Kubernetes existente ao Arco de Azure. Para uma abordagem conceptual sobre os clusters de ligação ao Arco de Azure, consulte o artigo de [Arquitetura do Agente Kubernetes habilitado para o Arco de Azure](./conceptual-agent-architecture.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Um aglomerado kubernetes em funcionamento. Se não tiver um, pode criar um cluster usando uma destas opções:
    * [Kubernetes em Docker (KIND)](https://kind.sigs.k8s.io/)
    * Crie um cluster Kubernetes usando Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Cluster Kubernetes auto-gerido usando [Cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > O cluster precisa de ter pelo menos um nó de sistema operativo e tipo de `linux/amd64` arquitetura. Aglomerados com `linux/arm64` apenas nós ainda não estão apoiados.
    
* Um `kubeconfig` ficheiro e contexto que aponta para o seu cluster.
* Permissões de 'Ler' e 'Escrever' no Arco Azure ativaram o tipo de recurso Kubernetes `Microsoft.Kubernetes/connectedClusters` ().

* Instale o [último lançamento do Helm 3](https://helm.sh/docs/intro/install).

- [Instalar ou atualizar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a versão >= 2.16.0
* Instale a `connectedk8s` extensão Azure CLI da versão >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Se a `connectedk8s` extensão já estiver instalada, atualize-a para a versão mais recente utilizando o seguinte comando - `az extension update --name connectedk8s`


>[!NOTE]
>A lista de regiões apoiadas por Azure Arc habilitada a Kubernetes pode ser encontrada [aqui.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

>[!NOTE]
> Se você quiser usar localizações personalizadas no cluster, então use regiões do Leste dos EUA ou da Europa Ocidental para ligar o seu cluster como localizações personalizadas só está disponível nestas regiões a partir de agora. Todas as outras funcionalidades de Kubernetes ativadas pelo Azure Arc estão disponíveis em todas as regiões listadas acima.

## <a name="meet-network-requirements"></a>Cumprir os requisitos da rede

>[!IMPORTANT]
>Os agentes da Azure Arc exigem que funcionem os seguintes protocolos/portas/URLs de saída:
>* TCP na porta 443: `https://:443`
>* TCP no porto 9418: `git://:9418`
  
| Ponto final (DNS) | Descrição |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | É necessário que o agente se conecte ao Azure e registe o cluster.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Ponto final do plano de dados para o agente empurrar o estado e obter informações de configuração.                                      |  
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar fichas do Azure Resource Manager.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Obrigado a retirar imagens de contentores para agentes do Azure Arc.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  Necessário para retirar certificados de identidade de serviço gerido (MSI) atribuídos pelo sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registar os dois fornecedores da Azure Arc habilitado a Kubernetes

1. Introduza os seguintes comandos:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Monitorize o processo de registo. A inscrição pode demorar até 10 minutos.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
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

> [!TIP]
> O comando acima referido sem o parâmetro de localização especificado cria o recurso Azure Arc ativado kubernetes no mesmo local que o grupo de recursos. Para criar o Arco Azure ativou o recurso Kubernetes num local diferente, especifique `--location <region>` ou quando executa o `-l <region>` `az connectedk8s connect` comando.

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
> [Implementar configurações usando Gitops](tutorial-use-gitops-connected-cluster.md)
