---
title: Principais de serviço do Azure Kubernetes Services (AKS)
description: Criar e gerir um principal de serviço do Azure Active Directory para um cluster no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: fb3b85ca1ee6a96a45c4cc26953a29d9ad365ae3
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406274"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Principais de serviço com o Serviço Kubernetes do Azure (AKS)

Para interagir com as APIs do Azure, os clusters do AKS requerem um [principal de serviço do Azure Active Directory (AD)][aad-service-principal]. O principal de serviço é necessário para criar e gerir dinamicamente outros recursos do Azure como um balanceador de carga ou registo de contentor do Azure (ACR).

Este artigo mostra como criar e utilizar um principal de serviço para os seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar um principal de serviço do Azure AD, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Se não tiver as permissões necessárias, poderá ter de pedir ao administrador do Microsoft Azure AD ou da subscrição para atribuir as permissões necessárias ou pré-criar um principal de serviço para utilizar com o cluster do AKS.

Também precisa da versão 2.0.59 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e utilizar um principal de serviço automaticamente

Quando criar um cluster do AKS no portal do Azure ou com o comando [az aks create][az-aks-create], o Azure pode gerar automaticamente um principal de serviço.

No exemplo seguinte da CLI do Azure, não está especificado nenhum principal de serviço. Neste cenário, a CLI do Azure cria um principal de serviço para o cluster do AKS. Para concluir esta operação com êxito, a sua conta do Azure tem de ter os direitos adequados para criar um principal de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar um principal de serviço manualmente

Para criar manualmente um principal de serviço com a CLI do Azure, utilize o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Estes valores serão utilizados quando criar um cluster do AKS na secção seguinte.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Especificar um principal de serviço para um cluster do AKS

Para utilizar um principal de serviço existente quando criar um cluster do AKS com o comando [az aks create][az-aks-create], utilize os parâmetros `--service-principal` e `--client-secret` para especificar `appId` e `password` no resultado do comando [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Se implementar um cluster do AKS com o portal do Azure, na página *Autenticação* da caixa de diálogo **Criar cluster do Kubernetes**, opte por **Configurar principal de serviço**. Selecione **Utilizar existente** e especifique os seguintes valores:

- O **ID de cliente do principal de serviço** é o seu *appId*
- O **Segredo do cliente do principal de serviço** é o valor da *palavra-passe*

![Imagem de navegação para o Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegar o acesso a outros recursos do Azure

O principal de serviço para o cluster do AKS pode ser utilizado para aceder a outros recursos. Por exemplo, se pretender implementar o seu cluster do AKS para uma sub-rede de rede virtual do Azure existente ou ligar para o Azure Container Registry (ACR), terá de delegar o acesso a esses recursos para o principal de serviço.

Delegar permissões, criar uma atribuição de função com o [criação da atribuição de função de az] [ az-role-assignment-create] comando. Atribuir o `appId` a um âmbito específico, tal como um grupo de recursos ou recurso de rede virtual. Uma função, em seguida, define quais as permissões que o principal de serviço tem no recurso, conforme mostrado no exemplo a seguir:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope` para um recurso tem de ser um ID de recurso completo, como */subscriptions/\<guid\>/resourceGroups/myResourceGroup* ou */subscriptions/\<guid \>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

As secções seguintes detalham as delegações comuns que poderá ter de fazer.

### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Se utilizar o Azure Container Registry (ACR) como arquivo de imagem de contentor, tem de conceder permissões para o seu cluster do AKS ler e solicitar imagens. O principal de serviço do AKS cluster deve ser delegado a *leitor* função no Registro. Para obter passos detalhados, consulte [acesso AKS de concessão para o ACR][aks-to-acr].

### <a name="networking"></a>Redes

Pode utilizar as redes avançados em que a rede virtual e sub-rede ou endereços IP públicos são outro grupo de recursos. Atribua um conjunto de permissões de função seguinte:

- Criar uma [função personalizada] [ rbac-custom-role] e definir as seguintes permissões de função:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
- Ou, atribuir os [contribuinte de rede] [ rbac-network-contributor] função incorporada na sub-rede na rede virtual

### <a name="storage"></a>Armazenamento

Terá de aceder aos recursos de disco existentes noutro grupo de recursos. Atribua um conjunto de permissões de função seguinte:

- Criar uma [função personalizada] [ rbac-custom-role] e definir as seguintes permissões de função:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribuir os [contribuinte de conta de armazenamento] [ rbac-storage-contributor] função incorporada no grupo de recursos

### <a name="azure-container-instances"></a>Azure Container Instances

Se usar o Virtual Kubelet para integrar com o AKS e optar por executar o Azure Container Instances (ACI) no grupo de recursos separados para o cluster do AKS, tem de ser concedido o principal de serviço do AKS *contribuinte* permissões no recurso do ACI grupo.

## <a name="additional-considerations"></a>Considerações adicionais

Quando utilizar principais de serviço do AKS e do Microsoft Azure AD, tenha em atenção as seguintes considerações.

- O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.
- Por predefinição, as credenciais do principal de serviço são válidas durante um ano. Pode [atualizar ou rodar as credenciais do principal de serviço] [ update-credentials] em qualquer altura.
- Cada principal de serviço está associado a uma aplicação do Azure AD. O principal de serviço de um cluster do Kubernetes pode ser associado a qualquer nome de aplicação do Microsoft Azure AD válido (por exemplo: *https://www.contoso.org/example*). O URL para a aplicação não tem de ser um ponto final real.
- Quando especificar o **ID de Cliente** do principal de serviço, utilize o valor de `appId`.
- Nas VMs de nós e principais no cluster do Kubernetes, as credenciais do principal de serviço são armazenadas no ficheiro `/etc/kubernetes/azure.json`
- Quando utilizar o comando [az aks create][az-aks-create] para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro `~/.azure/aksServicePrincipal.json` no computador utilizado para executar o comando.
- Ao eliminar um cluster do AKS que tenha sido criado pelo [az aks create][az-aks-create], o principal de serviço que foi criado automaticamente não é eliminado.
    - Para eliminar o principal de serviço, de consulta para o seu cluster *servicePrincipalProfile.clientId* e, em seguida, elimine com [eliminação do az ad app][az-ad-app-delete]. Substitua os seguintes nomes de cluster e de grupo de recursos com seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre principais de serviço do Azure Active Directory, consulte [aplicativos e objetos de principal de serviço][service-principal].

Para obter informações sobre como atualizar as credenciais, consulte [atualizar ou rodar as credenciais para um principal de serviço no AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md
