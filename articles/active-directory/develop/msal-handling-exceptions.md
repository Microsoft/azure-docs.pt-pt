---
title: Erros e exceções (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, Acesso Condicional e reclama desafios em aplicações MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b4ed1e21c33a952b639009b619db4f497f2cfbf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860070"
---
# <a name="handle-msal-exceptions-and-errors"></a>Lidar com exceções e erros da MSAL

Este artigo apresenta uma visão geral dos diferentes tipos de erros e recomendações para lidar com erros comuns de inscrição.

## <a name="msal-error-handling-basics"></a>Básicos de manipulação de erros msal

As exceções na Microsoft Authentication Library (MSAL) destinam-se a programadores de aplicações para resolver problemas - não para exibição para utilizadores finais. As mensagens de exceção não são localizadas.

Ao processar exceções e erros, pode utilizar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de entrada, poderá encontrar erros sobre consentimentos, acesso condicional (MFA, Gestão de Dispositivos, restrições baseadas na localização), emissão e resgate de fichas e propriedades do utilizador.

Consulte a seguinte secção que corresponde ao idioma que está a usar para obter mais detalhes sobre o tratamento de erros para a sua aplicação.

## <a name="net"></a>[.NET](#tab/dotnet)

Ao processar exceções .NET, pode utilizar o próprio tipo de exceção e o `ErrorCode` membro para distinguir entre exceções. `ErrorCode` valores são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

Você também pode dar uma olhada nos campos de [MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception), e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Se [o MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) for lançado, experimente [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-net-exceptions"></a>Exceções comuns .NET

Aqui estão as exceções comuns que podem ser lançadas e algumas possíveis mitigações:  

| Exceção | Código de erro | Mitigação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: O utilizador ou administrador não consentiu em utilizar a aplicação com o iD '{appId}' denominado '{appName}'. Envie um pedido de autorização interativa para este utilizador e recurso.| Primeiro, tem de obter o consentimento do utilizador. Se não estiver a utilizar o .NET Core (que não tem UI web), ligue (apenas uma vez) `AcquireTokeninteractive` . Se estiver a utilizar o núcleo .NET ou não quiser fazer `AcquireTokenInteractive` um, o utilizador pode navegar até um URL para dar o seu consentimento: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . para `AcquireTokenInteractive` chamar: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: O utilizador é obrigado a utilizar a [autenticação multi-factor (MFA)](../authentication/concept-mfa-howitworks.md).| Não há atenuação. Se a MFA estiver configurada para o seu inquilino e o Azure Ative Directory (AAD) decidir aplicá-lo, tem de recuar para um fluxo interativo como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode` . .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: O tipo de subvenção não é suportado sobre os pontos finais */comuns* ou */consumidores.* Utilize as */organizações* ou ponto final específico do inquilino. Usou */comum.*| Como explicado na mensagem da Azure AD, a autoridade precisa de ter um inquilino ou outras *organizações.*|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: O organismo de pedido deve conter o seguinte parâmetro: `client_secret or client_assertion` .| Esta exceção pode ser lançada se o seu pedido não tiver sido registado como um pedido de cliente público na Azure AD. No portal Azure, edite o manifesto para a sua aplicação e desatado `allowPublicClient` para `true` . |
| [MentedescientExcepção](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Não foi possível identificar o utilizador com sessão registada| A biblioteca não foi capaz de consultar o utilizador atual com início de sessão do Windows ou este utilizador não está atado a AD ou AAD (os utilizadores unidos no local de trabalho não são suportados). Mitigação 1: no UWP, verifique se a aplicação tem as seguintes capacidades: Autenticação empresarial, Redes Privadas (Cliente e Servidor), Informação da Conta do Utilizador. Mitigação 2: Implemente a sua própria lógica para buscar o nome de utilizador (por exemplo, john@contoso.com ) e use o formulário que toma no nome de `AcquireTokenByIntegratedWindowsAuth` utilizador.|
| [MentedescientExcepção](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Este método baseia-se num protocolo exposto pelo Ative Directory (AD). Se um utilizador foi criado no Azure Ative Directory sem o suporte de AD (utilizador "gerido"), este método falhará. Os utilizadores criados em AD e apoiados por utilizadores AAD (utilizadores "federados") podem beneficiar deste método não interativo de autenticação. Mitigação: Utilize a autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de estado comum devolvidos de MSAL.NET quando telefona `AcquireTokenSilent()` é `MsalError.InvalidGrantError` . Este código de estado significa que a aplicação deve voltar a ligar para a biblioteca de autenticação, mas em modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicações de clientes públicos, e fazer um desafio em aplicações Web). Isto porque é necessária uma interação adicional do utilizador antes de poder ser emitida uma ficha de autenticação.

A maior parte do tempo, quando `AcquireTokenSilent` falha, é porque a cache simbólica não tem fichas correspondentes ao seu pedido. Os tokens de acesso expiram em 1 hora, e `AcquireTokenSilent` tentarão obter um novo com base num token de atualização (em termos OAuth2, este é o fluxo "Refresh Token"). Este fluxo também pode falhar por várias razões, por exemplo, se um inquilino admin configurar políticas de login mais rigorosas. 

A interação visa fazer com que o utilizador faça uma ação. Algumas dessas condições são fáceis de resolver para os utilizadores (por exemplo, aceitar Termos de Utilização com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa de se ligar a uma rede corporativa específica). Alguns ajudam a autenticação multi-factor do utilizador ou instalam o Microsoft Authenticator no seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` enumeração classificação

A MSAL expõe um `Classification` campo, que pode ler para proporcionar uma melhor experiência de utilizador, por exemplo, para dizer ao utilizador que a sua palavra-passe expirou ou que terá de fornecer consentimento para utilizar alguns recursos. Os valores suportados fazem parte do `UiRequiredExceptionClassification` enum:

| Classificação    | Significado           | Manuseamento recomendado |
|-------------------|-------------------|----------------------|
| BasicAction | A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Ligue para AcquireTokenInteractively(). |
| Aacção adicional | A condição pode ser resolvida através de uma interação corretiva adicional com o sistema, fora do fluxo de autenticação interativa. | Ligue para AcquireTokenInteractively para mostrar uma mensagem que explique a ação corretiva. A aplicação de chamada pode optar por ocultar fluxos que exijam additional_action se o utilizador não conseguir completar a ação corretiva. |
| MensagemOnly      | A condição não pode ser resolvida neste momento. O lançamento do fluxo de autenticação interativa mostrará uma mensagem explicando a condição. | Ligue para AcquireTokenInteractively para mostrar uma mensagem que explique a condição. AcquireTokenInteractively() devolverá o erro do UserCanceled depois de o utilizador ler a mensagem e fechar a janela. A aplicação de chamada pode optar por ocultar fluxos que resultem em message_only se o utilizador não beneficiar da mensagem.|
| ConsentimentoRequirado  | O consentimento do utilizador está em falta ou foi revogado. | Ligue para AcquireTokenInteractively para que o utilizador dê o seu consentimento. |
| UserPasswordExpired | A senha do utilizador expirou. | Ligue para AcquireTokenInteractively para que o utilizador possa redefinir a sua palavra-passe. |
| PromptNeverFailed| A Autenticação Interativa foi chamada com o parâmetro prompt=nunca, forçando a MSAL a confiar nos cookies do navegador e a não exibir o navegador. Isto falhou. | Ligue para AcquireTokenInteractively() sem Prompt.Nenhum |
| AcquireTokenSilentFailed | A MSAL SDK não tem informação suficiente para ir buscar um símbolo à cache. Isto pode ser porque não há fichas na cache ou uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Ligue para AcquireTokenInteractively(). |
| Nenhum    | Não são fornecidos mais detalhes. A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Ligue para AcquireTokenInteractively(). |

## <a name="net-code-example"></a>exemplo de código .NET

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
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js fornece objetos de erro que abstraem e classificam os diferentes tipos de erros comuns. Também fornece interface para aceder a detalhes específicos dos erros, tais como mensagens de erro para manuseá-los adequadamente.

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

Ao alargar a classe de erro, tem acesso às seguintes propriedades:
- `AuthError.message`: O mesmo que o `errorMessage` .
- `AuthError.stack`: Pilhar vestígios para erros arremessais.

### <a name="error-types"></a>Tipos de erros

Estão disponíveis os seguintes tipos de erro:

- `AuthError`: Classe de erro base para a biblioteca MSAL.js, também utilizada para erros inesperados.

- `ClientAuthError`: Classe de erro, que denota um problema com a autenticação do Cliente. A maioria dos erros que vêm da biblioteca serão ClientAuthErrors. Estes erros resultam de coisas como chamar um método de login quando o login já está em andamento, o utilizador cancela o login, e assim por diante.

- `ClientConfigurationError`: Classe de erro, estende-se `ClientAuthError` antes de os pedidos serem feitos quando os parâmetros de configuração do utilizador são mal formados ou em falta.

- `ServerError`: Classe de erro, representa as cadeias de erro enviadas pelo servidor de autenticação. Estes podem ser erros como formatos ou parâmetros de pedido inválidos, ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o utilizador.

- `InteractionRequiredAuthError`: Classe de erro, estende-se `ServerError` para representar erros do servidor, que requerem uma chamada interativa. Este erro é lançado se `acquireTokenSilent` o utilizador for obrigado a interagir com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos de erro incluem `"interaction_required"` `"login_required"` , e `"consent_required"` .

Para o manuseamento de erros nos fluxos de autenticação com métodos de redireccionamento `loginRedirect` (, `acquireTokenRedirect` , terá de registar a chamada de retorno, que é chamada com sucesso ou falha após o redirecionamento utilizando o método da seguinte `handleRedirectCallback()` forma:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a experiência pop-up `loginPopup` (, `acquireTokenPopup` ) promessas de retorno, para que você possa usar o padrão de promessa (.em seguida e.catch) para manuseá-los como mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erros que requerem interação

Um erro é devolvido quando se tenta usar um método não interativo de adquirir um símbolo como `acquireTokenSilent` , mas o MSAL não conseguiu fazê-lo silenciosamente.

As razões possíveis são:

- precisa de se inscrever
- você precisa consentir
- você precisa passar por uma experiência de autenticação multi-factor.

A remediação é chamar um método interativo `acquireTokenPopup` `acquireTokenRedirect` como:

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

## <a name="python"></a>[Python](#tab/python)

No MSAL para Python, a maioria dos erros são transmitidos como um valor de retorno da chamada API. O erro é representado como um dicionário que contém a resposta JSON da plataforma de identidade da Microsoft.

* Uma resposta bem sucedida contém a `"access_token"` chave. O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.1 Resposta com sucesso](https://tools.ietf.org/html/rfc6749#section-5.1)
* Uma resposta de erro contém `"error"` e normalmente `"error_description"` . O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando um erro é devolvido, a `"error_description"` tecla contém uma mensagem legível pelo homem; que, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter mais informações sobre os vários códigos de erro, consulte [códigos de erro de autenticação e autorização](./reference-aadsts-error-codes.md).

No MSAL para Python, as exceções são raras porque a maioria dos erros são tratados devolvendo um valor de erro. A `ValueError` exceção só é lançada quando há um problema com a forma como está a tentar usar a biblioteca - como quando os parâmetros(s) da API estão mal formados.

## <a name="java"></a>[Java](#tab/java)

No MSAL para Java, existem três tipos de exceções: `MsalClientException` , e ; todas as que `MsalServiceException` `MsalInteractionRequiredException` herdam de `MsalException` .

- `MsalClientException` é lançado quando ocorre um erro que é local para a biblioteca ou dispositivo.
- `MsalServiceException` é lançado quando o serviço de fichas de segurança (STS) retorna uma resposta de erro ou outro erro de rede ocorre.
- `MsalInteractionRequiredException` é jogado quando a interação da UI é necessária para que a autenticação tenha sucesso.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expõe cabeçalhos HTTP devolvidos nos pedidos ao STS. Aceda-os através de `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um dos códigos de estado comum devolvidos da MSAL para a Java quando telefona `AcquireTokenSilently()` é `InvalidGrantError` . Isto significa que é necessária uma interação adicional do utilizador antes de um token de autenticação poder ser emitido. A sua aplicação deve voltar a ligar para a biblioteca de autenticação, mas em modo interativo, enviando `AuthorizationCodeParameters` ou `DeviceCodeParameters` para aplicações de clientes públicos.

A maior parte do tempo, quando falha, é porque a cache simbólica `AcquireTokenSilently` não tem um símbolo correspondente ao seu pedido. Os tokens de acesso expiram em uma hora, e `AcquireTokenSilently` tentarão obter um novo com base num token de atualização. Em termos OAuth2, este é o fluxo Refresh Token. Este fluxo também pode falhar por várias razões, tais como quando um inquilino admin configura políticas de login mais rigorosas.

Algumas condições que resultam neste erro são fáceis de resolver para os utilizadores. Por exemplo, podem ter de aceitar termos de utilização. Ou talvez o pedido não possa ser preenchido com a configuração atual porque a máquina precisa de se ligar a uma rede corporativa específica.

A MSAL expõe um `reason` campo, que pode utilizar para proporcionar uma melhor experiência ao utilizador. Por exemplo, o `reason` campo pode levá-lo a dizer ao utilizador que a sua palavra-passe expirou ou que terá de fornecer consentimento para utilizar alguns recursos. Os valores suportados fazem parte do  `InteractionRequiredExceptionReason` enum:

| Razão | Significado | Manuseamento recomendado |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa | Chamada `acquireToken` com parâmetros interativos |
| `AdditionalAction` | A condição pode ser resolvida através de uma interação corretiva adicional com o sistema fora do fluxo de autenticação interativa. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a tomar. A aplicação de chamadas pode optar por ocultar fluxos que requerem ação adicional se o utilizador não conseguir completar a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida neste momento. Lance o fluxo de autenticação interativa para mostrar uma mensagem explicando a condição. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken` retornará o `UserCanceled` erro depois de o utilizador ler a mensagem e fechar a janela. A aplicação pode optar por ocultar fluxos que resultem em mensagem se o utilizador não beneficiar da mensagem. |
| `ConsentRequired`| O consentimento do utilizador está em falta ou foi revogado. |Ligue `acquireToken` com parâmetros interativos para que o utilizador possa dar o seu consentimento. |
| `UserPasswordExpired` | A senha do utilizador expirou. | Chamada `acquireToken` com parâmetro interativo para que o utilizador possa redefinir a sua palavra-passe |
| `None` |  Mais detalhes são fornecidos. A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Chamada `acquireToken` com parâmetros interativos |

### <a name="code-example"></a>Exemplo de código

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

A lista completa de MSAL para erros iOS e macOS está listada no [MSALError enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Todos os erros produzidos pela MSAL são devolvidos com `MSALErrorDomain` domínio.

Para erros do sistema, a MSAL devolve o original `NSError` da API do sistema. Por exemplo, se a aquisição de token falhar devido à falta de conectividade da rede, a MSAL devolve um erro com o domínio e o `NSURLErrorDomain` `NSURLErrorNotConnectedToInternet` código.

Recomendamos que lide com pelo menos os seguintes dois erros MSAL do lado do cliente:

- `MSALErrorInteractionRequired`: O utilizador deve fazer um pedido interativo. Existem muitas condições que podem levar a este erro, como uma sessão de autenticação caducada ou a necessidade de requisitos adicionais de autenticação. Ligue para a API de aquisição interativa da MSAL para recuperar. 

- `MSALErrorServerDeclinedScopes`: Alguns ou todos os âmbitos foram recusados. Decida se deve continuar apenas com os âmbitos de aplicação concedidos ou parar o processo de inscrição.

> [!NOTE]
> O `MSALInternalError` enum só deve ser utilizado para referência e depuração. Não tente lidar automaticamente com estes erros no tempo de funcionação. Se a sua aplicação encontrar algum dos erros que `MSALInternalError` se enquadram, pode querer mostrar uma mensagem genérica do utilizador a explicar o que aconteceu.

Por exemplo, `MSALInternalErrorBrokerResponseNotReceived` significa que o utilizador não completou a autenticação e regressou manualmente à aplicação. Neste caso, a sua aplicação deve mostrar uma mensagem de erro genérica explicando que a autenticação não foi completada e sugere que tentem autenticar novamente.

O seguinte código de amostra objecto-C demonstra as melhores práticas para o tratamento de algumas condições de erro comuns:

```objc
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

---

## <a name="conditional-access-and-claims-challenges"></a>Acesso Condicional e reclama desafios

Ao receber tokens silenciosamente, a sua aplicação pode receber erros quando um [desafio de reclamações de acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política de MFA, é exigido por uma API que está a tentar aceder.

O padrão para lidar com este erro é adquirir interativamente um símbolo usando o MSAL. A aquisição interativa de um símbolo leva o utilizador e dá-lhes a oportunidade de satisfazer a necessária política de Acesso Condicional.

Em certos casos, ao ligar para uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Por exemplo, se a política de Acesso Condicional pretender ter um dispositivo gerido (Intune) o erro será algo como [AADSTS53000: O seu dispositivo é obrigado a ser gerido para aceder a este recurso](reference-aadsts-error-codes.md) ou algo similar. Neste caso, pode passar as reclamações na chamada de ficha de aquisição para que o utilizador seja solicitado para satisfazer a política adequada.

### <a name="net"></a>.NET

Ao ligar para uma API que requer acesso condicional a partir de MSAL.NET, a sua aplicação terá de lidar com exceções de reclamação. Isto aparecerá como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) onde a propriedade [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) não estará vazia.

Para lidar com o desafio de reclamação, terá de usar o `.WithClaim()` método da `PublicClientApplicationBuilder` classe.

### <a name="javascript"></a>JavaScript

Ao obter tokens silenciosamente (usando) `acquireTokenSilent` usando MSAL.js, a sua aplicação pode receber erros quando um [desafio de reclamações de acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política de MFA, é exigido por uma API que está a tentar aceder.

O padrão para lidar com este erro é fazer uma chamada interativa para adquirir token em MSAL.js como `acquireTokenPopup` ou como no exemplo `acquireTokenRedirect` seguinte:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

A aquisição interativa do símbolo leva o utilizador e dá-lhes a oportunidade de satisfazer a necessária política de Acesso Condicional.

Ao ligar para uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Neste caso, pode passar as reclamações devolvidas no erro para o `claimsRequest` campo da classe para satisfazer a política `AuthenticationParameters.ts` adequada. 

Consulte [solicitação de reclamações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

A MSAL para iOS e macOS permite-lhe solicitar reclamações específicas em cenários de aquisição de token interativos e silenciosos.

Para solicitar reclamações personalizadas, especifique `claimsRequest` em `MSALSilentTokenParameters` ou `MSALInteractiveTokenParameters` .

Consulte [pedidos de reclamações personalizadas usando MSAL para iOS e macOS](request-custom-claims.md) para mais informações.

## <a name="retrying-after-errors-and-exceptions"></a>Reagem após erros e exceções

Espera-se que implemente as suas próprias políticas de relícola quando ligar para o MSAL. A MSAL faz chamadas HTTP para o serviço AAD, e podem ocorrer falhas ocasionais, por exemplo, a rede pode desligar-se ou o servidor está sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

MSAL.NET implementa um mecanismo simples de retripação de erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o Serviço Token Server (STS) está sobrecarregado com muitos pedidos, retorna o erro HTTP 429 com uma dica sobre quanto tempo pode tentar novamente no `Retry-After` campo de resposta.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) surge `System.Net.Http.Headers.HttpResponseHeaders` como uma `namedHeaders` propriedade. Pode utilizar informações adicionais a partir do código de erro para melhorar a fiabilidade das suas aplicações. No caso descrito, pode utilizar o `RetryAfterproperty` (do `RetryConditionHeaderValue` tipo) e calcular quando voltar a tentar.

Aqui está um exemplo para uma aplicação daemon usando o fluxo de credenciais do cliente. Você pode adaptá-lo a qualquer um dos métodos para adquirir um símbolo.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
