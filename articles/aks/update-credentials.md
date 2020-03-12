---
title: Redefinir as credenciais para um cluster azure Kubernetes Service (AKS)
description: Saiba como atualizar ou redefinir as credenciais do diretor de serviço ou da aplicação AAD para um cluster do Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096099"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Atualizar ou rodar as credenciais para o Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS são criados com um diretor de serviço que tem um prazo de validade de um ano. À medida que se aproxima da data de validade, pode redefinir as credenciais para estender o diretor de serviço por um período adicional de tempo. Também pode querer atualizar, ou rodar, as credenciais como parte de uma política de segurança definida. Este artigo detalha como atualizar estas credenciais para um cluster AKS.

Também pode ter integrado o seu cluster AKS com o [Azure Ative Directory,][aad-integration]e usá-lo como fornecedor de autenticação para o seu cluster. Nesse caso, terá mais 2 identidades criadas para o seu cluster, a App AAD Server e a App de Clientes AAD, podendo também redefinir essas credenciais. 

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.65 ou posteriormente instalada e configurada. Execute `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Atualize ou crie um novo Diretor de Serviço para o seu cluster AKS

Quando pretender atualizar as credenciais para um cluster AKS, pode optar por:

* atualizar as credenciais para o principal de serviço existente utilizado pelo cluster, ou
* criar um diretor de serviço e atualizar o cluster para usar estas novas credenciais.

### <a name="reset-existing-service-principal-credential"></a>Redefinir a credencial principal do serviço existente

Para atualizar as credenciais para o principal de serviço existente, obtenha o principal de serviço do seu cluster usando o comando [de show az aks.][az-aks-show] O exemplo seguinte obtém o ID para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup.* O ID principal do serviço é definido como uma variável chamada *SP_ID* para uso em comando adicional.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto variável que contém o ID principal do serviço, agora redefinir as credenciais usando [reset credencial az ad sp][az-ad-sp-credential-reset]. O exemplo que se segue permite à plataforma Azure gerar um novo segredo seguro para o diretor de serviço. Este novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora continue a atualizar o [cluster AKS com novas credenciais principais](#update-aks-cluster-with-new-service-principal-credentials)de serviço. Este passo é necessário para que as alterações do Diretor de Serviço reflitam sobre o cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar um novo diretor de serviço

Se optou por atualizar as credenciais principais do serviço existentes na secção anterior, ignore este passo. Continue a atualizar o [cluster AKS com novas credenciais principais](#update-aks-cluster-with-new-service-principal-credentials)de serviço.

Para criar um diretor de serviço e, em seguida, atualizar o cluster AKS para usar estas novas credenciais, use o comando [az ad sp create-for-rbac.][az-ad-sp-create] No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Estes valores são usados no próximo passo.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina variáveis para o id principal de serviço e segredo do cliente usando a saída do seu próprio comando [az ad sp create-for-rbac,][az-ad-sp-create] como mostra o seguinte exemplo. O *SP_ID* é o seu *appId*, e o *SP_SECRET* é a sua *palavra-passe:*

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Agora continue a atualizar o [cluster AKS com novas credenciais principais](#update-aks-cluster-with-new-service-principal-credentials)de serviço. Este passo é necessário para que as alterações do Diretor de Serviço reflitam sobre o cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Atualizar cluster AKS com novas credenciais principais de serviço

Independentemente de ter optado por atualizar as credenciais para o principal de serviço existente ou criar um diretor de serviço, atualize agora o cluster AKS com as suas novas credenciais utilizando o comando de [credenciais de atualização az aks.][az-aks-update-credentials] As variáveis para o *--serviço-principal* e *--segredo do cliente* são usadas:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Leva alguns momentos para que as credenciais principais do serviço sejam atualizadas no AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Atualizar cluster AKS com novas credenciais de aplicação AAD

Pode criar novas aplicações aAD Server e Client seguindo os passos de [integração do AAD.][create-aad-app] Ou redefinir as aplicações AAD existentes seguindo o mesmo método que para o [reset principal](#reset-existing-service-principal-credential)do serviço . Depois disso, basta atualizar as credenciais de aplicação AAD do seu cluster utilizando o mesmo comando de [credenciais de atualização az aks,][az-aks-update-credentials] mas utilizando as variáveis *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Passos seguintes

Neste artigo, o principal de serviço do próprio cluster AKS e das Aplicações de Integração AAD foi atualizado. Para obter mais informações sobre como gerir a identidade para cargas de trabalho dentro de um cluster, consulte [as melhores práticas de autenticação e autorização no AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
