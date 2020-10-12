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
ms.date: 07/08/2020
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 4a902ed53e92cd073d81626e80bdb3c8629ad072
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437875"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

A Microsoft Authentication Library (MSAL) suporta vários fluxos de autenticação para utilização em diferentes cenários de aplicação.

| Fluxo | Descrição | Usado em |
|--|--|--|
| [Código de autorização](#authorization-code) | Usado em apps que são instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Permite-lhe adicionar acesso de iniciar sôns e API às suas aplicações móveis e desktop. | [Aplicativos para desktop,](scenario-desktop-overview.md) [aplicativos móveis,](scenario-mobile-overview.md) [aplicações web](scenario-web-app-call-api-overview.md) |
| [Credenciais de cliente](#client-credentials) | Permite-lhe aceder a recursos hospedados na Web utilizando a identidade de uma aplicação. Geralmente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador. | [Aplicações daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os utilizadores inscrevam-se em dispositivos com restrições de entrada, como uma smart TV, dispositivo IoT ou impressora. | [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Subvenção implícita](#implicit-grant) | Permite que a aplicação obtenha fichas sem realizar uma troca de credenciais de servidor de back-end. Permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs da web, tudo dentro do código JavaScript do cliente. | [Aplicações de uma só página (SPA)](scenario-spa-overview.md) |
| [Em nome de](#on-behalf-of) | Uma aplicação invoca um serviço ou API web, que por sua vez precisa de chamar outro serviço ou API web. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Nome de utilizador/palavra-passe](#usernamepassword) | Permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Este fluxo não é recomendado. | [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#username-and-password) |
| [Autenticação Integrada do Windows](#integrated-windows-authentication) | Permite que aplicações em domain ou Azure Ative Directory (Azure AD) se juntem a computadores para adquirir um símbolo silenciosamente (sem qualquer interação de UI do utilizador). | [Aplicativos para desktop/mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos

Dependendo da forma como a aplicação do seu cliente é construída, pode utilizar um ou mais dos fluxos de autenticação suportados pela plataforma de identidade da Microsoft. Estes fluxos podem produzir vários tipos de fichas, bem como códigos de autorização, e requerem fichas diferentes para fazê-los funcionar.

| Fluxo                                                                               | Requer            | id_token | token de acesso | token refresh | código de autorização |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Fluxo de código de autorização](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (apenas para aplicações) |               |                    |
| [Fluxo de código do dispositivo](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Fluxo implícito](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md)                                | token de acesso        | x        | x            | x             |                    |
| [Nome de utilizador/senha](v2-oauth-ropc.md) (ROPC)                                       | palavra-passe & de nome de utilizador | x        | x            | x             |                    |
| [Fluxo híbrido OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Atualizar a redenção simbólica](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | token refresh       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Autenticação interativa e não interativa

Vários destes fluxos suportam a aquisição de fichas interativas e não interativas.

  - **Interativo** significa que o utilizador pode ser solicitado para a entrada. Por exemplo, levando o utilizador a iniciar sessão, a realizar a autenticação de vários fatores (MFA) ou a conceder consentimento adicional aos recursos.
  - A autenticação **não interativa**, ou *silenciosa,* tenta adquirir um token de forma a que o servidor de login *não possa* solicitar ao utilizador informações adicionais.

A sua aplicação baseada em MSAL deve primeiro tentar adquirir um símbolo *silenciosamente*, e depois interativamente apenas se o método não interativo falhar. Para obter mais informações sobre este padrão, consulte [fichas de aquisição e cache utilizando a Biblioteca de Autenticação da Microsoft (MSAL)](msal-acquire-cache-tokens.md).

## <a name="authorization-code"></a>Código de autorização

A [bolsa de código de autorização OAuth 2](v2-oauth2-auth-code-flow.md) pode ser usada em aplicações que são instaladas num dispositivo para ter acesso a recursos protegidos como APIs web. Isto permite-lhe adicionar acesso de início de s-in e API às suas aplicações móveis e desktop.

Quando os utilizadores insinam-se em aplicações web (websites), a aplicação web recebe um código de autorização. O código de autorização é reembolsado para adquirir um token para chamar APIs web.

![Diagrama do fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, a aplicação:

1. Solicita um código de autorização, que é reembolsado para um token de acesso.
2. Usa o token de acesso para chamar uma API web.

### <a name="considerations"></a>Considerações

- Só pode usar o código de autorização uma vez para resgatar um símbolo. Não tente adquirir um símbolo várias vezes com o mesmo código de autorização porque é explicitamente proibido pela especificação padrão do protocolo. Se resgatar o código várias vezes, intencionalmente ou porque desconhece que uma estrutura também o faz por si, terá o seguinte erro:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>Credenciais de cliente

O [fluxo de credenciais de cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md) permite-lhe aceder a recursos hospedados na Web utilizando a identidade de uma aplicação. Este tipo de concessão é comumente usado para interações servidor-a-servidor que devem ser executadas em segundo plano, sem interação imediata com um utilizador. Estes tipos de aplicações são muitas vezes referidos como daemons ou contas de serviço.

As credenciais do cliente concedem fluxo permite que um serviço web (um cliente confidencial) utilize as suas próprias credenciais, em vez de se fazer passar por utilizador, para autenticar quando liga para outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou um site. Para um nível de garantia mais elevado, a plataforma de identidade da Microsoft também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

> [!NOTE]
> O fluxo de clientes confidenciais não está disponível em plataformas móveis como UWP, Xamarin.iOS e Xamarin.Android porque suportam apenas aplicações de clientes públicos. As aplicações de clientes públicos não sabem como provar a identidade do pedido ao fornecedor de identidade. Uma ligação segura pode ser alcançada em aplicativos web ou web API back-ends através da implementação de um certificado.

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
- Passou ao construir o objeto de aplicação de cliente confidencial no seu código.

## <a name="device-code"></a>Código do dispositivo

O [fluxo de código do dispositivo OAuth 2](v2-oauth2-device-code.md) permite que os utilizadores inscrevam-se em dispositivos com restrições de entrada, como smart TVs, dispositivos IoT e impressoras. A autenticação interativa com Azure AD requer um navegador web. Quando o dispositivo ou sistema operativo não fornece um navegador web, o fluxo de código do dispositivo permite que o utilizador utilize outro dispositivo como um computador ou telemóvel para assinar interativamente.

Ao utilizar o fluxo de código do dispositivo, a aplicação obtém fichas através de um processo em duas etapas concebido para estes dispositivos e sistemas operativos. Exemplos de tais aplicações incluem aqueles que estão em execução em dispositivos IoT e ferramentas de interface de linha de comando (CLI).

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que é necessária a autenticação do utilizador, a aplicação fornece um código e pede ao utilizador que utilize outro dispositivo como um smartphone ligado à Internet para visitar um URL (por exemplo, `https://microsoft.com/devicelogin` ). O utilizador é então solicitado a introduzir o código, e proceder através de uma experiência de autenticação normal, incluindo solicitações de consentimento e [autenticação de vários fatores,](../authentication/concept-mfa-howitworks.md)se necessário.
1. Após a autenticação bem sucedida, a aplicação da linha de comando recebe os tokens necessários através de um canal traseiro, e utiliza-os para executar as chamadas web da API de que necessita.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo está disponível apenas em aplicações de clientes públicos.
- A autoridade transmitida na construção do pedido de cliente público deve ser uma das seguintes:
  - Arrendado, na forma `https://login.microsoftonline.com/{tenant}/,` em que está o GUID representando o ID do inquilino ou um nome de domínio associado ao `{tenant}` arrendatário.
  - Para trabalho e contas escolares no `https://login.microsoftonline.com/organizations/` formulário.

## <a name="implicit-grant"></a>Subvenção implícita

O fluxo [de subvenção implícita OAuth 2](v2-oauth2-implicit-grant-flow.md) permite que a app obtenha fichas da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de servidor back-end. Este fluxo permite que a aplicação assine no utilizador, mantenha uma sessão e obtenha fichas para outras APIs da web, tudo dentro do código JavaScript do cliente.

![Diagrama do fluxo de subvenção implícito](media/msal-authentication-flows/implicit-grant.svg)

Muitas aplicações web modernas são construídas como aplicações de página única (SPA) do lado do cliente, escritas em JavaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Estas aplicações são executadas num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que aplicações de página única assinem nos utilizadores e obtenham fichas para aceder a serviços de back-end ou APIs web, utilizando o fluxo de subvenção implícito. O fluxo implícito permite que a aplicação obtenha fichas de identificação para representar o utilizador autenticado, e também acesso a fichas necessárias para chamar APIs protegidas.

Este fluxo de autenticação não inclui cenários de aplicação que utilizem quadros javaScript de plataforma cruzada como o Electron ou React-Native porque requerem mais capacidades de interação com as plataformas nativas.

Os tokens emitidos através do modo de fluxo implícito têm uma **limitação de comprimento** porque são devolvidos ao navegador por URL (onde `response_mode` está ou `query` `fragment` ). Alguns navegadores limitam o comprimento do URL na barra de navegador e falham quando é muito longo. Assim, estes tokens de fluxo implícito não contêm `groups` ou `wids` reivindicações.

## <a name="on-behalf-of"></a>Em nome de

O [fluxo de fluxo de fluxo de autenticação OAuth 2 em nome de é](v2-oauth2-on-behalf-of-flow.md) utilizado quando uma aplicação invoca um serviço ou API web que, por sua vez, precisa de chamar outro serviço ou API web. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. Para que o serviço de nível médio faça pedidos autenticados para o serviço a jusante, este necessita de garantir um sinal de acesso a partir da plataforma de identidade da Microsoft *em nome do* utilizador.

![Diagrama de fluxo em nome do fluxo](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. A aplicação adquire um símbolo de acesso para a API web.
2. Um cliente (web, desktop, mobile ou aplicação de uma página) chama uma API web protegida, adicionando o token de acesso como símbolo portador no cabeçalho de autenticação do pedido HTTP. A API web autentica o utilizador.
3. Quando o cliente liga para a API web, a API web solicita outro símbolo em nome do utilizador.
4. A API web protegida utiliza este token para chamar uma API web a jusante em nome do utilizador. A API web também pode solicitar mais tarde tokens para outras APIs a jusante (mas ainda em nome do mesmo utilizador).

## <a name="usernamepassword"></a>Nome de utilizador/palavra-passe

A [concessão de credenciais de senha do proprietário de recursos OAuth 2](v2-oauth-ropc.md) (ROPC) permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Na sua aplicação para desktop, pode utilizar o username/password flow para adquirir um símbolo silenciosamente. Não é necessária UI ao utilizar a aplicação.

![Diagrama do nome de utilizador/fluxo de senha](media/msal-authentication-flows/username-password.png)

No diagrama anterior, a aplicação:

1. Adquire um token enviando o nome de utilizador e senha para o fornecedor de identidade.
2. Chama uma API web usando o token.

> [!WARNING]
> Este fluxo não é recomendado. Requer um alto grau de confiança e exposição credencial. *Só* deve utilizar este fluxo quando não forem utilizados fluxos mais seguros. Para mais informações, veja [qual é a solução para o problema crescente das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)

O fluxo preferido para adquirir um símbolo silenciosamente em máquinas unidas ao domínio do Windows é [a Autenticação Integrada do Windows](#integrated-windows-authentication). Noutros casos, utilize o [fluxo de código](#device-code)do dispositivo .

Embora o username/password flow possa ser útil em alguns cenários como DevOps, evite-o se quiser utilizar o nome de utilizador/palavra-passe em cenários interativos onde fornece o seu próprio UI.

Utilizando o nome de utilizador/palavra-passe:

- Os utilizadores que necessitem de realizar a autenticação de vários fatores não poderão iniciar sação porque não existe interação.
- Os utilizadores não poderão fazer uma única s inscrição.

### <a name="constraints"></a>Restrições

Para além dos [constrangimentos integrados](#integrated-windows-authentication)de autenticação do Windows, aplicam-se também os seguintes constrangimentos:

- O username/password flow não é compatível com acesso condicional e autenticação de vários fatores. Como consequência, se a sua aplicação funcionar num inquilino AZure AD onde o administrador inquilino requer autenticação multi-factor, não pode utilizar este fluxo. Muitas organizações fazem isso.
- A ROPC trabalha apenas para contas de trabalho e escolas. Não é possível utilizar o ROPC para contas microsoft (MSA).
- O fluxo está disponível em .NET desktop e .NET Core, mas não na Plataforma Universal windows.
- No Azure AD B2C, o fluxo ROPC funciona apenas para contas locais. Para obter informações sobre o ROPC em MSAL.NET e Azure AD B2C, consulte [utilizando o ROPC com Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

A MSAL suporta a Autenticação Integrada do Windows (IWA) para aplicações de ambiente de trabalho e móveis que funcionam num computador Windows ligado a domínio ou Azure AD. Utilizando a IWA, estas aplicações podem adquirir um símbolo silenciosamente sem exigir interação ui pelo utilizador.

![Diagrama de Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, a aplicação:

1. Adquire um token utilizando a Autenticação Integrada do Windows.
2. Usa o símbolo para fazer pedidos do recurso.

### <a name="constraints"></a>Restrições

A Autenticação Integrada do Windows (IWA) suporta *apenas* utilizadores federados - utilizadores criados em Ative Directory e apoiados pelo Azure AD. Os utilizadores criados diretamente no AD Azure sem suporte ative de diretório (utilizadores geridos) não podem utilizar este fluxo de autenticação. Esta limitação não afeta o [nome de utilizador/fluxo de senha](#usernamepassword).

IWA destina-se a aplicações .NET Framework, .NET Core e Universal Windows Platform.

A IWA não contorna a autenticação de vários fatores. Se a autenticação multi-factor for configurada, a IWA pode falhar se for necessário um desafio de autenticação de vários fatores. A autenticação multi-factor requer interação do utilizador.

Não controla quando o fornecedor de identidade solicita a autenticação de dois fatores a ser realizada. O administrador inquilino tem. Normalmente, a autenticação de dois fatores é necessária quando você entra em um país/região diferente, quando você não está conectado via VPN a uma rede corporativa, e às vezes mesmo quando você está conectado via VPN. A Azure AD utiliza IA para aprender continuamente se é necessária autenticação de dois fatores. Se a IWA falhar, deve recorrer a uma [solicitação interativa do utilizador](#interactive-and-non-interactive-authentication).

A autoridade transmitida na construção do pedido de cliente público deve ser uma das seguintes:

- Arrendado, na forma `https://login.microsoftonline.com/{tenant}/,` em que está o GUID representando o ID do inquilino ou um nome de domínio associado ao `{tenant}` arrendatário.
- Para qualquer trabalho e contas escolares ( `https://login.microsoftonline.com/organizations/` ). As contas pessoais da Microsoft (MSA) não são suportadas; não pode usar `/common` ou `/consumers` inquilinos.

Como a IWA é um fluxo silencioso, um dos seguintes deve ser verdade:

- O utilizador da sua aplicação deve ter previamente consentido em utilizar a aplicação.
- O administrador do arrendatário deve ter previamente consentido com todos os utilizadores do arrendatário para utilizarem a aplicação.

Isto significa que um dos seguintes é verdade:

- Como desenvolvedor, escolheu **Grant** no portal Azure para si mesmo.
- Um administrador de inquilino selecionou **o consentimento de administração Grant/revoke para {domínio do inquilino}** no separador **permissões API** do registo de aplicações no portal Azure (ver [Adicionar permissões para aceder à sua API web).](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)
- Forneceu uma forma de os utilizadores consentirem com a aplicação; ver [Solicitar o consentimento individual do utilizador.](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Forneceu uma maneira de o administrador do inquilino consentir o pedido; ver [consentimento administrativo](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

O fluxo IWA está ativado para aplicações .NET desktop, .NET Core e Windows Universal Platform. Em .NET Core deve fornecer o nome de utilizador à IWA, porque .NET Core não pode obter nomes de utilizador do sistema operativo.

Para obter mais informações sobre o consentimento, consulte [permissões v2.0 e consentimento.](v2-permissions-and-consent.md)

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os fluxos de autenticação suportados pela Microsoft Authentication Library (MSAL), saiba a aquisição e caching dos tokens utilizados nestes fluxos:

[Adquirir e cache fichas utilizando a Biblioteca de Autenticação da Microsoft (MSAL)](msal-acquire-cache-tokens.md)
