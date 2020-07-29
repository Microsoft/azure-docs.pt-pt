---
title: Tipos de aplicações para plataforma de identidade microsoft / Rio Azure
description: Os tipos de aplicações e cenários suportados pela plataforma de identidade da Microsoft (v2.0) ponto final.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: bdacee476fbc25154fe225700730f1b8f7f872ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83682267"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Tipos de aplicações para plataforma de identidade microsoft

A plataforma de identidade da Microsoft (v2.0) suporta a autenticação para uma variedade de arquiteturas modernas de aplicações, todas elas baseadas em protocolos padrão da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicações que pode construir utilizando a plataforma de identidade da Microsoft, independentemente do seu idioma ou plataforma preferido. A informação foi concebida para o ajudar a compreender cenários de alto nível antes de [começar a trabalhar com o código.](v2-overview.md#getting-started)

## <a name="the-basics"></a>Noções básicas

Tem de registar cada aplicação que utiliza o ponto final da plataforma de identidade da Microsoft nas [inscrições](https://go.microsoft.com/fwlink/?linkid=2083908)da App portal Azure. O processo de registo da aplicação recolhe e atribui estes valores para a sua aplicação:

* Um **ID de aplicação (cliente)** que identifica exclusivamente a sua aplicação
* Um **URI redirecionado** que pode usar para direcionar as respostas de volta à sua app
* Alguns outros valores específicos do cenário, tais como tipos de conta suportados

Para mais detalhes, saiba como [registar uma aplicação.](quickstart-register-app.md)

Depois de a aplicação ser registada, a aplicação comunica com a plataforma de identidade da Microsoft enviando pedidos para o ponto final. Fornecemos quadros e bibliotecas de código aberto que tratam dos detalhes destes pedidos. Tem também a opção de implementar a lógica de autenticação, criando pedidos para estes pontos finais:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)

Muitas aplicações modernas têm uma primeira página de aplicações escritas principalmente em JavaScript, muitas vezes com uma estrutura como Angular, React ou Vue. O ponto final da plataforma de identidade da Microsoft suporta estas aplicações utilizando o [fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Neste fluxo, a aplicação recebe um código a partir do ponto final da plataforma de identidade da `authorize` Microsoft, e resgata-o para tokens e tokens de atualização usando pedidos de web cross-site. O token de atualização expira a cada 24 horas, e a aplicação deve solicitar outro código.

![Fluxo de código para aplicativos SPA](media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.png)

Para ver este cenário em ação, consulte o [Tutorial: Inscreva-se nos utilizadores e ligue para a API do Gráfico Microsoft a partir de um JavaScript SPA utilizando o fluxo de código auth](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Fluxo de código de autorização vs. fluxo implícito

Durante a maior parte da história do OAuth 2.0, o [fluxo implícito](v2-oauth2-implicit-grant-flow.md) foi a forma recomendada de construir aplicações de uma só página. Com a remoção de [cookies de terceiros](reference-third-party-cookies-spas.md) e [uma maior atenção](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) às preocupações de segurança em torno do fluxo implícito, passamos para o fluxo de código de autorização para aplicações de uma só página.

Para garantir a compatibilidade da sua aplicação no Safari e noutros navegadores conscientes da privacidade, já não recomendamos a utilização do fluxo implícito e, em vez disso, recomendamos o fluxo de código de autorização.

## <a name="web-apps"></a>Web Apps

Para aplicações web (.NET, PHP, Java, Ruby, Python, Nó) a que o utilizador acede através de um browser, pode utilizar [o OpenID Connect](active-directory-v2-protocols.md) para iniciar sing-in do utilizador. No OpenID Connect, a aplicação web recebe um token de ID. Um token de identificação é um símbolo de segurança que verifica a identidade do utilizador e fornece informações sobre o utilizador sob a forma de reclamações:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Outros detalhes dos diferentes tipos de fichas utilizados no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência [de acesso e](access-tokens.md) id_token [referência](id-tokens.md)

Nas aplicações do servidor web, o fluxo de autenticação de entrada toma estes passos de alto nível:

![Mostra o fluxo de autenticação de aplicativos web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Pode garantir a identidade do utilizador validando o token de ID com uma chave de assinatura pública que é recebida a partir do ponto final da plataforma de identidade da Microsoft. É definido um cookie de sessão, que pode ser usado para identificar o utilizador em pedidos de página subsequentes.

Para ver este cenário em ação, experimente uma das amostras de código de inscrição da web na plataforma de identidade da [Microsoft a iniciar a](v2-overview.md#getting-started) secção.

Além de um simples s-in, uma aplicação de servidor web pode precisar de aceder a outro serviço web, como uma API REST. Neste caso, a aplicação do servidor web envolve um fluxo combinado OpenID Connect e OAuth 2.0, utilizando o fluxo de [código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md). Para mais informações sobre este cenário, leia sobre [começar com aplicações web e APIs web.](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)


## <a name="web-apis"></a>APIs da Web

Pode utilizar o ponto final da plataforma de identidade da Microsoft para garantir serviços web, como a API web RESTful da sua aplicação. As APIs web podem ser implementadas em inúmeras plataformas e idiomas. Também podem ser implementados utilizando triggers HTTP em Funções Azure. Em vez de fichas de ID e cookies de sessão, uma API web usa um token de acesso OAuth 2.0 para proteger os seus dados e autenticar pedidos de entrada. O autor da chamada de uma API web anexa um token de acesso no cabeçalho de autorização de um pedido HTTP, como este:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API web utiliza o token de acesso para verificar a identidade do chamador API e para extrair informações sobre o chamador de reclamações que estão codificadas no token de acesso. Outros detalhes de diferentes tipos de fichas utilizados no ponto final da plataforma de identidade da Microsoft estão disponíveis na referência [de acesso e](access-tokens.md) [id_token.](id-tokens.md)

Uma API web pode dar aos utilizadores o poder de optar por não ter funcionalidades ou dados específicos expondo permissões, também conhecidas como [âmbitos.](v2-permissions-and-consent.md) Para que uma aplicação de chamada adquira permissão para um âmbito, o utilizador deve consentir com o âmbito durante um fluxo. O ponto final da plataforma de identidade da Microsoft pede permissão ao utilizador e, em seguida, regista permissões em todos os tokens de acesso que a API web recebe. A API web valida os tokens de acesso que recebe em cada chamada e realiza verificações de autorização.

Uma API web pode receber tokens de acesso de todos os tipos de aplicações, incluindo aplicações de servidores web, aplicações de desktop e mobile, aplicações de página única, daemons do lado do servidor e até mesmo outras APIs web. O fluxo de alto nível para uma API web é assim:

![Mostra o fluxo de autenticação da API web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para aprender a proteger uma API web utilizando fichas de acesso OAuth2, consulte as amostras de código da API web na plataforma de identidade da [Microsoft a iniciar a](v2-overview.md#getting-started) secção.

Em muitos casos, as APIs da web também precisam de fazer pedidos de saída para outras APIs da web a jusante protegidas pela plataforma de identidade da Microsoft. Para tal, as APIs web podem aproveitar o fluxo **On-Behalf-Of,** que permite à API web trocar um token de acesso de entrada para outro token de acesso a ser usado em pedidos de saída. Para obter mais informações, consulte [a plataforma de identidade da Microsoft e o fluxo OAuth 2.0 On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicações móveis e nativas

As aplicações instaladas no dispositivo, como aplicações móveis e desktop, precisam muitas vezes de aceder a serviços de back-end ou APIs web que armazenam dados e desempenham funções em nome de um utilizador. Estas aplicações podem adicionar entrada e autorização a serviços de back-end utilizando o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Neste fluxo, a aplicação recebe um código de autorização a partir do ponto final da plataforma de identidade da Microsoft quando o utilizador assina. O código de autorização representa a permissão da app para ligar para serviços de back-end em nome do utilizador que se inscreveu. A aplicação pode trocar o código de autorização em segundo plano por um token de acesso OAuth 2.0 e um token de atualização. A aplicação pode usar o token de acesso para autenticar para apis web em pedidos HTTP, e usar o token de atualização para obter novos tokens de acesso quando os tokens de acesso mais antigos expirarem.

![Mostra o fluxo de autenticação de aplicativos nativo](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor

As aplicações que têm processos de longa duração ou que operam sem interação com um utilizador também precisam de uma forma de aceder a recursos seguros, como APIs web. Estas aplicações podem autenticar e obter fichas utilizando a identidade da aplicação, em vez da identidade delegada de um utilizador, com o fluxo de credenciais de cliente OAuth 2.0. Pode provar a identidade da aplicação usando um segredo ou certificado do cliente. Para obter mais informações, consulte [a aplicação da consola .NET Core daemon utilizando a plataforma de identidade da Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

Neste fluxo, a app interage diretamente com o `/token` ponto final para obter acesso:

![Mostra o fluxo de autenticação da app Daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para construir uma app Daemon, consulte a [documentação](v2-oauth2-client-creds-grant-flow.md)das credenciais do cliente, ou experimente uma [aplicação de amostra .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Próximos passos

Agora que está familiarizado com os tipos de aplicações suportadas pela plataforma de identidade da Microsoft, saiba mais sobre [o OAuth 2.0 e o OpenID Connect](active-directory-v2-protocols.md) para obter uma compreensão dos componentes do protocolo utilizados pelos diferentes cenários.