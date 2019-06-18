---
title: Problemas em browsers (biblioteca de autenticação da Microsoft para JavaScript) conhecidos | Azure
description: Saiba mais sobre saiba que esses problemas quando utiliza a biblioteca de autenticação da Microsoft para JavaScript (msal) com navegadores de Internet Explorer e o Microsoft Edge.
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
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873904"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problemas conhecidos em browsers do Internet Explorer e o Microsoft Edge com msal

## <a name="issues-due-to-security-zones"></a>Problemas devido a zonas de segurança
Tivemos vários relatórios de problemas com a autenticação no IE e o Microsoft Edge (desde a atualização do *versão do browser Microsoft Edge para 40.15063.0.0*). Estamos a controlar estes e realizar a equipe do Microsoft Edge. Enquanto o Microsoft Edge funciona numa resolução, eis uma descrição dos problemas frequentes e as possíveis soluções alternativas que podem ser implementadas.

### <a name="cause"></a>Causa
Segue-se a causa para a maioria desses problemas. O armazenamento de sessão e o armazenamento local são particionados por zonas de segurança no browser Microsoft Edge. Nesta versão específica do Microsoft Edge, quando o aplicativo é redirecionado em zonas, o armazenamento de sessão e o armazenamento local são limpos. Especificamente, o armazenamento de sessão é limpo na navegação do navegador de normal, e a sessão e o armazenamento local são limpos no modo InPrivate do navegador. Msal guarda determinado Estado no armazenamento de sessão e depende de a verificação neste estado durante os fluxos de autenticação. Quando o armazenamento de sessão é limpo, este estado é perdido e, por conseguinte, resulta em experiências quebradas.

### <a name="issues"></a>Problemas

- **Ciclos de redirecionamento infinita e página recarrega durante a autenticação**. Quando os utilizadores iniciam sessão para a aplicação no Microsoft Edge, eles são redirecionados volta a partir da página de início de sessão do AAD e ficam bloqueados num loop infinito de redirecionamento, resultando em recargas de página repetidas. Isso normalmente é acompanhado por um `invalid_state` erro no armazenamento de sessão.

- **Infinito adquirir o token loops e erro AADSTS50058**. Quando uma aplicação em execução no Microsoft Edge tenta adquirir um token para um recurso, o aplicativo pode ficar preso num loop infinito da aquisição token chamada juntamente com o seguinte erro do AAD em seu rastreamento de rede:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Janela de pop-up não fechar ou está com dificuldades ao utilizar o início de sessão por meio de pop-up para autenticar**. Durante a autenticação por meio da janela de pop-up no Microsoft Edge ou IE(InPrivate), depois de introduzir credenciais e ao iniciar sessão, se vários domínios em zonas de segurança estiverem envolvidos no painel de navegação, a janela de pop-up não fechar porque o identificador para perde a msal a janela de pop-up.  

    Seguem-se links para esses problemas em que o controlador de problemas do Microsoft Edge:  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Atualização: Correção disponível no msal 0.2.3
Correções para os problemas de ciclo de redirecionamento de autenticação que tenham sido publicados nos [msal 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Ativar o sinalizador `storeAuthStateInCookie` a configuração de msal para aproveitar esta correção. Por padrão, esse sinalizador estiver definido como false.

Quando o `storeAuthStateInCookie` sinalizador estiver ativado, msal irá utilizar os cookies do browser para armazenar o estado do pedido necessário para a validação dos fluxos de autenticação.

> [!NOTE]
> Esta correção ainda não está disponível para os invólucros de msal angular e msal angularjs. Esta correção não resolver o problema com o windows de pop-up.

Utilize soluções alternativas abaixo.

#### <a name="other-workarounds"></a>Outras soluções alternativas
Certifique-se de que o problema está a ocorrer apenas na versão específica do browser Microsoft Edge e funciona em outros navegadores antes de adotar estas soluções deixam de teste.  
1. Como primeiro passo para resolver estes problemas, certifique-se de que o domínio do aplicativo, e quaisquer outros sites envolvidos na redirecionamentos do fluxo de autenticação são adicionados como sites confiáveis nas definições de segurança do navegador, para que pertencem à mesma zona de segurança.
Para tal, siga estes passos:
    - Open **Internet Explorer** e clique nas **definições** (ícone de engrenagem) no canto superior direito
    - Selecione **opções da Internet**
    - Selecione o **segurança** separador
    - Sob o **Sites confiáveis** opção, clique nas **sites** botão e adicionar os URLs na caixa de diálogo que é aberta.

2. Como mencionado antes, desde apenas a sessão de armazenamento é limpa durante a navegação normal, só pode configurar a msal para utilizar o armazenamento local em vez disso. Isto pode ser definido como o `cacheLocation` parâmetro de configuração ao inicializar a MSAL.

Tenha em atenção que isto não resolverá o problema para a Navegação InPrivate, uma vez que a sessão e o armazenamento local são limpos.

## <a name="issues-due-to-popup-blockers"></a>Problemas devido a bloqueadores de popup

Há casos em pop-ups estão bloqueados no IE ou Microsoft Edge, por exemplo, quando um pop-up segundo ocorre durante a autenticação multifator. Receberá um alerta no browser para permitir pop-up uma vez ou sempre. Se optar por permitir, o browser abre-se a janela de pop-up automaticamente e retorna um `null` processar para o mesmo. Como resultado, a biblioteca não tem um identificador da janela e não é possível para fechar a janela de pop-up. O mesmo problema não acontece no Chrome quando pede-lhe para permitir pop-ups, porque não abra automaticamente uma janela de pop-up.

Como um **solução**, os desenvolvedores serão necessário permitir pop-ups no IE e o Microsoft Edge, antes que comece a utilizar a sua aplicação para evitar este problema.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [através de msal no Internet Explorer](msal-js-use-ie-browser.md).
