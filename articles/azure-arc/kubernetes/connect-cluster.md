---
title: Conecte um cluster Kubernetes ativado pelo Arco Azure (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Conecte um aglomerado de Kubernetes habilitado a Azure Arc
keywords: Kubernetes, Arc, Azure, K8s, contentores
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390560"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Conecte um cluster Kubernetes ativado pelo Arco Azure (Pré-visualização)

Este artigo abrange o processo de ligação de qualquer cluster certificado da Cloud Native Computing Foundation (CNCF), como o motor AKS em Azure, motor AKS no Azure Stack Hub, GKE, EKS e VMware vSphere cluster a Azure Arc.

## <a name="before-you-begin"></a>Antes de começar

Verifique se preparou os seguintes pré-requisitos:

* Um aglomerado kubernetes em funcionamento. Se não tiver um cluster Kubernetes existente, pode utilizar um dos seguintes guias para criar um cluster de teste:
  * Crie um cluster Kubernetes utilizando [Kubernetes em Docker (tipo)](https://kind.sigs.k8s.io/).
  * Crie um cluster Kubernetes utilizando Docker para [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) ou [Windows](https://docs.docker.com/docker-for-windows/#kubernetes).
* Um ficheiro kubeconfig para aceder ao cluster e papel de administração de cluster no cluster para implantação de agentes de Arc habilitados Kubernetes.
* O utilizador ou o principal de serviço utilizado `az login` e `az connectedk8s connect` os comandos devem ter as permissões de 'Ler' e 'Escrever' no tipo de recurso 'Microsoft.Kubernetes/connectedclusters'. A função "Kubernetes Cluster - Azure Arc Onboarding" tem estas permissões e pode ser usada para atribuições de funções no utilizador ou principal de serviço.
* Leme 3 para o embarque do cluster utilizando uma extensão connectedk8s. [Instale a mais recente versão do Helm 3](https://helm.sh/docs/intro/install) para satisfazer este requisito.
* A versão 2.15+ do Azure CLI para a instalação do Arco Azure permitiu extensões CLI de Kubernetes. [Instale o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) ou atualize a versão mais recente.
* Instale as extensões CLI ativadas pelo Arco:
  
  * Instale a `connectedk8s` extensão, o que o ajuda a ligar os clusters Kubernetes ao Azure:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * Instalar a `k8sconfiguration` extensão:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Se pretender atualizar estas extensões mais tarde, execute os seguintes comandos:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Regiões suportadas

* E.U.A. Leste
* Europa Ocidental

## <a name="network-requirements"></a>Requisitos de rede

Os agentes da Azure Arc exigem que funcionem os seguintes protocolos/portas/URLs de saída:

* TCP na porta 443: `https://:443`
* TCP no porto 9418: `git://:9418`

| Ponto final (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | É necessário que o agente se conecte ao Azure e registe o cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Ponto final do plano de dados para o agente empurrar o estado e obter informações de configuração.                                      |
| `https://login.microsoftonline.com`                                                                            | Necessário para buscar e atualizar fichas do Azure Resource Manager.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | Obrigado a retirar imagens de contentores para agentes do Azure Arc.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Obrigado a retirar certificados de identidade geridos atribuídos pelo sistema.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registe os dois fornecedores da Azure Arc habilitado a Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

A inscrição é um processo assíncrodo e pode demorar aproximadamente 10 minutos. Pode monitorizar o processo de registo com os seguintes comandos:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

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

Em seguida, ligaremos o nosso cluster Kubernetes ao Azure `az connectedk8s connect` utilizando:

1. Verifique a conectividade com o seu cluster Kubernetes através de um dos seguintes:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Implementar agentes Azure Arc para Kubernetes usando Helm 3 no `azure-arc` espaço de nome:

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

Utilize o seguinte comando para listar os seus agrupamentos ligados:

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

Também pode ver este recurso no [portal Azure.](https://portal.azure.com/) Abra o portal no seu navegador e navegue para o grupo de recursos e o Arco Azure permitiu o recurso Kubernetes, com base no nome de recursos e entradas de nome de grupo de recursos usadas anteriormente no `az connectedk8s connect` comando.  
> [!NOTE]
> Depois de embarcar no cluster, leva cerca de 5 a 10 minutos para que os metadados do cluster (versão de cluster, versão do agente, número de nós, etc.) surjam na página geral do Arco Azure habilitado o recurso Kubernetes no portal Azure.

## <a name="connect-using-an-outbound-proxy-server"></a>Conecte-se usando um servidor de procuração de saída

Se o seu cluster estiver por trás de um servidor de procuração de saída, a Azure CLI e o Arc ativados os agentes Kubernetes precisam de encaminhar os seus pedidos através do servidor de procuração de saída:

1. Verifique a versão da `connectedk8s` extensão instalada na sua máquina:

    ```console
    az -v
    ```

    Precisa da `connectedk8s` versão de extensão 0.2.5+ para configurar agentes com procuração de saída. Se tiver a versão 0.2.3 ou mais antiga na sua máquina, siga os passos de [atualização](#before-you-begin) para obter a versão mais recente da extensão na sua máquina.

2. Desaprova as variáveis ambientais necessárias para que o Azure CLI utilize o servidor de procuração de saída:

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

3. Executar o comando de ligação com parâmetros de procuração especificados:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Especificar `excludedCIDR` abaixo `--proxy-skip-range` é importante para garantir que a comunicação no cluster não seja quebrada para os agentes.
> * Embora , e são esperados para a maioria dos `--proxy-http` `--proxy-https` `--proxy-skip-range` ambientes de procuração de saída, `--proxy-cert` só é necessário se os certificados fidedignos de proxy precisarem de ser injetados na loja de certificados fidedignas de cápsulas de agente.
> * A especificação de procuração acima é atualmente aplicada apenas aos agentes Arc e não às cápsulas de fluxo utilizadas na fonteControlConfiguration. A equipa da Arc ativada pela Kubernetes está a trabalhar ativamente nesta funcionalidade e estará disponível em breve.

## <a name="azure-arc-agents-for-kubernetes"></a>Agentes do Azure Arc para Kubernetes

Azure Arc habilitado a Kubernetes implanta alguns operadores no `azure-arc` espaço de nomes. Pode ver estas implementações e cápsulas utilizando:

```console
kubectl -n azure-arc get deployments,pods
```

**Saída:**

```console
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

Azure Arc habilitado Kubernetes é composto por alguns agentes (operadores) que funcionam no seu cluster implantados no `azure-arc` espaço de nome.

| Agentes (Operadores)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Observa o cluster conectado para recursos de configuração de controlo de origem aplicados no cluster e atualiza o estado de conformidade.                                                        |
| `deployment.apps/controller-manager` | Um operador de operadores que orquestra interações entre componentes do Arco Azure.                                      |
| `deployment.apps/metrics-agent`                                                                            | Recolhe métricas de desempenho de outros agentes do Arco.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | Recolhe metadados de cluster, tais como versão de cluster, contagem de nós e versão agente Azure Arc.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Sincroniza os metadados de cluster acima mencionados para Azure.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Azure Arc ativou Kubernetes atualmente suporta a identidade atribuída ao sistema. `clusteridentityoperator` mantém o certificado de identidade de serviço gerido (MSI) utilizado por outros agentes para comunicação com a Azure.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Recolhe registos dos operadores de fluxo implantados como parte da configuração do controlo de fontes.                                                                  |

## <a name="delete-a-connected-cluster"></a>Eliminar um cluster conectado

Pode eliminar um `Microsoft.Kubernetes/connectedcluster` recurso utilizando o portal Azure CLI ou Azure.


* **Supressão utilizando o Azure CLI**: Utilize o seguinte comando Azure CLI para iniciar a supressão do arco Azure ativado recurso Kubernetes.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Este comando remove o `Microsoft.Kubernetes/connectedCluster` recurso e quaisquer recursos associados `sourcecontrolconfiguration` em Azure. O CLI Azure também utiliza `helm uninstall` para remover os agentes que estão a trabalhar no cluster.

* **Eliminação no portal Azure**: A eliminação do Arco Azure permitiu que o recurso Kubernetes no portal Azure elimina o `Microsoft.Kubernetes/connectedcluster` recurso e quaisquer recursos associados `sourcecontrolconfiguration` em Azure, mas *não* remove os agentes que estão a funcionar no cluster. 

  Para remover os agentes que funcionam no cluster, executar o seguinte comando:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o GitOps num cluster ligado](./use-gitops-connected-cluster.md)
* [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
