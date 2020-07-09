---
title: Problemas no Internet Explorer (MSAL.js) Rio Azure
titleSuffix: Microsoft identity platform
description: Utilize a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com o navegador Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76695862"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemas conhecidos nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

A Microsoft Authentication Library for JavaScript (MSAL.js) é gerada para [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) para que possa ser executada no Internet Explorer. Há, no entanto, algumas coisas para saber.

## <a name="run-an-app-in-internet-explorer"></a>Executar uma aplicação no Internet Explorer
Se pretender utilizar MSAL.js em aplicações que podem ser executadas no Internet Explorer, terá de adicionar uma referência a um polifill de promessa antes de fazer referência ao script MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Isto porque o Internet Explorer não suporta promessas javaScript de forma nativa.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Depurar uma aplicação em execução no Internet Explorer

### <a name="running-in-production"></a>Correndo em produção
A implementação da sua aplicação para a produção (por exemplo, nas aplicações Azure Web) funciona normalmente bem, desde que o utilizador final tenha aceite popups. Testámo-lo com o Internet Explorer 11.

### <a name="running-locally"></a>Correndo localmente
Se quiser executar e depurar localmente a sua aplicação em execução no Internet Explorer, tem de estar atento às seguintes considerações (assuma que pretende executar a sua aplicação *http://localhost:1234* como):

- O Internet Explorer tem um mecanismo de segurança chamado "modo protegido", que impede MSAL.js de funcionar corretamente. Entre os sintomas, depois de iniciar sedutada, a página pode ser redirecionada para http://localhost:1234/null .

- Para executar e depurar a sua aplicação localmente, terá de desativar este "modo protegido". Para isto:

    1. Clique em **Ferramentas** de Internet Explorer (o ícone da engrenagem).
    1. Selecione **Opções de Internet** e, em seguida, o separador **Segurança.**
    1. Clique na zona da **Internet** e **desmarque Ativar o Modo Protegido (requer o reinício do Internet Explorer)**. O Internet Explorer avisa que o seu computador já não está protegido. Clique em **OK**.
    1. Reinicie o Internet Explorer.
    1. Corra e depure a sua candidatura.

Quando terminar, restaure as definições de segurança do Internet Explorer.  Selecione **Definições**  ->  **Opções de Internet**  ->  **Segurança**Redefinir todas  ->  **as zonas para o nível predefinido**.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre [questões conhecidas ao utilizar MSAL.js no Internet Explorer.](msal-js-use-ie-browser.md)