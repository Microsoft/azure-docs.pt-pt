---
title: Gerir tokens (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre a aquisição e colocação em cache tokens utilizando o Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546073"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Adquirir e colocação em cache tokens com MSAL
[Tokens de acesso](access-tokens.md) permitem que os clientes chamar as APIs protegidas pelo Azure web com segurança. Existem várias formas de adquirir um token com o Microsoft Authentication Library (MSAL). Algumas formas requerem interações do usuário por meio de um navegador da web. Alguns não requerem qualquer interações de utilizador. Em geral, a forma de adquirir um token depende se a aplicação é uma aplicação de cliente público (aplicação de ambiente de trabalho ou móvel) ou um aplicativo de cliente confidencial (aplicação Web, Web API ou o daemon de aplicativo, como um serviço do Windows).

A MSAL armazena em cache um token após ela foi adquirida.  Código da aplicação deve tentar obter um token silenciosamente (da cache), em primeiro lugar, antes de adquirir um token através de outros meios.

Também pode limpar a cache de token, o que é obtida ao remover as contas de cache. Isto não remove o cookie de sessão que está no navegador, no entanto.

## <a name="scopes-when-acquiring-tokens"></a>Âmbitos quando aquisição de tokens
[Âmbitos](v2-permissions-and-consent.md) são as permissões que expõe uma API web para aplicativos de cliente solicitar acesso a. Aplicativos cliente solicitar o consentimento do utilizador para estes âmbitos quando são efetuados pedidos de autenticação para obter os tokens para aceder a APIs web. A MSAL permite-lhe obter os tokens de aceder ao Azure AD para desenvolvedores (v1.0) e plataforma de identidade da Microsoft (v2.0) APIs. protocolo de v2.0 utiliza âmbitos em vez de recursos nos pedidos. Para obter mais informações, leia [comparação de versões v1.0 e v2.0](active-directory-v2-compare.md). Com base na configuração da API web da versão do token, que ela aceita, o ponto final v2.0 devolve o token de acesso a MSAL.

Um número de MSAL adquirir métodos token exigir um *âmbitos* parâmetro. Este parâmetro é apenas uma lista de cadeias de caracteres que declarar as permissões pretendidas e os recursos que são solicitados. Âmbitos conhecidos são os [permissões do Microsoft Graph](/graph/permissions-reference).

Também é possível no MSAL para aceder aos recursos da versão 1.0. Para obter mais informações, leia [âmbitos para uma aplicação de v1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Pedir âmbitos específicos para uma API web
Quando seu aplicativo precisa para pedir tokens com permissões específicas de um recurso de API, terá de passar os âmbitos que contenha a aplicação de URI de ID da API no formato abaixo:  *&lt;URI do ID&gt; / &lt;âmbito&gt;*

Por exemplo, âmbitos para a Microsoft Graph API: `https://graph.microsoft.com/User.Read`

Ou, por exemplo, os âmbitos para uma API web personalizada: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Para o Microsoft Graph API, apenas, um valor de âmbito `user.read` mapeia para `https://graph.microsoft.com/User.Read` formatar e pode ser utilizado alternadamente.

> [!NOTE]
> Determinados web APIs como a API do Azure Resource Manager (https://management.core.windows.net/) esperar à direita ' /' no público-alvo de afirmação (aud) do token de acesso. Neste caso, é importante passar o âmbito como https://management.core.windows.net//user_impersonation (tenha em atenção que a barra invertida dupla) para o token seja válido na API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Âmbitos de dinâmicos de pedido de consentimento incremental
Ao criar aplicativos usando a versão 1.0, tinha que registe-se o conjunto completo de permissões (âmbitos estáticos) exigidas pela aplicação para o utilizador dar consentimento ao tempo de início de sessão. Na versão 2.0, pode solicitar permissões adicionais conforme necessário, utilizando o parâmetro de âmbito. Estas são designadas por âmbitos dinâmicos e permitem ao utilizador para fornecer consentimento incremental para âmbitos.

Por exemplo, pode inicialmente inicia a sessão do utilizador e negá-los a qualquer tipo de acesso. Mais tarde, pode lhes dar a capacidade para ler o calendário do usuário solicitando o âmbito de calendário, os métodos de token de aquisição e obter o consentimento do utilizador.

Por exemplo: `https://graph.microsoft.com/User.Read` e `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Aquisição de tokens silenciosamente (da cache)
A MSAL mantém um cache de token (ou dois caches para aplicativos cliente confidencial) e coloca em cache um token após ela foi adquirida.  Em muitos casos, tentando silenciosamente obter um token irá obter outro token com mais âmbitos com base num token na cache. Também é capaz de atualizar um token quando ele está ficando prestes a expirar (como o cache de tokens também contém um token de atualização).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendada para aplicações de cliente público
Código da aplicação deve tentar obter um token silenciosamente (da cache), em primeiro lugar.  Se a chamada de método retornar um "Da interface do Usuário necessária" erro ou exceção, tente adquirir um token através de outros meios. 

No entanto, existem dois fluxos antes do qual **não deve** tentar silenciosamente adquirir um token:

- [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials), que não utiliza a cache de token de utilizador, mas uma cache de tokens de aplicação. Este método se encarrega de verificação desta cache de token de aplicação antes de enviar um pedido para o STS.
- [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) nas aplicações Web, como ele redeems um código que o aplicativo obteve por iniciar a sessão do utilizador e fazendo com que eles consentimento para obter mais âmbitos. Uma vez que um código é transmitido como um parâmetro e não uma conta, o método não é possível verificar na cache antes de obter o código, o que requer que, de qualquer forma, uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Recomendado padrão de chamada em aplicações Web com o fluxo de código de autorização 
Para aplicações Web que utilizam o [fluxo de código de autorização de OpenID Connect](v2-protocols-oidc.md), o padrão recomendado em controladores de é:

- Criar uma instância de uma aplicação de cliente confidenciais com uma cache de token com a serialização personalizada. 
- Adquirir o token com o fluxo de código de autorização

## <a name="acquiring-tokens"></a>Aquisição de tokens
Em geral, o método de aquisição de um token depende quer se trate de um cliente público ou a aplicação de cliente confidencial.

### <a name="public-client-applications"></a>Aplicativos cliente público
Para aplicativos de cliente público (aplicação de ambiente de trabalho ou móvel),:
- Muitas vezes, adquira tokens interativamente, se o utilizador iniciar sessão através de uma interface do Usuário ou de uma janela pop-up.
- Pode [obter um token automaticamente para o utilizador com sessão iniciada](msal-authentication-flows.md#integrated-windows-authentication) usar autenticação integrada do Windows (IWA/Kerberos) se o aplicativo de área de trabalho está em execução num computador Windows associados a um domínio ou para o Azure.
- Pode [obter um token com um nome de utilizador e palavra-passe](msal-authentication-flows.md#usernamepassword) no cliente de desktop do .NET framework aplicativos, mas isso não é recomendado. Não utilize o nome de utilizador/palavra-passe em aplicativos cliente confidencial.
- Pode adquirir um token através do [fluxo de código de dispositivo](msal-authentication-flows.md#device-code) em aplicativos em execução em dispositivos que não têm um navegador da web. O utilizador é fornecido com um URL e um código, que, em seguida, vai para um navegador da web noutro dispositivo e introduzir o código e inicia sessão.  O Azure AD, em seguida, envia um token de volta para o dispositivo sem browser.

### <a name="confidential-client-applications"></a>Aplicações de cliente confidencial 
Para aplicativos de clientes confidenciais (aplicação Web, Web API ou o daemon de aplicativo como um serviço do Windows),:
- Adquira tokens **da própria aplicação** e não para um utilizador, utilizando o [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials). Isso pode ser usado para ferramentas de sincronização, ou ferramentas que processam os usuários em geral e não um utilizador específico. 
- Utilize o [fluxo em-nome-de](msal-authentication-flows.md#on-behalf-of) para uma API web para chamar uma API em nome do utilizador. O aplicativo esteja identificado com credenciais de cliente para adquirir um token com base numa asserção (SAML por exemplo, ou um token JWT) de utilizador. Este fluxo é utilizado por aplicações que precisam de aceder a recursos de um determinado usuário em chamadas de serviço para serviço.
- Adquirir tokens utilizando o [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) nas aplicações web, depois do utilizador inicia a sessão por meio da autorização URL do pedido. Aplicação de ligação OpenID normalmente usar esse mecanismo, o que permite ao utilizador ligar de início de sessão através do ID de aberto e, em seguida, APIs da web do acesso em nome do utilizador.


## <a name="authentication-results"></a>Resultados de autenticação 
Quando o cliente solicita um token de acesso, o Azure AD também devolve um resultado de autenticação que inclui alguns metadados sobre o token de acesso. Estas informações incluem a hora de expiração do token de acesso e os âmbitos para a qual é válido. Estes dados permite à aplicação fazer a colocação em cache inteligente de tokens de acesso sem ter de analisar o token de acesso em si.  Expõe o resultado de autenticação:

- O [token de acesso](access-tokens.md) para a API web para aceder aos recursos. Esta é uma cadeia de caracteres, normalmente, um base64 codificado JWT, mas o cliente nunca deve ser dentro do token de acesso. O formato não é garantido permaneça estável e podem ser encriptados para o recurso. As pessoas escrever códigos, dependendo do conteúdo de token de acesso no cliente é uma das maiores origens de erros e quebras de lógica de cliente.
- O [token de ID](id-tokens.md) para o usuário (este é um JWT).
- A expiração do token, que indica a data/hora quando o token expira.
- O ID de inquilino contém o inquilino no qual o utilizador foi encontrado. Para os utilizadores convidados (cenários do Azure AD B2B), o ID de inquilino é o inquilino de convidado, o inquilino não exclusivo. Quando o token é entregue no nome de um utilizador, o resultado da autenticação também contém informações sobre este utilizador. Para fluxos de cliente confidencial em que os tokens são solicitados com nenhum utilizador (para o aplicativo), estas informações de utilizador são nulas.
- Os âmbitos para a qual o token foi emitido.
- O ID exclusivo para o utilizador.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [tratamento de erros e exceções](msal-handling-exceptions.md). 
