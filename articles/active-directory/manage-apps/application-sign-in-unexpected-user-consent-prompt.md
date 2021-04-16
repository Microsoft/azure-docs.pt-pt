---
title: Pedido de consentimento inesperado ao iniciar sessão num | de pedido Microsoft Docs
description: Como resolver problemas quando um utilizador vê um pedido de consentimento para uma aplicação que integrou com a AD Azure que não esperava
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378063"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Pedido de consentimento inesperado ao iniciar sessão de candidatura

Muitas aplicações que se integram com o Azure Ative Directory requerem permissões a vários recursos para poder executar. Quando estes recursos também são integrados com o Azure Ative Directory, as permissões de acesso aos mesmos são solicitadas utilizando o quadro de consentimento Azure AD. 

Isto resulta numa indicação de consentimento da primeira vez que um pedido é usado, que é frequentemente uma operação única. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Cenários em que os utilizadores vêem pedidos de consentimento

Podem ser esperadas solicitações adicionais em vários cenários:

* O conjunto de permissões exigidas pela aplicação mudou.

* O utilizador que inicialmente consentiu na aplicação não era um administrador, e agora um utilizador diferente (não administrador) está a usar a aplicação pela primeira vez.

* O utilizador que inicialmente consentiu com o pedido era um administrador, mas não consentiu em nome de toda a organização.

* O pedido está a usar [o consentimento incremental e dinâmico](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) para solicitar permissões adicionais após o consentimento ter sido inicialmente concedido. Isto é frequentemente usado quando as funcionalidades opcionais de uma aplicação adicional requerem permissões para além das necessárias para a funcionalidade de base.

* O consentimento foi revogado depois de ter sido concedido inicialmente.

* O desenvolvedor configura o pedido para exigir um pedido de consentimento sempre que é usado (nota: esta não é a melhor prática).

## <a name="next-steps"></a>Passos seguintes

-   [Aplicativos, permissões e consentimento no Azure Ative Directory (v1.0 endpoint)](../develop/quickstart-register-app.md)

-   [Âmbitos, permissões e consentimento no Diretório Ativo do Azure (v2.0 ponto final)](../develop/v2-permissions-and-consent.md)
