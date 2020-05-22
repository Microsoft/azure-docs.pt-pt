---
title: Fluxos de autenticação MSAL Azure
titleSuffix: Microsoft identity platform
description: Conheça os fluxos de autenticação e subsídios utilizados pela Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: ce81af90baeeda519f1b56d1e10a46923ebd22c2
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772136"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os diferentes fluxos de autenticação fornecidos pela Microsoft Authentication Library (MSAL).  Estes fluxos podem ser usados em vários cenários de aplicação diferentes.

| Fluxo | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o símbolo através de um processo interativo que leva o utilizador a credenciais através de um browser ou janela pop-up. | [Aplicativos de desktop,](scenario-desktop-overview.md) [aplicativos móveis](scenario-mobile-overview.md) |
| [Subvenção implícita](#implicit-grant) | Permite que a aplicação obtenha fichas sem realizar uma troca de credenciais de servidor de back-end. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs web, tudo dentro do código JavaScript do cliente.| [Aplicações de página única (SPA)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em apps que estão instaladas num dispositivo para ter acesso a recursos protegidos, como APIs web. Isto permite-lhe adicionar acesso de sessão e API às suas aplicações móveis e desktop. | [Aplicativos de desktop,](scenario-desktop-overview.md) [aplicativos móveis,](scenario-mobile-overview.md) [aplicações web](scenario-web-app-call-api-overview.md) | 
| [Em nome de](#on-behalf-of) | Uma aplicação invoca um serviço ou Web API, que por sua vez precisa de chamar outro serviço ou Web API. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais de cliente](#client-credentials) | Permite-lhe aceder a recursos hospedados na Web utilizando a identidade de uma aplicação. Comumente usado para interações servidor-a-servidor que devem ser executados em segundo plano, sem interação imediata com um utilizador. | [Aplicações daemon](scenario-daemon-overview.md) |
| [Código do dispositivo](#device-code) | Permite que os utilizadores inscrevam dispositivos limitados à entrada, como uma tv inteligente, dispositivo IoT ou impressora. | [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que aplicações em domínio ou Azure Ative Directory (Azure AD) se juntem a computadores para adquirir um token silenciosamente (sem qualquer interação ui do utilizador).| [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de utilizador/palavra-passe](scenario-desktop-acquire-token.md#username-and-password) | Permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Este fluxo não é recomendado. | [Aplicativos desktop/mobile](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite fichas e códigos
 
Dependendo da forma como o seu cliente é construído, pode utilizar um (ou vários) fluxos de autenticação suportados pela plataforma de identidade da Microsoft.  Estes fluxos podem produzir uma variedade de tokens (id_tokens, fichas de atualização, fichas de acesso) bem como códigos de autorização, e requerem diferentes fichas para fazê-los funcionar. Este gráfico fornece uma visão geral:
 
|Fluxo | Requer | id_token | ficha de acesso | ficha refrescante | código de autorização | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo de código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo híbrido oIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Refrescar redenção de token](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | ficha refrescante | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | ficha de acesso| x| x| x| |
|[Fluxo de código do dispositivo](v2-oauth2-device-code.md) | | x| x| x| |
|[Credenciais de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (apenas app)| | |
 
Os tokens emitidos através do modo implícito têm uma limitação de comprimento devido a ser passado de volta para o navegador através do URL (onde `response_mode` está `query` ou `fragment` ).  Alguns navegadores têm um limite no tamanho do URL que pode ser colocado na barra do navegador e falhar quando é muito longo.  Assim, estas fichas não têm `groups` nem `wids` reclamam.

## <a name="interactive"></a>Interativo

A MSAL suporta a capacidade de incitar interativamente o utilizador para que as suas credenciais entrem e obtenham um sinal utilizando essas credenciais.

![Diagrama de fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre a utilização de MSAL.NET para adquirir fichas interativamente em plataformas específicas, consulte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas em MSAL.js, consulte o [comportamento rápido em pedidos interativos MSAL.js](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Subvenção implícita

A MSAL suporta o fluxo implícito de [subvenção OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que a app obtenha fichas da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de servidor back-end. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs web, tudo dentro do código JavaScript do cliente.

![Diagrama do fluxo implícito de concessão](media/msal-authentication-flows/implicit-grant.svg)

Muitas aplicações web modernas são construídas como aplicações do lado do cliente, de página única, escritas usando JavaScript ou uma estrutura SPA como Angular, Vue.js e React.js. Estas aplicações funcionam num navegador web e têm características de autenticação diferentes das aplicações tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que as aplicações de uma página única sinuosos os utilizadores, e obter fichas para aceder a serviços back-end ou APIs web, utilizando o fluxo de subvenção implícito. O fluxo implícito permite que a aplicação obtenha fichas de identificação para representar o utilizador autenticado, e também aceder a fichas necessárias para chamar APIs protegidos.

Este fluxo de autenticação não inclui cenários de aplicação que utilizam quadros javaScript de plataforma cruzada, como Eletrão e Reagir-Nativo, porque requerem mais capacidades de interação com as plataformas nativas.

## <a name="authorization-code"></a>Código de autorização

A MSAL apoia a concessão do código de [autorização OAuth 2.](v2-oauth2-auth-code-flow.md) Esta subvenção pode ser utilizada em apps que estão instaladas num dispositivo para ter acesso a recursos protegidos, como apis web. Isto permite-lhe adicionar acesso de sessão e API às suas aplicações móveis e desktop. 

Quando os utilizadores acedem a aplicações web (websites), a aplicação web recebe um código de autorização.  O código de autorização é reembolsado para adquirir um símbolo para ligar para a web APIs. Em ASP.NET e ASP.NET aplicações web Core, o único objetivo `AcquireTokenByAuthorizationCode` é adicionar um token à cache simbólica. O símbolo pode então ser utilizado pela aplicação (geralmente nos controladores, que apenas obtêm um símbolo para uma API `AcquireTokenSilent` utilizando).

![Diagrama do fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

No diagrama anterior, a aplicação:

1. Solicita um código de autorização, que é reembolsado por um sinal de acesso.
2. Usa o sinal de acesso para chamar uma API web.

### <a name="considerations"></a>Considerações

- Só pode usar o código de autorização uma vez para resgatar um símbolo. Não tente adquirir um símbolo várias vezes com o mesmo código de autorização (é explicitamente proibido pela especificação padrão do protocolo). Se resgatar o código várias vezes intencionalmente, ou porque não sabe que uma estrutura também o faz por si, terá o seguinte erro:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se estiver a escrever um pedido ASP.NET ou ASP.NET Core, isto pode acontecer se não disser à estrutura que já redimiu o código de autorização. Para isso, você precisa ligar para o `context.HandleCodeRedemption()` método do manipulador de `AuthorizationCodeReceived` eventos.

- Evite partilhar o símbolo de acesso com ASP.NET, o que pode impedir que o consentimento incremental aconteça corretamente. Para mais informações, consulte [a #693.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)

## <a name="on-behalf-of"></a>Em nome de

A MSAL apoia o [OAuth 2 em nome do fluxo de autenticação.](v2-oauth2-on-behalf-of-flow.md)  Este fluxo é utilizado quando uma aplicação invoca um serviço ou API web, que por sua vez precisa de chamar outro serviço ou API web. A ideia é propagar a identidade e permissões de utilizador delegadas através da cadeia de pedidos. Para que o serviço de nível médio faça pedidos autenticados ao serviço a jusante, este precisa de garantir um sinal de acesso da plataforma de identidade da Microsoft, em nome do utilizador.

![Diagrama de fluxo em nome](media/msal-authentication-flows/on-behalf-of.png)

No diagrama anterior:

1. A aplicação adquire um sinal de acesso para a Web API.
2. Um cliente (aplicação web, desktop, mobile ou uma página única) chama uma API web protegida, adicionando o token de acesso como um token portador no cabeçalho de autenticação do pedido HTTP. A Web API autentica o utilizador.
3. Quando o cliente chama a Web API, a Web API solicita outro símbolo em nome do utilizador.  
4. A API da web protegida utiliza este símbolo para chamar uma API web a jusante em nome do utilizador.  A Web API também pode solicitar tokens para outras APIs a jusante (mas ainda em nome do mesmo utilizador).

## <a name="client-credentials"></a>Credenciais de cliente

A MSAL suporta o fluxo de credenciais de [clientes OAuth 2.](v2-oauth2-client-creds-grant-flow.md) Este fluxo permite-lhe aceder a recursos web hospedados utilizando a identidade de uma aplicação. Este tipo de subvenção é comumente usado para interações servidor-servidor que devem ser executados em segundo plano, sem interação imediata com um utilizador. Estes tipos de aplicações são muitas vezes referidos como daemons ou contas de serviço. 

As credenciais de cliente concedem fluxo de fluxo permite que um serviço web (um cliente confidencial) use as suas próprias credenciais, em vez de se fazer passar por um utilizador, para autenticar quando chama outro serviço web. Neste cenário, o cliente é tipicamente um serviço web de nível médio, um serviço daemon ou um website. Para um nível mais elevado de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamadas utilize um certificado (em vez de um segredo partilhado) como credencial.

> [!NOTE]
> O fluxo de clientes confidenciais não está disponível nas plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android), porque estas apenas suportam aplicações de clientes públicos. As aplicações de clientes públicos não sabem como provar a identidade da aplicação ao Fornecedor de Identidade. Uma ligação segura pode ser alcançada na web app ou web API back ends através da implementação de um certificado.

MSAL.NET suporta dois tipos de credenciais de cliente. Estas credenciais de cliente precisam de ser registadas na Azure AD. As credenciais são transmitidas aos construtores da aplicação confidencial do cliente no seu código.

### <a name="application-secrets"></a>Segredos de aplicação

![Diagrama de cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

No diagrama anterior, a aplicação:

1. Adquire um símbolo utilizando credenciais secretas de aplicação ou password.
2. Usa o símbolo para fazer pedidos do recurso.

### <a name="certificates"></a>Certificados

![Diagrama de cliente confidencial com cert](media/msal-authentication-flows/confidential-client-certificate.png)

No diagrama anterior, a aplicação:

1. Adquire um símbolo utilizando credenciais de certificado.
2. Usa o símbolo para fazer pedidos do recurso.

Estas credenciais de cliente têm de ser:
- Registado na Azure AD.
- Passou na construção da aplicação confidencial do cliente no seu código.

## <a name="device-code"></a>Código do dispositivo

A MSAL suporta o fluxo de código do [dispositivo OAuth 2,](v2-oauth2-device-code.md)que permite aos utilizadores iniciarem sessão em dispositivos com restrições de entrada, como uma tv inteligente, dispositivo IoT ou impressora. A autenticação interativa com a AD Azure requer um navegador web. O fluxo de código do dispositivo permite que o utilizador utilize outro dispositivo (por exemplo, outro computador ou um telemóvel) para iniciar sessão interativamente, onde o dispositivo ou sistema operativo não fornece um navegador web.

Ao utilizar o fluxo de código do dispositivo, a aplicação obtém fichas através de um processo em duas etapas especialmente concebido para estes dispositivos ou sistemas operativos. Exemplos de tais aplicações incluem as que estão a funcionar em dispositivos IoT ou ferramentas de linha de comando (CLI). 

![Diagrama do fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

No diagrama anterior:

1. Sempre que é necessária a autenticação do utilizador, a aplicação fornece um código e pede ao utilizador que utilize outro dispositivo (como um smartphone ligado à Internet) para ir a um URL (por `https://microsoft.com/devicelogin` exemplo). O utilizador é então solicitado a introduzir o código e procede através de uma experiência de autenticação normal, incluindo solicitações de consentimento e [autenticação de vários fatores,](../authentication/concept-mfa-howitworks.md) se necessário.

2. Após a autenticação bem sucedida, a aplicação de linha de comando recebe os tokens necessários através de um canal traseiro, e usa-os para executar as chamadas aAPI da web que necessita.

### <a name="constraints"></a>Restrições

- O fluxo de código do dispositivo só está disponível nas aplicações de clientes públicos.
- A autoridade aprovada na construção da aplicação do cliente público deve ser uma das seguintes:
  - Arrendatário (da forma `https://login.microsoftonline.com/{tenant}/` em que é o GUID que representa o ID do `{tenant}` arrendatário ou um domínio associado ao inquilino).
  - Para qualquer trabalho e contas escolares `https://login.microsoftonline.com/organizations/` ( ).
- As contas pessoais da Microsoft ainda não são suportadas pelo ponto final da AD Azure (não pode usar os `/common` `/consumers` ou inquilinos).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows.

A MSAL suporta a Autenticação Integrada do Windows (IWA) para aplicações de desktop ou mobile que funcionam num domínio ligado ou a Azure AD juntou-se ao computador Windows. Utilizando o IWA, estas aplicações podem adquirir um símbolo silenciosamente (sem qualquer interação ui do utilizador). 

![Diagrama de autenticação integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

No diagrama anterior, a aplicação:

1. Adquire um símbolo utilizando a Autenticação Integrada do Windows.
2. Usa o símbolo para fazer pedidos do recurso.

### <a name="constraints"></a>Restrições

O IWA suporta apenas utilizadores federados, o que significa que os utilizadores criados em Diretório Ativo e apoiados pela Azure AD. Os utilizadores criados diretamente no Azure AD, sem o suporte do Ative Directory (utilizadores geridos) não podem utilizar este fluxo de autenticação. Esta limitação não afeta o fluxo de [username/password](#usernamepassword).

O IWA é para aplicações escritas para .NET Framework, .NET Core e plataformas universal windows platform.

A IWA não ignora a autenticação de vários fatores. Se a autenticação de vários fatores estiver configurada, o IWA poderá falhar se for necessário um desafio de autenticação multifactor. A autenticação de vários fatores requer interação do utilizador.

Não controla quando o fornecedor de identidade pede autenticação de dois fatores para ser realizada. O administrador do inquilino tem. Normalmente, é necessária a autenticação de dois fatores quando se inscreve num país/região diferente, quando não está ligado via VPN a uma rede corporativa, e às vezes mesmo quando está ligado via VPN. A Azure AD utiliza AI para aprender continuamente se é necessária autenticação de dois fatores. Se o IWA falhar, deve recuar para um [pedido de utilizador interativo] (#interactive).

A autoridade aprovada na construção da aplicação do cliente público deve ser uma das seguintes:
- Arrendatário (da forma `https://login.microsoftonline.com/{tenant}/` onde é o guia que representa o ID do inquilino ou um domínio associado ao `tenant` inquilino).
- Para qualquer trabalho e contas escolares `https://login.microsoftonline.com/organizations/` ( ). As contas pessoais da Microsoft não são suportadas (não pode usar `/common` ou `/consumers` inquilinos).

Como a IWA é um fluxo silencioso, um dos seguintes deve ser verdade:
- O utilizador da sua aplicação deve ter previamente consentido em utilizar a aplicação. 
- O administrador do arrendatário deve ter previamente consentido todos os utilizadores do arrendatário a utilização da aplicação.

Isto significa que um dos seguintes é verdade:
- Você, como desenvolvedor, **selecionou** grant no portal Azure para si mesmo.
- Um administrador de inquilino selecionou **o consentimento do administrador Grant/revogado para o domínio do {inquilino}** no separador de **permissões DaPI** do registo para o pedido (ver [Adicionar permissões para aceder a APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Forneceu uma forma de os utilizadores consentirem com a aplicação (ver Solicitando o consentimento individual do [utilizador).](v2-permissions-and-consent.md#requesting-individual-user-consent)
- Forneceu uma forma de o administrador inquilino consentir com o pedido (ver [consentimento do administrador).](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

O fluxo IWA está ativado para aplicações de ambiente de trabalho .NET, .NET Core e Windows Universal Platform. No .NET Core deve fornecer o nome de utilizador ao IWA, porque o .NET Core não consegue obter nomes de utilizador do sistema operativo.
  
Para mais informações sobre o consentimento, consulte [as permissões e consentimento do v2.0.](v2-permissions-and-consent.md)

## <a name="usernamepassword"></a>Nome de utilizador/palavra-passe

A MSAL suporta a concessão de credenciais de senha do proprietário de [recursos OAuth 2,](v2-oauth-ropc.md)que permite que uma aplicação assine no utilizador manuseando diretamente a sua palavra-passe. Na sua aplicação de ambiente de trabalho, pode utilizar o fluxo de username/password para adquirir um token silenciosamente. Não é necessário uI ao utilizar a aplicação.

![Diagrama do username/fluxo de palavra-passe](media/msal-authentication-flows/username-password.png)

No diagrama anterior, a aplicação:

1. Adquire um símbolo enviando o nome de utilizador e a palavra-passe para o fornecedor de identidade.
2. Chama uma API web usando o símbolo.

> [!WARNING]
> Este fluxo não é recomendado. Requer um elevado grau de confiança e exposição ao utilizador.  Só deve utilizar este fluxo quando outros fluxos mais seguros não podem ser utilizados. Para mais informações, veja [Qual é a solução para o problema crescente das palavras-passe?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) 

O fluxo preferido para adquirir um símbolo silenciosamente em máquinas unidas ao domínio do Windows é a [Autenticação Integrada do Windows](#integrated-windows-authentication). Caso contrário, também pode utilizar o [fluxo de código do dispositivo](#device-code).

Embora isso seja útil em alguns casos (cenários de DevOps), se quiser usar o nome de utilizador/palavra-passe em cenários interativos onde fornece o seu próprio UI, tente evitá-lo. Utilizando o nome de utilizador/palavra-passe:
- Os utilizadores que necessitem de fazer a autenticação multi-factor não poderão entrar (uma vez que não existe interação).
- Os utilizadores não poderão fazer um único sinal.

### <a name="constraints"></a>Restrições

Para além dos [constrangimentos integrados de autenticação do Windows,](#integrated-windows-authentication)aplicam-se também os seguintes constrangimentos:

- O fluxo de username/password não é compatível com acesso condicional e autenticação de vários fatores. Como consequência, se a sua aplicação for executado num inquilino da AD Azure onde o administrador do inquilino requer a autenticação de vários fatores, não pode utilizar este fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas de trabalho e escola (não contas da Microsoft).
- O fluxo está disponível em .NET desktop e .NET Core, mas não na Plataforma Universal Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificidades do Azure AD B2C

Para obter mais informações sobre a utilização do ROPC em MSAL.NET e Azure AD B2C, consulte [AD PC com Azure AD B2C](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).
