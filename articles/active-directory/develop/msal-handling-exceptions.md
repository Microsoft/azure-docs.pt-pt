---
title: Erros e exceções (MSAL) | Azure
description: Saiba como lidar com erros e exceções, o acesso condicional e o desafio de declarações em aplicativos MSAL.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: negoe
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 280746281fd45b3286cc76be5d3483f0cc65f90f
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70872798"
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
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O usuário ou administrador não consentiu usar o aplicativo com a ID ' {appId} ' denominada ' {appName} '. Envie uma solicitação de autorização interativa para este usuário e recurso.| Primeiro, você precisa obter o consentimento do usuário. Se você não estiver usando o .NET Core (que não tem nenhuma interface do usuário da Web), chame ( `AcquireTokeninteractive`apenas uma vez). Se você estiver usando o .NET Core ou não quiser fazer um `AcquireTokenInteractive`, o usuário poderá navegar para uma URL para dar consentimento: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. Para chamar `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: O usuário é solicitado a usar a autenticação multifator.| Não há nenhuma mitigação-se a MFA estiver configurada para seu locatário e o AAD decidir imaplicá-la, você precisará fazer fallback `AcquireTokenInteractive` para `AcquireTokenByDeviceCode`um fluxo interativo, como ou.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Não há suporte para o tipo Grant nos pontos de extremidade */Common* ou */consumers* . Use o ponto de extremidade específico do */Organizations* ou do locatário. Você usou */Common*.| Conforme explicado na mensagem do Azure AD, a autoridade precisa ter um locatário ou, de outra forma, */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O corpo da solicitação deve conter o seguinte parâmetro: ' client_secret ' ou client_assertion '.| Essa exceção poderá acontecer se o aplicativo não tiver sido registrado como um aplicativo cliente público no Azure AD. No portal do Azure, edite o manifesto para seu aplicativo e defina `allowPublicClient` como. `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| Mensagem de unknown_user: Não foi possível identificar o usuário conectado| A biblioteca não pôde consultar o usuário conectado do Windows atual ou este usuário não está ingressado no AD ou AAD (não há suporte para usuários ingressados no local do trabalho). Mitigação 1: no UWP, verifique se o aplicativo tem os seguintes recursos: Autenticação corporativa, redes privadas (cliente e servidor), informações de conta de usuário. Mitigação 2: Implemente sua própria lógica para buscar o nome de usuário ( john@contoso.compor exemplo,) `AcquireTokenByIntegratedWindowsAuth` e use o formulário que usa o nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Esse método se baseia em um protocolo exposto pelo Active Directory (AD). Se um usuário tiver sido criado em Azure Active Directory sem backup de AD (usuário "gerenciado"), esse método falhará. Os usuários criados no AD e apoiados pelo AAD (usuários "federados") podem se beneficiar desse método de autenticação não interativo. Atenuação Use a autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de status comuns retornados de MSAL.net ao `AcquireTokenSilent()` chamar `MsalError.InvalidGrantError`é. Esse código de status significa que o aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicativos cliente públicos e fazer um desafio em aplicativos Web). Isso ocorre porque a interação adicional do usuário é necessária antes que o token de autenticação possa ser emitido.

Na maioria das vezes, `AcquireTokenSilent` quando falha, é porque o cache de token não tem tokens que correspondam à sua solicitação. Tokens de acesso expiram em 1 hora `AcquireTokenSilent` e tentarão buscar um novo com base em um token de atualização (em termos de OAuth2, esse é o fluxo de "token de atualização"). Esse fluxo também pode falhar por vários motivos, por exemplo, se um administrador de locatários configurar políticas de logon mais rigorosas. 

A interação tem como objetivo fazer com que o usuário faça uma ação. Algumas dessas condições são fáceis para os usuários resolverem (por exemplo, aceitarem os termos de uso com um único clique) e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa se conectar a uma rede corporativa específica). Alguns ajudam a configurar o usuário na autenticação multifator ou a instalar Microsoft Authenticator em seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`Enumeração de classificação

O MSAL expõe `Classification` um campo, que você pode ler para fornecer uma melhor experiência de usuário, por exemplo, para informar ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da `UiRequiredExceptionClassification` enumeração:

| Classificação    | Significado           | Tratamento recomendado |
|-------------------|-------------------|----------------------|
| Basicaction | A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativa. | Chame AcquireTokenInteractively (). |
| Adicionalaction | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo de autenticação interativa. | Chame AcquireTokenInteractively () para mostrar uma mensagem que explica a ação corretiva. A chamada de aplicativo pode optar por ocultar fluxos que exigem additional_action se for improvável que o usuário conclua a ação corretiva. |
| MessageOnly      | A condição não pode ser resolvida neste momento. Iniciar o fluxo de autenticação interativa mostrará uma mensagem explicando a condição. | Chame AcquireTokenInteractively () para mostrar uma mensagem que explica a condição. AcquireTokenInteractively () retornará um erro de cancelado depois que o usuário ler a mensagem e fechar a janela. Chamar o aplicativo pode optar por ocultar os fluxos que resultam em message_only se for improvável que o usuário se beneficie da mensagem.|
| ConsentRequired  | O consentimento do usuário está ausente ou foi revogado. | Chame AcquireTokenInteractively () para que o usuário dê consentimento. |
| UserPasswordExpired | A senha do usuário expirou. | Chame AcquireTokenInteractively () para que o usuário possa redefinir sua senha. |
| PromptNeverFailed| A autenticação interativa foi chamada com o parâmetro prompt = Never, forçando MSAL a contar com cookies de navegador e não para exibir o navegador. Falha. | Chamar AcquireTokenInteractively () sem prompt. None |
| AcquireTokenSilentFailed | O SDK do MSAL não tem informações suficientes para buscar um token do cache. Isso pode ser porque nenhum token está no cache ou uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Chame AcquireTokenInteractively (). |
| Nenhum    | Nenhum detalhe adicional é fornecido. A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativa. | Chame AcquireTokenInteractively (). |

## <a name="code-example"></a>Exemplo de código

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
   res = await application.AcquireTokenInteractive(scopes)
                          .ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```


## <a name="javascript-errors"></a>Erros de JavaScript

O MSAL. js fornece objetos de erro que abstraim e classificam os diferentes tipos de erros comuns. Ele também fornece a interface para acessar detalhes específicos dos erros, como mensagens de erro para tratá-los adequadamente.

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
* **AuthError.message:**  O mesmo que errorMessage.
* **AuthError.stack:** Rastreamento de pilha para erros lançados. Permite o rastreamento de ponto de origem de erro.

**Tipos de erro**

Os seguintes tipos de erro estão disponíveis:

* *AuthError:* Classe de erro base para a biblioteca MSAL. js, também usada para erros inesperados.

* *ClientAuthError:* Classe Error, que denota um problema com a autenticação do cliente. A maioria dos erros provenientes da biblioteca será ClientAuthErrors. Esses erros são como chamar um método de logon quando o logon está em andamento, os usuários cancelam o logon e assim por diante. 

* *ClientConfigurationError:* Classe de erro que estende o ClientAuthError gerado antes das solicitações serem feitas quando os parâmetros de configuração do usuário especificados estão malformados ou ausentes.

* *ServerError* Classe de erro para representar as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Esses podem ser erros como formatos de solicitação ou parâmetros inválidos ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o usuário.

* *InteractionRequiredAuthError:* Classe de erro que estende ServerError para representar erros de servidor, que exigem uma chamada interativa. Esse erro é gerado pelo `acquireTokenSilent` se for necessário que o usuário interaja com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos de erro incluem "interaction_required", "login_required", "consent_required".

Para o tratamento de erros nos fluxos de autenticação com`loginRedirect`métodos `acquireTokenRedirect`de redirecionamento (,), você precisará registrar o retorno de chamada, que é chamado com `handleRedirectCallback()` êxito ou falha após o método redirecionar usando o seguinte:

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

Um erro é retornado quando você tenta usar um método não interativo de aquisição de um token (por exemplo, `acquireTokenSilent`) e MSAL não podia fazê-lo silenciosamente.

As possíveis razões são:

* o usuário precisa entrar
* o usuário precisa consentir
* o usuário precisa passar por uma experiência de autenticação multifator.

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
Ao chamar uma API que requer acesso condicional do MSAL.NET, seu aplicativo precisará lidar com exceções de desafio de declaração. Isso será exibido como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) em que a propriedade [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) não estará vazia.

Para lidar com o desafio da declaração, você precisará usar `.WithClaim()` o método `PublicClientApplicationBuilder` da classe.

### <a name="javascript"></a>JavaScript
Ao obter os tokens silenciosamente `acquireTokenSilent`(usando) usando MSAL. js, seu aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](conditional-access-dev-guide.md) , como a política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para lidar com esse erro é fazer uma chamada interativa para adquirir o token em MSAL. js, `acquireTokenPopup` como `acquireTokenRedirect` ou como no exemplo a seguir:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Adquirir interativamente o token solicita o usuário e dá a ele a oportunidade de satisfazer a política de acesso condicional necessária.

Ao chamar uma API que requer acesso condicional, você pode receber um desafio de declarações no erro da API. Nesse caso, você pode passar as declarações retornadas no erro para o `claimsRequest` campo `AuthenticationParameters.ts` da classe para atender à política apropriada. 

Consulte [solicitando declarações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

## <a name="retrying-after-errors-and-exceptions"></a>Repetindo após erros e exceções

Você deve implementar suas próprias políticas de repetição ao chamar MSAL. O MSAL faz chamadas HTTP para o serviço do AAD e podem ocorrer falhas ocasionais, por exemplo, a rede pode ficar inoperante ou o servidor está sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

O MSAL.NET implementa um mecanismo simples de repetição única para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o servidor de token de serviço (STS) está sobrecarregado com muitas solicitações, ele retorna um erro HTTP 429 com uma dica sobre quando você pode tentar novamente em termos de tempo. O erro pode ser lido no `Retry-After` campo de resposta.

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
