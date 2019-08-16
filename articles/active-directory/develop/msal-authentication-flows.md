---
title: Fluxos de autenticação (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre os fluxos de autenticação e as concessões usadas pela MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd932d2b11c61c380638a1a95f8da357d0c62e3
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533008"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os diferentes fluxos de autenticação fornecidos pela MSAL (biblioteca de autenticação da Microsoft).  Esses fluxos podem ser usados em uma variedade de cenários de aplicativos diferentes.

| Fluxo | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token por meio de um processo interativo que solicita as credenciais ao usuário por meio de um navegador ou janela pop-up. | [Aplicativos de desktop](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obtenha tokens sem executar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo entre no usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo no código JavaScript do cliente.| [Aplicativos de página única (SPA)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Isso permite que você adicione entrada e acesso à API aos seus aplicativos móveis e de área de trabalho. | [Aplicativos de desktop](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md), [aplicativos Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Um aplicativo invoca um serviço ou uma API Web que, por sua vez, precisa chamar outro serviço ou API da Web. A ideia é propagar a identidade do usuário delegado e as permissões por meio da cadeia de solicitação. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais de cliente](#client-credentials) | Permite que você acesse recursos hospedados pela Web usando a identidade de um aplicativo. Normalmente usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. | [Aplicativos de daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os usuários entrem em dispositivos com restrição de entrada, como uma TV inteligente, um dispositivo IoT ou uma impressora. | [Aplicativos móveis/desktop](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticação integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que os aplicativos em computadores ingressados no domínio ou no Azure Active Directory (Azure AD) adquiram um token silenciosamente (sem qualquer interação com a interface do usuário).| [Aplicativos móveis/desktop](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de usuário/senha](scenario-desktop-acquire-token.md#username--password) | Permite que um aplicativo Conecte o usuário manipulando sua senha diretamente. Esse fluxo não é recomendado. | [Aplicativos móveis/desktop](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interativo
O MSAL dá suporte à capacidade de solicitar interativamente ao usuário suas credenciais para entrar e obter um token usando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre como usar o MSAL.NET para adquirir tokens de forma interativa em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas no MSAL. js, consulte [comportamento de prompt em solicitações interativas do MSAL. js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Concessão implícita

O MSAL dá suporte ao [fluxo de concessão implícita do OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que o aplicativo obtenha tokens da plataforma de identidade da Microsoft sem executar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo entre no usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo no código JavaScript do cliente.

![Diagrama de fluxo de concessão implícito](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos Web modernos são criados como aplicativos do lado do cliente, de página única, escritos usando JavaScript ou uma estrutura SPA como angular, Vue. js e reagir. js. Esses aplicativos são executados em um navegador da Web e têm características de autenticação diferentes dos aplicativos Web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicativos de página única conectem usuários e obtenham tokens para acessar serviços de back-end ou APIs da Web usando o fluxo de concessão implícito. O fluxo implícito permite que o aplicativo obtenha tokens de ID para representar o usuário autenticado e também os tokens de acesso necessários para chamar APIs protegidas.

Esse fluxo de autenticação não inclui cenários de aplicativos que usam estruturas JavaScript de plataforma cruzada, como o ar-nativo e o reajam, porque exigem mais recursos para interação com as plataformas nativas.

## <a name="authorization-code"></a>Código de autorização
O MSAL dá suporte à [concessão de código de autorização OAuth 2](v2-oauth2-auth-code-flow.md). Essa concessão pode ser usada em aplicativos que estão instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Isso permite que você adicione entrada e acesso à API aos seus aplicativos móveis e de área de trabalho. 

Quando os usuários entram em aplicativos Web (sites), o aplicativo Web recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar APIs da Web. Em ASP.net e ASP.NET Core aplicativos Web, o único objetivo do `AcquireTokenByAuthorizationCode` é adicionar um token ao cache do token. O token pode ser usado pelo aplicativo (geralmente nos controladores, que apenas obtêm um token para uma API usando `AcquireTokenSilent`).

![Diagrama de fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, o aplicativo:

1. Solicita um código de autorização, que é resgatado para um token de acesso.
2. Usa o token de acesso para chamar uma API da Web.

### <a name="considerations"></a>Considerações
- Você pode usar o código de autorização apenas uma vez para resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (ele é explicitamente proibido pela especificação de protocolo padrão). Se você resgatar o código várias vezes intencionalmente ou porque não está ciente de que uma estrutura também faz isso para você, você receberá o seguinte erro:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se você estiver escrevendo um aplicativo ASP.NET ou ASP.NET Core, isso poderá acontecer se você não informar à estrutura que já resgatou o código de autorização. Para isso, você precisa chamar o `context.HandleCodeRedemption()` método `AuthorizationCodeReceived` do manipulador de eventos.

- Evite compartilhar o token de acesso com ASP.NET, o que pode impedir que o consentimento incremental ocorra corretamente. Para obter mais informações, consulte [issue #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Em nome de

O MSAL dá suporte ao [fluxo de autenticação em nome de OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Esse fluxo é usado quando um aplicativo chama um serviço ou API Web, que, por sua vez, precisa chamar outro serviço ou API Web. A ideia é propagar a identidade do usuário delegado e as permissões por meio da cadeia de solicitação. Para que o serviço de camada intermediária faça solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma Microsoft Identity, em nome do usuário.

![Diagrama de fluxo em nome de](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API Web.
2. Um cliente (Web, desktop, móvel ou aplicativo de página única) chama uma API Web protegida, adicionando o token de acesso como um token de portador no cabeçalho de autenticação da solicitação HTTP. A API da Web autentica o usuário.
3. Quando o cliente chama a API da Web, a API Web solicita outro token em nome do usuário.  
4. A API Web protegida usa esse token para chamar uma API Web downstream em nome do usuário.  A API da Web também pode solicitar tokens para outras APIs de downstream (mas ainda em nome do mesmo usuário).

## <a name="client-credentials"></a>Credenciais de cliente

O MSAL dá suporte ao [fluxo de credenciais do cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md). Esse fluxo permite que você acesse recursos hospedados na Web usando a identidade de um aplicativo. Esse tipo de concessão é comumente usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativos são geralmente chamados de daemons ou contas de serviço. 

O fluxo de concessão de credenciais de cliente permite que um serviço Web (um cliente confidencial) Use suas próprias credenciais, em vez de representar um usuário, para autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é normalmente um serviço Web de camada intermediária, um serviço de daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo do cliente confidencial não está disponível nas plataformas móveis (UWP, Xamarin. iOS e Xamarin. Android), pois eles só dão suporte a aplicativos cliente públicos. Aplicativos cliente públicos não sabem como provar a identidade do aplicativo para o provedor de identidade. Uma conexão segura pode ser obtida em back-ends de aplicativo Web ou de API Web Implantando um certificado.

O MSAL.NET dá suporte a dois tipos de credenciais de cliente. Essas credenciais de cliente precisam ser registradas com o Azure AD. As credenciais são passadas para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos do aplicativo 

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de senha ou de segredo do aplicativo.
2. Usa o token para fazer solicitações do recurso.

### <a name="certificates"></a>Certificados 

![Diagrama de cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando credenciais de certificado.
2. Usa o token para fazer solicitações do recurso.

Essas credenciais de cliente precisam ser:
- Registrado no Azure AD.
- Passado na construção do aplicativo cliente confidencial em seu código.


## <a name="device-code"></a>Código do dispositivo
O MSAL dá suporte ao [fluxo de código do dispositivo OAuth 2](v2-oauth2-device-code.md), que permite aos usuários entrar em dispositivos com restrições de entrada, como uma TV inteligente, um dispositivo IOT ou uma impressora. A autenticação interativa com o Azure AD requer um navegador da Web. O fluxo de código do dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um telefone celular) para entrar interativamente, onde o dispositivo ou o sistema operacional não fornece um navegador da Web.

Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas, especialmente projetado para esses dispositivos ou sistemas operacionais. Exemplos desses aplicativos incluem aqueles em execução em dispositivos IoT ou em CLI (ferramentas de linha de comando). 

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e solicita que o usuário use outro dispositivo (como um smartphone conectado à Internet) para ir para uma URL (por exemplo https://microsoft.com/devicelogin),. Em seguida, o usuário é solicitado a inserir o código e prossegue com uma experiência de autenticação normal, incluindo prompts de consentimento e autenticação multifator, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários por meio de um canal de fundo e os usa para executar as chamadas de API Web necessárias.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo só está disponível em aplicativos cliente públicos.
- A autoridade passada ao construir o aplicativo cliente público deve ser uma das seguintes:
  - Locatário (do formulário `https://login.microsoftonline.com/{tenant}/` em que `{tenant}` é o GUID que representa a ID do locatário ou um domínio associado ao locatário).
  - Para qualquer conta corporativa e de estudante`https://login.microsoftonline.com/organizations/`().
- As contas pessoais da Microsoft ainda não têm suporte pelo ponto de extremidade v 2.0 do Azure ad `/common` ( `/consumers` você não pode usar os locatários ou).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows
O MSAL dá suporte à autenticação integrada do Windows (IWA) para aplicativos móveis ou de área de trabalho que são executados em um computador Windows ingressado no domínio ou no Azure AD. Usando o IWA, esses aplicativos podem adquirir um token silenciosamente (sem nenhuma interação da interface do usuário). 

![Diagrama de autenticação integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, o aplicativo:

1. Adquire um token usando a autenticação integrada do Windows.
2. Usa o token para fazer solicitações do recurso.

### <a name="constraints"></a>Restrições

O IWA dá suporte apenas a usuários federados, o que significa que os usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD, sem Active Directory backup (usuários gerenciados) não podem usar esse fluxo de autenticação. Essa limitação não afeta o [fluxo de nome de usuário/senha](#usernamepassword).

O IWA é para aplicativos escritos para as plataformas .NET Framework, .NET Core e Plataforma Universal do Windows.

IWA não ignora a autenticação multifator. Se a autenticação multifator estiver configurada, IWA poderá falhar se um desafio de autenticação multifator for necessário. A autenticação multifator requer interação do usuário.

Você não controla quando o provedor de identidade solicita que a autenticação de dois fatores seja executada. O administrador de locatários faz. Normalmente, a autenticação de dois fatores é necessária quando você entra em um país diferente, quando você não está conectado via VPN a uma rede corporativa e, às vezes, mesmo quando você está conectado via VPN. O Azure AD usa o ia para aprender continuamente se a autenticação de dois fatores é necessária. Se IWA falhar, você deverá retornar a um [prompt de usuário interativo] (#interactive).

A autoridade passada ao construir o aplicativo cliente público deve ser uma das seguintes:
- Locatário (do formulário `https://login.microsoftonline.com/{tenant}/` em que `tenant` é o GUID que representa a ID do locatário ou um domínio associado ao locatário).
- Para qualquer conta corporativa e de estudante`https://login.microsoftonline.com/organizations/`(). Não há suporte para contas pessoais da Microsoft (você `/common` não `/consumers` pode usar ou locatários).

Como IWA é um fluxo silencioso, um dos seguintes deve ser verdadeiro:
- O usuário do seu aplicativo deve ter consentido antes de usar o aplicativo. 
- O administrador de locatários deve ter consentido anteriormente para todos os usuários no locatário para usar o aplicativo.

Isso significa que uma das seguintes opções é verdadeira:
- Você, como desenvolvedor, selecionou **Grant** no portal do Azure por conta própria.
- Um administrador de locatários selecionou **conceder/revogar consentimento de administrador para {domínio de locatário}** na guia **permissões de API** do registro do aplicativo (consulte [adicionar permissões para acessar APIs Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Você forneceu uma maneira para os usuários consentirem com o aplicativo (consulte [solicitando consentimento de usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Você forneceu uma maneira para o administrador do locatário consentir para o aplicativo (consulte [consentimento do administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

O fluxo IWA está habilitado para aplicativos .NET desktop, .NET Core e Windows universal Platform. No .NET Core, somente a sobrecarga que está usando o nome de usuário está disponível. A plataforma .NET Core não pode solicitar o nome de usuário para o sistema operacional.
  
Para obter mais informações sobre consentimento, consulte [permissões e consentimento do v 2.0](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de utilizador/palavra-passe 
O MSAL dá suporte à [concessão de credenciais de senha do proprietário do recurso OAuth 2](v2-oauth-ropc.md), que permite que um aplicativo Conecte o usuário manipulando sua senha diretamente. Em seu aplicativo de área de trabalho, você pode usar o fluxo de nome de usuário/senha para adquirir um token silenciosamente. Nenhuma interface do usuário é necessária ao usar o aplicativo.

![Diagrama do fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, o aplicativo:

1. Adquire um token enviando o nome de usuário e a senha para o provedor de identidade.
2. Chama uma API da Web usando o token.

> [!WARNING]
> Esse fluxo não é recomendado. Ele requer um alto grau de confiança e exposição do usuário.  Você só deve usar esse fluxo quando outros, mais seguros, fluxos não podem ser usados. Para obter mais informações, consulte [qual é a solução para o crescente problema de senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

O fluxo preferencial para adquirir um token silenciosamente em computadores ingressados no domínio do Windows é a [autenticação integrada do Windows](#integrated-windows-authentication). Caso contrário, você também pode usar o [fluxo de código do dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários DevOps), se você quiser usar o nome de usuário/senha em cenários interativos em que você fornece sua própria interface do usuário, tente evitá-la. Usando nome de usuário/senha:
- Os usuários que precisam fazer a autenticação multifator não poderão entrar (como não há nenhuma interação).
- Os usuários não poderão fazer logon único.

### <a name="constraints"></a>Restrições

Além das [restrições de autenticação integrada do Windows](#integrated-windows-authentication), as seguintes restrições também se aplicam:

- O fluxo de nome de usuário/senha não é compatível com o acesso condicional e a autenticação multifator. Como consequência, se seu aplicativo for executado em um locatário do Azure AD em que o administrador de locatários requer autenticação multifator, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Ele funciona apenas para contas corporativas e de estudante (não contas da Microsoft).
- O fluxo está disponível no .NET desktop e no .NET Core, mas não em Plataforma Universal do Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificações Azure AD B2Cs

Para obter mais informações sobre como usar MSAL.NET e Azure AD B2C, consulte [usando o ROPC com o Azure ad B2C (MSAL.net)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
