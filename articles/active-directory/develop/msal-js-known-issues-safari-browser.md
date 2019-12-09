---
title: Problemas conhecidos do navegador Safari (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas conhecidos ao usar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com o navegador Safari.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fda1471d629ab8d84d06a3cc5bcfae0a6d9ec5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916337"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemas conhecidos no navegador Safari com MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Renovação de token silenciosa no Safari 12 e ITP 2,0

Os sistemas operacionais Apple iOS 12 e MacOS 10,14 incluíam uma versão do [navegador Safari 12](https://developer.apple.com/safari/whats-new/). Para fins de segurança e privacidade, o Safari 12 inclui a [prevenção de rastreamento inteligente 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Essencialmente, isso faz com que o navegador descartar os cookies de terceiros que estão sendo definidos. O ITP 2,0 também trata os cookies definidos por provedores de identidade como cookies de terceiros.

### <a name="impact-on-msaljs"></a>Impacto no MSAL. js

O MSAL. js usa um iframe oculto para executar a aquisição e a renovação de tokens silenciosas como parte das chamadas de `acquireTokenSilent`. As solicitações de token silenciosas dependem do iframe que tem acesso à sessão de usuário autenticado representada pelos cookies definidos pelo Azure AD. Com o ITP 2,0 impedindo o acesso a esses cookies, o MSAL. js falha ao adquirir e renovar silenciosamente tokens e isso resulta em falhas de `acquireTokenSilent`.

Não há nenhuma solução para esse problema neste ponto e estamos avaliando opções com a comunidade de padrões.

### <a name="work-around"></a>Solução alternativa

Por padrão, a configuração ITP está habilitada no navegador Safari. Você pode desabilitar essa configuração navegando até **preferências** -> **privacidade** e desmarcando a opção **impedir rastreamento entre sites** .

![configuração do Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Você precisará manipular as falhas de `acquireTokenSilent` com uma chamada de token de aquisição interativa, que solicita que o usuário entre.
Para evitar entradas repetidas, uma abordagem que você pode implementar é manipular a falha de `acquireTokenSilent` e fornecer ao usuário uma opção para desabilitar a configuração ITP no Safari antes de prosseguir com a chamada interativa. Quando a configuração estiver desabilitada, as renovações de token silencioso subsequentes deverão ser realizadas com sucesso.
