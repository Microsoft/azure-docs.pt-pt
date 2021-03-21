---
title: Principais de serviço do Azure Kubernetes Services (AKS)
description: Criar e gerir um principal de serviço do Azure Active Directory para um cluster no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: b4b5b3eedb2e63686e1bb26580ea653e3a50a910
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507828"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Principais de serviço com o Serviço Kubernetes do Azure (AKS)

Para interagir com as APIs do Azure, um cluster AKS requer um [diretor de serviço Azure Ative (AD)][aad-service-principal] ou uma [identidade gerida.](use-managed-identity.md) Um principal serviço ou identidade gerida é necessário para criar e gerir dinamicamente outros recursos Azure, tais como um equilibrador de carga Azure ou registo de contentores (ACR).

Este artigo mostra como criar e utilizar um principal de serviço para os seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar um principal de serviço do Azure AD, tem de ter permissões para registar uma aplicação no seu inquilino do Azure AD e para atribuir a aplicação a uma função na sua subscrição. Se não tiver as permissões necessárias, poderá ter de pedir ao administrador do Microsoft Azure AD ou da subscrição para atribuir as permissões necessárias ou pré-criar um principal de serviço para utilizar com o cluster do AKS.

Se você estiver usando um diretor de serviço de um inquilino AZure AD diferente, há considerações adicionais em torno das permissões disponíveis quando você implanta o cluster. Pode não ter as permissões adequadas para ler e escrever informações de diretório. Para mais informações, consulte [quais são as permissões de utilizador predefinidas no Azure Ative Directory?][azure-ad-permissions]

Também precisa da versão Azure CLI 2.0.59 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

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
> Se estiver a usar um diretor de serviço existente com segredo personalizado, certifique-se de que o segredo não é mais do que 190 bytes.

Se implementar um cluster do AKS com o portal do Azure, na página *Autenticação* da caixa de diálogo **Criar cluster do Kubernetes**, opte por **Configurar principal de serviço**. Selecione **Utilizar existente** e especifique os seguintes valores:

- O **ID de cliente do principal de serviço** é o seu *appId*
- O **Segredo do cliente do principal de serviço** é o valor da *palavra-passe*

![Imagem de navegação para o Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegado de acesso a outros recursos da Azure

O principal de serviço do cluster AKS pode ser usado para aceder a outros recursos. Por exemplo, se pretender implantar o seu cluster AKS numa sub-rede de rede virtual Azure existente ou ligar-se ao Registo de Contentores Azure (ACR), tem de delegar o acesso a esses recursos ao principal do serviço.

Para delegar permissões, crie uma tarefa de função utilizando a [atribuição de funções az criar][az-role-assignment-create] comando. Atribua-o `appId` a um determinado âmbito, como um grupo de recursos ou um recurso de rede virtual. Uma função define então as permissões que o diretor de serviço tem sobre o recurso, como mostra o seguinte exemplo:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope` recurso tem de ser um ID de recursos completo, como */subscrições/ \<guid\> /resourceGroups/myResourceGroup* ou */subscrições/ \<guid\> /resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Se tiver removido a atribuição de função do Contribuinte do grupo de recursos de nó, as operações abaixo poderão falhar.  

As seguintes secções detalham as delegações comuns que poderá ter de fazer.

### <a name="azure-container-registry"></a>Registo de Contentores do Azure

Se utilizar o Registo de Contentores Azure (ACR) como loja de imagens do contentor, tem de conceder permissões ao principal de serviço para que o seu cluster AKS leia e retire imagens. Atualmente, a configuração recomendada é usar o comando [de az aks create][az-aks-create] ou [az aks update][az-aks-update] para integrar-se com um registo e atribuir o papel adequado para o principal serviço. Para obter etapas detalhadas, consulte [Authenticate with Azure Container Registry from Azure Kubernetes Service][aks-to-acr].

### <a name="networking"></a>Rede

Pode utilizar uma rede avançada onde a rede virtual e os endereços IP da sub-rede ou do ip público se encontram noutro grupo de recursos. Atribua a função de colaborador de [rede][rbac-network-contributor] incorporada na sub-rede dentro da rede virtual. Em alternativa, pode criar uma [função personalizada][rbac-custom-role] com permissões de acesso aos recursos da rede nesse grupo de recursos. Consulte [as permissões de serviço da AKS][aks-permissions] para obter mais detalhes.

### <a name="storage"></a>Armazenamento

Poderá ter de aceder aos recursos existentes em disco noutro grupo de recursos. Atribuir um dos seguintes conjuntos de permissões de função:

- Crie um [papel personalizado][rbac-custom-role] e defina as seguintes permissões de função:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribuir o [papel de Contribuinte de Conta][rbac-storage-contributor] de Armazenamento no grupo de recursos

### <a name="azure-container-instances"></a>Azure Container Instances

Se utilizar o Kubelet Virtual para integrar-se com a AKS e optar por executar a Azure Container Instances (ACI) em grupo de recursos separado do cluster AKS, o principal de serviço AKS deve receber permissões *de contribuinte* no grupo de recursos ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Quando utilizar principais de serviço do AKS e do Microsoft Azure AD, tenha em atenção as seguintes considerações.

- O principal de serviço para Kubernetes faz parte da configuração do cluster. No entanto, não utilize a identidade para implementar o cluster.
- Por padrão, as principais credenciais do serviço são válidas por um ano. Pode [atualizar ou rodar as credenciais principais do serviço a][update-credentials] qualquer momento.
- Cada principal de serviço está associado a uma aplicação do Azure AD. O principal de serviço de um cluster Kubernetes pode ser associado a qualquer nome de aplicação AZure AD válido (por exemplo: *https://www.contoso.org/example* ). O URL para a aplicação não tem de ser um ponto final real.
- Quando especificar o **ID de Cliente** do principal de serviço, utilize o valor de `appId`.
- No número de agentes VMs no cluster Kubernetes, as credenciais principais de serviço são armazenadas no ficheiro `/etc/kubernetes/azure.json`
- Quando utilizar o comando [az aks create][az-aks-create] para gerar automaticamente o principal de serviço, as credenciais do principal de serviço são escritas no ficheiro `~/.azure/aksServicePrincipal.json` no computador utilizado para executar o comando.
- Se não passar especificamente um principal de serviço em comandos AKS CLI adicionais, é utilizado o principal de serviço padrão `~/.azure/aksServicePrincipal.json` localizado.  
- Também pode remover opcionalmente o aksServicePrincipal.jsem ficheiro, e a AKS criará um novo diretor de serviço.
- Ao eliminar um cluster do AKS que tenha sido criado pelo [az aks create][az-aks-create], o principal de serviço que foi criado automaticamente não é eliminado.
    - Para eliminar o principal serviço, consultar o seu serviço de *clusterPrincipalProfile.clientId* e, em seguida, apagar com [az ad sp delete][az-ad-sp-delete]. Substitua os seguintes nomes de grupo de recursos e cluster pelos seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Resolução de problemas

As principais credenciais de serviço para um cluster AKS são cached pelo Azure CLI. Se estas credenciais expirarem, encontra erros na implantação de clusters AKS. A seguinte mensagem de erro ao executar [a az aks criar][az-aks-create] pode indicar um problema com as principais credenciais do serviço em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verifique a idade do ficheiro de credenciais utilizando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O prazo de validade padrão para as principais credenciais de serviço é de um ano. Se o seu *aksServicePrincipal.jsem* ficheiro for superior a um ano, elimine o ficheiro e tente implementar novamente um cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os principais do serviço Azure Ative Directory, consulte [os objetos principais de aplicação e serviço.][service-principal]

Para obter informações sobre como atualizar as credenciais, consulte [Update ou rode as credenciais para um responsável de serviço em AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
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
[aks-permissions]: concepts-identity.md#aks-service-permissions
