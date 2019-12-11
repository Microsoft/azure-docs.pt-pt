---
title: Adquirir tokens de cache & com MSAL | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre como adquirir e armazenar em cache tokens usando a MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3060f79f85db6504c38ba4fc6b7d3df97f0edb55
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963567"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Adquirir e armazenar tokens em cache usando a MSAL (biblioteca de autenticação da Microsoft)

Os [tokens de acesso](access-tokens.md) permitem que os clientes chamem com segurança APIs da Web protegidas pelo Azure. Há várias maneiras de adquirir um token usando a MSAL (biblioteca de autenticação da Microsoft). Algumas maneiras exigem interações com o usuário por meio de um navegador da Web. Alguns não exigem nenhuma interação do usuário. Em geral, a maneira de adquirir um token depende de se o aplicativo é um aplicativo cliente público (desktop ou aplicativo móvel) ou um aplicativo cliente confidencial (aplicativo Web, API da Web ou aplicativos daemon, como um serviço do Windows).

MSAL armazena em cache um token depois que ele é adquirido.  O código do aplicativo deve tentar obter um token silenciosamente (do cache), primeiro antes de adquirir um token por outros meios.

Você também pode limpar o cache de token, que é obtido removendo as contas do cache. No entanto, isso não remove o cookie de sessão que está no navegador.

## <a name="scopes-when-acquiring-tokens"></a>Escopos ao adquirir tokens

[Escopos](v2-permissions-and-consent.md) são as permissões que uma API Web expõe para aplicativos cliente solicitarem acesso ao. Aplicativos cliente solicitam o consentimento do usuário para esses escopos ao fazer solicitações de autenticação para obter tokens para acessar as APIs da Web. O MSAL permite que você obtenha tokens para acessar as APIs do Azure AD para desenvolvedores (v 1.0) e plataforma Microsoft Identity (v 2.0). o protocolo v 2.0 usa escopos em vez de recurso nas solicitações. Para obter mais informações, leia [comparação v 1.0 e v 2.0](active-directory-v2-compare.md). Com base na configuração da API Web da versão do token que ele aceita, o ponto de extremidade v 2.0 retorna o token de acesso para MSAL.

Um número de métodos de token de aquisição MSAL requer um parâmetro de *escopos* . Esse parâmetro é uma lista simples de cadeias de caracteres que declaram as permissões e os recursos desejados que são solicitados. Os escopos bem conhecidos são as [permissões de Microsoft Graph](/graph/permissions-reference).

Também é possível em MSAL acessar recursos v 1.0. Para obter mais informações, leia [escopos para um aplicativo v 1.0](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Escopos específicos de solicitação para uma API Web

Quando seu aplicativo precisar solicitar tokens com permissões específicas para uma API de recurso, você precisará passar os escopos que contêm o URI de ID do aplicativo da API no formato abaixo: *&lt;URI da ID do aplicativo&gt;/&lt;escopo&gt;*

Por exemplo, escopos para Microsoft Graph API: `https://graph.microsoft.com/User.Read`

Ou, por exemplo, escopos para uma API Web personalizada: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Para a API de Microsoft Graph, apenas um valor de escopo `user.read` é mapeado para `https://graph.microsoft.com/User.Read` formato e pode ser usado de forma intercambiável.

> [!NOTE]
> Determinadas APIs da Web, como Azure Resource Manager API (https://management.core.windows.net/) esperam um '/' à direita na declaração de audiência (AUD) do token de acesso. Nesse caso, é importante passar o escopo como https://management.core.windows.net//user_impersonation (Observe a barra dupla), para que o token seja válido na API.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Solicitar escopos dinâmicos para consentimento incremental

Ao criar aplicativos usando a v 1.0, era necessário registrar o conjunto completo de permissões (escopos estáticos) exigido pelo aplicativo para que o usuário consentisse no momento do logon. Na v 2.0, você pode solicitar permissões adicionais conforme necessário usando o parâmetro de escopo. Eles são chamados de escopos dinâmicos e permitem que o usuário forneça um consentimento incremental aos escopos.

Por exemplo, você pode entrar inicialmente no usuário e negar a eles qualquer tipo de acesso. Posteriormente, você pode conceder a eles a capacidade de ler o calendário do usuário solicitando o escopo do calendário nos métodos de token de aquisição e obter o consentimento do usuário.

Por exemplo: `https://graph.microsoft.com/User.Read` e `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Adquirindo tokens silenciosamente (do cache)

O MSAL mantém um cache de token (ou dois caches para aplicativos cliente confidenciais) e armazena em cache um token depois que ele é adquirido.  Em muitos casos, a tentativa de obter um token de forma silenciosa adquirirá outro token com mais escopos com base em um token no cache. Ele também é capaz de atualizar um token quando está ficando próximo da expiração (pois o cache do token também contém um token de atualização).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Padrão de chamada recomendado para aplicativos cliente públicos

O código do aplicativo deve tentar obter um token silenciosamente (do cache), primeiro.  Se a chamada do método retornar um erro ou exceção de "IU necessária", tente adquirir um token por outros meios. 

No entanto, há dois fluxos antes dos quais você **não deve** tentar adquirir um token silenciosamente:

- [fluxo de credenciais de cliente](msal-authentication-flows.md#client-credentials), que não usa o cache de token de usuário, mas um cache de token de aplicativo. Esse método cuida da verificação deste cache de token de aplicativo antes de enviar uma solicitação para o STS.
- o [fluxo de código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web, pois ele resgata um código que o aplicativo obteve ao entrar no usuário e concede-lhes consentimento para mais escopos. Como um código é passado como um parâmetro, e não uma conta, o método não pode procurar no cache antes de resgatar o código, o que requer, de qualquer forma, uma chamada para o serviço.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Padrão de chamada recomendado em aplicativos Web usando o fluxo de código de autorização

Para aplicativos Web que usam o [fluxo de código de autorização OpenID Connect](v2-protocols-oidc.md), o padrão recomendado nos controladores é:

- Crie uma instância de um aplicativo cliente confidencial com um cache de token com serialização personalizada. 
- Adquirir o token usando o fluxo do código de autorização

## <a name="acquiring-tokens"></a>Adquirindo tokens

Em geral, o método de aquisição de um token depende se ele é um cliente público ou um aplicativo cliente confidencial.

### <a name="public-client-applications"></a>Aplicativos cliente públicos

Para aplicativos cliente públicos (desktop ou aplicativo móvel), você:
- Geralmente, adquire tokens interativamente, fazendo com que o usuário entre por meio de uma janela de interface de usuário ou pop-up.
- Pode [obter um token silenciosamente para o usuário conectado](msal-authentication-flows.md#integrated-windows-authentication) usando a autenticação integrada do Windows (IWA/Kerberos) se o aplicativo da área de trabalho estiver em execução em um computador com Windows ingressado em um domínio ou no Azure.
- Pode [obter um token com um nome de usuário e senha](msal-authentication-flows.md#usernamepassword) em aplicativos cliente de desktop do .NET Framework, mas isso não é recomendado. Não use nome de usuário/senha em aplicativos cliente confidenciais.
- Pode adquirir um token por meio do [fluxo de código do dispositivo](msal-authentication-flows.md#device-code) em aplicativos em execução em dispositivos que não têm um navegador da Web. O usuário recebe uma URL e um código que, em seguida, vai para um navegador da Web em outro dispositivo e insere o código e entra.  Em seguida, o Azure AD envia um token de volta para o dispositivo sem navegador.

### <a name="confidential-client-applications"></a>Aplicativos cliente confidenciais

Para aplicativos cliente confidenciais (aplicativo Web, API da Web ou aplicativos daemon, como um serviço do Windows), você:
- Adquira tokens **para o aplicativo em si** e não para um usuário, usando o [fluxo de credenciais do cliente](msal-authentication-flows.md#client-credentials). Isso pode ser usado para ferramentas de sincronização ou ferramentas que processam os usuários em geral e não um usuário específico. 
- Use o [fluxo em nome de](msal-authentication-flows.md#on-behalf-of) de uma API da Web para chamar uma API em nome do usuário. O aplicativo é identificado com as credenciais do cliente para adquirir um token com base em uma asserção do usuário (SAML, por exemplo, ou um token JWT). Esse fluxo é usado por aplicativos que precisam acessar recursos de um usuário específico em chamadas de serviço a serviço.
- Adquira tokens usando o [fluxo do código de autorização](msal-authentication-flows.md#authorization-code) em aplicativos Web depois que o usuário entrar por meio da URL de solicitação de autorização. O aplicativo OpenID Connect normalmente usa esse mecanismo, que permite que o usuário entre usando Open ID Connect e, em seguida, acesse APIs da Web em nome do usuário.

## <a name="authentication-results"></a>Resultados da autenticação

Quando o cliente solicita um token de acesso, o AD do Azure também retorna um resultado de autenticação que inclui alguns metadados sobre o token de acesso. Essas informações incluem o tempo de expiração do token de acesso e os escopos para os quais ele é válido. Esses dados permitem que seu aplicativo faça o cache inteligente de tokens de acesso sem precisar analisar o token de acesso em si.  O resultado da autenticação expõe:

- O [token de acesso](access-tokens.md) para a API da Web acessar recursos. Essa é uma cadeia de caracteres, geralmente um JWT codificado em base64, mas o cliente nunca deve olhar dentro do token de acesso. Não há garantia de que o formato permaneça estável e possa ser criptografado para o recurso. As pessoas que escrevem código dependendo do conteúdo do token de acesso no cliente são uma das maiores fontes de erros e quebras lógicas do cliente.
- O [token de ID](id-tokens.md) para o usuário (este é um JWT).
- A expiração do token, que informa a data/hora em que o token expira.
- A ID do locatário contém o locatário no qual o usuário foi encontrado. Para usuários convidados (cenários B2B do Azure AD), a ID do locatário é o locatário convidado, não o locatário exclusivo. Quando o token é entregue no nome de um usuário, o resultado da autenticação também contém informações sobre esse usuário. Para fluxos de cliente confidenciais em que os tokens são solicitados sem usuário (para o aplicativo), essas informações de usuário são nulas.
- Os escopos para os quais o token foi emitido.
- A ID exclusiva para o usuário.

## <a name="next-steps"></a>Passos seguintes

Se você estiver usando o MSAL para Java, saiba mais sobre a [serialização de cache de token personalizada em MSAL para Java](msal-java-token-cache-serialization.md).

Saiba mais sobre como [tratar erros e exceções](msal-handling-exceptions.md).
