---
title: Limitar o acesso a kubeconfig no Azure Kubernetes Service (AKS)
description: Saiba como controlar o acesso ao arquivo de configuração Kubernetes (kubeconfig) para administradores de cluster e usuários de cluster
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615679"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Utilizar controlos de acesso baseado em funções do Azure para definir o acesso ao arquivo de configuração de Kubernetes no Azure Kubernetes Service (AKS)

Pode interagir com os clusters de Kubernetes com o `kubectl` ferramenta. A CLI do Azure fornece uma forma fácil de obter as credenciais de acesso e informações de configuração para ligar ao seu AKS clusters com `kubectl`. Limite quem pode obter essa configuração Kubernetes (*kubeconfig*) informações e para limitar as permissões, em seguida, eles têm, pode usar controles de acesso baseado em função do Azure (RBAC).

Este artigo mostra-lhe como atribuir funções RBAC esse limite que pode obter as informações de configuração para um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente. Se precisar de um cluster do AKS, consulte o guia de introdução do AKS [com a CLI do Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Este artigo também requer a execução da versão 2.0.65 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de funções de cluster disponível

Quando interage com um cluster do AKS com o `kubectl` ferramenta, um ficheiro de configuração é utilizada que define informações de ligação do cluster. Este ficheiro de configuração é normalmente armazenado nas *~/.kube/config*. Vários clusters podem ser definidos nesta *kubeconfig* ficheiro. Alternar entre clusters com o [uso de configuração de kubectl-contexto][kubectl-config-use-context] comando.

O [az aks get-credentials][az-aks-get-credentials] comando permite-lhe obter as credenciais de acesso para um cluster do AKS e intercala-as para o *kubeconfig* ficheiro. Pode usar controles de acesso baseado em função do Azure (RBAC) para controlar o acesso a estas credenciais. Estas funções de RBAC do Azure permitem-lhe definir quem pode obter o *kubeconfig* ficheiro e o que permissões que têm, em seguida, dentro do cluster.

As duas funções incorporadas são:

* **Função de administrador de Cluster do serviço Kubernetes do Azure**  
    * Permite o acesso a *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* chamada à API. Esta chamada à API [lista as credenciais de administrador de cluster][api-cluster-admin].
    * Downloads *kubeconfig* para o *clusterAdmin* função.
* **Função de utilizador de Cluster do serviço Kubernetes do Azure**
    * Permite o acesso a *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* chamada à API. Esta chamada à API [lista as credenciais de utilizador do cluster][api-cluster-user].
    * Downloads *kubeconfig* para *clusterUser* função.

Estas funções RBAC podem ser aplicadas a um utilizador do Azure Active Directory (AD) ou grupo.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Atribuir permissões de função a um utilizador ou grupo

Para atribuir uma das funções disponíveis, terá de obter o ID de recurso do AKS cluster e o ID da conta de utilizador do Azure AD ou grupo. Os seguintes comandos de exemplo:

* Obter o recurso de cluster ID com o [show do az aks][az-aks-show] comando para o cluster com o nome *myAKSCluster* no *myResourceGroup* grupo de recursos. Fornece seu próprio nome do grupo de cluster e recursos conforme necessário.
* Utiliza a [show de conta de az][az-account-show] and [az ad user show][az-ad-user-show] comandos para obter sua ID de utilizador.
* Por fim, atribui uma função com o [criação da atribuição de função de az][az-role-assignment-create] comando.

O exemplo seguinte atribui o *função de administrador de Cluster do Azure Kubernetes Service* para uma conta de utilizador individuais:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Se pretender atribuir permissões a um grupo do Azure AD, atualize o `--assignee` parâmetro mostrado no exemplo anterior com o ID de objeto para o *grupo* em vez de um *utilizador*. Para obter o ID de objeto para um grupo, utilize o [show de grupo do ad az][az-ad-group-show] comando. O exemplo seguinte obtém o ID de objeto de grupo do Azure AD com o nome *appdev*: `az ad group show --group appdev --query objectId -o tsv`

Pode alterar a atribuição anterior para o *função de utilizador do Cluster* conforme necessário.

O resultado de exemplo seguinte mostra que a atribuição de função foi criada com êxito:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Obter e verifique se as informações de configuração

Com as funções do RBAC atribuídas, utilize o [az aks get-credentials][az-aks-get-credentials] comando para obter o *kubeconfig* definição para o seu cluster do AKS. O exemplo seguinte obtém o *– o administrador* as credenciais, que funcionará corretamente se o utilizador tenha sido concedido a *função de administrador de Cluster*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, pode utilizar o [vista de configuração de kubectl][kubectl-config-view] comando para verificar se o *contexto* para o cluster mostra que as informações de configuração de administrador foi aplicadas:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Remover permissões de função

Para remover atribuições de funções, utilize o [eliminar atribuição de função de az][az-role-assignment-delete] comando. Especifique a conta de ID e o ID de recurso de cluster, tal como obtidas nos comandos anteriores. Se atribuído a função a um grupo, em vez de um utilizador, especifique o objeto de grupo apropriadas ID em vez de ID de objeto de conta para o `--assignee` parâmetro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Passos Seguintes

Para uma maior segurança no acesso aos clusters do AKS, [integrar a autenticação do Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
