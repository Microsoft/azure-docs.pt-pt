---
title: Edições no Internet Explorer (MSAL.js) / Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695862"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Questões conhecidas nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

A Microsoft Authentication Library for JavaScript (MSAL.js) é gerada para [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) para que possa ser executada no Internet Explorer. Há, no entanto, algumas coisas para saber.

## <a name="run-an-app-in-internet-explorer"></a>Executar uma aplicação no Internet Explorer
Se pretender utilizar mSAL.js em aplicações que podem ser executadas no Internet Explorer, terá de adicionar uma referência a uma promessa de polifill antes de se referir ao script MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Isto porque o Internet Explorer não suporta as promessas javaScript de forma nativa.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Depurando uma aplicação em execução no Internet Explorer

### <a name="running-in-production"></a>Funcionamento em produção
A implementação da sua aplicação para produção (por exemplo, em aplicações Azure Web) normalmente funciona bem, desde que o utilizador final tenha aceitado popups. Testámo-lo com o Internet Explorer 11.

### <a name="running-locally"></a>Correndo localmente
Se quiser executar e depurar localmente a sua aplicação em execução no Internet Explorer, tem *http://localhost:1234*de estar ciente das seguintes considerações (assuma que pretende executar a sua aplicação como ):

- O Internet Explorer tem um mecanismo de segurança chamado "modo protegido", que impede o MSAL.js de funcionar corretamente. Entre os sintomas, depois de iniciar sessão, http://localhost:1234/nulla página pode ser redirecionada para .

- Para executar e depurar a sua aplicação localmente, terá de desativar este "modo protegido". Para isto:

    1. Clique em **Ferramentas** de Explorador de Internet (o ícone da engrenagem).
    1. Selecione **Opções** de Internet e, em seguida, o separador **Segurança.**
    1. Clique na zona da **Internet** e **desverifique o modo protected Enable (requer reiniciar o Internet Explorer)**. O Internet Explorer avisa que o seu computador já não está protegido. Clique em **OK**.
    1. Reiniciar o Internet Explorer.
    1. Executar e depurar a sua aplicação.

Quando terminar, restaure as definições de segurança do Internet Explorer.  Selecione **Definições** -> **Opções** -> de Internet**Segurança** -> **Redefinir todas as zonas para o nível padrão**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [questões conhecidas ao utilizar MSAL.js no Internet Explorer](msal-js-use-ie-browser.md).