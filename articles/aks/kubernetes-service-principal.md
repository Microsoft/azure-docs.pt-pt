---
title: Principais de serviço do Azure Kubernetes Services (AKS)
description: Criar e gerir um principal de serviço do Azure Active Directory para um cluster no Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: ded3fc97c4cdf041fdf50d7b4aa9a9b2fbdf1c84
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913494"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Principais de serviço com o Serviço Kubernetes do Azure (AKS)

Para interagir com as APIs do Azure, um cluster AKS requer uma [entidade de serviço Azure Active Directory (AD)][aad-service-principal]. O principal de serviço é necessário para criar e gerir dinamicamente outros recursos do Azure como um balanceador de carga ou registo de contentor do Azure (ACR).

Este artigo mostra como criar e utilizar um principal de serviço para os seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar um principal de serviço do Azure AD, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Se não tiver as permissões necessárias, poderá ter de pedir ao administrador do Microsoft Azure AD ou da subscrição para atribuir as permissões necessárias ou pré-criar um principal de serviço para utilizar com o cluster do AKS.

Se você estiver usando uma entidade de serviço de um locatário do Azure AD diferente, haverá considerações adicionais sobre as permissões disponíveis quando você implantar o cluster. Talvez você não tenha as permissões apropriadas para ler e gravar informações de diretório. Para obter mais informações, consulte [quais são as permissões de usuário padrão no Azure Active Directory?][azure-ad-permissions]

Você também precisa do CLI do Azure versão 2.0.59 ou posterior instalada e configurada. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e utilizar um principal de serviço automaticamente

Quando você cria um cluster AKS no portal do Azure ou usando o comando [AZ AKs Create][az-aks-create] , o Azure pode gerar automaticamente uma entidade de serviço.

No exemplo seguinte da CLI do Azure, não está especificado nenhum principal de serviço. Neste cenário, a CLI do Azure cria um principal de serviço para o cluster do AKS. Para concluir esta operação com êxito, a sua conta do Azure tem de ter os direitos adequados para criar um principal de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar um principal de serviço manualmente

Para criar manualmente uma entidade de serviço com o CLI do Azure, use o comando [AZ ad SP Create-for-RBAC][az-ad-sp-create] . No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

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

Para usar uma entidade de serviço existente ao criar um cluster AKS usando o comando [AZ AKs Create][az-aks-create] , use os parâmetros `--service-principal` e `--client-secret` para especificar o `appId` e `password` da saída do comando [AZ ad SP Create-for-RBAC][az-ad-sp-create] :

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

## <a name="delegate-access-to-other-azure-resources"></a>Delegar acesso a outros recursos do Azure

A entidade de serviço para o cluster AKS pode ser usada para acessar outros recursos. Por exemplo, se você deseja implantar o cluster AKS em uma sub-rede de rede virtual do Azure existente ou conectar-se ao registro de contêiner do Azure (ACR), você precisa delegar acesso a esses recursos para a entidade de serviço.

Para delegar permissões, crie uma atribuição de função usando o comando [AZ role Assignment Create][az-role-assignment-create] . Atribua o `appId` a um escopo específico, como um grupo de recursos ou recurso de rede virtual. Uma função define quais permissões a entidade de serviço tem no recurso, conforme mostrado no exemplo a seguir:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope` de um recurso precisa ser uma ID de recurso completa, como */subscriptions/\<guid\>/resourceGroups/myResourceGroup* ou */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet*

As seções a seguir detalham as delegações comuns que talvez você precise fazer.

### <a name="azure-container-registry"></a>Azure Container Registry

Se você usar o ACR (registro de contêiner do Azure) como seu repositório de imagens de contêiner, precisará conceder permissões à entidade de serviço para o cluster AKS ler e efetuar pull de imagens. Atualmente, a configuração recomendada é usar o comando [AZ AKs Create][az-aks-create] ou [AZ AKs Update][az-aks-update] para integrar com um registro e atribuir a função apropriada para a entidade de serviço. Para obter etapas detalhadas, consulte [autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure][aks-to-acr].

### <a name="networking"></a>Funcionamento em Rede

Você pode usar a rede avançada em que a rede virtual e a sub-rede ou os endereços IP públicos estão em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Crie uma [função personalizada][rbac-custom-role] e defina as seguintes permissões de função:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Ou, atribua a função interna de [colaborador de rede][rbac-network-contributor] na sub-rede dentro da rede virtual

### <a name="storage"></a>Armazenamento

Talvez seja necessário acessar os recursos de disco existentes em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Crie uma [função personalizada][rbac-custom-role] e defina as seguintes permissões de função:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribua a função interna de [colaborador da conta de armazenamento][rbac-storage-contributor] no grupo de recursos

### <a name="azure-container-instances"></a>Azure Container Instances

Se você usar o Kubelet virtual para integrar com o AKS e optar por executar as instâncias de contêiner do Azure (ACI) no grupo de recursos separado para o cluster AKS, a entidade de serviço AKS deverá receber permissões de *colaborador* no grupo de recursos ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Quando utilizar principais de serviço do AKS e do Microsoft Azure AD, tenha em atenção as seguintes considerações.

- O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.
- Por padrão, as credenciais da entidade de serviço são válidas por um ano. Você pode [atualizar ou girar as credenciais da entidade de serviço][update-credentials] a qualquer momento.
- Cada principal de serviço está associado a uma aplicação do Azure AD. O principal de serviço de um cluster do Kubernetes pode ser associado a qualquer nome de aplicação do Microsoft Azure AD válido (por exemplo: *https://www.contoso.org/example* ). O URL para a aplicação não tem de ser um ponto final real.
- Quando especificar o **ID de Cliente** do principal de serviço, utilize o valor de `appId`.
- Nas VMs do nó do agente no cluster kubernetes, as credenciais da entidade de serviço são armazenadas no arquivo `/etc/kubernetes/azure.json`
- Quando você usa o comando [AZ AKs Create][az-aks-create] para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço são gravadas no arquivo `~/.azure/aksServicePrincipal.json` no computador usado para executar o comando.
- Quando você exclui um cluster AKS que foi criado por [AZ AKs Create][az-aks-create], a entidade de serviço que foi criada automaticamente não é excluída.
    - Para excluir a entidade de serviço, consulte o cluster *servicePrincipalProfile. clientId* e, em seguida, exclua com [AZ ad app Delete][az-ad-app-delete]. Substitua o seguinte grupo de recursos e nomes de cluster pelos seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Resolução de problemas

As credenciais da entidade de serviço para um cluster AKS são armazenadas em cache pelo CLI do Azure. Se essas credenciais tiverem expirado, você encontrará erros ao implantar clusters AKS. A seguinte mensagem de erro ao executar [AZ AKs Create][az-aks-create] pode indicar um problema com as credenciais da entidade de serviço armazenada em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verifique a idade do arquivo de credenciais usando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O tempo de expiração padrão para as credenciais da entidade de serviço é de um ano. Se o arquivo *aksServicePrincipal. JSON* tiver mais de um ano, exclua o arquivo e tente implantar um cluster AKs novamente.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre Azure Active Directory entidades de serviço, consulte [objetos de aplicativo e entidade de serviço][service-principal].

Para obter informações sobre como atualizar as credenciais, consulte [atualizar ou girar as credenciais de uma entidade de serviço no AKs][update-credentials].

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
