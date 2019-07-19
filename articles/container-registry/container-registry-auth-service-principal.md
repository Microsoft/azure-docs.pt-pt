---
title: Autenticação do registro de contêiner do Azure com uma entidade de serviço
description: Forneça acesso a imagens em seu registro de contêiner privado usando uma entidade de serviço Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309951"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do registro de contêiner do Azure com entidades de serviço

Você pode usar uma entidade de serviço Azure Active Directory (AD do Azure) para fornecer `docker push` a `pull` imagem de contêiner e o acesso ao registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a aplicativos e serviços "sem periféricos".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

As *entidades de serviço* do Azure ad fornecem acesso aos recursos do Azure em sua assinatura. Você pode considerar uma entidade de serviço como uma identidade de usuário para um serviço, em que "serviço" é qualquer aplicativo, serviço ou plataforma que precise de acesso aos recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas para os recursos que você especificar. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do registro de contêiner do Azure, você pode criar uma entidade de serviço do Azure AD com pull, push e pull ou outras permissões para seu registro particular no Azure. Para obter uma lista completa, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner privado. Você pode criar entidades de serviço diferentes para cada um dos seus aplicativos ou serviços, cada um com direitos de acesso adaptados ao registro. E, como você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar credenciais ou revogar o acesso apenas para a entidade de serviço (e, portanto, o aplicativo) escolhido.

Por exemplo, seu aplicativo Web pode usar uma entidade de serviço que fornece somente acesso `pull` a imagens, enquanto o sistema de compilação pode usar uma entidade de serviço que fornece o `push` e `pull` o acesso. Se o desenvolvimento do seu aplicativo mudar de mãos, você poderá girar suas credenciais de princípio de serviço sem afetar o sistema de compilação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer acesso ao registro em **cenários**sem periféricos. Ou seja, qualquer aplicativo, serviço ou script que deva enviar por Push ou extrair imagens de contêiner de maneira automatizada ou autônoma.

Para acesso individual a um registro, como quando você efetua pull manualmente de uma imagem de contêiner para sua estação de trabalho de desenvolvimento, você deve usar sua própria [identidade do Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) para acesso ao registro (por exemplo, com [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para CLI do Azure no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos seguintes

Depois que você tiver uma entidade de serviço que concedeu acesso ao registro de contêiner, poderá usar suas credenciais em seus aplicativos e serviços para interação com o registro sem periféricos. Você pode usar as credenciais da entidade de serviço de qualquer serviço do Azure que possa autenticar com um registro de contêiner do Azure. Os exemplos incluem:

* [Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure (AKS)](container-registry-auth-aks.md)
* [Autenticar com o registro de contêiner do Azure de ACI (instâncias de contêiner do Azure)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
