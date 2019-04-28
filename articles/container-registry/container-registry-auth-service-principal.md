---
title: Autenticação de registo de contentor do Azure com um principal de serviço
description: Fornece acesso a imagens no seu registo de contentor privado, utilizando um principal de serviço do Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347086"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação de registo de contentor do Azure com os principais de serviço

Pode utilizar um principal de serviço do Azure Active Directory (Azure AD) para fornecer a imagem de contentor `docker push` e `pull` acesso ao seu registo de contentor. Ao utilizar um principal de serviço, pode fornecer acesso a aplicações e serviços "sem interface".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

O Azure AD *principais de serviço* fornecer acesso aos recursos do Azure na sua subscrição. Pode considerar um serviço principal como uma identidade de utilizador para um serviço, em que o "serviço" é qualquer aplicativo, o serviço ou a plataforma que precisa de acesso aos recursos. Pode configurar um principal de serviço com direitos de acesso no âmbito apenas a esses recursos que especificar. Em seguida, configure a sua aplicação ou serviço para utilizar credenciais do principal de serviço para aceder a esses recursos.

No contexto do registo de contentor do Azure, pode criar um Azure AD principal de serviço com a solicitação, push e pull ou outras permissões para o seu registo privado no Azure. Para obter uma lista completa, consulte [permissões e funções do Azure Container Registry](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar um principal de serviço?

Ao utilizar um principal de serviço do Azure AD, pode fornecer acesso de âmbito para o seu registo de contentor privado. Pode criar principais de serviço diferentes para cada um dos seus aplicativos ou serviços, cada um com direitos de acesso sob medida para o seu registo. Além disso, uma vez que pode evitar o compartilhamento de credenciais entre serviços e aplicações, pode alternar credenciais ou revogar o acesso para apenas o principal de serviço (e, portanto, o aplicativo) que escolher.

Por exemplo, seu aplicativo web pode utilizar um principal de serviço que fornece-lhe a imagem `pull` aceder apenas, enquanto o sistema de compilação pode utilizar um principal de serviço que fornece-lhe ambos `push` e `pull` acesso. Se o desenvolvimento da sua aplicação for alterado mãos, pode girar suas credenciais de principal de serviço sem afetar o sistema de compilação.

## <a name="when-to-use-a-service-principal"></a>Quando utilizar um principal de serviço

Deve utilizar um principal de serviço para fornecer acesso ao registo no **cenários sem periféricos**. Ou seja, qualquer aplicação, serviço ou script que tem push ou pull contentor de imagens de forma autónoma automatizada ou de outra forma.

Para obter acesso individual para um registo, por exemplo, quando extrair manualmente uma imagem de contentor para a estação de trabalho de desenvolvimento, deve usar sua própria [identidade do Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) para acesso ao Registro (por exemplo, com [az acr início de sessão][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Scripts de exemplo

Pode encontrar os scripts de exemplo anterior para a CLI do Azure no GitHub, como versões bem para o Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos Seguintes

Assim que tiver um principal de serviço que tenha concedido acesso ao seu registo de contentor, pode utilizar as respetivas credenciais em seus aplicativos e serviços para interação de registo sem periféricos. Pode usar as credenciais do principal de serviço a partir de qualquer serviço do Azure que podem autenticar com um Azure container registry. Os exemplos incluem:

* [Autenticar com o registo de contentores do Azure de serviço Kubernetes do Azure (AKS)](container-registry-auth-aks.md)
* [Autenticar com o registo de contentores do Azure de Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
