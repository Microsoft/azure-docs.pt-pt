---
title: Autenticar entre inquilinos
description: Descreve como o Azure Resource Manager trata as solicitações de autenticação entre locatários.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75478828"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticar solicitações entre locatários

Ao criar um aplicativo multilocatário, talvez seja necessário manipular solicitações de autenticação para recursos que estão em locatários diferentes. Um cenário comum é quando uma máquina virtual em um locatário deve ingressar em uma rede virtual em outro locatário. Azure Resource Manager fornece um valor de cabeçalho para armazenar tokens auxiliares para autenticar as solicitações para diferentes locatários.

## <a name="header-values-for-authentication"></a>Valores de cabeçalho para autenticação

A solicitação tem os seguintes valores de cabeçalho de autenticação:

| Nome do cabeçalho | Descrição | Valor de exemplo |
| ----------- | ----------- | ------------ |
| Autorização | Token primário | Portador &lt;token primário&gt; |
| x-ms-authorization-auxiliary | Tokens auxiliares | Portador &lt;auxiliares-token1&gt;, EncryptedBearer &lt;Auxiliary-token2&gt;, portador &lt;auxiliar-token3&gt; |

O cabeçalho auxiliar pode conter até três tokens auxiliares. 

No código do aplicativo multilocatário, obtenha o token de autenticação para outros locatários e armazene-os nos cabeçalhos auxiliares. Todos os tokens devem ser do mesmo usuário ou aplicativo. O usuário ou aplicativo deve ter sido convidado como convidado para os outros locatários.

## <a name="processing-the-request"></a>Processando a solicitação

Quando seu aplicativo envia uma solicitação ao Gerenciador de recursos, a solicitação é executada sob a identidade do token primário. O token primário deve ser válido e não expirado. Esse token deve ser de um locatário que possa gerenciar a assinatura.

Quando a solicitação faz referência a um recurso de um locatário diferente, o Gerenciador de recursos verifica os tokens auxiliares para determinar se a solicitação pode ser processada. Todos os tokens auxiliares no cabeçalho devem ser válidos e não expirados. Se qualquer token tiver expirado, o Resource Manager retornará um código de resposta 401. A resposta inclui a ID do cliente e a ID do locatário do token que não é válido. Se o cabeçalho auxiliar contiver um token válido para o locatário, a solicitação de locatário cruzado será processada.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre solicitações de autenticação, consulte [fluxos de autenticação e cenários de aplicativos](../../active-directory/develop/authentication-flows-app-scenarios.md).
* Para obter mais informações sobre tokens, consulte [Azure Active Directory tokens de acesso](../../active-directory/develop/access-tokens.md).
