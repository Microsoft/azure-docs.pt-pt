---
title: Edições no Internet Explorer & Microsoft Edge (MSAL.js) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre problemas ao utilizar a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js) com navegadores Internet Explorer e Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772085"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Questões conhecidas nos navegadores Internet Explorer e Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemas devido a zonas de segurança
Tivemos vários relatos de problemas com autenticação no IE e no Microsoft Edge (desde a atualização da versão do *navegador Microsoft Edge para 40.15063.0.0*). Estamos a rastreá-los e informámos a equipa do Microsoft Edge. Enquanto o Microsoft Edge trabalha numa resolução, aqui está uma descrição dos problemas frequentes que ocorrem e das possíveis soluções alternativas que podem ser implementadas.

### <a name="cause"></a>Causa
A causa para a maioria destas questões é a seguinte. O armazenamento da sessão e o armazenamento local são divididos por zonas de segurança no navegador Microsoft Edge. Nesta versão específica do Microsoft Edge, quando a aplicação é redirecionada através de zonas, o armazenamento da sessão e o armazenamento local são limpos. Especificamente, o armazenamento da sessão é limpo na navegação regular do navegador, e tanto a sessão como o armazenamento local são limpos no modo InPrivate do navegador. MSAL.js salva determinado estado no armazenamento da sessão e depende da verificação deste estado durante os fluxos de autenticação. Quando o armazenamento da sessão é limpo, este estado perde-se e, portanto, resulta em experiências quebradas.

### <a name="issues"></a>Problemas

- **Loops de redirecionamento infinito sinuosos e recargas**de página durante a autenticação . Quando os utilizadores iniciam sessão na aplicação no Microsoft Edge, são redirecionados da página de login AAD e ficam presos num ciclo de redirecionamento infinito, resultando em recargas repetidas de página. Isto é geralmente acompanhado por um `invalid_state` erro no armazenamento da sessão.

- **Infinita aquisição de loops simbólicos e erro AADSTS50058**. Quando uma aplicação em execução no Microsoft Edge tenta adquirir um símbolo para um recurso, a aplicação pode ficar presa num ciclo infinito da chamada simbólica de aquisição juntamente com o seguinte erro da AAD no seu traço de rede:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **A janela popup não fecha ou fica presa ao utilizar login através do Popup para autenticar**. Ao autenticar através da janela popup no Microsoft Edge ou IE(InPrivate), depois de introduzir credenciais e iniciar sessão, se vários domínios em zonas de segurança estiverem envolvidos na navegação, a janela popup não fecha porque mSAL.js perde a pega na janela popup.  

### <a name="update-fix-available-in-msaljs-023"></a>Atualização: Correção disponível em MSAL.js 0.2.3
As correções para as emissões de redirecionamento de autenticação foram lançadas em [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Ative a bandeira `storeAuthStateInCookie` no config MSAL.js para tirar partido desta correção. Por defeito, esta bandeira está definida como falsa.

Quando a `storeAuthStateInCookie` bandeira estiver ativada, a MSAL.js utilizará os cookies do navegador para armazenar o estado de pedido necessário para a validação dos fluxos auth.

> [!NOTE]
> Esta correção ainda não está disponível para os invólucros msal-angulares e msal-angulares. Esta correção não aborda o problema com as janelas Popup.

Use as sobras abaixo.

#### <a name="other-workarounds"></a>Outras sobras
Certifique-se de testar que o seu problema está a ocorrer apenas na versão específica do navegador Microsoft Edge e funciona nos outros navegadores antes de adotar estas seleções.  
1. Como primeiro passo para contornar estes problemas, certifique-se de que o domínio da aplicação e quaisquer outros sites envolvidos nos redirecionamentos do fluxo de autenticação são adicionados como sites fidedignos nas definições de segurança do navegador, de modo a que pertençam à mesma zona de segurança.
Para o fazer, siga estes passos:
    - Abra o **Internet Explorer** e clique nas **definições** (ícone de engrenagem) no canto superior direito
    - Selecione **Opções de Internet**
    - Selecione o separador **Segurança**
    - Sob a opção **Sites Fidedignos,** clique no botão de **sites** e adicione os URLs na caixa de diálogo que se abre.

2. Como mencionado anteriormente, uma vez que apenas o armazenamento da sessão é limpo durante a navegação regular, pode configurar MSAL.js para utilizar o armazenamento local. Isto pode ser definido como o `cacheLocation` parâmetro de config enquanto inicializa mSAL.

Note que isto não resolverá o problema da navegação inPrivate uma vez que tanto a sessão como o armazenamento local estão limpos.

## <a name="issues-due-to-popup-blockers"></a>Problemas devido a bloqueadores pop-up

Há casos em que os popups são bloqueados no IE ou no Microsoft Edge, por exemplo quando ocorre um segundo popup durante a [autenticação de vários fatores](../authentication/concept-mfa-howitworks.md). Você receberá um alerta no navegador para permitir o popup uma ou sempre. Se optar por permitir, o navegador abre automaticamente a janela popup e devolve-lhe uma `null` pega. Como resultado, a biblioteca não tem uma pega para a janela e não há como fechar a janela popup. O mesmo problema não acontece no Chrome quando o leva a permitir popups porque não abre automaticamente uma janela popup.

Como **supérs,** os desenvolvedores terão de permitir popups no IE e no Microsoft Edge antes de começarem a usar a sua aplicação para evitar este problema.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a utilização de MSAL.js no Internet Explorer](msal-js-use-ie-browser.md).
