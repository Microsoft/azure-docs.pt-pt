---
title: Redefinir as credenciais para um cluster
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar ou redefinir as credenciais de serviço principal ou AAD para um cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 914e043e2c0cf39c18480b5ca5e34332398806f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905379"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Atualizar ou rodar as credenciais para o Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS são criados com um principal de serviço que tem um prazo de validade de um ano. À medida que se aproxima da data de validade, pode redefinir as credenciais para prolongar o principal de serviço por um período adicional de tempo. Também pode querer atualizar, ou rodar, as credenciais como parte de uma política de segurança definida. Este artigo detalha como atualizar estas credenciais para um cluster AKS.

Pode também ter [integrado o seu cluster AKS com o Azure Ative Directory][aad-integration]e usá-lo como fornecedor de autenticação para o seu cluster. Nesse caso, terá mais duas identidades criadas para o seu cluster, a App AAD Server e a App cliente AAD, podendo também redefinir essas credenciais.

Em alternativa, pode utilizar uma identidade gerida para permissões em vez de um principal de serviço. As identidades geridas são mais fáceis de gerir do que os principais de serviço e não requerem atualizações ou rotações. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)

## <a name="before-you-begin"></a>Before you begin

Precisa da versão Azure CLI 2.0.65 ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Atualize ou crie um novo Diretor de Serviço para o seu cluster AKS

Quando pretender atualizar as credenciais de um cluster AKS, pode optar por:

* atualizar as credenciais para o principal de serviço existente utilizado pelo cluster, ou
* criar um chefe de serviço e atualizar o cluster para usar estas novas credenciais.

### <a name="reset-existing-service-principal-credential"></a>Repor a credencial principal do serviço existente

Para atualizar as credenciais para o principal serviço existente, obtenha o iD principal de serviço do seu cluster usando o comando [az aks show.][az-aks-show] O exemplo a seguir obtém o ID para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup.* O ID principal de serviço é definido como uma variável chamada *SP_ID* para uso em comando adicional. Estes comandos usam sintaxe Bash.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto variável que contém o ID principal de serviço, agora repõe as credenciais usando [a az ad sp credencial reset][az-ad-sp-credential-reset]. O exemplo seguinte permite que a plataforma Azure gere um novo segredo seguro para o diretor de serviço. Este novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora continue a [atualizar o cluster AKS com novas credenciais principais de serviço.](#update-aks-cluster-with-new-service-principal-credentials) Este passo é necessário para que as alterações principais do serviço reflitam sobre o cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar um novo diretor de serviço

Se optar por atualizar as credenciais principais do serviço existentes na secção anterior, ignore este passo. Continue a [atualizar o cluster AKS com novas credenciais principais de serviço.](#update-aks-cluster-with-new-service-principal-credentials)

Para criar um principal de serviço e, em seguida, atualizar o cluster AKS para usar estas novas credenciais, use o comando [ad sp create-for-rbac.][az-ad-sp-create] No exemplo a seguir, o parâmetro `--skip-assignment` impede que sejam atribuídas atribuições predefinidas adicionais:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado será semelhante ao seguinte exemplo. Tome nota do seu `appId` e `password`. Estes valores são utilizados no passo seguinte.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina variáveis para o iD principal de serviço e segredo do cliente usando a saída do seu próprio comando [ad sp create-for-rbac,][az-ad-sp-create] como mostra o exemplo seguinte. O *SP_ID* é o seu *appId,* e o *SP_SECRET* é a sua *palavra-passe:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Agora continue a [atualizar o cluster AKS com novas credenciais principais de serviço.](#update-aks-cluster-with-new-service-principal-credentials) Este passo é necessário para que as alterações principais do serviço reflitam sobre o cluster AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Atualizar cluster AKS com novas credenciais principais de serviço

Independentemente de ter optado por atualizar as credenciais para o principal de serviço existente ou criar um principal de serviço, agora atualiza o cluster AKS com as suas novas credenciais utilizando o comando [de ações az aks.][az-aks-update-credentials] As variáveis para o *---serviço-principal* e *--cliente-segredo* são usados:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Leva alguns momentos para que as credenciais principais do serviço sejam atualizadas na AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Atualizar cluster AKS com novas credenciais de aplicação AAD

Pode criar novas aplicações AAD Server e Client seguindo os [passos de integração da AAD.][create-aad-app] Ou reinicie as aplicações AAD existentes seguindo o [mesmo método que para o reset principal de serviço](#reset-existing-service-principal-credential). Depois disso, basta atualizar as credenciais de aplicação do cluster AAD utilizando o mesmo comando [de credenciais de atualização az aks,][az-aks-update-credentials] mas usando as variáveis *--reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Próximos passos

Neste artigo, o principal de serviço do cluster AKS em si e as Aplicações de Integração da AAD foram atualizados. Para obter mais informações sobre como gerir a identidade para cargas de trabalho dentro de um cluster, consulte [as melhores práticas de autenticação e autorização em AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
