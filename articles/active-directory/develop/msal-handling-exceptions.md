---
title: Erros e exceções (MSAL) | Azure
description: Saiba como lidar com erros e exceções, o acesso condicional e o desafio de declarações em aplicativos MSAL.
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
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c294e3bd8ac04454c2d715c665e0da4f9a4f4535
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835036"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Tratamento de exceções e erros usando MSAL
Exceções na MSAL (biblioteca de autenticação da Microsoft) são destinadas para os desenvolvedores de aplicativos solucionarem problemas e não para exibição para os usuários finais. As mensagens de exceção não são localizadas.

Ao processar exceções e erros, você pode usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Exceções do .NET
Ao processar exceções, você pode usar o próprio tipo de exceção e `ErrorCode` o membro para distinguir entre exceções. Os valores de `ErrorCode` são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Você também pode observar os campos de [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) for gerado, o código de erro poderá conter um código que você pode encontrar nos [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Exceções comuns
Aqui estão as exceções comuns que podem ser geradas e algumas possíveis reduções.

| Exceção | Código de erro | Mitigação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O usuário ou administrador não consentiu usar o aplicativo com a ID ' {appId} ' denominada ' {appName} '. Envie uma solicitação de autorização interativa para este usuário e recurso.| Primeiro, você precisa obter o consentimento do usuário. Se você não estiver usando o .NET Core (que não tem nenhuma interface do usuário da Web), chame ( `AcquireTokeninteractive`apenas uma vez). Se você estiver usando o .NET Core ou não quiser fazer um `AcquireTokenInteractive`, o usuário poderá navegar para uma URL para dar consentimento: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . Para chamar `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: O usuário é solicitado a usar a autenticação multifator.| Não há nenhuma mitigação-se a MFA estiver configurada para seu locatário e o AAD decidir imaplicá-la, você precisará fazer fallback `AcquireTokenInteractive` para `AcquireTokenByDeviceCode`um fluxo interativo, como ou.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Não há suporte para o tipo Grant nos pontos de extremidade */Common* ou */consumers* . Use o ponto de extremidade específico do */Organizations* ou do locatário. Você usou */Common*.| Conforme explicado na mensagem do Azure AD, a autoridade precisa ter um locatário ou, de outra forma, */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O corpo da solicitação deve conter o seguinte parâmetro: ' client_secret ' ou client_assertion '.| Isso pode acontecer se seu aplicativo não tiver sido registrado como um aplicativo cliente público no Azure AD. No portal do Azure, edite o manifesto para seu aplicativo e defina `allowPublicClient` como. `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Mensagem de unknown_user: Não foi possível identificar o usuário conectado| A biblioteca não pôde consultar o usuário conectado do Windows atual ou este usuário não está ingressado no AD ou AAD (não há suporte para usuários ingressados no local do trabalho). Mitigação 1: no UWP, verifique se o aplicativo tem os seguintes recursos: Autenticação corporativa, redes privadas (cliente e servidor), informações de conta de usuário. Mitigação 2: Implemente sua própria lógica para buscar o nome de usuário ( john@contoso.compor exemplo,) `AcquireTokenByIntegratedWindowsAuth` e use o formulário que usa o nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Esse método se baseia em um protocolo exposto pelo Active Directory (AD). Se um usuário tiver sido criado em Azure Active Directory sem backup de AD (usuário "gerenciado"), esse método falhará. Os usuários criados no AD e apoiados pelo AAD (usuários "federados") podem se beneficiar desse método de autenticação não interativo. Atenuação Use a autenticação interativa.|

## <a name="javascript-errors"></a>Erros de JavaScript

O MSAL. js fornece objetos de erro que abstraim e classificam os diferentes tipos de erros comuns e têm uma interface para acessar detalhes específicos dos erros, como mensagens de erro para tratá-los adequadamente.

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
Ao estender a classe Error, você tem acesso às seguintes propriedades:
* **AuthError.message:** Isso é o mesmo que errorMessage.
* **AuthError.stack:** Rastreamento de pilha para erros lançados. Permite o rastreamento de ponto de origem de erro.

**Tipos de erro**

Os seguintes tipos de erro estão disponíveis:

* *AuthError:* Classe de erro base para a biblioteca MSAL. js, também usada para erros inesperados.

* *ClientAuthError:* Classe de erro que denota um problema com a autenticação do cliente. A maioria dos erros provenientes da biblioteca será ClientAuthErrors. Esses podem ser erros, como chamar um método de logon quando o logon está em andamento, usuários cancelando o logon etc.

* *ClientConfigurationError:* Classe de erro que estende o ClientAuthError gerado antes das solicitações serem feitas quando os parâmetros de configuração do usuário especificados estão malformados ou ausentes.

* *ServerError* Classe de erro para representar as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Esses podem ser erros como formatos de solicitação ou parâmetros inválidos ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o usuário.

* *InteractionRequiredAuthError:* Classe de erro que estende ServerError para representar erros de servidor que exigem uma chamada interativa. Isso é gerado pelo `acquireTokenSilent` se for necessário que o usuário interaja com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos de erro incluem "interaction_required", "login_required", "consent_required".

Para o tratamento de erros nos fluxos de autenticação com`loginRedirect`métodos `acquireTokenRedirect`de redirecionamento (,), você precisará registrar o retorno de chamada que é chamado com `handleRedirectCallback()` êxito ou falha após o método de redirecionamento usando o seguinte:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a experiência de pop-`loginPopup`up `acquireTokenPopup`(,) retornam promessas, para que você possa usar o padrão Promise (. em seguida e. catch) para tratá-los, conforme mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Erros de interação necessária

Um erro é retornado quando uma interação de interface do usuário é necessária. Isso significa que você tentou usar um método não interativo de adquirir um token (por exemplo, `acquireTokenSilent`), mas MSAL não pôde fazer isso silenciosamente. As possíveis razões são:

* Você precisa entrar
* Você precisa consentir
* Você precisa passar por uma experiência de autenticação multifator.

A correção é chamar um método interativo, como `acquireTokenPopup` ou: `acquireTokenRedirect`

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

## <a name="conditional-access-and-claims-challenges"></a>Desafios de acesso condicional e declarações
Ao obter os tokens silenciosamente, seu aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](conditional-access-dev-guide.md) , como a política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para lidar com esse erro é adquirir um token interativamente usando MSAL. Adquirir interativamente um token solicita o usuário e dá a ele a oportunidade de satisfazer a política de acesso condicional necessária.

Em determinados casos, ao chamar uma API que requer acesso condicional, você pode receber um desafio de declarações no erro da API. Por exemplo, se a política de acesso condicional for ter um dispositivo gerenciado (Intune), o erro será algo [como AADSTS53000: Seu dispositivo precisa ser gerenciado para acessar esse recurso](reference-aadsts-error-codes.md) ou algo semelhante. Nesse caso, você pode passar as declarações na chamada de token de aquisição para que o usuário seja solicitado a atender à política apropriada.

### <a name="net"></a>.NET
Ao chamar uma API que requer acesso condicional do MSAL.NET, seu aplicativo precisará lidar com exceções de desafio de declaração. Isso será exibido como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) em que [](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) a propriedade Claims não estará vazia.

Para lidar com o desafio da declaração, você precisará usar `.WithClaim()` o método `PublicClientApplicationBuilder` da classe.

### <a name="javascript"></a>JavaScript
Ao obter os tokens silenciosamente `acquireTokenSilent`(usando) usando MSAL. js, seu aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](conditional-access-dev-guide.md) , como a política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para lidar com esse erro é fazer uma chamada interativa para adquirir o token em MSAL. js, `acquireTokenPopup` como `acquireTokenRedirect` ou como no exemplo a seguir:

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

Adquirir interativamente o token solicita o usuário e dá a ele a oportunidade de satisfazer a política de acesso condicional necessária.

Ao chamar uma API que requer acesso condicional, você pode receber um desafio de declarações no erro da API. Nesse caso, você pode passar as declarações retornadas no erro como `extraQueryParameters` na chamada para adquirir tokens para que o usuário seja solicitado a atender à política apropriada:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Repetindo após erros e exceções

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600
O MSAL.NET implementa um mecanismo simples de repetição única para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429
Quando o servidor de token de serviço (STS) está muito ocupado devido a "muitas solicitações", ele retorna um erro HTTP 429 com uma dica sobre quando você pode tentar novamente (o campo de resposta Retry-After) como um atraso em segundos ou uma data.

#### <a name="net"></a>.NET
A exceção [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) é `System.Net.Http.Headers.HttpResponseHeaders` superfícies como uma `namedHeaders`propriedade. Portanto, você pode aproveitar informações adicionais do código de erro para melhorar a confiabilidade de seus aplicativos. No caso que acabamos de descrever, você pode usar `RetryAfterproperty` o (do `RetryConditionHeaderValue`tipo) e calcular quando tentar novamente.

Aqui está um exemplo de um aplicativo daemon (usando o fluxo de credenciais do cliente), mas você pode adaptá-lo a qualquer um dos métodos que adquirem um token.

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
