---
title: Conhecidos problemas de navegador safari (MSAL.js) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba sobre problemas de know quando utilizar a Biblioteca de Autenticação do Microsoft para JavaScript (MSAL.js) com o navegador Safari.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76696117"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemas conhecidos no navegador Safari com MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação silenciosa do Token no Safari 12 e ITP 2.0

Os sistemas operativos Apple iOS 12 e MacOS 10.14 incluíam um lançamento do [navegador Safari 12](https://developer.apple.com/safari/whats-new/). Para fins de segurança e privacidade, o Safari 12 inclui a [Prevenção inteligente de rastreio 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Isto essencialmente faz com que o navegador deixe cair cookies de terceiros sendo definidos. O ITP 2.0 também trata os cookies definidos pelos fornecedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto na MSAL.js

MSAL.js usa um Iframe oculto para realizar a aquisição e renovação de token silencioso como parte das `acquireTokenSilent` chamadas. Os pedidos de token silencioso dependem do Iframe ter acesso à sessão de utilizador autenticada representada pelos cookies definidos pela Azure AD. Com o ITP 2.0 a impedir o acesso a estes cookies, MSAL.js não consegue adquirir e renovar silenciosamente tokens e isso resulta em `acquireTokenSilent` falhas.

Não há solução para esta questão neste momento e estamos a avaliar opções com a comunidade de padrões.

### <a name="work-around"></a>Trabalhar ao redor

Por predefinição, a definição DE ITP está ativada no navegador Safari. Pode desativar esta definição navegando para a Privacidade **das Preferências**e  ->  **Privacy** desmarcando a opção **de rastreio do site Prevent.**

![configuração de safari](./media/msal-js-known-issue-safari-browser/safari.png)

Terá de lidar com as `acquireTokenSilent` falhas com uma chamada de ficha de aquisição interativa, o que leva o utilizador a iniciar sômposições.
Para evitar repetidos insus máximos, uma abordagem que pode implementar é lidar com a `acquireTokenSilent` falha e fornecer ao utilizador a opção de desativar a definição de ITP no Safari antes de prosseguir com a chamada interativa. Uma vez desativada a regulação, as renovações de token silenciosa subsequentes devem ter sucesso.
