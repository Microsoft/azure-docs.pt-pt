---
title: Limitar o acesso ao kubeconfig no Serviço Azure Kubernetes (AKS)
description: Saiba como controlar o acesso ao ficheiro de configuração Kubernetes (kubeconfig) para administradores de clustere utilizadores de clusters
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/28/2020
ms.author: mlearned
ms.openlocfilehash: 8b9b1bf2a24002d606659c81e6fc426614fd5fd6
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114211"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Utilize controlos de acesso baseados em funções azure para definir o acesso ao ficheiro de configuração Kubernetes no Serviço Azure Kubernetes (AKS)

Pode interagir com os clusters Kubernetes utilizando a ferramenta `kubectl`. O Azure CLI fornece uma maneira fácil de obter as credenciais de acesso e informações de configuração para se conectar aos seus clusters AKS usando `kubectl`. Para limitar quem pode obter a configuração kubernetes *(kubeconfig)* e limitar as permissões que têm, pode utilizar controlos de acesso baseados em funções azure (RBAC).

Este artigo mostra-lhe como atribuir funções RBAC que limitam quem pode obter a informação de configuração para um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Este artigo também requer que esteja a executar a versão Azure CLI 2.0.65 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de papéis de cluster disponíveis

Quando interage com um cluster AKS utilizando a ferramenta `kubectl`, é utilizado um ficheiro de configuração que define as informações de ligação do cluster. Este ficheiro de configuração é normalmente armazenado em *~/.kube/config*. Vários clusters podem ser definidos neste ficheiro *kubeconfig.* Alterne entre aglomerados utilizando o comando [de contexto de uso de config kubectl.][kubectl-config-use-context]

O comando [az aks get-credentials][az-aks-get-credentials] permite-lhe obter as credenciais de acesso para um cluster AKS e fundi-las no ficheiro *kubeconfig.* Pode utilizar controlos de acesso baseados em funções azure (RBAC) para controlar o acesso a estas credenciais. Estas funções Azure RBAC permitem definir quem pode recuperar o ficheiro *kubeconfig* e que permissões têm dentro do cluster.

Os dois papéis incorporados são:

* **Papel de administrador de cluster de serviço Azure Kubernetes**  
  * Permite o acesso à *chamada Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API. Esta chamada da API [lista as credenciais de administração do cluster][api-cluster-admin].
  * *Downloadkubeconfig* para o papel *clusterAdmin.*
* **Função de utilizador do cluster de serviço Azure Kubernetes**
  * Permite o acesso à *chamada Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API. Esta chamada DaPi [lista as credenciais de utilizador do cluster][api-cluster-user].
  * *Downloadkubeconfig* para *o papel de clusterUser.*

Estas funções RBAC podem ser aplicadas a um utilizador ou grupo de Diretórios Ativos Azure (AD).

> ! [NOTA] Nos clusters que utilizam o Azure AD, os utilizadores com a função *clusterUser* têm um ficheiro *kubeconfig* vazio que solicita um login. Uma vez iniciado o início, os utilizadores têm acesso com base nas definições de utilizador ou grupo do Azure AD. Os utilizadores com a função *clusterAdmin* têm acesso a administradores.
>
> Os clusters que não utilizam a AD Azure apenas utilizam a função *clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Atribuir permissões de funções a um utilizador ou grupo

Para atribuir uma das funções disponíveis, é necessário obter o ID de recurso do cluster AKS e o ID da conta ou grupo de utilizadores da AD Azure. O seguinte exemplo ordena:

* Obtenha o ID de recurso do cluster usando o comando [az aks mostrar][az-aks-show] comando para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup.* Forneça o seu próprio cluster e nome de grupo de recursos, conforme necessário.
* Utilize o show de [conta az][az-account-show] e [az ad user show][az-ad-user-show] comandos para obter o seu ID de utilizador.
* Finalmente, atribuir um papel usando a atribuição de [funções az criar][az-role-assignment-create] comando.

O exemplo seguinte atribui a Função de Administrador a cluster de serviço Saque de *Serviço Azure Kubernetes* a uma conta de utilizador individual:

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
> Se pretender atribuir permissões a um grupo Azure AD, atualize o parâmetro `--assignee` mostrado no exemplo anterior com o ID do objeto para o *grupo* e não para um *utilizador*. Para obter o ID do objeto para um grupo, use o comando de [show de grupo az az.][az-ad-group-show] O exemplo seguinte obtém o ID do objeto para o grupo Azure AD chamado *appdev*: `az ad group show --group appdev --query objectId -o tsv`

Pode alterar a atribuição anterior à Função de Utilizador do *Cluster,* conforme necessário.

A saída de exemplo seguinte mostra que a atribuição de funções foi criada com sucesso:

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

## <a name="get-and-verify-the-configuration-information"></a>Obtenha e verifique as informações de configuração

Com as funções RBAC atribuídas, use o comando [az aks get-credentials][az-aks-get-credentials] para obter a definição *de kubeconfig* para o seu cluster AKS. O exemplo seguinte obtém as credenciais *--administração,* que funcionam corretamente se o utilizador tiver recebido a *Função de Administrador*cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, pode utilizar o comando de [visualização de config kubectl][kubectl-config-view] para verificar se o *contexto* do cluster mostra que a informação de configuração de administração foi aplicada:

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

## <a name="remove-role-permissions"></a>Remover permissões de funções

Para remover as atribuições de funções, utilize a atribuição de [funções az eliminar][az-role-assignment-delete] o comando. Especifique o ID da conta e o ID do recurso do cluster, conforme obtido nos comandos anteriores. Se atribuiu a função a um grupo e não a um utilizador, especifique o ID de objeto de grupo apropriado em vez de identificar o id do objeto para o parâmetro `--assignee`:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Passos seguintes

Para uma maior segurança no acesso aos clusters AKS, integre a [autenticação do Diretório Ativo Azure.][aad-integration]

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
