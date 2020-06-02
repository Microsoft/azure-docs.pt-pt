---
title: Fluxos de autenticação MSAL / Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os fluxos de autenticação e subsídios utilizados pela Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47978317b1ae914e952b764def854d8a011293e0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266615"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os diferentes fluxos de autenticação fornecidos pela Microsoft Authentication Library (MSAL).  Estes fluxos podem ser usados em diferentes cenários de aplicação.

| Fluxo | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o símbolo através de um processo interativo que incita o utilizador a obter credenciais através de um navegador ou janela pop-up. | [Aplicativos para desktop,](scenario-desktop-overview.md) [aplicativos móveis](scenario-mobile-overview.md) |
| [Subvenção implícita](#implicit-grant) | Permite que a aplicação obtenha fichas sem realizar uma troca de credenciais de servidor de back-end. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs da web, tudo dentro do código JavaScript do cliente.| [Aplicações de uma só página (SPA)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em apps que são instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Isto permite-lhe adicionar acesso de início de s-in e API às suas aplicações móveis e desktop. | [Aplicativos para desktop,](scenario-desktop-overview.md) [aplicativos móveis,](scenario-mobile-overview.md) [aplicações web](scenario-web-app-call-api-overview.md) | 
| [Em nome de](#on-behalf-of) | Uma aplicação invoca um serviço ou API web, que por sua vez precisa de chamar outro serviço ou API web. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais de cliente](#client-credentials) | Permite-lhe aceder a recursos hospedados na Web utilizando a identidade de uma aplicação. Geralmente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador. | [Aplicações daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os utilizadores inscrevam-se em dispositivos com restrições de entrada, como uma smart TV, dispositivo IoT ou impressora. | [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que aplicações em domain ou Azure Ative Directory (Azure AD) se juntem a computadores para adquirir um símbolo silenciosamente (sem qualquer interação de UI do utilizador).| [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de utilizador/palavra-passe](scenario-desktop-acquire-token.md#username-and-password) | Permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Este fluxo não é recomendado. | [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos
 
Dependendo da forma como o seu cliente é construído, pode utilizar um (ou vários) dos fluxos de autenticação suportados pela plataforma de identidade da Microsoft.  Estes fluxos podem produzir uma variedade de fichas (id_tokens, tokens de atualização, fichas de acesso) bem como códigos de autorização, e requerem diferentes fichas para fazê-los funcionar. Este gráfico fornece uma visão geral:
 
|Fluxo | Requer | id_token | token de acesso | token refresh | código de autorização | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo híbrido OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Atualizar a redenção simbólica](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token refresh | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | token de acesso| x| x| x| |
|[Fluxo de código do dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (apenas para aplicações)| | |
 
Os tokens emitidos através do modo implícito têm uma limitação de comprimento devido a ser passado de volta para o navegador através do URL (onde `response_mode` está `query` ou `fragment` ).  Alguns navegadores têm um limite no tamanho do URL que pode ser colocado na barra de navegador e falhar quando é muito longo.  Assim, estes tokens não têm `groups` nem `wids` reivindicações.

## <a name="interactive"></a>Interativo

A MSAL suporta a capacidade de incitar interativamente o utilizador para que as suas credenciais se inscrevam e obtenham um símbolo utilizando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre a utilização de MSAL.NET para adquirir inativamente fichas em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas em MSAL.js, consulte [o comportamento rápido nos pedidos interativos MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Subvenção implícita

A MSAL suporta o [fluxo de subvenção implícita OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que a aplicação obtenha fichas da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de servidor back-end. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs da web, tudo dentro do código JavaScript do cliente.

![Diagrama do fluxo de subvenção implícito](media/msal-authentication-flows/implicit-grant.svg)

Muitas aplicações web modernas são construídas como aplicações de página única do lado do cliente, escritas usando JavaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicações de página única assinem nos utilizadores e obtenham fichas para aceder a serviços de back-end ou APIs web, utilizando o fluxo de subvenção implícito. O fluxo implícito permite que a aplicação obtenha fichas de identificação para representar o utilizador autenticado, e também acesso a fichas necessárias para chamar APIs protegidas.

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada, como Electrn e React-Native, porque requerem mais capacidades de interação com as plataformas nativas.

## <a name="authorization-code"></a>Código de autorização

A MSAL apoia a [concessão do código de autorização OAuth 2](v2-oauth2-auth-code-flow.md). Esta bolsa pode ser utilizada em aplicações instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Isto permite-lhe adicionar acesso de início de s-in e API às suas aplicações móveis e desktop. 

Quando os utilizadores insinam-se em aplicações web (websites), a aplicação web recebe um código de autorização.  O código de autorização é reembolsado para adquirir um token para chamar APIs web. Em ASP.NET e ASP.NET aplicações web Core, o único objetivo `AcquireTokenByAuthorizationCode` é adicionar um símbolo à cache simbólica. O token pode então ser utilizado pela aplicação (normalmente nos controladores, que apenas obtêm um símbolo para uma API `AcquireTokenSilent` utilizando).

![Diagrama do fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, a aplicação:

1. Solicita um código de autorização, que é reembolsado para um token de acesso.
2. Usa o token de acesso para chamar uma API web.

### <a name="considerations"></a>Considerações

- Só pode usar o código de autorização uma vez para resgatar um símbolo. Não tente adquirir um símbolo várias vezes com o mesmo código de autorização (é explicitamente proibido pela especificação padrão do protocolo). Se resgatar o código várias vezes intencionalmente, ou porque não tem conhecimento de que uma estrutura também o faz por si, terá o seguinte erro:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se estiver a escrever uma aplicação ASP.NET ou ASP.NET Core, isto pode acontecer se não disser à estrutura que já resgatou o código de autorização. Para isso, tem de chamar o `context.HandleCodeRedemption()` método do manipulador de `AuthorizationCodeReceived` eventos.

- Evite partilhar o token de acesso com ASP.NET, o que pode impedir que o consentimento incremental aconteça corretamente. Para mais informações, consulte [a emissão #693.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)

## <a name="on-behalf-of"></a>Em nome de

A MSAL suporta o [fluxo de autenticação OAuth 2 em nome.](v2-oauth2-on-behalf-of-flow.md)  Este fluxo é utilizado quando uma aplicação invoca um serviço ou API web, que por sua vez precisa de chamar outro serviço ou web API. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. Para que o serviço de nível médio faça pedidos autenticados para o serviço a jusante, este necessita de garantir um sinal de acesso a partir da plataforma de identidade da Microsoft, em nome do utilizador.

![Diagrama de fluxo em nome do fluxo](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. A aplicação adquire um símbolo de acesso para a API web.
2. Um cliente (web, desktop, mobile ou aplicação de uma página) chama uma API web protegida, adicionando o token de acesso como símbolo portador no cabeçalho de autenticação do pedido HTTP. A API web autentica o utilizador.
3. Quando o cliente liga para a API web, a API web solicita outro símbolo em nome do utilizador.  
4. A API web protegida utiliza este token para chamar uma API web a jusante em nome do utilizador.  A API web também pode solicitar mais tarde tokens para outras APIs a jusante (mas ainda em nome do mesmo utilizador).

## <a name="client-credentials"></a>Credenciais de cliente

A MSAL suporta o fluxo de credenciais de [clientes OAuth 2](v2-oauth2-client-creds-grant-flow.md). Este fluxo permite-lhe aceder a recursos hospedados na Web utilizando a identidade de uma aplicação. Este tipo de concessão é comumente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador. Estes tipos de aplicações são muitas vezes referidos como daemons ou contas de serviço. 

As credenciais do cliente concedem fluxo permite que um serviço web (um cliente confidencial) utilize as suas próprias credenciais, em vez de se fazer passar por utilizador, para autenticar quando liga para outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou um site. Para um nível de garantia mais elevado, a plataforma de identidade da Microsoft também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

> [!NOTE]
> O fluxo de clientes confidenciais não está disponível nas plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android), porque estes apenas suportam aplicações de clientes públicos. As aplicações de clientes públicos não sabem como provar a identidade do pedido ao Fornecedor de Identidade. Uma ligação segura pode ser alcançada em aplicativos web ou na web API termina através da implementação de um certificado.

MSAL.NET suporta dois tipos de credenciais de cliente. Estas credenciais de cliente precisam de ser registadas na Azure AD. As credenciais são transmitidas aos construtores da aplicação confidencial do cliente no seu código.

### <a name="application-secrets"></a>Segredos de aplicação

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, a aplicação:

1. Adquire um token utilizando credenciais secretas de aplicação ou palavra-passe.
2. Usa o símbolo para fazer pedidos do recurso.

### <a name="certificates"></a>Certificados

![Diagrama de cliente confidencial com cert](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, a aplicação:

1. Adquire um símbolo utilizando credenciais de certificado.
2. Usa o símbolo para fazer pedidos do recurso.

Estas credenciais de cliente têm de ser:
- Registado na Azure AD.
- Passou na construção do pedido de cliente confidencial no seu código.

## <a name="device-code"></a>Código do dispositivo

O MSAL suporta o fluxo de código do [dispositivo OAuth 2](v2-oauth2-device-code.md), que permite que os utilizadores entrem em dispositivos com restrições de entrada, como tv inteligente, dispositivo IoT ou impressora. A autenticação interativa com Azure AD requer um navegador web. O fluxo de código do dispositivo permite ao utilizador utilizar outro dispositivo (por exemplo, outro computador ou telemóvel) para assinar interativamente, onde o dispositivo ou sistema operativo não fornece um navegador web.

Ao utilizar o fluxo de código do dispositivo, a aplicação obtém fichas através de um processo em duas etapas especialmente concebido para estes dispositivos ou sistemas operativos. Exemplos de tais aplicações incluem aqueles que estão em execução em dispositivos IoT ou ferramentas de linha de comando (CLI). 

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que é necessária a autenticação do utilizador, a aplicação fornece um código e pede ao utilizador que utilize outro dispositivo (como um smartphone ligado à Internet) para ir a um URL (por exemplo, `https://microsoft.com/devicelogin` ). O utilizador é então solicitado a introduzir o código, e procede através de uma experiência de autenticação normal, incluindo pedidos de consentimento e [autenticação de vários fatores,](../authentication/concept-mfa-howitworks.md) se necessário.

2. Após a autenticação bem sucedida, a aplicação da linha de comando recebe os tokens necessários através de um canal traseiro, e utiliza-os para executar as chamadas web da API de que necessita.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo só está disponível em aplicações de clientes públicos.
- A autoridade transmitida na construção do pedido de cliente público deve ser uma das seguintes:
  - Arrendado (do formulário `https://login.microsoftonline.com/{tenant}/` onde está o GUID representando o `{tenant}` ID do inquilino ou um domínio associado ao arrendatário).
  - Para qualquer trabalho e contas escolares ( `https://login.microsoftonline.com/organizations/` ).
- As contas pessoais da Microsoft ainda não são suportadas pelo ponto final Azure AD v2.0 (não pode usar o `/common` ou `/consumers` os inquilinos).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

A MSAL suporta a Autenticação Integrada do Windows (IWA) para aplicações de ambiente de trabalho ou móveis que funcionam num domínio ligado ou a AZure AD juntou-se ao computador Windows. Utilizando o IWA, estas aplicações podem adquirir um símbolo silenciosamente (sem qualquer interação ui do utilizador). 

![Diagrama de Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, a aplicação:

1. Adquire um token utilizando a Autenticação Integrada do Windows.
2. Usa o símbolo para fazer pedidos do recurso.

### <a name="constraints"></a>Restrições

A IWA suporta apenas utilizadores federados, o que significa que os utilizadores criados no Ative Directory e apoiados pela Azure AD. Os utilizadores criados diretamente no AZure AD, sem o suporte do Ative Directory (utilizadores geridos) não podem utilizar este fluxo de autenticação. Esta limitação não afeta o [nome de utilizador/fluxo de senha](#usernamepassword).

IWA destina-se a aplicações escritas para plataformas .NET Framework, .NET Core e Universal Windows Platform.

A IWA não contorna a autenticação de vários fatores. Se a autenticação multi-factor for configurada, a IWA pode falhar se for necessário um desafio de autenticação de vários fatores. A autenticação multi-factor requer interação do utilizador.

Não controla quando o fornecedor de identidade solicita a autenticação de dois fatores a ser realizada. O administrador inquilino tem. Normalmente, a autenticação de dois fatores é necessária quando você entra em um país/região diferente, quando você não está conectado via VPN a uma rede corporativa, e às vezes mesmo quando você está conectado via VPN. A Azure AD utiliza IA para aprender continuamente se é necessária autenticação de dois fatores. Se a IWA falhar, deve recorrer a uma [solicitação de utilizador interativa] (#interactive).

A autoridade transmitida na construção do pedido de cliente público deve ser uma das seguintes:
- Arrendado (da forma `https://login.microsoftonline.com/{tenant}/` onde está o guia que representa o `tenant` ID do inquilino ou um domínio associado ao arrendatário).
- Para qualquer trabalho e contas escolares ( `https://login.microsoftonline.com/organizations/` ). As contas pessoais da Microsoft não são suportadas (não pode usar `/common` ou `/consumers` inquilinos).

Como a IWA é um fluxo silencioso, um dos seguintes deve ser verdade:
- O utilizador da sua aplicação deve ter previamente consentido em utilizar a aplicação. 
- O administrador do arrendatário deve ter previamente consentido com todos os utilizadores do arrendatário para utilizarem a aplicação.

Isto significa que um dos seguintes é verdade:
- Como desenvolvedor, escolheu **Grant** no portal Azure para si mesmo.
- Um administrador de inquilino selecionou **o consentimento de administração Grant/revoke para {domínio do inquilino}** no separador **permissões API** do registo do pedido (ver [Adicionar permissões para aceder a APIs web).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)
- Forneceu uma forma de os utilizadores consentirem com a aplicação (ver [Pedido de consentimento individual do utilizador).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Forneceu uma forma de o administrador inquilino consentir com o pedido (ver [consentimento administrativo).](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

O fluxo IWA está ativado para aplicações .NET desktop, .NET Core e Windows Universal Platform. Em .NET Core deve fornecer o nome de utilizador à IWA, porque .NET Core não pode obter nomes de utilizador do sistema operativo.
  
Para obter mais informações sobre o consentimento, consulte [permissões v2.0 e consentimento.](v2-permissions-and-consent.md)

## <a name="usernamepassword"></a>Nome de utilizador/palavra-passe

A MSAL suporta a [concessão de credenciais de senha do proprietário de recursos OAuth 2,](v2-oauth-ropc.md)que permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Na sua aplicação para desktop, pode utilizar o username/password flow para adquirir um símbolo silenciosamente. Não é necessária UI ao utilizar a aplicação.

![Diagrama do nome de utilizador/fluxo de senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, a aplicação:

1. Adquire um token enviando o nome de utilizador e senha para o fornecedor de identidade.
2. Chama uma API web usando o token.

> [!WARNING]
> Este fluxo não é recomendado. Requer um elevado grau de confiança e exposição do utilizador.  Só deve utilizar este fluxo quando outros fluxos, mais seguros, não puderem ser utilizados. Para mais informações, veja [qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

O fluxo preferido para adquirir um símbolo silenciosamente em máquinas unidas ao domínio do Windows é [a Autenticação Integrada do Windows](#integrated-windows-authentication). Caso contrário, também pode utilizar [o fluxo de código do dispositivo.](#device-code)

Embora isso seja útil em alguns casos (cenários DevOps), se quiser usar o nome de utilizador/palavra-passe em cenários interativos onde fornece o seu próprio UI, tente evitá-lo. Utilizando o nome de utilizador/palavra-passe:
- Os utilizadores que necessitem de fazer a autenticação multi-factor não poderão iniciar sação (uma vez que não existe interação).
- Os utilizadores não poderão fazer uma única s inscrição.

### <a name="constraints"></a>Restrições

Para além dos [constrangimentos integrados](#integrated-windows-authentication)de autenticação do Windows, aplicam-se também os seguintes constrangimentos:

- O username/password flow não é compatível com acesso condicional e autenticação de vários fatores. Como consequência, se a sua aplicação funcionar num inquilino AZure AD onde o administrador inquilino requer autenticação multi-factor, não pode utilizar este fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas de trabalho e escola (não contas microsoft).
- O fluxo está disponível em .NET desktop e .NET Core, mas não na Plataforma Universal windows.

### <a name="azure-ad-b2c-specifics"></a>Especificidades Azure AD B2C

Para obter mais informações sobre a utilização do ROPC em MSAL.NET e Azure AD B2C, consulte [utilizar o ROPC com Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).
