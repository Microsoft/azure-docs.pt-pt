---
title: Pedido de consentimento inesperado ao iniciar sessão com um requerimento Microsoft Docs
description: Como resolver problemas quando um utilizador vê um pedido de consentimento para uma aplicação que integrou com a Azure AD que não esperava
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "65781152"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Pedido de consentimento inesperado ao iniciar sessão num pedido

Muitas aplicações que se integram com o Diretório Ativo azure requerem permissões a vários recursos para executar. Quando estes recursos também são integrados com o Diretório Ativo Azure, são solicitadas permissões para aceder aos mesmos utilizando o quadro de consentimento da AD Azure. 

Isto resulta num pedido de consentimento que é mostrado na primeira vez que um pedido é usado, o que é frequentemente uma operação única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Cenários em que os utilizadores vêem pedidos de consentimento

Podem ser esperadas solicitações adicionais em vários cenários:

* O conjunto de permissões exigidas pelo pedido mudou.

* O utilizador que inicialmente consentiu na aplicação não era administrador, e agora um utilizador diferente (não administrador) está a utilizar a aplicação pela primeira vez.

* O utilizador que inicialmente consentiu no pedido era um administrador, mas não consentiu em nome de toda a organização.

* O pedido está a usar [consentimento incremental e dinâmico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permissões adicionais após a concessão inicial do consentimento. Isto é frequentemente usado quando as funcionalidades opcionais de uma aplicação adicionais requerem permissões para além das necessárias para a funcionalidade de base.

* O consentimento foi revogado depois de ter sido concedido inicialmente.

* O desenvolvedor configurou a aplicação para exigir um pedido de consentimento sempre que é utilizada (nota: esta não é a melhor prática).

## <a name="next-steps"></a>Passos seguintes

-   [Apps, permissões e consentimento no Diretório Ativo azure (v1.0 endpoint)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Âmbitos, permissões e consentimento no Diretório Ativo Azure (v2.0 endpoint)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


