---
title: Principais de serviço do Azure Kubernetes Services (AKS)
description: Criar e gerir um principal de serviço do Azure Active Directory para um cluster no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 2c792eb4dc060e3f5d7fa2d8f2176bdd51538c43
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392730"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Principais de serviço com o Serviço Kubernetes do Azure (AKS)

Para interagir com apis azure, um cluster AKS requer um diretor de serviço de [Diretório Ativo Azure (AD)][aad-service-principal] ou uma [identidade gerida](use-managed-identity.md). Um principal de serviço ou identidade gerida é necessário para criar e gerir de forma dinâmica outros recursos Azure, como um balanceador de carga Azure ou registo de contentores (ACR).

Este artigo mostra como criar e utilizar um principal de serviço para os seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar um principal de serviço do Azure AD, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Se não tiver as permissões necessárias, poderá ter de pedir ao administrador do Microsoft Azure AD ou da subscrição para atribuir as permissões necessárias ou pré-criar um principal de serviço para utilizar com o cluster do AKS.

Se estiver a utilizar um diretor de serviço de um inquilino azure diferente, existem considerações adicionais em torno das permissões disponíveis quando implementa o cluster. Pode não ter as permissões adequadas para ler e escrever informações de diretório. Para mais informações, consulte [quais são as permissões padrão dos utilizadores no Diretório Ativo Azure?][azure-ad-permissions]

Também precisa da versão 2.0.59 do Azure CLI ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e utilizar um principal de serviço automaticamente

Quando criar um cluster do AKS no portal do Azure ou com o comando [az aks create][az-aks-create], o Azure pode gerar automaticamente um principal de serviço.

No exemplo seguinte da CLI do Azure, não está especificado nenhum principal de serviço. Neste cenário, a CLI do Azure cria um principal de serviço para o cluster do AKS. Para concluir esta operação com êxito, a sua conta do Azure tem de ter os direitos adequados para criar um principal de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar um principal de serviço manualmente

Para criar manualmente um principal de serviço com a CLI do Azure, utilize o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Estes valores serão utilizados quando criar um cluster do AKS na secção seguinte.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
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

> [!NOTE]
> Se estiver a usar um diretor de serviço existente com segredo personalizado, certifique-se de que o segredo não é superior a 190 bytes.

Se implementar um cluster do AKS com o portal do Azure, na página *Autenticação* da caixa de diálogo **Criar cluster do Kubernetes**, opte por **Configurar principal de serviço**. Selecione **Utilizar existente** e especifique os seguintes valores:

- O **ID de cliente do principal de serviço** é o seu *appId*
- O **Segredo do cliente do principal de serviço** é o valor da *palavra-passe*

![Imagem de navegação para o Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegar acesso a outros recursos do Azure

O diretor de serviço do cluster AKS pode ser usado para aceder a outros recursos. Por exemplo, se pretender implantar o seu cluster AKS numa subnet de rede virtual Azure existente ou ligar-se ao Registo de Contentores Azure (ACR), precisa de delegar o acesso a esses recursos ao diretor de serviço.

Para delegar permissões, crie uma atribuição de funções usando a atribuição de [funções az criar][az-role-assignment-create] comando. Atribuir o `appId` a um determinado âmbito, como um grupo de recursos ou um recurso de rede virtual. Uma função então define quais as permissões que o diretor de serviço tem sobre o recurso, como mostra o seguinte exemplo:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope` recurso tem de ser um ID de recursos completo, tais como */subscrições/\<guid\>/resourceGroups/myResourceGroup* ou */subscrições/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

As seguintes secções detalham as delegações comuns que poderá ter de fazer.

### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Se utilizar o Registo de Contentores Azure (ACR) como loja de imagens de contentores, tem de conceder permissões ao diretor de serviço para o seu cluster AKS ler e puxar imagens. Atualmente, a configuração recomendada é usar o comando de [az aks criar][az-aks-create] ou [az aks atualizar][az-aks-update] para integrar com um registo e atribuir o papel adequado para o diretor de serviço. Para etapas detalhadas, consulte [Authenticate com registo de contentores Azure do Serviço Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Redes

Pode utilizar uma rede avançada onde a rede virtual e os endereços IP da rede virtual ou do IP público se encontram noutro grupo de recursos. Atribuir uma das seguintes permissões de funções:

- Crie um [papel personalizado][rbac-custom-role] e defina as seguintes permissões de função:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresss/join/action*
  - *Microsoft.Network/publicIPAddresss/read*
  - *Microsoft.Network/publicIPAddresss/write*
- Ou, atribuir o papel integrado do Colaborador da [Rede][rbac-network-contributor] na subrede dentro da rede virtual

### <a name="storage"></a>Armazenamento

Poderá ter de aceder aos recursos do Disco existentes noutro grupo de recursos. Atribuir uma das seguintes permissões de funções:

- Crie um [papel personalizado][rbac-custom-role] e defina as seguintes permissões de função:
  - *Microsoft.Compute/discos/ler*
  - *Microsoft.Compute/disks/write*
- Ou, atribuir o papel integrado do Contribuinte da Conta de [Armazenamento][rbac-storage-contributor] no grupo de recursos

### <a name="azure-container-instances"></a>Azure Container Instances

Se utilizar o Virtual Kubelet para integrar com o AKS e optar por executar as Instâncias de Contentores Azure (ACI) em grupo de recursos separados do cluster AKS, o diretor de serviço aks deve receber permissões *de contribuinte* no grupo de recursos ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Quando utilizar principais de serviço do AKS e do Microsoft Azure AD, tenha em atenção as seguintes considerações.

- O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.
- Por predefinição, as credenciais principais do serviço são válidas por um ano. Pode [atualizar ou rodar as credenciais principais do serviço][update-credentials] a qualquer momento.
- Cada principal de serviço está associado a uma aplicação do Azure AD. O diretor de serviço de um cluster Kubernetes pode ser associado a *https://www.contoso.org/example*qualquer nome de aplicação Azure AD válido (por exemplo: ). O URL para a aplicação não tem de ser um ponto final real.
- Quando especificar o **ID de Cliente** do principal de serviço, utilize o valor de `appId`.
- No nó de agente VMs no cluster Kubernetes, as credenciais principais do serviço são armazenadas no ficheiro`/etc/kubernetes/azure.json`
- Quando utilizar o comando [az aks create][az-aks-create] para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro `~/.azure/aksServicePrincipal.json` no computador utilizado para executar o comando.
- Se não passar especificamente um diretor de serviço em comandos ADICIONAis `~/.azure/aksServicePrincipal.json` AKS CLI, o principal de serviço predefinido localizado é utilizado.  
- Também pode remover opcionalmente o ficheiro aksServicePrincipal.json, e o AKS criará um novo diretor de serviço.
- Ao eliminar um cluster do AKS que tenha sido criado pelo [az aks create][az-aks-create], o principal de serviço que foi criado automaticamente não é eliminado.
    - Para eliminar o diretor de serviço, consulta para o seu serviço de *clusterPrincipalProfile.clientId* e, em seguida, eliminar com a [aplicação az ad delete][az-ad-app-delete]. Substitua os seguintes nomes de grupos de recursos e cluster sem os seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Resolução de problemas

As principais credenciais de serviço para um cluster AKS são cached pelo Azure CLI. Se estas credenciais expirarem, encontra erros de implantação de clusters AKS. A seguinte mensagem de erro ao executar [az aks criar][az-aks-create] pode indicar um problema com as credenciais principais do serviço em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verifique a idade do ficheiro de credenciais utilizando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O prazo de validade padrão para as credenciais principais do serviço é de um ano. Se o seu ficheiro *aksServicePrincipal.json* tiver mais de um ano, elimine o ficheiro e tente implementar novamente um cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os principais de serviço sonárcea do Azure Ative Diretório, consulte [os objetos principais de aplicação e serviço.][service-principal]

Para obter informações sobre como atualizar as credenciais, consulte [Atualizar ou rodar as credenciais para um diretor][update-credentials]de serviço no AKS .

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
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
