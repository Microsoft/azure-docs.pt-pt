---
title: Pedido de consentimento inesperado ao iniciar sessão de candidatura Microsoft Docs
description: Como resolver problemas quando um utilizador vê um pedido de consentimento para uma aplicação que integrou com a AD Azure que não esperava
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9559e22a8396de93bbe46f20b76855d9ff00f753
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763725"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Pedido de consentimento inesperado ao iniciar sessão de candidatura

Muitas aplicações que se integram com o Azure Ative Directory requerem permissões a vários recursos para poder executar. Quando estes recursos também são integrados com o Azure Ative Directory, as permissões de acesso aos mesmos são solicitadas utilizando o quadro de consentimento Azure AD. 

Isto resulta numa indicação de consentimento da primeira vez que um pedido é usado, que é frequentemente uma operação única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Cenários em que os utilizadores vêem pedidos de consentimento

Podem ser esperadas solicitações adicionais em vários cenários:

* O conjunto de permissões exigidas pela aplicação mudou.

* O utilizador que inicialmente consentiu na aplicação não era um administrador, e agora um utilizador diferente (não administrador) está a usar a aplicação pela primeira vez.

* O utilizador que inicialmente consentiu com o pedido era um administrador, mas não consentiu em nome de toda a organização.

* O pedido está a usar [o consentimento incremental e dinâmico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permissões adicionais após o consentimento ter sido inicialmente concedido. Isto é frequentemente usado quando as funcionalidades opcionais de uma aplicação adicional requerem permissões para além das necessárias para a funcionalidade de base.

* O consentimento foi revogado depois de ter sido concedido inicialmente.

* O desenvolvedor configura o pedido para exigir um pedido de consentimento sempre que é usado (nota: esta não é a melhor prática).

## <a name="next-steps"></a>Passos seguintes

-   [Aplicativos, permissões e consentimento no Azure Ative Directory (v1.0 endpoint)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Âmbitos, permissões e consentimento no Diretório Ativo do Azure (v2.0 ponto final)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


