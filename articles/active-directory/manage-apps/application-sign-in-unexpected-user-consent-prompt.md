---
title: Pedido de consentimento inesperado ao iniciar sessão a uma aplicação | Documentos da Microsoft
description: Como resolver problemas quando um utilizador vê uma solicitação de consentimento para uma aplicação que tiver integrado com o Azure AD que não era esperado
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65781152"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Pedido de consentimento inesperado ao iniciar sessão a uma aplicação

Muitos aplicativos que se integram com o Azure Active Directory requerem permissões a vários recursos para ser executado. Quando esses recursos também estão integrados no Azure Active Directory, permissões para aceder aos mesmos é solicitado a usando a estrutura de consentimento do Azure AD. 

Isso resulta numa ser do prompt de consentimento mostrado na primeira vez que um aplicativo é usado, que é muitas vezes uma operação única. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Pedidos de consentimento de cenários em que os utilizadores veem

Os pedidos adicionais podem ser esperados em vários cenários:

* O conjunto de permissões exigidas pela aplicação foi alterado.

* O utilizador que consentiu originalmente para a aplicação não era administrador e, agora um usuário diferente (não-administrador) está a utilizar a aplicação pela primeira vez.

* O utilizador que consentiu originalmente para o aplicativo era administrador, mas não consentiu em nome de toda a organização.

* O aplicativo está usando [consentimento de incremental e dinâmico](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) para solicitar permissões adicionais após a autorização ser concedida inicialmente. Isto é frequentemente utilizado quando funcionalidades opcionais de um aplicativo adicional requerem permissões para além daquelas necessárias para a funcionalidade de linha de base.

* Consentimento foi revogado após ser concedido inicialmente.

* O desenvolvedor tenha configurado o aplicativo exigir um pedido de consentimento, sempre que é utilizada (Nota: não é recomendado).

## <a name="next-steps"></a>Passos Seguintes

-   [Aplicações, permissões e consentimento no Azure Active Directory (v1.0 ponto final)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Âmbitos, permissões e consentimento no Azure Active Directory (ponto final v2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


