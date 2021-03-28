---
title: Redefinir as credenciais para um cluster
titleSuffix: Azure Kubernetes Service
description: Saiba como atualizar ou redefinir as credenciais de serviço principal ou AAD para um cluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 91d40a5a738737098b0941bf3e41e4edd515e6df
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640471"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Atualizar ou rodar as credenciais para o Serviço Azure Kubernetes (AKS)

Os clusters AKS criados com um diretor de serviço têm um prazo de validade de um ano. À medida que se aproxima da data de validade, pode redefinir as credenciais para prolongar o principal de serviço por um período adicional de tempo. Também pode querer atualizar, ou rodar, as credenciais como parte de uma política de segurança definida. Este artigo detalha como atualizar estas credenciais para um cluster AKS.

Pode também ter [integrado o seu cluster AKS com o Azure Ative Directory][aad-integration]e usá-lo como fornecedor de autenticação para o seu cluster. Nesse caso, terá mais duas identidades criadas para o seu cluster, a App AAD Server e a App cliente AAD, podendo também redefinir essas credenciais.

Em alternativa, pode utilizar uma identidade gerida para permissões em vez de um principal de serviço. As identidades geridas são mais fáceis de gerir do que os principais de serviço e não requerem atualizações ou rotações. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.65 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Atualize ou crie um novo diretor de serviço para o seu cluster AKS

Quando pretende atualizar as credenciais de um cluster AKS, pode optar por:

* Atualize as credenciais para o principal de serviço existente.
* Crie um novo diretor de serviço e atualize o cluster para usar estas novas credenciais. 

> [!WARNING]
> Se optar por criar um *novo* diretor de serviço, atualizar um grande cluster AKS para usar estas credenciais pode demorar muito tempo a ser concluído.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Verifique a data de validade do seu serviço principal

Para verificar a data de validade do seu principal serviço, utilize o comando da lista de [credenciais az ad spdential.][az-ad-sp-credential-list] O exemplo a seguir obtém o ID principal de serviço para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup* usando o comando [az aks show.][az-aks-show] O ID principal de serviço é definido como uma variável chamada *SP_ID* para uso com o comando [da lista de credenciais ad sp.][az-ad-sp-credential-list]

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Repor a credencial principal de serviço existente

Para atualizar as credenciais para o principal serviço existente, obtenha o iD principal de serviço do seu cluster usando o comando [az aks show.][az-aks-show] O exemplo a seguir obtém o ID para o cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup.* O ID principal de serviço é definido como uma variável chamada *SP_ID* para uso em comando adicional. Estes comandos usam sintaxe Bash.

> [!WARNING]
> Quando repõe as suas credenciais de cluster num cluster AKS que utiliza conjuntos de escala de máquina virtual Azure, é realizada uma [atualização de imagem de nó][node-image-upgrade] para atualizar os seus nós com as novas informações credenciais.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Com um conjunto variável que contém o ID principal de serviço, agora repõe as credenciais usando [a az ad sp credencial reset][az-ad-sp-credential-reset]. O exemplo seguinte permite que a plataforma Azure gere um novo segredo seguro para o diretor de serviço. Este novo segredo seguro também é armazenado como uma variável.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora continue a [atualizar o cluster AKS com novas credenciais principais de serviço.](#update-aks-cluster-with-new-service-principal-credentials) Este passo é necessário para que as alterações principais do serviço reflitam sobre o cluster AKS.

### <a name="create-a-new-service-principal"></a>Criar um novo diretor de serviços

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

> [!IMPORTANT]
> Para grandes clusters, a atualização do cluster AKS com um novo diretor de serviço pode demorar muito tempo a ser concluída.

Independentemente de ter optado por atualizar as credenciais para o principal de serviço existente ou criar um principal de serviço, agora atualiza o cluster AKS com as suas novas credenciais utilizando o comando [de ações az aks.][az-aks-update-credentials] As variáveis para o *---serviço-principal* e *--cliente-segredo* são usados:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Para agrupamentos de pequena e média dimensão, são necessários alguns momentos para que as principais credenciais do serviço sejam atualizadas na AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Atualizar cluster AKS com novas credenciais de aplicação AAD

Pode criar novas aplicações AAD Server e Client seguindo os [passos de integração da AAD.][create-aad-app] Ou reinicie as aplicações AAD existentes seguindo o [mesmo método que para o reset principal de serviço](#reset-the-existing-service-principal-credential). Depois disso, basta atualizar as credenciais de aplicação do cluster AAD utilizando o mesmo comando [de credenciais de atualização az aks,][az-aks-update-credentials] mas usando as variáveis *--reset-aad.*

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

Neste artigo, o principal de serviço do cluster AKS em si e as Aplicações de Integração da AAD foram atualizados. Para obter mais informações sobre como gerir a identidade para cargas de trabalho dentro de um cluster, consulte [as melhores práticas de autenticação e autorização em AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
