---
title: Problemas conhecidos do navegador Safari (MSAL.js) / Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre problemas ao utilizar a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com o navegador Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696117"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Questões conhecidas no navegador Safari com MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação silenciosa de token no Safari 12 e ITP 2.0

Os sistemas operativos Apple iOS 12 e MacOS 10.14 incluíram um lançamento do [navegador Safari 12.](https://developer.apple.com/safari/whats-new/) Para efeitos de segurança e privacidade, o Safari 12 inclui a [Prevenção inteligente de rastreio 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Isto essencialmente faz com que o navegador deixe cair cookies de terceiros sendo definidos. ItP 2.0 também trata os cookies definidos pelos fornecedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto em MSAL.js

MSAL.js usa um Iframe escondido para realizar aquisição e `acquireTokenSilent` renovação silenciosa como parte das chamadas. Os pedidos de ficha silenciosa dependem do Iframe ter acesso à autenticada sessão de utilizadores representada pelos cookies definidos pela Azure AD. Com a ITP 2.0 a impedir o acesso a estes cookies, a MSAL.js `acquireTokenSilent` não adquire e renova silenciosamente tokens e isso resulta em falhas.

Não há nenhuma solução para esta questão neste momento e estamos a avaliar opções com a comunidade de normas.

### <a name="work-around"></a>Trabalhar ao redor

Por defeito, a definição itp está ativada no navegador Safari. Pode desativar esta definição navegando para a**Privacidade** **das Preferências** -> e desverificando a opção de rastreio **do cross-site.**

![configuração de safari](./media/msal-js-known-issue-safari-browser/safari.png)

Terá de lidar `acquireTokenSilent` com as falhas com uma chamada simbólica de aquisição interativa, o que leva o utilizador a iniciar sessão.
Para evitar inscrições repetidas, uma abordagem que `acquireTokenSilent` pode implementar é lidar com a falha e fornecer ao utilizador uma opção para desativar a definição de ITP no Safari antes de prosseguir com a chamada interativa. Uma vez desativada a definição, as renovações subsequentes de token silenciosos devem ter sucesso.
