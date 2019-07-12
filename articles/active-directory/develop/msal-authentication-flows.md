---
title: Fluxos de autenticação (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre os fluxos de autenticação e concessões utilizadas pelo Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ba6ae188c098e85573503a1518ba65480d713a
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807201"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os fluxos de autenticação diferentes fornecidos pelo Microsoft Authentication Library (MSAL).  Estes fluxos podem ser utilizados numa variedade de cenários de aplicação diferente.

| Fluxo | Descrição | Utilizado no|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token através de um processo interativo que solicita ao utilizador as credenciais através de um browser ou janela pop-up. | [Aplicações de ambiente de trabalho](scenario-desktop-overview.md), [aplicações móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que a aplicação obter os tokens sem efetuar uma troca de credenciais do servidor back-end. Isso permite que a aplicação iniciar a sessão do utilizador, manter a sessão e obtenha tokens para outras APIs para a web, tudo dentro do cliente código JavaScript.| [Aplicativos de página única (SPA)](scenario-spa-overview.md) |
| [código de autorização](#authorization-code) | Utilizadas em aplicações que são instaladas num dispositivo para obter acesso a recursos protegidos, como as APIs web. Isto permite-lhe adicionar o início de sessão e acesso à API às suas aplicações móveis e de Desktops. | [Aplicações de ambiente de trabalho](scenario-desktop-overview.md), [aplicações móveis](scenario-mobile-overview.md), [aplicações web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Um aplicativo invoca um serviço ou a API web, que por sua vez tem de chamar outro serviço ou a web API. A idéia é propagar a identidade de utilizador delegado e permissões através da cadeia de pedido. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais de cliente](#client-credentials) | Permite-lhe aceder a recursos hospedados na web utilizando a identidade de um aplicativo. Geralmente, é utilizada para interações de servidor para servidor que devem ser executado em segundo plano, sem interação imediata com um utilizador. | [aplicações daemon](scenario-daemon-overview.md) |
| [Código de dispositivo](#device-code) | Permite aos utilizadores iniciar sessão em dispositivos de restrição de entrada, como uma smart TV, dispositivos de IoT ou impressora. | [Aplicações de ambiente de trabalho/móvel](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticação integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que os computadores de aplicações no domínio ou do Azure Active Directory (Azure AD) associado ao adquirir um token automaticamente (sem qualquer interação da interface do Usuário do usuário).| [Aplicações de ambiente de trabalho/móvel](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de utilizador/palavra-passe](scenario-desktop-acquire-token.md#username--password) | Permite que um aplicativo iniciar o utilizador ao lidar diretamente com a palavra-passe. Este fluxo não é recomendado. | [Aplicações de ambiente de trabalho/móvel](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interativo
A MSAL suporta a capacidade de forma interativa solicitar ao utilizador as credenciais para iniciar sessão e obter um token com essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre como utilizar MSAL.NET interativamente adquirir tokens em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre as chamadas interativas no msal, consulte [solicitar o comportamento em pedidos interativos de msal](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Concessão implícita

A MSAL suporta o [fluxo de concessão implícito de OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que a aplicação obter os tokens de plataforma de identidade da Microsoft sem executar um servidor de back-end exchange de credencial. Isso permite que a aplicação iniciar a sessão do utilizador, manter a sessão e obtenha tokens para outras APIs para a web, tudo dentro do cliente código JavaScript.

![Diagrama de fluxo de concessão implícita](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos web modernos baseiam-se que as aplicações de página do lado do cliente, o único, escritas por usando JavaScript ou uma estrutura SPA como Angular, VUE e react. js. Estas aplicações executadas num navegador da web e têm características de autenticação diferentes que os aplicativos da web tradicional do lado do servidor. A plataforma de identidade da Microsoft permite que os aplicativos de única página Iniciar sessão dos utilizadores e obter tokens para aceder a serviços de back-end ou APIs, web, utilizando o fluxo de concessão implícita. O fluxo implícito permite que o aplicativo obter os tokens de ID para representar o utilizador autenticado e também acessar tokens necessários para chamar as APIs protegidas.

Este fluxo de autenticação não inclui os cenários de aplicativos que usam estruturas de JavaScript de várias plataformas, como o Bombardeador e React nativas, pois ainda mais requerem capacidades para interação com as plataformas nativas.

## <a name="authorization-code"></a>código de autorização
A MSAL suporta o [concessão do código de autorização de OAuth 2](v2-oauth2-auth-code-flow.md). Esta concessão pode ser utilizada nas aplicações que são instaladas num dispositivo para obter acesso a recursos protegidos, como as APIs web. Isto permite-lhe adicionar o início de sessão e acesso à API às suas aplicações móveis e de Desktops. 

Quando os utilizadores iniciam sessão às aplicações web (Web sites), o aplicativo web recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar as APIs web. Em aplicativos de web ASP.NET e ASP.NET Core, o objetivo apenas de `AcquireTokenByAuthorizationCode` é adicionar um token para a cache de tokens. O token, em seguida, pode ser usado pelo aplicativo (normalmente em controladores de que apenas obter um token para uma API com `AcquireTokenSilent`).

![Diagrama de fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, o aplicativo:

1. Pedidos de um código de autorização, que é trocado por um token de acesso.
2. Utiliza o token de acesso para chamar uma API web.

### <a name="considerations"></a>Considerações
- Pode utilizar apenas uma vez o código de autorização para utilizar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (explicitamente for proibido pela especificação de protocolo de padrão). Se resgatar o código várias vezes intencionalmente ou porque não está ciente de que uma estrutura também faz tudo por si, obterá o erro seguinte: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se estiver escrevendo um aplicativo ASP.NET ou ASP.NET Core, isto pode acontecer que se não informam à estrutura que já tiver resgatado o código de autorização. Para isso, precisa chamar o `context.HandleCodeRedemption()` método da `AuthorizationCodeReceived` manipulador de eventos.

- Evite o token de acesso de partilha com o ASP.NET, o que poderá impedir o consentimento incremental corretamente a acontecer. Para obter mais informações, consulte [emitir #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

A MSAL suporta o [fluxo de autenticação em-nome-do OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Este fluxo é utilizado quando um aplicativo invoca um serviço ou a API web, que por sua vez tem de chamar outro serviço ou a web API. A idéia é propagar a identidade de utilizador delegado e permissões através da cadeia de pedido. Para o serviço de camada intermediária fazer pedidos autenticados para o serviço downstream, ele precisa de proteger um token de acesso da plataforma de identidades da Microsoft, em nome do utilizador.

![Diagrama de fluxo em-nome-de](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. O aplicativo adquire um token de acesso para a API web.
2. Um cliente (web, aplicação de ambiente de trabalho, móvel ou página única) chama um API, adicionar o token de acesso como um token de portador no cabeçalho de autenticação da solicitação HTTP do web protegida. A API web autentica o utilizador.
3. Quando o cliente chama a API web, a API web solicita outro token em-nome-do utilizador.  
4. A API web protegida utiliza esse token para chamar uma API web a jusante em-nome-do utilizador.  A API web também pode pedir tokens de posteriores para outras APIs de downstream (mas ainda em nome do utilizador mesmo).

## <a name="client-credentials"></a>Credenciais de cliente

A MSAL suporta o [fluxo de credenciais de cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md). Este fluxo permite-lhe aceder a recursos hospedados na web utilizando a identidade de um aplicativo. Este tipo de concessão é geralmente utilizado para interações de servidor para servidor que devem ser executado em segundo plano, sem interação imediata com um utilizador. Esses tipos de aplicativos são, muitas vezes, referidos como daemons ou contas de serviço. 

Concessão de credenciais de cliente permite do fluxo que um serviço web (um cliente confidencial) para utilizar as suas próprias credenciais, em vez de representar um utilizador, para autenticação ao chamar outro serviço web. Neste cenário, o cliente é normalmente um serviço web de camada intermediária, um serviço de daemon ou um Web site. Para um nível mais elevado de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada utilizar um certificado (em vez de um segredo partilhado) como uma credencial.

> [!NOTE]
> O fluxo de cliente confidencial não está disponível nas plataformas móveis (UWP, xamarin. IOS e xamarin. Android), uma vez que estes só suportam aplicações cliente público. Aplicativos cliente público não sabem como provar a identidade da aplicação para o fornecedor de identidade. Uma ligação segura pode ser obtida na aplicação web ou web API volta termina ao implementar um certificado.

MSAL.NET suporta dois tipos de credenciais de cliente. Estas credenciais de cliente têm de ser registados com o Azure AD. As credenciais são passadas para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos da aplicação 

![Diagrama de um cliente confidencial com palavra-passe](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, o aplicativo:

1. Recebe um token ao utilizar as credenciais de palavra-passe ou o segredo da aplicação.
2. Utiliza o token para fazer pedidos do recurso.

### <a name="certificates"></a>Certificados 

![Diagrama de um cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, o aplicativo:

1. Recebe um token com credenciais de certificado.
2. Utiliza o token para fazer pedidos do recurso.

Estas credenciais de cliente tem de ser:
- Registado com o Azure AD.
- Passado na construção do aplicativo cliente confidencial em seu código.


## <a name="device-code"></a>Código de dispositivo
A MSAL suporta o [fluxo de código de dispositivo do OAuth 2](v2-oauth2-device-code.md), que permite aos utilizadores iniciar sessão dispositivos de restrição de entrada, como uma smart TV, o dispositivo de IoT ou impressora. Autenticação interativa com o Azure AD necessita de um navegador da web. O fluxo de código de dispositivo permite que o utilizador utilize outro dispositivo (por exemplo, outro computador ou um número de telemóvel) para iniciar sessão interativamente, em que o dispositivo ou o sistema operativo não fornece um navegador da web.

Ao utilizar o fluxo de código de dispositivo, o aplicativo obtém tokens através de um processo de dois passos especialmente concebida para estes dispositivos ou sistemas operativos. Exemplos de tais aplicativos incluem os que são executados em dispositivos de IoT ou ferramentas de linha de comandos (CLI). 

![Diagrama de fluxo de código de dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que for necessária a autenticação de utilizador, a aplicação fornece um código e pede ao utilizador para utilizar outro dispositivo (por exemplo, um smartphone ligados à internet) para ir para uma URL (por exemplo, https://microsoft.com/devicelogin). O utilizador, em seguida, é-lhe pedido que introduza o código e continua por meio de uma experiência de autenticação normal, incluindo pedidos de consentimento e a autenticação multifator, se necessário.

2. Após a autenticação com êxito, a aplicação de linha de comando recebe os tokens necessários através de um canal de back e os utiliza para efetuar as chamadas à API web que precisa.

### <a name="constraints"></a>Restrições

- Fluxo de código de dispositivo só está disponível em aplicativos cliente público.
- A autoridade transmitida ao construir a aplicação cliente público tem de ser um dos seguintes procedimentos:
  - Inquilinos (do formulário `https://login.microsoftonline.com/{tenant}/` onde `{tenant}` é qualquer um do GUID que representa a ID do inquilino ou um domínio associado ao inquilino).
  - para qualquer trabalho contas escolares ou profissionais (`https://login.microsoftonline.com/organizations/`).
- Contas pessoais da Microsoft ainda não são suportadas pelo ponto final v2.0 do Azure AD (não é possível utilizar o `/common` ou `/consumers` inquilinos).

## <a name="integrated-windows-authentication"></a>Autenticação integrada do Windows
A MSAL suporta a autenticação integrada do Windows (IWA) para o ambiente de trabalho ou aplicativos móveis que são executados num domínio associado ou do Azure AD associado a um computador Windows. Utilizar o IWA, esses aplicativos podem adquirir um token automaticamente (sem qualquer interação da interface do Usuário do usuário). 

![Diagrama de autenticação integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, o aplicativo:

1. Recebe um token ao utilizar a autenticação integrada do Windows.
2. Utiliza o token para fazer pedidos do recurso.

### <a name="constraints"></a>Restrições

IWA suporta utilizadores federados apenas, que significa que os utilizadores no Active Directory e apoiadas pelo Azure AD. Os utilizadores criados diretamente no Azure AD, sem o Active Directory de segurança (utilizadores geridos) não é possível utilizar este fluxo de autenticação. Esta limitação não afeta a [fluxo de nome de utilizador/palavra-passe](#usernamepassword).

IWA destina-se a aplicativos escritos para plataformas .NET Framework, .NET Core e a plataforma Universal do Windows.

IWA não ignora autenticação multifator. Se estiver configurada a autenticação multifator, IWA poderá falhar se for necessário a um desafio de autenticação multifator. Autenticação multifator requer interação do utilizador.

Não controlar quando o fornecedor de identidade solicita a autenticação de dois fatores a serem executadas. O administrador de inquilinos faz. Normalmente, autenticação de dois fatores é necessário quando iniciar sessão a partir de um país diferente, quando não está ligado através de VPN a uma rede empresarial e, às vezes, mesmo quando estão ligados através de VPN. Azure AD utiliza ia para aprender continuamente se a autenticação de dois fatores é necessária. Se IWA falhar, deve reverter para uma [linha do utilizador interativo] (#interactive).

A autoridade transmitida ao construir a aplicação cliente público tem de ser um dos seguintes procedimentos:
- Inquilinos (do formulário `https://login.microsoftonline.com/{tenant}/` onde `tenant` é qualquer um do guid que representa a ID do inquilino ou um domínio associado ao inquilino).
- para qualquer trabalho contas escolares ou profissionais (`https://login.microsoftonline.com/organizations/`). Não são suportadas contas pessoais da Microsoft (não é possível usar `/common` ou `/consumers` inquilinos).

Como o IWA é um fluxo silencioso, um dos seguintes deve ser verdadeiro:
- o utilizador da sua aplicação tem tiver anteriormente autorizado a utilizar a aplicação. 
- O administrador inquilino tem tiver autorizado anteriormente a todos os utilizadores no inquilino a utilizar a aplicação.

Isso significa que uma das seguintes opções for verdadeira:
- Como desenvolvedor selecionou **concessão** no portal do Azure para si próprio.
- Selecionou um administrador de inquilinos **conceder/revogar o consentimento de administrador para {domínio de inquilino}** no **permissões de API** separador de registo para o aplicativo (consulte [adicionar permissões para aceder a web APIs ](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Forneceu uma maneira para os utilizadores consentirem que o aplicativo (consulte [pedir consentimento do usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Forneceu uma maneira para que o administrador de inquilinos dar consentimento para a aplicação (veja [consentimento de administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

O fluxo IWA está ativado para ambiente de trabalho do .NET, .NET Core e aplicações da plataforma Universal do Windows. No .NET Core, apenas a sobrecarga de colocar o nome de utilizador está disponível. A plataforma .NET Core, não é possível pedir o nome de utilizador para o sistema operativo.
  
Para obter mais informações sobre o consentimento, consulte [v2.0 permissões e consentimento](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de utilizador/palavra-passe 
A MSAL suporta o [OAuth 2 credenciais de palavra-passe de proprietário do recurso concedem](v2-oauth-ropc.md), que permite que um aplicativo iniciar o utilizador ao lidar diretamente com a palavra-passe. Em seu aplicativo de desktop, pode utilizar o fluxo de nome de utilizador/palavra-passe para adquirir um token silenciosamente. Nenhuma interface do Usuário é necessária ao utilizar a aplicação.

![Diagrama do fluxo de nome de utilizador/palavra-passe](media/msal-authentication-flows/username-password.png)

No diagrama anterior, o aplicativo:

1. Recebe um token através do envio de nome de utilizador e palavra-passe para o fornecedor de identidade.
2. Chamar uma API web utilizando o token.

> [!WARNING]
> Este fluxo não é recomendado. Ele requer um alto grau de exposição de confiança e de utilizador.  Só deve utilizar este fluxo quando os fluxos de outros, mais seguros, não podem ser utilizados. Para obter mais informações, consulte [qual é a solução para o problema crescente de palavras-passe?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

O fluxo preferencial para obter um token silenciosamente em máquinas associadas a domínios do Windows é [a autenticação integrada do Windows](#integrated-windows-authentication). Caso contrário, também pode usar [fluxo de código de dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários de DevOps), se pretender utilizar o nome de utilizador/palavra-passe em cenários interativos em que fornecer sua própria interface do Usuário, tente evitá-lo. Com o nome de utilizador/palavra-passe:
- Os utilizadores que precisam de fazer a autenticação multifator não será possível iniciar sessão (como não há nenhuma interação).
- Os utilizadores não será capazes de início de sessão único.

### <a name="constraints"></a>Restrições

Apart a partir do [restrições de autenticação integrada do Windows](#integrated-windows-authentication), também são aplicáveis as seguintes restrições:

- O fluxo de nome de utilizador/palavra-passe não é compatível com acesso condicional e a autenticação multifator. Como conseqüência, se a aplicação for executada no inquilino do Azure AD em que o administrador de inquilinos requer autenticação multifator, não é possível utilizar este fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas profissionais e escolares (não contas da Microsoft).
- O fluxo está disponível no ambiente de trabalho do .NET e .NET Core, mas não em plataforma Universal do Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificações do Azure AD B2C

Para obter mais informações sobre como utilizar MSAL.NET e do Azure AD B2C, consulte [ROPC a utilizar com o Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
