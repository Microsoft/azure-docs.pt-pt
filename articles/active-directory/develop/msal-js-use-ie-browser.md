---
title: Usar o Internet Explorer com MSAL. js | Azure
titleSuffix: Microsoft identity platform
description: Use a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com o navegador Internet Explorer.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 715e92a10ca0b4cbe38119931bc66d36c3b9259a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916286"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Usar os navegadores do Internet Explorer e do Microsoft Edge com o MSAL. js

A biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) é gerada para o [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) para que ele possa ser executado no Internet Explorer. No entanto, há algumas coisas que você precisa saber.

## <a name="run-an-app-in-internet-explorer"></a>Executar um aplicativo no Internet Explorer
Se você pretende usar MSAL. js em aplicativos que podem ser executados no Internet Explorer, será necessário adicionar uma referência a um antifill de promessa antes de fazer referência ao script MSAL. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Isso ocorre porque o Internet Explorer não dá suporte às promessas do JavaScript nativamente.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Depurando um aplicativo em execução no Internet Explorer

### <a name="running-in-production"></a>Em execução na produção
Implantar seu aplicativo para produção (por exemplo, em aplicativos Web do Azure) normalmente funciona bem, desde que o usuário final tenha aceitado os pop-ups. Testamos isso com o Internet Explorer 11.

### <a name="running-locally"></a>Executando localmente
Se você quiser executar e depurar localmente seu aplicativo em execução no Internet Explorer, você precisa estar ciente das seguintes considerações (Suponha que você deseja executar seu aplicativo como *http://localhost:1234* ):

- O Internet Explorer tem um mecanismo de segurança chamado "modo protegido", que impede que o MSAL. js funcione corretamente. Entre os sintomas, depois de entrar, a página pode ser redirecionada para http://localhost:1234/null.

- Para executar e depurar seu aplicativo localmente, você precisará desabilitar esse "modo protegido". Para isso:

    1. Clique em **ferramentas** do Internet Explorer (o ícone de engrenagem).
    1. Selecione **Opções da Internet** e, em seguida, a guia **segurança** .
    1. Clique na zona da **Internet** e desmarque **habilitar modo protegido (requer a reinicialização do Internet Explorer)** . O Internet Explorer avisa que o computador não está mais protegido. Clique em **OK**.
    1. Reinicie o Internet Explorer.
    1. Execute e depure seu aplicativo.

Quando terminar, restaure as configurações de segurança do Internet Explorer.  Selecione **configurações** -> **opções da Internet** -> **segurança** -> **redefinir todas as zonas para o nível padrão**.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [problemas conhecidos ao usar o MSAL. js no Internet Explorer](msal-js-use-ie-browser.md).