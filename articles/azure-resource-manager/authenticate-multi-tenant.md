---
title: Autenticar entre locatários-Azure Resource Manager
description: Descreve como o Azure Resource Manager trata as solicitações de autenticação entre locatários.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tomfitz
ms.openlocfilehash: b85ed32ac333402caeca4901e4d91bbe4d1d112c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300344"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar solicitações entre locatários

Ao criar um aplicativo multilocatário, talvez seja necessário manipular solicitações de autenticação para recursos que estão em locatários diferentes. Um cenário comum é quando uma máquina virtual em um locatário deve ingressar em uma rede virtual em outro locatário. Azure Resource Manager fornece um valor de cabeçalho para armazenar tokens auxiliares para autenticar as solicitações para diferentes locatários.

## <a name="header-values-for-authentication"></a>Valores de cabeçalho para autenticação

A solicitação tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Token primário | Portador &lt;primary-token @ no__t-1 |
| x-MS-Authorization-auxiliar | Tokens auxiliares | Portador &lt;auxiliary-token1 @ no__t-1, EncryptedBearer &lt;auxiliary-token2 @ no__t-3, portador &lt;auxiliary-token3 @ no__t-5 |

O cabeçalho auxiliar pode conter até três tokens auxiliares. 

No código do aplicativo multilocatário, obtenha o token de autenticação para outros locatários e armazene-os nos cabeçalhos auxiliares. Todos os tokens devem ser do mesmo usuário ou aplicativo. O usuário ou aplicativo deve ter sido convidado como convidado para os outros locatários.

## <a name="processing-the-request"></a>Processando a solicitação

Quando seu aplicativo envia uma solicitação ao Gerenciador de recursos, a solicitação é executada sob a identidade do token primário. O token primário deve ser válido e não expirado. Esse token deve ser de um locatário que possa gerenciar a assinatura.

Quando a solicitação faz referência a um recurso de um locatário diferente, o Gerenciador de recursos verifica os tokens auxiliares para determinar se a solicitação pode ser processada. Todos os tokens auxiliares no cabeçalho devem ser válidos e não expirados. Se qualquer token tiver expirado, o Resource Manager retornará um código de resposta 401. A resposta inclui a ID do cliente e a ID do locatário do token que não é válido. Se o cabeçalho auxiliar contiver um token válido para o locatário, a solicitação de locatário cruzado será processada.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre solicitações de autenticação, consulte [fluxos de autenticação e cenários de aplicativos](../active-directory/develop/authentication-flows-app-scenarios.md).
* Para obter mais informações sobre tokens, consulte [Azure Active Directory tokens de acesso](../active-directory/develop/access-tokens.md).
