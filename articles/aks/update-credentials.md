---
title: Redefinir as credenciais para um cluster do serviço de kubernetes do Azure (AKS)
description: Saiba como atualizar ou redefinir as credenciais da entidade de serviço para um cluster no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: bda0ab50b829fa2e6d58e73b51e3a0a0f6c9e2af
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432914"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Atualizar ou girar as credenciais de uma entidade de serviço no serviço kubernetes do Azure (AKS)

Por padrão, os clusters AKS são criados com uma entidade de serviço que tem um tempo de expiração de um ano. Como você perto da data de validade, você pode redefinir as credenciais para estender a entidade de serviço por um período de tempo adicional. Talvez você também queira atualizar ou girar as credenciais como parte de uma política de segurança definida. Este artigo fornece detalhes sobre como atualizar essas credenciais para um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.65 ou posterior instalado e configurado. Execute @ no__t-0 para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Optar por atualizar ou criar uma entidade de serviço

Quando desejar atualizar as credenciais de um cluster AKS, você pode optar por:

* Atualize as credenciais para a entidade de serviço existente usada pelo cluster ou
* Crie uma entidade de serviço e atualize o cluster para usar essas novas credenciais.

### <a name="update-existing-service-principal-expiration"></a>Atualizar a expiração da entidade de serviço existente

Para atualizar as credenciais da entidade de serviço existente, obtenha a ID da entidade de serviço do seu cluster usando o comando [AZ AKs show][az-aks-show] . O exemplo a seguir obtém a ID do cluster chamado *myAKSCluster* no grupo de recursos *MyResource* Group. A ID da entidade de serviço é definida como uma variável chamada *SP_ID* para uso em um comando adicional.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto de variáveis que contém a ID da entidade de serviço, agora redefina as credenciais usando [AZ ad SP Credential Reset][az-ad-sp-credential-reset]. O exemplo a seguir permite que a plataforma Azure gere um novo segredo seguro para a entidade de serviço. Esse novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora, continue em para [atualizar o cluster AKs com novas credenciais](#update-aks-cluster-with-new-credentials). Essa etapa é necessária para que as alterações da entidade de serviço sejam refletidas no cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar uma nova entidade de serviço

Se você optar por atualizar as credenciais da entidade de serviço existente na seção anterior, ignore esta etapa. Continue [atualizando o cluster AKs com novas credenciais](#update-aks-cluster-with-new-credentials).

Para criar uma entidade de serviço e, em seguida, atualizar o cluster AKS para usar essas novas credenciais, use o comando [AZ ad SP Create-for-RBAC][az-ad-sp-create] . No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Esses valores são usados na próxima etapa.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora, defina variáveis para a ID da entidade de serviço e o segredo do cliente usando a saída de seu próprio comando [AZ ad SP Create-for-RBAC][az-ad-sp-create] , conforme mostrado no exemplo a seguir. O *SP_ID* é sua *AppID*e o *SP_SECRET* é sua *senha*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Agora, continue em para [atualizar o cluster AKs com novas credenciais](#update-aks-cluster-with-new-credentials). Essa etapa é necessária para que as alterações da entidade de serviço sejam refletidas no cluster AKS.

## <a name="update-aks-cluster-with-new-credentials"></a>Atualizar o cluster AKS com novas credenciais

Independentemente de você optar por atualizar as credenciais para a entidade de serviço existente ou criar uma entidade de serviço, agora você atualiza o cluster AKS com suas novas credenciais usando o comando [AZ AKs Update-Credentials][az-aks-update-credentials] . As variáveis para *--Service-principal* e *--Client-Secret* são usadas:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Leva alguns minutos para que as credenciais da entidade de serviço sejam atualizadas no AKS.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, a entidade de serviço do próprio cluster AKS foi atualizada. Para obter mais informações sobre como gerenciar a identidade de cargas de trabalho em um cluster, consulte [práticas recomendadas para autenticação e autorização no AKs][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
