---
title: Utilize cluster Connect para ligar ao arco Azure ativado clusters Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Utilize cluster Connect para ligar de forma segura aos clusters de Kubernetes ativados pelo Arco azul
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451134"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Utilize cluster Connect para ligar ao arco Azure ativado clusters Kubernetes

Com o Cluster Connect, pode ligar-se de forma segura aos clusters kubernetes ativados pelo Arco Azure sem que seja ativada qualquer porta de entrada na firewall. O acesso ao `apiserver` cluster Kubernetes ativado pelo Arco permite os seguintes cenários:
* Permitir depuração interativa e resolução de problemas.
* Fornecer acesso de cluster aos serviços Azure para [localizações personalizadas](custom-locations.md) e outros recursos criados em cima dele.

Uma visão geral conceptual desta funcionalidade está disponível no Cluster connect - Azure Arc ativou o artigo [de Kubernetes.](conceptual-cluster-connect.md)

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Pré-requisitos   

- [Instalar ou atualizar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) para a versão >= 2.16.0

- Instale a `connectedk8s` extensão Azure CLI da versão >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Se já instalou a `connectedk8s` extensão, atualize a extensão para a versão mais recente:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Um arco Azure existente permitiu o agrupamento de kubernetes conectado.
    - Se ainda não ligou um cluster, use o nosso [quickstart](quickstart-connect-cluster.md).
    - [Atualize os seus agentes](agent-upgrade.md#manually-upgrade-agents) para a versão >= 1.1.0.

- Ativar o Cluster Connect em qualquer agrupamento de Azure Arc ativado através da execução do seguinte comando numa máquina onde o `kubeconfig` ficheiro é apontado para o conjunto de preocupações:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Permitir os pontos finais abaixo para acesso de saída, para além dos mencionados ao [ligar um cluster Kubernetes ao Arco de Azure:](quickstart-connect-cluster.md#meet-network-requirements)

    | Ponto final | Porta |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Utilização

Duas opções de autenticação são suportadas com a função Cluster Connect: 
* Azure Active Directory (Azure AD) 
* Ficha de conta de serviço

### <a name="option-1-azure-active-directory"></a>Opção 1: Diretório Ativo Azure

1. Com o `kubeconfig` ficheiro a apontar para o seu cluster `apiserver` Kubernetes, crie um ClusterRoleBinding ou RoleBinding para a entidade AZure AD (principal de serviço ou utilizador) que exija acesso:

    **Para o utilizador:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Para a aplicação AD AZure:**

    1. Obtenha a `objectId` associada com a sua aplicação AD Azure:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Criar um ClusterRoleBinding ou RoleBinding para a entidade Azure AD (principal de serviço ou utilizador) que precisa de aceder a este cluster:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Depois de iniciar sessão no Azure CLI utilizando a entidade de interesse AZure AD, obtenha o Cluster Connect `kubeconfig` necessário para comunicar com o cluster a partir de qualquer lugar (mesmo fora da firewall que rodeia o cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Utilizar `kubectl` para enviar pedidos para o cluster:

    ```console
    kubectl get pods
    ```
    
    Deve agora ver uma resposta do cluster que contém a lista de todas as cápsulas no espaço de `default` nomes.

### <a name="option-2-service-account-bearer-token"></a>Opção 2: Token portador de conta de serviço

1. Com o `kubeconfig` ficheiro a apontar para o seu cluster `apiserver` Kubernetes, crie uma conta de serviço em qualquer espaço de nome (o comando seguinte cria-o no espaço de nome predefinido):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Criar ClusterRoleBinding ou RoleBinding para conceder a esta [conta de serviço as permissões adequadas no cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Obtenha o símbolo da conta de serviço usando os seguintes comandos

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Obtenha o Cluster Connect `kubeconfig` necessário para comunicar com o cluster a partir de qualquer lugar (mesmo fora da firewall que rodeia o cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Utilizar `kubectl` para enviar pedidos para o cluster:

    ```console
    kubectl get pods
    ```

    Deve agora ver uma resposta do cluster que contém a lista de todas as cápsulas no espaço de `default` nomes.

## <a name="known-limitations"></a>Limitações conhecidas

Ao fazer pedidos ao cluster Kubernetes, se a entidade AZure AD utilizada fizer parte de mais de 200 grupos, observa-se o seguinte erro, uma vez que esta é uma limitação conhecida:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Para ultrapassar este erro:
1. Criar um [diretor de serviços](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), que é menos provável de ser membro de mais de 200 grupos.
1. [Inscreva-se no](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) Azure CLI com o diretor de serviço antes de executar o `az connectedk8s proxy` comando.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> Confiem [do Azure AD RBAC](azure-rbac.md) nos seus clusters