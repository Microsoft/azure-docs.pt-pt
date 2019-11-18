---
title: Erros e exceções (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, o acesso condicional e os desafios de declarações em aplicativos MSAL.
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
ms.date: 11/13/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5bfc5e6471d768b89a66610a2618bc1a44cf709d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145923"
---
# <a name="handle-msal-exceptions-and-errors"></a>Manipular exceções e erros do MSAL

Este artigo fornece uma visão geral dos diferentes tipos de erros e recomendações para lidar com erros de entrada comuns.

## <a name="msal-error-handling-basics"></a>Noções básicas de tratamento de erros MSAL

Exceções na MSAL (biblioteca de autenticação da Microsoft) são destinadas para os desenvolvedores de aplicativos solucionarem problemas e não para exibição para os usuários finais. As mensagens de exceção não são localizadas.

Ao processar exceções e erros, você pode usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a aquisição de tokens silencioso ou interativo, os aplicativos podem vir erros durante a experiência de entrada, como erros sobre conenvios, acesso condicional (MFA, gerenciamento de dispositivos, restrições baseadas em local), emissão e resgate de token e usuário Properties.

## <a name="msal-for-ios-and-macos-errors"></a>MSAL para erros do iOS e macOS

A lista completa de erros está listada em [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Todos os erros produzidos pelo MSAL são retornados com `MSALErrorDomain` domínio.

Para erros do sistema, MSAL retorna o `NSError` original da API do sistema. Por exemplo, se a aquisição de token falhar devido à falta de conectividade de rede, o MSAL retornará um erro com o `NSURLErrorDomain` domínio e `NSURLErrorNotConnectedToInternet` código.

É recomendável que você manipule pelo menos os dois erros MSAL a seguir no lado do cliente:

- `MSALErrorInteractionRequired`: o usuário deve fazer uma solicitação interativa. Há muitas condições que podem levar a esse erro, como uma sessão de autenticação expirada ou a necessidade de requisitos de autenticação adicionais. Chame a API de aquisição de token interativo do MSAL para recuperar. 

- `MSALErrorServerDeclinedScopes`: alguns ou todos os escopos foram recusados. Decida se deseja continuar com apenas os escopos concedidos ou parar o processo de entrada.

> [!NOTE]
> O `MSALInternalError` enum só deve ser usado para referência e depuração. Não tente lidar automaticamente com esses erros no tempo de execução. Se seu aplicativo encontrar algum dos erros que se enquadram em `MSALInternalError`, talvez você queira mostrar uma mensagem de volta do usuário genérico explicando o que aconteceu.

Por exemplo, `MSALInternalErrorBrokerResponseNotReceived` significa que o usuário não concluiu a autenticação e retornou manualmente para o aplicativo. Nesse caso, seu aplicativo deve mostrar uma mensagem de erro genérica explicando que a autenticação não foi concluída e sugerindo que ele tente autenticar novamente.

O seguinte código de exemplo Objective-C demonstra as práticas recomendadas para lidar com algumas condições de erro comuns:

Objective-C
```ObjC
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

Swift
```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

## <a name="msal-for-python-error-handling"></a>MSAL para tratamento de erros do Python

No MSAL para Python, a maioria dos erros é transmitida como um valor de retorno da chamada à API. O erro é representado como um dicionário que contém a resposta JSON da plataforma Microsoft Identity.

* Uma resposta bem-sucedida contém a chave de `"access_token"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [resposta bem-sucedida do 5,1](https://tools.ietf.org/html/rfc6749#section-5.1)
* Uma resposta de erro contém `"error"` e geralmente `"error_description"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [resposta de erro 5,2](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando um erro é retornado, a chave de `"error_description"` contém uma mensagem legível; que, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter detalhes sobre os vários códigos de erro, consulte [autenticação e códigos de erro de autorização](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

No MSAL para Python, as exceções são raras porque a maioria dos erros é tratada retornando um valor de erro. A exceção `ValueError` é gerada apenas quando há um problema com o modo como você está tentando usar a biblioteca, como quando os parâmetros da API estão malformados.

## <a name="net-exceptions"></a>Exceções do .NET

Ao processar exceções, você pode usar o próprio tipo de exceção e o membro `ErrorCode` para distinguir entre exceções. os valores de `ErrorCode` são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Você também pode observar os campos de [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) for gerado, tente [autenticação e códigos de erro de autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-exceptions"></a>Exceções comuns

Aqui estão as exceções comuns que podem ser geradas e algumas das possíveis reduções:  

| Exceção | Código de erro | Mitigação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: o usuário ou administrador não consentiu usar o aplicativo com a ID ' {appId} ' nomeada ' {appName} '. Envie uma solicitação de autorização interativa para este usuário e recurso.| Primeiro, você precisa obter o consentimento do usuário. Se você não estiver usando o .NET Core (que não tem nenhuma interface do usuário da Web), chame (somente uma vez) `AcquireTokeninteractive`. Se você estiver usando o .NET Core ou não quiser fazer uma `AcquireTokenInteractive`, o usuário poderá navegar para uma URL para dar consentimento: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read. para chamar `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: o usuário precisa usar a autenticação multifator (MFA).| Não há mitigação. Se o MFA estiver configurado para seu locatário e Azure Active Directory (AAD) decidir imaplicá-lo, você precisará fazer fallback para um fluxo interativo, como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: o tipo Grant não tem suporte nos pontos de extremidade */Common* ou */consumers* . Use o ponto de extremidade específico do */Organizations* ou do locatário. Você usou */Common*.| Conforme explicado na mensagem do Azure AD, a autoridade precisa ter um locatário ou, de outra forma, */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: o corpo da solicitação deve conter o seguinte parâmetro: `client_secret or client_assertion`.| Essa exceção poderá ser gerada se o aplicativo não tiver sido registrado como um aplicativo cliente público no Azure AD. No portal do Azure, edite o manifesto para seu aplicativo e defina `allowPublicClient` como `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: não foi possível identificar o usuário conectado| A biblioteca não pôde consultar o usuário conectado do Windows atual ou este usuário não está ingressado no AD ou AAD (não há suporte para usuários ingressados no local de trabalho). Mitigação 1: no UWP, verifique se o aplicativo tem os seguintes recursos: autenticação corporativa, redes privadas (cliente e servidor), informações de conta de usuário. Mitigação 2: implemente sua própria lógica para buscar o nome de usuário (por exemplo, john@contoso.com) e use o formulário `AcquireTokenByIntegratedWindowsAuth` que usa o nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Esse método se baseia em um protocolo exposto pelo Active Directory (AD). Se um usuário tiver sido criado em Azure Active Directory sem backup de AD (usuário "gerenciado"), esse método falhará. Os usuários criados no AD e apoiados pelo AAD (usuários "federados") podem se beneficiar desse método de autenticação não interativo. Mitigação: Use a autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de status comuns retornados de MSAL.NET ao chamar `AcquireTokenSilent()` é `MsalError.InvalidGrantError`. Esse código de status significa que o aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicativos cliente públicos e fazer um desafio em aplicativos Web). Isso ocorre porque a interação adicional do usuário é necessária antes que o token de autenticação possa ser emitido.

Na maioria das vezes em que `AcquireTokenSilent` falha, é porque o cache de token não tem tokens que correspondam à sua solicitação. Os tokens de acesso expiram em 1 hora e `AcquireTokenSilent` tentará buscar um novo com base em um token de atualização (em termos OAuth2, esse é o fluxo de "token de atualização"). Esse fluxo também pode falhar por vários motivos, por exemplo, se um administrador de locatários configurar políticas de logon mais rigorosas. 

A interação tem como objetivo fazer com que o usuário faça uma ação. Algumas dessas condições são fáceis para os usuários resolverem (por exemplo, aceitarem os termos de uso com um único clique) e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa se conectar a uma rede corporativa específica). Alguns ajudam a configurar o usuário na autenticação multifator ou a instalar Microsoft Authenticator em seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>Enumeração de classificação de `MsalUiRequiredException`

O MSAL expõe um campo `Classification`, que você pode ler para fornecer uma melhor experiência de usuário, por exemplo, para informar ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da enumeração de `UiRequiredExceptionClassification`:

| Classificação    | Significado           | Tratamento recomendado |
|-------------------|-------------------|----------------------|
| Basicaction | A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativa. | Chame AcquireTokenInteractively (). |
| Adicionalaction | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo de autenticação interativa. | Chame AcquireTokenInteractively () para mostrar uma mensagem que explica a ação corretiva. A chamada de aplicativo pode optar por ocultar fluxos que exigem additional_action se for improvável que o usuário conclua a ação corretiva. |
| MessageOnly      | A condição não pode ser resolvida neste momento. Iniciar o fluxo de autenticação interativa mostrará uma mensagem explicando a condição. | Chame AcquireTokenInteractively () para mostrar uma mensagem que explica a condição. AcquireTokenInteractively () retornará um erro de cancelado depois que o usuário ler a mensagem e fechar a janela. A chamada de aplicativo pode optar por ocultar os fluxos que resultam em message_only se for improvável que o usuário se beneficie da mensagem.|
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

### <a name="error-object"></a>Objeto de erro

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
* **AuthError. Message:**  O mesmo que errorMessage.
* **AuthError. Stack:** Rastreamento de pilha para erros lançados. Permite o rastreamento de ponto de origem de erro.

### <a name="error-types"></a>Tipos de erro

Os seguintes tipos de erro estão disponíveis:

- `AuthError`: classe de erro base para a biblioteca MSAL. js, também usada para erros inesperados.

- `ClientAuthError`: classe Error, que denota um problema com a autenticação do cliente. A maioria dos erros provenientes da biblioteca será ClientAuthErrors. Esses erros resultam de coisas como chamar um método de logon quando o logon já está em andamento, o usuário cancela o logon e assim por diante.

- `ClientConfigurationError`: classe Error, estende `ClientAuthError` lançada antes que as solicitações sejam feitas quando os parâmetros de configuração do usuário especificados estiverem malformados ou ausentes.

- `ServerError`: classe Error representa as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Esses podem ser erros como formatos de solicitação ou parâmetros inválidos ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o usuário.

- `InteractionRequiredAuthError`: classe Error, estende `ServerError` para representar erros de servidor, que exigem uma chamada interativa. Esse erro será gerado por `acquireTokenSilent` se for necessário que o usuário interaja com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos de erro incluem `"interaction_required"`, `"login_required"`e `"consent_required"`.

Para o tratamento de erros nos fluxos de autenticação com métodos de redirecionamento (`loginRedirect`, `acquireTokenRedirect`), você precisará registrar o retorno de chamada, que é chamado com êxito ou falha após o redirecionamento usando o método `handleRedirectCallback()` da seguinte maneira:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a experiência de pop-up (`loginPopup`, `acquireTokenPopup`) retornam promessas, para que você possa usar o padrão Promise (. then e. catch) para tratá-los, conforme mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Interação necessária, erros

Um erro é retornado quando você tenta usar um método não interativo de aquisição de um token, como `acquireTokenSilent`, mas MSAL não podia fazê-lo silenciosamente.

As possíveis razões são:

- Você precisa entrar
- Você precisa consentir
- Você precisa passar por uma experiência de autenticação multifator.

A correção é chamar um método interativo, como `acquireTokenPopup` ou `acquireTokenRedirect`:

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

O padrão para lidar com esse erro é adquirir interativamente um token usando MSAL. Adquirir interativamente um token solicita o usuário e dá a ele a oportunidade de satisfazer a política de acesso condicional necessária.

Em determinados casos, ao chamar uma API que requer acesso condicional, você pode receber um desafio de declarações no erro da API. Por exemplo, se a política de acesso condicional for ter um dispositivo gerenciado (Intune), o erro será algo como [AADSTS53000: seu dispositivo precisa ser gerenciado para acessar esse recurso](reference-aadsts-error-codes.md) ou algo semelhante. Nesse caso, você pode passar as declarações na chamada de token de aquisição para que o usuário seja solicitado a atender à política apropriada.

### <a name="net"></a>.NET

Ao chamar uma API que requer acesso condicional do MSAL.NET, seu aplicativo precisará lidar com exceções de desafio de declaração. Isso será exibido como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) em que a propriedade [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) não estará vazia.

Para lidar com o desafio da declaração, você precisará usar o método `.WithClaim()` da classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

Ao obter os tokens silenciosamente (usando `acquireTokenSilent`) usando MSAL. js, seu aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](conditional-access-dev-guide.md) , como a política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para lidar com esse erro é fazer uma chamada interativa para adquirir o token em MSAL. js, como `acquireTokenPopup` ou `acquireTokenRedirect`, como no exemplo a seguir:

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

Ao chamar uma API que requer acesso condicional, você pode receber um desafio de declarações no erro da API. Nesse caso, você pode passar as declarações retornadas no erro para o campo `claimsRequest` da classe `AuthenticationParameters.ts` para satisfazer a política apropriada. 

Consulte [solicitando declarações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

O MSAL para iOS e macOS permite que você solicite declarações específicas em cenários de aquisição de tokens interativos e silenciosos.

Para solicitar declarações personalizadas, especifique `claimsRequest` em `MSALSilentTokenParameters` ou `MSALInteractiveTokenParameters`.

Consulte [solicitar declarações personalizadas usando o MSAL para IOS e MacOS](request-custom-claims.md) para obter mais informações.

## <a name="retrying-after-errors-and-exceptions"></a>Repetindo após erros e exceções

Você deve implementar suas próprias políticas de repetição ao chamar MSAL. O MSAL faz chamadas HTTP para o serviço do AAD e podem ocorrer falhas ocasionais, por exemplo, a rede pode ficar inoperante ou o servidor está sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

O MSAL.NET implementa um mecanismo simples de repetição única para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o servidor de token de serviço (STS) está sobrecarregado com muitas solicitações, ele retorna o erro HTTP 429 com uma dica sobre o tempo até que você possa tentar novamente no campo de resposta `Retry-After`.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) superfícies `System.Net.Http.Headers.HttpResponseHeaders` como uma `namedHeaders`de propriedade. Você pode usar informações adicionais do código de erro para melhorar a confiabilidade de seus aplicativos. No caso descrito, você pode usar a `RetryAfterproperty` (do tipo `RetryConditionHeaderValue`) e calcular quando tentar novamente.

Veja um exemplo de um aplicativo daemon usando o fluxo de credenciais do cliente. Você pode adaptá-lo a qualquer um dos métodos para adquirir um token.

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
