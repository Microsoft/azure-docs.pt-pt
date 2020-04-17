---
title: Adquirir fichas de cache & com a MSAL Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a aquisição e o cache tokens usando a Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 647dff9e6401322371ef795a25ca5ced2b517e9c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534589"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Adquirir e cache tokens utilizando a biblioteca de autenticação da Microsoft (MSAL)

[Os tokens](access-tokens.md) de acesso permitem que os clientes liguem de forma segura para as APIs web protegidas pelo Azure. Existem muitas formas de adquirir um símbolo usando a Microsoft Authentication Library (MSAL). Algumas formas requerem interações do utilizador através de um navegador web. Alguns não requerem interações do utilizador. Em geral, a forma de adquirir um símbolo depende de se a aplicação for uma aplicação de cliente público (desktop ou aplicação móvel) ou uma aplicação confidencial do cliente (aplicação web, Web API ou aplicação daemon como um serviço Windows).

A MSAL caches um símbolo depois de ter sido adquirido.  O código de aplicação deve tentar obter um símbolo silenciosamente (a partir da cache), primeiro, antes de adquirir um símbolo por outros meios.

Também pode limpar a cache simbólica, que é conseguida removendo as contas da cache. Isto não remove o cookie de sessão que está no navegador, no entanto.

## <a name="scopes-when-acquiring-tokens"></a>Âmbitos ao adquirir fichas

[Os âmbitos](v2-permissions-and-consent.md) são as permissões que uma API web expõe para aplicações de clientes para solicitar acesso. As aplicações do cliente solicitam o consentimento do utilizador para estes âmbitos ao efecêu-los pedidos de autenticação para obter fichas para aceder às APIs web. O MSAL permite-lhe obter fichas para aceder a AD Azure para programadores (v1.0) e plataforma de identidade Microsoft (v2.0) APIs. v2.0 protocolo utiliza âmbitos em vez de recursos nos pedidos. Para mais informações, leia [a comparação v1.0 e v2.0.](active-directory-v2-compare.md) Com base na configuração da API web da versão simbólica que aceita, o ponto final v2.0 devolve o sinal de acesso ao MSAL.

Alguns métodos de aquisição de mSAL requerem um parâmetro de *âmbito.* Este parâmetro é uma simples lista de cordas que declaram as permissões e recursos desejados que são solicitados. Os âmbitos bem conhecidos são as [permissões](/graph/permissions-reference)do Microsoft Graph.

Também é possível na MSAL aceder aos recursos v1.0. Para mais informações, leia [Os Âmbitos para uma aplicação v1.0.](msal-v1-app-scopes.md)

### <a name="request-specific-scopes-for-a-web-api"></a>Solicitar âmbitos específicos para uma API web

Quando a sua aplicação necessitar de solicitar fichas com permissões específicas para um Recurso API, terá de passar os âmbitos que contêm a aplicação ID URI da API no formato abaixo: * &lt;app ID URI&gt;/&lt;scope&gt;*

Por exemplo, âmbitos para Microsoft Graph API:`https://graph.microsoft.com/User.Read`

Ou, por exemplo, os âmbitos para uma API web personalizada:`api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Para o Microsoft Graph API, `user.read` apenas, um âmbito de mapas de valor para `https://graph.microsoft.com/User.Read` formato e pode ser usado alternadamente.

> [!NOTE]
> Algumas APIs web, como ahttps://management.core.windows.net/) API gestora de recursos do Azure , esperam um '/' rasto na reivindicação do público (aud) do sinal de acesso. Neste caso, é importante passar o https://management.core.windows.net//user_impersonation âmbito como (note o duplo corte), para que o símbolo seja válido na API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Solicitar âmbitos dinâmicos para consentimento incremental

Ao construir aplicações utilizando v1.0, teve de registar o conjunto completo de permissões (âmbitos estáticos) exigidos pelo pedido para que o utilizador consinta no momento do login. Em v2.0, pode solicitar permissões adicionais conforme necessário utilizando o parâmetro de âmbito. Estes são chamados de âmbitos dinâmicos e permitem ao utilizador fornecer consentimento incremental aos âmbitos.

Por exemplo, pode inicialmente iniciar sessão no utilizador e negar-lhes qualquer tipo de acesso. Mais tarde, pode dar-lhes a capacidade de ler o calendário do utilizador solicitando o âmbito do calendário nos métodos de aquisição e obter o consentimento do utilizador.

Por exemplo: `https://graph.microsoft.com/User.Read` e`https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Adquirir fichas silenciosamente (a partir da cache)

A MSAL mantém uma cache simbólica (ou duas caches para aplicações confidenciais de clientes) e caches um símbolo após a sua aquisição.  Em muitos casos, tentar obter silenciosamente um símbolo adquirirá outro símbolo com mais âmbitos baseados num símbolo na cache. Também é capaz de refrescar um símbolo quando está perto da expiração (como a cache token também contém um token refrescante).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendado para aplicações de clientes públicos

O código de aplicação deve tentar obter um símbolo silenciosamente (a partir da cache), primeiro.  Se a chamada do método devolver um erro ou exceção "UI exigido", tente adquirir um símbolo por outros meios.

No entanto, há dois fluxos antes dos quais **não deve** tentar adquirir silenciosamente um símbolo:

- fluxo de [credenciais](msal-authentication-flows.md#client-credentials)de cliente , que não utiliza a cache token do utilizador, mas uma cache simbólica de aplicação. Este método trata de verificar esta cache token de aplicação antes de enviar um pedido para o STS.
- [fluxo](msal-authentication-flows.md#authorization-code) de código de autorização em aplicações web, uma vez que resgata um código que a aplicação obteve através da assinatura do utilizador, e tendo-lhes o consentimento para mais âmbitos. Uma vez que um código é passado como parâmetro, e não como uma conta, o método não pode olhar para a cache antes de resgatar o código, que requer, de qualquer forma, uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Padrão de chamada recomendado em aplicações web usando o fluxo do Código de Autorização

Para aplicações Web que utilizem o fluxo de código de [autorização OpenID Connect,](v2-protocols-oidc.md)o padrão recomendado nos controladores é:

- Instantiate uma aplicação cliente confidencial com uma cache simbólica com serialização personalizada.
- Adquirir o símbolo utilizando o fluxo de código de autorização

## <a name="acquiring-tokens"></a>Aquisição de fichas

Geralmente, o método de aquisição de um símbolo depende se é um cliente público ou uma aplicação confidencial do cliente.

### <a name="public-client-applications"></a>Aplicações de clientes públicos

Para aplicações de clientes públicos (desktop ou aplicativo móvel), você:
- Muitas vezes adquire fichas interativamente, tendo o utilizador iniciar sessão através de uma ui ou janela pop-up.
- Pode [obter um sinal silenciosamente para o utilizador inscrito](msal-authentication-flows.md#integrated-windows-authentication) usando a Autenticação Integrada do Windows (IWA/Kerberos) se a aplicação de ambiente de trabalho estiver a funcionar num computador Windows ligado a um domínio ou ao Azure.
- Pode [obter um símbolo com um nome](msal-authentication-flows.md#usernamepassword) de utilizador e senha em aplicações de cliente de ambiente de trabalho .NET, mas isso não é recomendado. Não utilize o nome de utilizador/palavra-passe em aplicações confidenciais do cliente.
- Pode adquirir um símbolo através do fluxo de código do [dispositivo](msal-authentication-flows.md#device-code) em aplicações em execução em dispositivos que não têm um navegador web. O utilizador é fornecido com um URL e um código, que depois vai para um navegador web em outro dispositivo e introduz o código e entra no sinal.  A Azure AD envia então um sinal de volta para o dispositivo sem navegador.

### <a name="confidential-client-applications"></a>Aplicações confidenciais de clientes

Para aplicações confidenciais de clientes (aplicação web, Web API ou aplicação daemon como um serviço Windows), você:
- Adquirir fichas **para a própria aplicação** e não para um utilizador, utilizando o fluxo de [credenciais](msal-authentication-flows.md#client-credentials)do cliente . Isto pode ser usado para sincronizar ferramentas, ou ferramentas que processam os utilizadores em geral e não um utilizador específico.
- Utilize o [fluxo em nome de](msal-authentication-flows.md#on-behalf-of) um API web para chamar uma API em nome do utilizador. A aplicação é identificada com credenciais de cliente para adquirir um símbolo com base numa afirmação do utilizador (SAML, por exemplo, ou um símbolo JWT). Este fluxo é utilizado por aplicações que precisam de aceder a recursos de um determinado utilizador em chamadas de serviço a serviço.
- Adquira fichas utilizando o fluxo de código de [autorização](msal-authentication-flows.md#authorization-code) em aplicações web após o utilizador assinar através do URL de pedido de autorização. A aplicação OpenID Connect utiliza normalmente este mecanismo, que permite ao utilizador iniciar sessão utilizando o open ID connect e, em seguida, aceder a APIs web em nome do utilizador.

## <a name="authentication-results"></a>Resultados da autenticação

Quando o seu cliente solicita um sinal de acesso, a Azure AD também devolve um resultado de autenticação que inclui alguns metadados sobre o token de acesso. Estas informações incluem o tempo de validade do token de acesso e os âmbitos para os quais é válido. Estes dados permitem que a sua aplicação faça um cache inteligente de fichas de acesso sem ter que analisar o próprio token de acesso.  O resultado da autenticação expõe:

- O [sinal de acesso](access-tokens.md) para a Web API para aceder a recursos. Esta é uma corda, geralmente um JWT codificado base64, mas o cliente nunca deve olhar para dentro do sinal de acesso. O formato não é garantido para permanecer estável e pode ser encriptado para o recurso. As pessoas que escrevem código dependendo do conteúdo simbólico de acesso ao cliente é uma das maiores fontes de erros e quebras de lógica do cliente.
- O [símbolo de identificação](id-tokens.md) para o utilizador (este é um JWT).
- A expiração do símbolo, que indica a data/hora em que o símbolo expira.
- A identificação do inquilino contém o inquilino no qual o utilizador foi encontrado. Para os utilizadores convidados (cenários Azure AD B2B), o ID do inquilino é o inquilino convidado, não o inquilino único. Quando o token é entregue em nome de um utilizador, o resultado da autenticação também contém informações sobre este utilizador. Para fluxos confidenciais de clientes onde são solicitados tokens sem utilizador (para a aplicação), esta informação do utilizador é nula.
- Os âmbitos para os quais o símbolo foi emitido.
- A identificação única para o utilizador.

## <a name="next-steps"></a>Passos seguintes

Se estiver a usar o MSAL para Java, aprenda sobre a serialização de [cache personalizada em MSAL para Java](msal-java-token-cache-serialization.md).

Aprenda sobre o manuseamento de [erros e exceções.](msal-handling-exceptions.md)
