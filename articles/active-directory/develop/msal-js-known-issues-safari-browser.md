---
title: Problemas em browsers (biblioteca de autenticação da Microsoft para JavaScript) conhecidos | Azure
description: Saiba mais sobre saiba que esses problemas quando utiliza a biblioteca de autenticação da Microsoft para JavaScript (msal) com o Safari browser.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873889"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemas conhecidos no browser Safari com msal 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação automática de token em 12 de Safari e ITP 2.0

Apple iOS 12 e sistemas de operativos MacOS 10.14 incluída uma versão dos [browser Safari 12](https://developer.apple.com/safari/whats-new/). Para fins de segurança e privacidade, Safari 12 inclui a [2.0 de prevenção de controlo inteligente](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Isso basicamente faz com que o browser eliminar cookies de terceiros que está a ser definidos. ITP 2.0 também trata os cookies definidos por fornecedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto na msal

Msal usa um Iframe oculto para executar e renovação de aquisição do token silenciosa como parte do `acquireTokenSilent` chamadas. Os pedidos de token silenciosas contam com a Iframe ter acesso à sessão do utilizador autenticado, representada por cookies definir pelo Azure AD. Com a 2.0 ITP impedir acesso a estes cookies, msal não conseguir adquirir e renovar tokens silenciosamente e isso resulta em `acquireTokenSilent` falhas.

Existe uma solução para este problema o mais neste momento, e estamos a avaliar opções com a Comunidade de padrões.

### <a name="work-around"></a>Contornar

Por predefinição, a definição de ITP está ativada no Safari browser. Pode desativar esta definição, navegando até **preferências** -> **privacidade** e desmarcar a opção a **impedir que o controlo de sites** opção.

![definição do Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Terá que tratar o `acquireTokenSilent` falhas com interativo adquirir a chamada de token, que pede ao utilizador para iniciar sessão.
Para evitar inícios de sessão repetidos, uma abordagem que pode implementar é lidar com o `acquireTokenSilent` falha e fornecer ao usuário uma opção para desativar a definição de ITP no Safari antes de continuar com a chamada interativa. Assim que a definição estiver desativada, renovações subseqüentes de token silenciosas devem ter êxito.
