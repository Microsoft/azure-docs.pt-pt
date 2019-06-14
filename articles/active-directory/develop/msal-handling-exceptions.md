---
title: Erros e exceções (MSAL) | Azure
description: Saiba como lidar com erros e exceções, acesso condicional e de declarações desafiar em aplicativos de MSAL.
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
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9a1b8cf0fe74554c78f2d06d601231ed2740172a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052351"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Manipulação de exceções e erros com MSAL
Exceções no Microsoft Authentication Library (MSAL) destinam-se para os programadores de aplicações resolver problemas e não para apresentar aos utilizadores finais. Mensagens de exceção não estão localizadas.

Durante o processamento de exceções e erros, pode utilizar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Exceções de .NET
Durante o processamento de exceções, pode usar o próprio tipo de exceção e o `ErrorCode` membro para distinguir entre exceções. Os valores dos `ErrorCode` são constantes de tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Também pode ter os campos de examinar [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) é gerada, o erro código pode conter um código que pode encontrar no [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Exceções comuns
Aqui estão as exceções comuns que podem ser geradas e alguns possíveis mitigações.

| Exceção | Código de erro | Mitigação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O utilizador ou o administrador não permitiu utilizar a aplicação com o ID '{appId}' com o nome "{appName}". Envie um pedido de autorização interativo para este utilizador e o recurso.| Tem de obter o consentimento do utilizador primeiro. Se não estiver a utilizar o .NET Core (que não tem qualquer interface do Usuário da Web), ligue para (uma vez apenas) `AcquireTokeninteractive`. Se estiver a utilizar o .NET core ou não desejar usar uma `AcquireTokenInteractive`, o usuário pode navegar para um URL para dar consentimento: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . Para chamar `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: É pedido ao utilizador para utilizar a autenticação multifator.| Não existe nenhuma forma de atenuar - se a MFA está configurado para o seu inquilino e AAD decide para impor a ele, terá de contingência para um fluxo de interativo como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: O tipo de concessão não é suportado através da */comuns* ou */consumers* pontos de extremidade. Utilize o */organizations* ou ponto de extremidade específico de inquilino. Utilizou */comuns*.| Conforme explicado na mensagem do Azure AD, a autoridade tem de ter um inquilino ou caso contrário */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O corpo do pedido tem de conter o seguinte parâmetro: "client_secret ou client_assertion'.| Isto pode acontecer se a aplicação não foi registada como um aplicativo de cliente público no Azure AD. No portal do Azure, editar o manifesto da sua aplicação e o conjunto a `allowPublicClient` para `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user mensagem: Não foi possível identificar o utilizador com sessão iniciada| A biblioteca não foi possível consultar o atual Windows utilizador com sessão iniciada ou este utilizador não é AD ou AAD associado (utilizadores de associado ao local de trabalho não são suportados). Atenuação 1: no UWP, verifique que a aplicação tem as seguintes capacidades: Autenticação empresarial, redes privadas (cliente e servidor), as informações de conta de utilizador. Atenuação 2: Implementar sua própria lógica para obter o nome de utilizador (por exemplo, john@contoso.com) e utilizar o `AcquireTokenByIntegratedWindowsAuth` formulário que usa o nome de utilizador.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Este método se baseia num protocolo exposto pelo Active Directory (AD). Se um utilizador foi criado no Azure Active Directory sem AD de segurança (usuário "gerenciado"), este método irá falhar. Os utilizadores criados no AD e apoiado pelo AAD ("federados" utilizadores) podem se beneficiar este método não interativo de autenticação. Atenuação: Utilize a autenticação interativa.|

## <a name="javascript-errors"></a>Erros de JavaScript

Msal fornece objetos de erro que abstraem e classificar os diferentes tipos de erros comuns e possuem uma interface para aceder aos detalhes específicos dos erros, tais como mensagens de erro para manipulá-las adequadamente.

**Objeto de erro**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Ao estender a classe de erro, tem acesso às seguintes propriedades:
* **AuthError.message:** Este é o mesmo que errorMessage.
* **AuthError.stack:** Rastreamento de pilha para erros gerados. Permite que o rastreio para o ponto de origem de erro.

**Tipos de erros**

Estão disponíveis os seguintes tipos de erro:

* *AuthError:* Classe de erro de base para a biblioteca de msal, também utilizada para erros inesperados.

* *ClientAuthError:* Classe de erro que indica um problema com a autenticação de cliente. A maioria dos erros que são provenientes da biblioteca será ClientAuthErrors. Estes podem ser erros, tais como chamar um método de início de sessão quando o início de sessão está em curso, os utilizadores a cancelar o início de sessão, etc.

* *ClientConfigurationError:* Classe de erro estendendo ClientAuthError gerada antes de pedidos são efetuados quando os parâmetros de configuração de utilizador especificado está incorreto ou em falta.

* *ServerError:* Classe de erro para representar as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Estes podem ser erros como formatos de pedido inválido ou parâmetros ou outros erros que impedem que o servidor de autenticação ou autorização de utilizador.

* *InteractionRequiredAuthError:* Classe de erro estendendo ServerError para representar os erros de servidor que requerem uma chamada interativa. Isso é emitido pelo `acquireTokenSilent` se é pedido ao utilizador para interagir com o servidor para fornecer as credenciais ou consentimento para autenticação/autorização. Códigos de erro incluem "interaction_required", "login_required", "consent_required".

Para redirecionar o tratamento de erros em fluxos de autenticação com métodos (`loginRedirect`, `acquireTokenRedirect`), terá de registar o retorno de chamada que é chamado com êxito ou falha depois de utilizar o redirecionamento `handleRedirectCallback()` método da seguinte forma:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a experiência de pop-up (`loginPopup`, `acquireTokenPopup`) retornar promessas, pelo que pode utilizar o padrão de promessa (. Then e .catch) para manipulá-las, conforme mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Erros de interação necessário

Quando é necessária uma interação de interface do Usuário, é devolvido um erro. Isso significa que foi efetuada uma tentativa de utilizar um método não interativo de aquisição de um token (por exemplo, `acquireTokenSilent`), mas MSAL não poderia fazê-lo silenciosamente. Razões possíveis são:

* tem de iniciar sessão
* precisa dar consentimento
* tem de aceder através de uma experiência de autenticação multifator.

A remediação é chamar um método interativo como `acquireTokenPopup` ou `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Desafios de afirmações e acesso condicionais
Ao obter tokens silenciosamente, seu aplicativo poderá receber erros quando um [desafio de afirmações de acesso condicional](conditional-access-dev-guide.md) como a política da MFA é necessária para uma API que está a tentar aceder.

O padrão para lidar com este erro é interativamente adquirir um token através de MSAL. Interativamente adquirir um token pede ao utilizador e dá a eles a oportunidade para satisfazer a política de acesso condicional necessário.

Em certos casos, ao chamar uma API que necessitam de acesso condicional, pode receber um desafio de afirmações no erro da API. Para a instância se a política de acesso condicional é fazer com que um dispositivo gerido (Intune) o erro será algo como [AADSTS53000: O dispositivo é necessário para ser gerido para aceder a este recurso](reference-aadsts-error-codes.md) ou algo parecido. Neste caso, pode transmitir as afirmações na chamada de token de aquisição, de modo a que é pedido ao utilizador para satisfazer a política apropriada.

### <a name="net"></a>.NET
Ao chamar uma API que necessitam de acesso condicional do MSAL.NET, seu aplicativo precisará lidar com exceções de desafio de afirmação. Isto irá aparecer como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) onde o [afirmações](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) propriedade não estará vazia.

Para lidar com o desafio de afirmação, terá de utilizar o `.WithClaim()` método da `PublicClientApplicationBuilder` classe.

### <a name="javascript"></a>JavaScript
Ao obter os tokens silenciosamente (usando `acquireTokenSilent`) a utilizar a msal, seu aplicativo poderá receber erros quando um [desafio de afirmações de acesso condicional](conditional-access-dev-guide.md) como a política da MFA é necessária para uma API que está a tentar aceder.

O padrão para lidar com este erro é fazer uma chamada interativa para adquirir o token no msal como `acquireTokenPopup` ou `acquireTokenRedirect` como no exemplo seguinte:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

Interativamente a adquirir o token pede ao utilizador e dá a eles a oportunidade para satisfazer a política de acesso condicional necessário.

Ao chamar uma API que necessitam de acesso condicional, pode receber um desafio de afirmações no erro da API. Neste caso, pode transmitir as afirmações devolvidas o erro como `extraQueryParameters` na chamada para adquirir tokens, de modo a que é pedido ao utilizador para satisfazer a política apropriada:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>A repetir depois de erros e exceções

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600
MSAL.NET implementa uma repetição simple-assim que o mecanismo de erros com o erro HTTP 500-600 de códigos.

### <a name="http-429"></a>HTTP 429
Quando o Token servidor STS (serviço) está demasiado ocupado devido a "demasiados pedidos", ele retorna um erro HTTP 429 uma dica sobre o quando pode tentar novamente (o campo de resposta Retry-After) como um atraso em segundos, ou uma data.

#### <a name="net"></a>.NET
O [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) superfícies de exceção `System.Net.Http.Headers.HttpResponseHeaders` como uma propriedade `namedHeaders`. Portanto, pode aproveitar informações adicionais do código de erro para melhorar a fiabilidade das suas aplicações. No caso que acabei de descrever, pode utilizar o `RetryAfterproperty` (do tipo `RetryConditionHeaderValue`) e de computação quando deve repetir.

Eis um exemplo de um aplicativo de daemon (usando o fluxo de credenciais de cliente), mas pode adaptar que para qualquer um dos métodos adquirir um token.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
