---
title: Limite o acesso ao kubeconfig no Serviço Azure Kubernetes (AKS)
description: Saiba como controlar o acesso ao ficheiro de configuração Kubernetes (kubeconfig) para administradores de clusters e utilizadores de clusters
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: c73c4a0ae46c3d2ac3a64543473bd6639d03b434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009295"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Utilize o controlo de acesso baseado em funções Azure para definir o acesso ao ficheiro de configuração Kubernetes no Serviço Azure Kubernetes (AKS)

Pode interagir com os clusters Kubernetes utilizando a `kubectl` ferramenta. O Azure CLI fornece uma maneira fácil de obter as credenciais de acesso e informações de configuração para ligar aos seus clusters AKS usando `kubectl` . Para limitar quem pode obter a configuração de Kubernetes *(kubeconfig*) e limitar as permissões que então têm, pode utilizar o controlo de acesso baseado em funções Azure (Azure RBAC).

Este artigo mostra-lhe como atribuir funções DE RBAC que limitam quem pode obter a informação de configuração para um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de funções de cluster disponíveis

Quando interage com um cluster AKS utilizando a `kubectl` ferramenta, é utilizado um ficheiro de configuração que define informações de ligação do cluster. Este ficheiro de configuração é normalmente armazenado em *~/.kube/config*. Vários clusters podem ser definidos neste ficheiro *kubeconfig.* Alterna entre clusters utilizando o comando [de contexto de utilização de config kubectl.][kubectl-config-use-context]

O comando [az aks get-credentials permite-lhe][az-aks-get-credentials] obter as credenciais de acesso para um cluster AKS e fundi-los no ficheiro *kubeconfig.* Você pode usar o controlo de acesso baseado em funções Azure (Azure RBAC) para controlar o acesso a estas credenciais. Estes papéis de Azure permitem definir quem pode recuperar o ficheiro *kubeconfig,* e que permissões têm dentro do cluster.

Os dois papéis embutidos são:

* **Azure Kubernetes Service Cluster Função de Administrador**  
  * Permite o acesso à chamada API *do Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API. Esta chamada da API [lista as credenciais de administrador de cluster][api-cluster-admin].
  * Descarrega *kubeconfig* para o papel *clusterAdmin.*
* **Papel de utilizador do cluster de serviço Azure Kubernetes**
  * Permite o acesso à chamada API *do Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API. Esta chamada da API [lista as credenciais de utilizador do cluster][api-cluster-user].
  * Descarrega *kubeconfig* para o papel *de clusterUser.*

Estas funções RBAC podem ser aplicadas a um utilizador ou grupo do Azure Ative Directory (AD).

> [!NOTE]
> Nos clusters que utilizam Azure AD, os utilizadores com a função *clusterUser* têm um ficheiro *kubeconfig* vazio que solicita um login. Uma vez iniciado o login, os utilizadores têm acesso com base nas definições do utilizador ou grupo Azure. Os utilizadores com o *clusterAmin* têm acesso administrativo.
>
> Os clusters que não usam Azure AD apenas usam o *clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Atribuir permissões de função a um utilizador ou grupo

Para atribuir uma das funções disponíveis, é necessário obter o ID de recursos do cluster AKS e o ID da conta ou grupo de utilizadores Azure AD. O exemplo seguinte comanda:

* Obtenha o ID do recurso de cluster usando o comando [az aks show][az-aks-show] para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup.* Forneça o seu próprio conjunto e nome de grupo de recursos, conforme necessário.
* Utilize o [programa de conta az][az-account-show] e os comandos [de exibição de utilizadores de anúncios az][az-ad-user-show] para obter o seu ID do utilizador.
* Finalmente, atribuir um papel usando a [atribuição de função az criar][az-role-assignment-create] comando.

O exemplo a seguir atribui o *Azure Kubernetes Service Cluster Admin Role* a uma conta de utilizador individual:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Se pretender atribuir permissões a um grupo AD Azure, atualize o `--assignee` parâmetro mostrado no exemplo anterior com o ID do objeto para o *grupo* e não com um *utilizador*. Para obter o ID do objeto para um grupo, utilize o comando [de exibição de grupo az ad.][az-ad-group-show] O exemplo a seguir obtém o ID do objeto para o grupo Azure AD nomeado *appdev:*`az ad group show --group appdev --query objectId -o tsv`

Pode alterar a atribuição anterior para a *Função de Utilizador do Cluster,* conforme necessário.

A saída de exemplo a seguir mostra que a atribuição de funções foi criada com sucesso:

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

## <a name="get-and-verify-the-configuration-information"></a>Obter e verificar as informações de configuração

Com as funções RBAC atribuídas, use o comando [az aks get-credentials][az-aks-get-credentials] para obter a definição *de kubeconfig* para o seu cluster AKS. O exemplo a seguir obtém as credenciais *de administração,* que funcionam corretamente se o utilizador tiver recebido o *Cluster Admin Role*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, pode utilizar o comando [de visualização de config kubectl][kubectl-config-view] para verificar se o *contexto* do cluster mostra que a informação de configuração de administração foi aplicada:

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

Para remover as atribuições de funções, utilize o comando de eliminação de [funções az.][az-role-assignment-delete] Especifique o ID da conta e o ID do recurso de cluster, conforme obtido nos comandos anteriores. Se atribuísse a função a um grupo e não a um utilizador, especifique o ID do objeto de grupo apropriado em vez de um ID do objeto de conta para o `--assignee` parâmetro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Passos seguintes

Para uma maior segurança no acesso aos clusters AKS, [integre a autenticação do Azure Ative Directory][aad-integration].

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
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
