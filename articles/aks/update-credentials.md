---
title: Repor as credenciais para um cluster do Azure Kubernetes Service (AKS)
description: Saiba como atualização ou redefinir o principal de serviço de credenciais para um cluster no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 189bcf2ddc7d301c8100f74e51374abd217a144f
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475496"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Atualizar ou rodar as credenciais para um principal de serviço no Azure Kubernetes Service (AKS)

Por predefinição, os clusters do AKS são criadas com um principal de serviço com um tempo de expiração de um ano. Como perto da data de expiração, pode repor as credenciais para estender o principal de serviço para um período adicional de tempo. Também poderá atualizar ou girar, as credenciais como parte de uma política de segurança definidas. Este artigo fornece detalhes sobre como atualizar estas credenciais para um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.65 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Optar por atualizar ou criar um principal de serviço

Quando quiser atualizar as credenciais de um cluster do AKS, pode optar por:

* atualizar as credenciais do principal de serviço existente utilizadas pelo cluster, ou
* criar um principal de serviço e atualizar o cluster para utilizar estas novas credenciais.

Se pretender criar um principal de serviço e, em seguida, atualize o cluster do AKS, ignore o resto dos passos nesta secção e continuar a [criar um principal de serviço](#create-a-service-principal). Se quiser atualizar as credenciais do principal de serviço existente utilizadas pelo cluster do AKS, continue com os passos nesta secção.

### <a name="get-the-service-principal-id"></a>Obter o ID de principal de serviço

Para atualizar as credenciais para o principal de serviço existente, obtenha o ID de principal de serviço de cluster com o [show do az aks] [ az-aks-show] comando. O exemplo seguinte obtém o ID para o cluster com o nome *myAKSCluster* no *myResourceGroup* grupo de recursos. O ID de principal de serviço está definido como uma variável chamada *SP_ID* para utilização no comando adicional.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Atualizar as credenciais do principal de serviço

Com um conjunto de variável que contém o ID de principal de serviço, agora a repor as credenciais a utilizar [credenciais do az ad sp reposição][az-ad-sp-credential-reset]. O exemplo a seguir permite gerar um novo segredo seguro para o principal de serviço da plataforma do Azure. Este novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora continue para [atualização de cluster do AKS com novas credenciais](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Se optar por atualizar as credenciais de principal de serviço existente na secção anterior, ignore este passo. Continuar a [atualização de cluster do AKS com novas credenciais](#update-aks-cluster-with-new-credentials).

Para criar um principal de serviço e, em seguida, atualize o cluster do AKS para utilizar estas credenciais novas, utilize o [az ad sp create-for-rbac] [ az-ad-sp-create] comando. No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Estes valores são utilizados no próximo passo.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina variáveis para o serviço principal ID e segredo do cliente usa a saída da sua própria [az ad sp create-for-rbac] [ az-ad-sp-create] de comando, conforme mostrado no exemplo a seguir. O *SP_ID* é sua *appId*e o *SP_SECRET* é o *palavra-passe*:

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Atualizar o cluster do AKS com novas credenciais

Independentemente de decidir atualizar as credenciais para o principal de serviço existente ou criar um principal de serviço, agora atualizar o cluster do AKS com as suas novas credenciais com o [az aks-as credenciais de atualização] [ az-aks-update-credentials] comando. As variáveis para o *– principal de serviço* e *– segredo do cliente* são utilizadas:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Demora alguns minutos para as credenciais do principal de serviço ser atualizado do AKS.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, o principal de serviço para o próprio cluster do AKS foi atualizado. Para obter mais informações sobre como gerir identidades para cargas de trabalho dentro de um cluster, consulte [melhores práticas para autenticação e autorização no AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
