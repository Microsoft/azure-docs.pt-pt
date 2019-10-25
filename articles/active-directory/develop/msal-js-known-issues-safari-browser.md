---
title: Problemas conhecidos em navegadores (biblioteca de autenticação da Microsoft para JavaScript)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas conhecidos ao usar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com o navegador Safari.
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
ms.openlocfilehash: 51d800ea2fbbc733a6213d7bc4f61f955612aba0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803078"
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
