---
title: Localizações personalizadas em Azure Arc habilitados Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Utilize localizações personalizadas para implementar serviços Azure PaaS em Azure Arc habilitado clusters Kubernetes
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451158"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Localizações personalizadas em Azure Arc habilitados Kubernetes

Como uma extensão de localização Azure, *as Localizações Personalizadas* fornecem uma maneira para os administradores de inquilinos usarem o seu Arco Azure habilitado clusters Kubernetes como locais-alvo para implantar instâncias de serviços Azure. Exemplos de recursos Azure incluem Azure Arc enabled INSTANCE e Azure Arc habilitado a hiperescala pósgresQL.

Semelhante às localizações do Azure, os utilizadores finais dentro do inquilino com acesso a Localizações Personalizadas podem aí utilizar recursos utilizando o computação privada da sua empresa.

Uma visão geral conceptual desta funcionalidade está disponível em [locais personalizados - Azure Arc ativou o artigo de Kubernetes.](conceptual-custom-locations.md)

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Instale ou atualize o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a versão >= 2.16.0.

- `connectedk8s` (versão >= 1.1.0), `k8s-extension` (versão >= 0,2.0) e `customlocation` (versão >= 0.1.0) Extensões Azure CLI. Instale estas extensões Azure CLI executando os seguintes comandos:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Se as `connectedk8s` `k8s-extension` extensões e `customlocation` extensões já estiverem instaladas, pode atualizá-las para a versão mais recente utilizando o seguinte comando:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- O registo do fornecedor está completo para `Microsoft.ExtendedLocation` .
    1. Introduza os seguintes comandos:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Monitorize o processo de registo. A inscrição pode demorar até 10 minutos.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Regiões apoiadas para locais personalizados:**
>* E.U.A. Leste
>* Europa Ocidental

## <a name="enable-custom-locations-on-cluster"></a>Ativar localizações personalizadas no cluster

Para ativar esta funcionalidade no seu cluster, execute o seguinte comando:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. A funcionalidade de localizações personalizadas depende da função Cluster Connect. Portanto, ambas as funcionalidades têm de ser ativadas para que os locais personalizados funcionem.
> 2. `az connectedk8s enable-features` precisa de ser executado numa máquina onde o `kubeconfig` ficheiro está a apontar para o cluster em que as funcionalidades devem ser ativadas.

## <a name="create-custom-location"></a>Criar localização personalizada

1. Crie um aglomerado de Kubernetes ativado pelo Arco Azure.
    - Se ainda não ligou um cluster, use o nosso [quickstart](quickstart-connect-cluster.md).
    - [Atualize os seus agentes](agent-upgrade.md#manually-upgrade-agents) para a versão >= 1.1.0.

1. Implemente a extensão de cluster do serviço Azure cuja instância deseja em cima da localização personalizada:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > O representante de saída sem autenticação e procuração de saída com autenticação básica é suportado pela extensão do cluster dos Serviços de Dados ativado pelo Arco. O representante de saída que espera certificados fidedignos não é atualmente suportado.

1. Obtenha o identificador Azure Resource Manager do cluster Kubernetes habilitado para o Arco Azure, referenciado em etapas posteriores como `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Obtenha o identificador Azure Resource Manager da extensão de cluster implantado em cima do aglomerado de Kubernetes ativado por Azure Arc, referenciado em etapas posteriores como `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Crie uma localização personalizada fazendo referência ao aglomerado de Kubernetes habilitado para o Arco Azure e a extensão:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> Ligue-se de forma segura ao cluster utilizando [o Cluster Connect](cluster-connect.md)