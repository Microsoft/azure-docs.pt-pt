---
title: Adquirir & fichas de cache com a Microsoft Authentication Library (MSAL) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como adquirir e caching tokens usando MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: da79a74121318993f807ec3bde101b652a8b49da
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628140"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Adquirir e cache fichas utilizando a Biblioteca de Autenticação da Microsoft (MSAL)

[Os tokens de acesso](access-tokens.md) permitem que os clientes liguem de forma segura para apis web protegidos pela Azure. Existem várias formas de adquirir um token utilizando a Microsoft Authentication Library (MSAL). Alguns requerem interação do utilizador através de um navegador web, enquanto outros não requerem interação do utilizador. Em geral, o método utilizado para adquirir um token depende se a aplicação é uma aplicação de cliente público, como desktop ou aplicativo móvel, ou uma aplicação confidencial do cliente como web app, Web API ou aplicação Daemon.

MSAL caches um símbolo depois de ter sido adquirido. O seu código de aplicação deve primeiro tentar obter um símbolo silenciosamente da cache antes de tentar adquirir um símbolo por outros meios.

Também pode limpar a cache simbólica, que é conseguida removendo as contas da cache. No entanto, isto não remove o cookie de sessão que está no navegador.

## <a name="scopes-when-acquiring-tokens"></a>Âmbitos na aquisição de fichas

[Os âmbitos](v2-permissions-and-consent.md) são as permissões que uma API web expõe a que as aplicações do cliente podem solicitar acesso. As aplicações do cliente solicitam o consentimento do utilizador para estes âmbitos ao escrutinar pedidos de autenticação para obter fichas para aceder às APIs da web. O MSAL permite-lhe obter fichas para aceder a Azure AD para programadores (v1.0) e plataforma de identidade microsoft (v2.0) APIs. o protocolo v2.0 utiliza âmbitos em vez de recursos nos pedidos. Para mais informações, leia [a comparação v1.0 e v2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Com base na configuração da API web da versão simbólica que aceita, o ponto final v2.0 devolve o token de acesso ao MSAL.

Vários dos métodos de aquisição simbólicos da MSAL requerem um `scopes` parâmetro. O `scopes` parâmetro é uma lista de cordas que declaram as permissões desejadas e os recursos solicitados. Os âmbitos bem conhecidos são as permissões do [Microsoft Graph](/graph/permissions-reference).

Também é possível no MSAL aceder aos recursos v1.0. Para mais informações, consulte [Scopes para obter uma aplicação v1.0](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Pedido de âmbitos para uma API web

Quando a sua aplicação necessitar de solicitar um token de acesso com permissões específicas para uma API de recurso, passe os âmbitos contendo a aplicação ID URI da API no `<app ID URI>/<scope>` formato.

Alguns valores de âmbito de exemplo para diferentes recursos:

- Microsoft Graph API: `https://graph.microsoft.com/User.Read`
- API web personalizada: `api://11111111-1111-1111-1111-111111111111/api.read`

O formato do valor de âmbito varia consoante o recurso (a API) que recebe o token de acesso e os valores de `aud` reclamação que aceita.

Apenas para o Microsoft Graph, os mapas de `user.read` âmbito para , e ambos os `https://graph.microsoft.com/User.Read` formatos de âmbito podem ser utilizados intercambiavelmente.

Algumas APIs web, como a Azure Resource Manager API https://management.core.windows.net/) (espere um corte para a frente ('/') na reivindicação do público ( `aud` ) do token de acesso. Neste caso, passe o âmbito como `https://management.core.windows.net//user_impersonation` , incluindo o corte duplo para a frente ('//').

Outras APIs podem exigir que *nenhum esquema ou anfitrião* esteja incluído no valor de âmbito, e espere apenas o ID da aplicação (a GUID) e o nome do âmbito, por exemplo:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Se o recurso a jusante não estiver sob o seu controlo, poderá ter de experimentar diferentes formatos de valor de âmbito (por exemplo, com/sem esquema e hospedeiro) se receber `401` ou outros erros ao passar o token de acesso ao recurso.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Solicitar âmbitos dinâmicos para consentimento incremental

À medida que as funcionalidades fornecidas pela sua aplicação ou os seus requisitos mudam, pode solicitar permissões adicionais, conforme necessário, utilizando o parâmetro de âmbito. Tais *âmbitos dinâmicos* permitem que os seus utilizadores forneçam um consentimento incremental aos âmbitos.

Por exemplo, pode assinar no utilizador, mas inicialmente negar-lhes o acesso a quaisquer recursos. Mais tarde, pode dar-lhes a possibilidade de visualizarem o seu calendário, solicitando o âmbito do calendário no método de aquisição e obtendo o consentimento do utilizador para o fazer. Por exemplo, solicitando os `https://graph.microsoft.com/User.Read` `https://graph.microsoft.com/Calendar.Read` e os âmbitos.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Aquisição de fichas silenciosamente (a partir da cache)

A MSAL mantém uma cache simbólica (ou dois caches para aplicações confidenciais de clientes) e caches um símbolo depois de ter sido adquirido. Em muitos casos, tentar obter silenciosamente um símbolo adquirirá outro símbolo com mais miras baseadas num símbolo na cache. Também é capaz de refrescar um símbolo quando está perto de expirar (como a cache simbólica também contém um token de atualização).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendado para aplicações de clientes públicos

O código de aplicação deve primeiro tentar obter um símbolo silenciosamente da cache. Se a chamada de método retornar um erro ou exceção "UI necessário", tente adquirir um símbolo por outros meios.

Existem dois fluxos, no entanto, em que **não deve** tentar adquirir silenciosamente um símbolo:

- [As credenciais do cliente fluem,](msal-authentication-flows.md#client-credentials)que não utiliza a cache de ficha do utilizador, mas uma cache de ficha de aplicação. Este método cuida de verificar a cache simbólica da aplicação antes de enviar um pedido ao serviço de fichas de segurança (STS).
- [O código de autorização flui](msal-authentication-flows.md#authorization-code) em aplicações web, uma vez que resgata um código que a aplicação obteve através da assinatura no utilizador e que as permite consentir com mais âmbitos. Uma vez que um código e não uma conta é passado como um parâmetro, o método não pode olhar na cache antes de resgatar o código, que invoca uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Padrão de chamada recomendado em aplicativos web usando o fluxo de código de autorização

Para aplicações Web que utilizem o [fluxo de código de autorização OpenID Connect,](v2-protocols-oidc.md)o padrão recomendado nos controladores é:

- Instantiizar uma aplicação de cliente confidencial com uma cache simbólica com serialização personalizada.
- Adquirir o símbolo utilizando o fluxo de código de autorização

## <a name="acquiring-tokens"></a>Aquisição de fichas

Geralmente, o método de aquisição de um símbolo depende se é um cliente público ou uma aplicação de cliente confidencial.

### <a name="public-client-applications"></a>Aplicações de clientes públicos

Para aplicações de clientes públicos (desktop ou aplicativo móvel), você:

- Muitas vezes adquirem fichas interativamente, tendo o utilizador de iniciar súm em uma janela de UI ou pop-up.
- Pode [obter um símbolo silenciosamente para o utilizador inscrito](msal-authentication-flows.md#integrated-windows-authentication) utilizando a Autenticação Integrada do Windows (IWA/Kerberos) se a aplicação para desktop estiver a ser executada num computador Windows ligado a um domínio ou ao Azure.
- Pode [obter um token com um nome de utilizador e palavra-passe](msal-authentication-flows.md#usernamepassword) em aplicações de clientes de ambiente de trabalho .NET (não recomendado). Não utilize o nome de utilizador/palavra-passe em aplicações confidenciais do cliente.
- Pode adquirir um token através do fluxo de código do [dispositivo](msal-authentication-flows.md#device-code) em aplicações em execução em dispositivos que não têm um navegador web. O utilizador é fornecido com um URL e um código, que depois vai para um navegador web em outro dispositivo e introduz o código e assina dentro A Azure AD envia então um símbolo de volta para o dispositivo sem navegador.

### <a name="confidential-client-applications"></a>Aplicações confidenciais de clientes

Para aplicações confidenciais de clientes (web app, web API ou uma aplicação daemon como um serviço Windows), você:

- Adquira fichas **para a própria aplicação** e não para um utilizador, utilizando o [fluxo de credenciais](msal-authentication-flows.md#client-credentials)do cliente. Esta técnica pode ser usada para sincronizar ferramentas ou ferramentas que processam os utilizadores em geral e não para um utilizador específico.
- Utilize o [fluxo em nome de](msal-authentication-flows.md#on-behalf-of) uma API web para ligar para uma API em nome do utilizador. A aplicação é identificada com credenciais de cliente para adquirir um token com base numa afirmação do utilizador (SAML, por exemplo, ou um token JWT). Este fluxo é utilizado por aplicações que precisam de aceder a recursos de um determinado utilizador em chamadas de serviço-a-serviço.
- Adquira fichas utilizando o fluxo de [código de autorização](msal-authentication-flows.md#authorization-code) em aplicações web após o utilizador assinar através do URL de pedido de autorização. A aplicação OpenID Connect normalmente utiliza este mecanismo, que permite ao utilizador iniciar sação utilizando o ID Aberto e, em seguida, aceder às APIs da web em nome do utilizador.

## <a name="authentication-results"></a>Resultados da autenticação

Quando o seu cliente solicita um token de acesso, a Azure AD também retorna um resultado de autenticação que inclui metadados sobre o token de acesso. Esta informação inclui o prazo de validade do token de acesso e os âmbitos para os quais é válido. Estes dados permitem que a sua app faça o caching inteligente de tokens de acesso sem ter de analisar o próprio token de acesso. O resultado da autenticação expõe:

- O [símbolo de acesso para](access-tokens.md) a API web para aceder a recursos. Esta corda é geralmente um JWT codificado base64, mas o cliente nunca deve olhar dentro do token de acesso. O formato não é garantido para permanecer estável, e pode ser encriptado para o recurso. As pessoas que escrevem código dependendo do conteúdo simbólico de acesso ao cliente é uma das fontes mais comuns de erros e quebra lógica do cliente.
- O [símbolo de ID para](id-tokens.md) o utilizador (um JWT).
- A validade do token, que indica a data/hora em que o token expira.
- A identificação do inquilino contém o inquilino no qual o utilizador foi encontrado. Para os utilizadores convidados (cenários Azure AD B2B), o ID do inquilino é o inquilino convidado, não o inquilino único. Quando o token é entregue em nome de um utilizador, o resultado da autenticação também contém informações sobre este utilizador. Para fluxos confidenciais de clientes onde são solicitadas fichas sem utilizador (para a aplicação), esta informação do utilizador é nula.
- As miras para as quais o símbolo foi emitido.
- O ID único para o utilizador.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>(Avançado) Acede aos tokens em cache do utilizador em aplicativos e serviços de fundo

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Passos seguintes

Se estiver a utilizar o MSAL para Java, aprenda sobre [a serialização de cache de fichas personalizadas em MSAL para Java.](msal-java-token-cache-serialization.md)

Saiba mais [sobre erros e exceções](msal-handling-exceptions.md)de manuseamento .