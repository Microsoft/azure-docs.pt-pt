---
title: Problemas conhecidos em navegadores (biblioteca de autenticação da Microsoft para JavaScript)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os problemas conhecidos ao usar a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) com os navegadores do Internet Explorer e do Microsoft Edge.
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
ms.openlocfilehash: 0d2de7f8a34b38d377d80d574dd7e52c46286bb9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803046"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problemas conhecidos no Internet Explorer e nos navegadores do Microsoft Edge com MSAL. js

## <a name="issues-due-to-security-zones"></a>Problemas devido a zonas de segurança
Tivemos vários relatórios de problemas com a autenticação no IE e no Microsoft Edge (desde a atualização da *versão do navegador Microsoft Edge para 40.15063.0.0*). Estamos acompanhando esses e informamos à equipe do Microsoft Edge. Embora o Microsoft Edge funcione em uma resolução, aqui está uma descrição dos problemas que ocorrem com frequência e das possíveis soluções alternativas que podem ser implementadas.

### <a name="cause"></a>Causa
A causa da maioria desses problemas é a seguinte. O armazenamento de sessão e o armazenamento local são particionados por zonas de segurança no navegador Microsoft Edge. Nesta versão específica do Microsoft Edge, quando o aplicativo é redirecionado entre as zonas, o armazenamento de sessão e o armazenamento local são limpos. Especificamente, o armazenamento de sessão é limpo na navegação regular do navegador, e a sessão e o armazenamento local são limpos no modo InPrivate do navegador. O MSAL. js salva determinado estado no armazenamento da sessão e se baseia em verificar esse estado durante os fluxos de autenticação. Quando o armazenamento da sessão é limpo, esse estado é perdido e, portanto, resulta em experiências interrompidas.

### <a name="issues"></a>Problemas

- **Loops de redirecionamento infinitos e recarregamentos de página durante a autenticação**. Quando os usuários entram no aplicativo no Microsoft Edge, eles são redirecionados de volta da página de logon do AAD e ficam presos em um loop de redirecionamento infinito, resultando em recargas repetidas de página. Geralmente, isso é acompanhado por um erro de `invalid_state` no armazenamento de sessão.

- **Loops de token de aquisição infinito e erro AADSTS50058**. Quando um aplicativo em execução no Microsoft Edge tenta adquirir um token para um recurso, o aplicativo pode ficar preso em um loop infinito da chamada de aquisição de token junto com o seguinte erro do AAD em seu rastreamento de rede:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **A janela pop-up não fecha ou está presa ao usar o logon por meio de Popup para autenticar**. Ao autenticar por meio da janela pop-up no Microsoft Edge ou IE (InPrivate), depois de inserir as credenciais e entrar, se vários domínios entre zonas de segurança estiverem envolvidos na navegação, a janela pop-up não será fechada porque o MSAL. js perde o identificador para a janela pop-up.  

    Aqui estão os links para esses problemas no controlador de problemas do Microsoft Edge:  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Atualização: correção disponível no MSAL. js 0.2.3
Correções para os problemas de loop de redirecionamento de autenticação foram liberadas no [MSAL. js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Habilite o sinalizador `storeAuthStateInCookie` na configuração MSAL. js para aproveitar essa correção. Por padrão, esse sinalizador é definido como false.

Quando o sinalizador de `storeAuthStateInCookie` estiver habilitado, o MSAL. js usará os cookies do navegador para armazenar o estado de solicitação necessário para a validação dos fluxos de autenticação.

> [!NOTE]
> Essa correção ainda não está disponível para os invólucros MSAL-angular e MSAL-angularjs. Essa correção não aborda o problema com janelas pop-up.

Use as soluções alternativas abaixo.

#### <a name="other-workarounds"></a>Outras soluções alternativas
Certifique-se de testar se o problema está ocorrendo apenas na versão específica do navegador Microsoft Edge e funciona nos outros navegadores antes de adotar essas soluções alternativas.  
1. Como uma primeira etapa para contornar esses problemas, certifique-se de que o domínio do aplicativo, e quaisquer outros sites envolvidos nos redirecionamentos do fluxo de autenticação sejam adicionados como sites confiáveis nas configurações de segurança do navegador, para que eles pertençam à mesma zona de segurança.
Para fazer isso, siga estas etapas:
    - Abra o **Internet Explorer** e clique nas **configurações** (ícone de engrenagem) no canto superior direito
    - Selecionar **Opções da Internet**
    - Selecione a guia **segurança**
    - Na opção **sites confiáveis** , clique no botão **sites** e adicione as URLs na caixa de diálogo que é aberta.

2. Como mencionado anteriormente, como apenas o armazenamento de sessão é limpo durante a navegação regular, você pode configurar o MSAL. js para usar o armazenamento local. Isso pode ser definido como o parâmetro de configuração `cacheLocation` ao inicializar MSAL.

Observe que isso não resolverá o problema de navegação InPrivate, pois a sessão e o armazenamento local são limpos.

## <a name="issues-due-to-popup-blockers"></a>Problemas devido a bloqueadores de pop-ups

Há casos em que os pop-ups são bloqueados no IE ou no Microsoft Edge, por exemplo, quando um segundo pop-up ocorre durante a autenticação multifator. Você receberá um alerta no navegador para permitir o pop-up uma vez ou sempre. Se você optar por permitir, o navegador abrirá a janela pop-up automaticamente e retornará um identificador de `null` para ele. Como resultado, a biblioteca não tem um identificador para a janela e não há como fechar a janela pop-up. O mesmo problema não acontece no Chrome quando ele solicita que você permita pop-ups porque ele não abre uma janela pop-up automaticamente.

Como **alternativa**, os desenvolvedores precisarão permitir pop-ups no IE e no Microsoft Edge antes de começarem a usar seu aplicativo para evitar esse problema.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [usar o MSAL. js no Internet Explorer](msal-js-use-ie-browser.md).
