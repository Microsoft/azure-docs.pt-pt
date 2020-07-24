---
title: Use Azure AD no serviço Azure Kubernetes
description: Saiba como utilizar o Azure AD no Serviço Azure Kubernetes (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 06a97126df449b77bf3fcc48bd23231512c9dff2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056646"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integração do Azure Ative Directory gerido pela AKS

A integração AZure AD gerida pela AKS foi projetada para simplificar a experiência de integração da AD Azure, onde os utilizadores eram previamente obrigados a criar uma aplicação para clientes, uma aplicação de servidor, e exigiu que o inquilino da AD Azure concedesse permissões de Leitura de Diretório. Na nova versão, o fornecedor de recursos AKS gere as aplicações do cliente e do servidor para si.

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação AD do Azure

Os administradores do cluster podem configurar o controlo de acesso baseado em funções (RBAC) baseado em funções com base na identidade de um utilizador ou na adesão ao grupo de diretórios. A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][open-id-connect].

Saiba mais sobre o fluxo de integração da AAD na documentação de [conceitos de integração do Azure Ative Directory.](concepts-identity.md#azure-active-directory-integration)

## <a name="region-availability"></a>Disponibilidade de região

A integração do Azure Ative Directory gerida pela AKS está disponível em regiões públicas onde [a AKS é apoiada.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

* O Governo de Azure não é apoiado neste momento.
* Azure China 21Vianet não é atualmente apoiada.

## <a name="limitations"></a>Limitações 

* A integração AD gerida pela AKS não pode ser desativada
* os clusters não ativados não-RBAC não são suportados para a integração AAD gerida pela AKS
* Mudar o inquilino AZure AD associado à integração AAD gerida pela AKS não é suportado

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "as-is" e "conforme disponível", e estão excluídas dos Contratos de Nível de Serviço e garantia limitada. As pré-visualizações da AKS são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam ao uso da produção. Para mais informações, consulte os seguintes artigos de apoio: 
> - [Políticas de apoio da AKS](support-policies.md) 
> - [FAQ de suporte Azure](faq.md)

## <a name="prerequisites"></a>Pré-requisitos

* A versão Azure CLI 2.9.0 ou mais tarde
* Kubectl com uma versão mínima de [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

> [!Important]
> Você deve usar Kubectl com uma versão mínima de 1.18

Para instalar kubectl, utilize os seguintes comandos:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
```

Utilize [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operativos.

```azurecli-interactive 
az feature register --name AAD-V2 --namespace Microsoft.ContainerService    
``` 

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 

```azurecli-interactive 
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"    
``` 

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register)    

```azurecli-interactive 
az provider register --namespace Microsoft.ContainerService 
``` 


## <a name="before-you-begin"></a>Before you begin

Para o seu grupo, precisa de um grupo AD Azure. Este grupo é necessário como grupo de administração para o cluster conceder permissões de administração de cluster. Você pode usar um grupo AD Azure existente, ou criar um novo. Grave a identificação do objeto do seu grupo AD Azure.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Para criar um novo grupo AD Azure para os seus administradores de cluster, utilize o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster AKS com Azure AD ativado

Crie um cluster AKS utilizando os seguintes comandos CLI.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Crie um cluster AKS e permita o acesso da administração ao seu grupo AD Azure

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma criação bem sucedida de um cluster Azure AD gerido pela AKS tem a seguinte secção no corpo de resposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Uma vez criado o cluster, pode começar a aceder-lhe.

## <a name="access-an-azure-ad-enabled-cluster"></a>Aceda a um cluster AD AD Azure

Você precisará do cluster [de serviço Azure Kubernetes função](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) incorporada para fazer os seguintes passos.

Obtenha as credenciais de utilizador para aceder ao cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Siga as instruções para iniciar sinsuposições.

Utilize o comando kubectl get nodes para visualizar os nóns no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configure [o Controlo de Acesso Baseado em Função (RBAC)](./azure-ad-rbac.md) para configurar grupos de segurança adicionais para os seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Problemas de acesso à resolução de problemas com a Azure AD

> [!Important]
> Os passos descritos abaixo estão a contornar a autenticação normal do grupo Azure AD. Use-os apenas em caso de emergência.

Se estiver permanentemente bloqueado por não ter acesso a um grupo Azure AD válido com acesso ao seu cluster, ainda pode obter as credenciais de administração para aceder diretamente ao cluster.

Para fazer estes passos, você precisará ter acesso ao admin de cluster de [serviço Azure Kubernetes.](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade para integração AD Azure gerida pela AKS

Se o seu cluster utilizar a integração Azure AD legacy, pode fazer upgrade para a integração Ad Ad gerida pela AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma migração bem sucedida de um cluster Azure AD gerido pela AKS tem a seguinte secção no corpo de resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se quiser aceder ao cluster, siga os passos [aqui.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Sinal não interativo com kubelogin

Existem alguns cenários não interativos, como os oleodutos de integração contínua, que não estão atualmente disponíveis com kubectl. Pode utilizar [`kubelogin`](https://github.com/Azure/kubelogin) para aceder ao cluster com o principal de inscrição do serviço não interativo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a integração do Azure RBAC para a Autorização Kubernetes][azure-rbac-integration]
* Saiba mais sobre [a integração da AD AZure com a Kubernetes RBAC][azure-ad-rbac].
* Utilize [kubelogin](https://github.com/Azure/kubelogin) para aceder a funcionalidades de autenticação Azure que não estejam disponíveis em kubectl.
* Saiba mais sobre [os conceitos de identidade AKS e Kubernetes.][aks-concepts-identity]
* Utilize [os modelos Azure Resource Manager (ARM)][aks-arm-template] para criar clusters Azure AD geridos pela AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
