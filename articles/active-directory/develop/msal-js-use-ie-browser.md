---
title: Utilize o Internet Explorer (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba como utilizar a biblioteca de autenticação da Microsoft para JavaScript (msal) com o navegador Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873919"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problemas conhecidos em browsers do Internet Explorer e o Microsoft Edge com msal

Biblioteca de autenticação da Microsoft para JavaScript (msal) é gerada para [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) para que ele pode ser executado no Internet Explorer. No entanto, existem alguns aspetos a saber.

## <a name="run-an-app-in-internet-explorer"></a>Executar uma aplicação no Internet Explorer
Se pretender utilizar a msal em aplicações que podem ser executadas no Internet Explorer, terá de adicionar uma referência a um suporte retroativo de promessa antes de fazer referência o script de msal.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Isto acontece porque o Internet Explorer não suporta nativamente promessas de JavaScript.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Depurar uma aplicação em execução no Internet Explorer

### <a name="running-in-production"></a>Em execução na produção
Implementar a aplicação para a produção (por exemplo, em aplicações Web do Azure) normalmente funciona bem, contanto que o utilizador final aceitou pop-ups. Testamos com o Internet Explorer 11.

### <a name="running-locally"></a>Em execução localmente
Se pretende executar e depurar localmente a aplicação em execução no Internet Explorer, tem de ter em consideração as seguintes considerações (partem do princípio de que pretende executar a aplicação como *http://localhost:1234*):

- Internet Explorer tem um mecanismo de segurança com o nome "modo protegido", o que impede que msal a funcionar corretamente. Entre os sintomas, depois de iniciar sessão, a página pode ser redirecionada para http://localhost:1234/null.

- Para executar e depurar a aplicação localmente, terá de desativar esta "modo protegido". Para isso:

    1. Clique em Internet Explorer **ferramentas** (o ícone de engrenagem).
    1. Selecione **opções da Internet** e, em seguida, o **segurança** separador.
    1. Clique nas **Internet** zona e desmarque **ativar modo protegido (é necessário reiniciar o Internet Explorer)**. Internet Explorer avisa que seu computador já não está protegido. Clique em **OK**.
    1. Reinicie o Internet Explorer.
    1. Executar e depurar seu aplicativo.

Quando tiver terminado, restaure as definições de segurança do Internet Explorer.  Selecione **configurações** -> **opções da Internet** -> **segurança** -> **repor todas as zonas para o nível predefinido**.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [problemas conhecidos ao utilizar a msal no Internet Explorer](msal-js-use-ie-browser.md).