---
title: Erros e exceções (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, Acesso Condicional e desafios de reclamações em aplicações MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884023"
---
# <a name="handle-msal-exceptions-and-errors"></a>Lidar com exceções e erros da MSAL

Este artigo apresenta uma visão geral dos diferentes tipos de erros e recomendações para lidar com erros comuns de inscrição.

## <a name="msal-error-handling-basics"></a>MSAL de manipulação de erros básicos

As exceções na Microsoft Authentication Library (MSAL) destinam-se a programadores de aplicações para resolver problemas - não para exibir utilizadores finais. As mensagens de exceção não estão localizadas.

Ao processar exceções e erros, pode utilizar o tipo de exceção em si e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte códigos de erro de [autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de entrada, poderá encontrar erros sobre consentimentos, Acesso Condicional (MFA, Gestão de Dispositivos, restrições baseadas na localização), emissão e redenção de fichas e propriedades do utilizador.

Consulte a seguinte secção que corresponde ao idioma que está a utilizar para obter mais detalhes sobre o manuseamento de erros para a sua aplicação.

## <a name="net"></a>[.NET](#tab/dotnet)

Ao processar as exceções .NET, pode utilizar `ErrorCode` o tipo de exceção em si e o membro para distinguir entre exceções. `ErrorCode`valores são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Também pode ver os campos de [MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se o [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) for lançado, experimente códigos de erro de [autenticação e autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-net-exceptions"></a>Exceções comuns .NET

Aqui estão as exceções comuns que podem ser lançadas e algumas possíveis atenuações:  

| Exceção | Código de erro | Mitigação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O utilizador ou administrador não consentiu em utilizar a aplicação com o nome de '{appId}' chamado '{appName}'. Envie um pedido de autorização interativa para este utilizador e recurso.| Primeiro, tem de obter o consentimento do utilizador. Se não estiver a utilizar o Núcleo .NET (que não tem UI Web), ligue (apenas uma vez) `AcquireTokeninteractive`. Se estiver a utilizar o núcleo .NET `AcquireTokenInteractive`ou não quiser fazer um, o `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`utilizador pode navegar para um URL para dar o seu consentimento: . para `AcquireTokenInteractive`chamar:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: O utilizador é obrigado a utilizar a autenticação multifactor (MFA).| Não há atenuação. Se o MFA estiver configurado para o seu inquilino e o Azure Ative Directory (AAD) `AcquireTokenInteractive` `AcquireTokenByDeviceCode`decidir aplicá-lo, precisa de recuar para um fluxo interativo como ou .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: O tipo de subvenção não é suportado sobre os pontos finais */comum* ou */consumidores.* Utilize o */organizaçãoou* ponto final específico do inquilino. Usou */comum.*| Como explica a mensagem da Azure AD, a autoridade precisa de ter um inquilino ou outra */organização.*|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O organismo de pedido deve `client_secret or client_assertion`conter o seguinte parâmetro: .| Esta exceção pode ser lançada se o seu pedido não for registado como uma aplicação de cliente público em Azure AD. No portal Azure, edite o manifesto `allowPublicClient` `true`para a sua aplicação e definido para . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Não foi possível identificar o registo registado no utilizador| A biblioteca não foi capaz de consultar o utilizador atual do Windows sessão ou este utilizador não é AD ou AAD aderiu (os utilizadores de local de trabalho não são suportados). Mitigação 1: no UWP, verifique se a aplicação tem as seguintes capacidades: Autenticação empresarial, Redes Privadas (Cliente e Servidor), Informação sobre Conta de Utilizador. Mitigação 2: Implemente a sua própria lógica john@contoso.compara obter `AcquireTokenByIntegratedWindowsAuth` o nome de utilizador (por exemplo, ) e utilize o formulário que toma no nome de utilizador.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Este método baseia-se num protocolo exposto pelo Ative Directory (AD). Se um utilizador foi criado no Diretório Ativo Azure sem suporte a DD (utilizador "gerido"), este método falhará. Os utilizadores criados em AD e apoiados por AAD (utilizadores "federados") podem beneficiar deste método de autenticação não interativo. Mitigação: Utilize a autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de estado `AcquireTokenSilent()` comuns devolvidos de MSAL.NET quando a chamada é `MsalError.InvalidGrantError`. Este código de estado significa que a aplicação deve voltar a ligar para a biblioteca de autenticação, mas em modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicações de clientes públicos, e fazer um desafio nas aplicações Web). Isto porque é necessária interação adicional do utilizador antes de ser emitido token de autenticação.

A maior parte `AcquireTokenSilent` do tempo, quando falha, é porque a cache simbólica não tem fichas correspondentes ao seu pedido. As fichas de acesso expiram em 1 hora, e `AcquireTokenSilent` tentarão obter uma nova com base num token refrescante (em termos OAuth2, este é o fluxo "Refresh Token"). Este fluxo também pode falhar por várias razões, por exemplo, se um administrador inquilino configurar políticas de login mais rigorosas. 

A interação visa fazer com que o utilizador faça uma ação. Algumas dessas condições são fáceis de resolver (por exemplo, aceitar Termos de Utilização com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa de se ligar a uma rede corporativa específica). Alguns ajudam o utilizador a configurar a autenticação multifactor ou a instalar o Microsoft Authenticator no seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`enumeração de classificação

A MSAL `Classification` expõe um campo, que pode ler para proporcionar uma melhor experiência do utilizador, por exemplo, para dizer ao utilizador que a sua palavra-passe expirou ou que terá de dar consentimento para utilizar alguns recursos. Os valores suportados `UiRequiredExceptionClassification` fazem parte do enum:

| Classificação    | Significado           | Manuseamento recomendado |
|-------------------|-------------------|----------------------|
| Ação Básica | A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Ligue para AcquireTokenInteractivemente(). |
| Ação Adicional | A condição pode ser resolvida através de uma interação adicional de reparação com o sistema, fora do fluxo de autenticação interativa. | Ligue para AcquireTokenInteractivemente() para mostrar uma mensagem que explique a ação corretiva. A aplicação de chamada pode optar por ocultar fluxos que exijam additional_action se o utilizador não conseguir completar a ação corretiva. |
| MensagemApenas      | A condição não pode ser resolvida neste momento. O lançamento do fluxo de autenticação interativa mostrará uma mensagem explicando a condição. | Ligue para AcquireTokenInteractivemente() para mostrar uma mensagem que explique a condição. AcquireTokenInteractively() devolverá o erro do UtilizadorCancelado após o utilizador ler a mensagem e fechar a janela. A aplicação de chamada pode optar por ocultar fluxos que resultem em message_only se o utilizador não for suscetível de beneficiar da mensagem.|
| Consentimento Necessário  | O consentimento do utilizador está em falta ou foi revogado. | Ligue para AcquireTokenInteractivemente() para que o utilizador dê o seu consentimento. |
| UserPasswordExpired | A palavra-passe do utilizador expirou. | Ligue para AcquireTokenInteractivemente() para que o utilizador possa redefinir a sua palavra-passe. |
| PromptNeverFailed| A Autenticação Interativa foi chamada com o parâmetro prompt=nunca, forçando a MSAL a confiar nos cookies do navegador e não a exibir o navegador. Isto falhou. | Ligue para AcquireTokenInteractively() sem Prompt.None |
| AcquireTokenSilentFailed | A MSAL SDK não tem informação suficiente para obter um sinal da cache. Isto pode ser porque não há fichas na cache ou uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Ligue para AcquireTokenInteractivemente(). |
| Nenhuma    | Não são fornecidos mais detalhes. A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Ligue para AcquireTokenInteractivemente(). |

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

MSAL.js fornece objetos de erro que abstratem e classificam os diferentes tipos de erros comuns. Também fornece interface para aceder a detalhes específicos dos erros, tais como mensagens de erro para manuseá-los adequadamente.

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
- `AuthError.message`: O `errorMessage`mesmo que o .
- `AuthError.stack`: Empilhar vestígios para erros lançados.

### <a name="error-types"></a>Tipos de erro

Estão disponíveis os seguintes tipos de erros:

- `AuthError`: Classe de erro de base para a biblioteca MSAL.js, também utilizada para erros inesperados.

- `ClientAuthError`: Classe de erro, que denota um problema com a autenticação do Cliente. A maioria dos erros que vêm da biblioteca serão ClientesAuthErrors. Estes erros resultam de coisas como chamar um método de login quando o login já está em andamento, o utilizador cancela o login, e assim por diante.

- `ClientConfigurationError`: Classe de `ClientAuthError` erro, estende-se antes de serem feitos pedidos quando os parâmetros de config do utilizador dada estão mal formados ou em falta.

- `ServerError`: Classe de erro, representa as cadeias de erro enviadas pelo servidor de autenticação. Estes podem ser erros como formatos ou parâmetros de pedido inválidos, ou quaisquer outros erros que impeçam o servidor de autenticar ou autorizar o utilizador.

- `InteractionRequiredAuthError`: Classe de `ServerError` erro, estende-se para representar erros do servidor, que requerem uma chamada interativa. Este erro é `acquireTokenSilent` lançado se o utilizador for obrigado a interagir com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos `"interaction_required"` `"login_required"`de `"consent_required"`erro incluem, e .

Para o manuseamento de erros nos`loginRedirect` `acquireTokenRedirect`fluxos de autenticação com métodos de redirecionamento ( `handleRedirectCallback()` ), terá de registar o backback, que é chamado com sucesso ou falha após o redirecionamento utilizando o método seguinte:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para a`loginPopup` `acquireTokenPopup`experiência pop-up ( , ) devolver promessas, para que você possa usar o padrão de promessa (.então e .catch) para manuseá-los como mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erros que requerem interação

Um erro é devolvido quando se tenta usar um método não `acquireTokenSilent`interativo de aquisição de um símbolo como , mas a MSAL não o conseguiu fazer silenciosamente.

As possíveis razões são:

- precisa assinar
- precisa consentir
- precisa passar por uma experiência de autenticação de vários fatores.

A reparação deve chamar um `acquireTokenPopup` `acquireTokenRedirect`método interativo, como ou:

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

Na MSAL para Python, a maioria dos erros são transmitidos como um valor de retorno da chamada da API. O erro é representado como um dicionário contendo a resposta JSON da plataforma de identidade da Microsoft.

* Uma resposta bem `"access_token"` sucedida contém a chave. O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.1 Resposta bem sucedida](https://tools.ietf.org/html/rfc6749#section-5.1)
* Uma resposta `"error"` de `"error_description"`erro contém e geralmente . O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando um erro é `"error_description"` devolvido, a chave contém uma mensagem legível pelo homem; que, por sua vez, normalmente contém um código de erro da plataforma de identidade microsoft. Para mais detalhes sobre os vários códigos de erro, consulte códigos de [erro de autenticação e autorização](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

No MSAL para python, as exceções são raras porque a maioria dos erros são tratados devolvendo um valor de erro. A `ValueError` exceção só é lançada quando há um problema com a forma como está a tentar utilizar a biblioteca - como quando os parâmetros api estão mal formados.

## <a name="java"></a>[Java](#tab/java)

No MSAL para Java, existem três `MsalClientException` `MsalServiceException`tipos `MsalInteractionRequiredException`de exceções: , e; tudo o `MsalException`que herda de .

- `MsalClientException`é lançado quando ocorre um erro que é local para a biblioteca ou dispositivo.
- `MsalServiceException`é lançado quando o serviço de fichas seguras (STS) devolve uma resposta de erro ou ocorre outro erro de rede.
- `MsalInteractionRequiredException`é lançado quando a interação ui é necessária para que a autenticação tenha sucesso.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`expõe os cabeçalhos HTTP devolvidos nos pedidos ao STS. Acede-los via`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um dos códigos de estado comuns devolvidos da MSAL para Java quando a chamada `AcquireTokenSilently()` é `InvalidGrantError`. Isto significa que é necessária uma interação adicional do utilizador antes de ser emitido um símbolo de autenticação. A sua aplicação deve voltar a ligar para `AuthorizationCodeParameters` `DeviceCodeParameters` a biblioteca de autenticação, mas em modo interativo, enviando ou para aplicações de clientes públicos.

A maior parte `AcquireTokenSilently` do tempo, quando falha, é porque a cache simbólica não tem um símbolo que corresponda ao seu pedido. As fichas de acesso expiram dentro de uma hora, e `AcquireTokenSilently` tentarão obter uma nova com base num token refrescante. Em termos OAuth2, este é o fluxo de Token Refresh. Este fluxo também pode falhar por várias razões, como quando um administrador inquilino confunde políticas de login mais rigorosas.

Algumas condições que resultam neste erro são fáceis de resolver pelos utilizadores. Por exemplo, podem ter de aceitar os Termos de Utilização. Ou talvez o pedido não possa ser cumprido com a configuração atual porque a máquina precisa de se ligar a uma rede corporativa específica.

A MSAL `reason` expõe um campo, que pode utilizar para proporcionar uma melhor experiência ao utilizador. Por exemplo, `reason` o campo pode levá-lo a dizer ao utilizador que a sua palavra-passe expirou ou que terá de dar consentimento para utilizar alguns recursos. Os valores suportados `InteractionRequiredExceptionReason` fazem parte do enum:

| Razão | Significado | Manuseamento Recomendado |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa | Chamada `acquireToken` com parâmetros interativos |
| `AdditionalAction` | A condição pode ser resolvida através de uma interação corretiva adicional com o sistema fora do fluxo de autenticação interativa. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a tomar. A aplicação de chamada pode optar por ocultar fluxos que requerem ação adicional se o utilizador não conseguir completar a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida neste momento. Lance o fluxo de autenticação interativa para mostrar uma mensagem explicando a condição. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken`devolverá `UserCanceled` o erro depois de o utilizador ler a mensagem e fechar a janela. A aplicação pode optar por ocultar fluxos que resultem em mensagem se o utilizador não for suscetível de beneficiar da mensagem. |
| `ConsentRequired`| O consentimento do utilizador está em falta ou foi revogado. |Ligue `acquireToken` com parâmetros interativos para que o utilizador possa dar consentimento. |
| `UserPasswordExpired` | A palavra-passe do utilizador expirou. | Ligue `acquireToken` com parâmetro interativo para que o utilizador possa redefinir a sua palavra-passe |
| `None` |  Mais detalhes são fornecidos. A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Chamada `acquireToken` com parâmetros interativos |

### <a name="code-example"></a>Exemplo de Código

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

Todos os erros produzidos pela `MSALErrorDomain` MSAL são devolvidos com domínio.

Para erros do sistema, a `NSError` MSAL devolve o original do sistema API. Por exemplo, se a aquisição de fichas falhar devido à falta de `NSURLErrorDomain` conectividade `NSURLErrorNotConnectedToInternet` da rede, a MSAL devolve um erro com o domínio e o código.

Recomendamos que lide pelo menos com os dois erros MSAL seguintes do lado do cliente:

- `MSALErrorInteractionRequired`: O utilizador deve fazer um pedido interativo. Existem muitas condições que podem levar a este erro, como uma sessão de autenticação caducada ou a necessidade de requisitos adicionais de autenticação. Ligue para a Aquisição interativa de Fichas MSAL para recuperar. 

- `MSALErrorServerDeclinedScopes`: Alguns ou todos os âmbitos foram recusados. Decida se deve continuar apenas com os âmbitos concedidos ou parar o processo de inscrição.

> [!NOTE]
> O `MSALInternalError` enum só deve ser utilizado para referência e depuração. Não tente lidar automaticamente com estes erros no tempo de funcionamento. Se a sua aplicação encontrar `MSALInternalError`algum dos erros que se aderem, poderá querer mostrar uma mensagem de frente para um utilizador genérico explicando o que aconteceu.

Por exemplo, `MSALInternalErrorBrokerResponseNotReceived` significa que o utilizador não completou a autenticação e regressou manualmente à aplicação. Neste caso, a sua aplicação deve mostrar uma mensagem de erro genérica explicando que a autenticação não foi completada e sugerir que tentem autenticar novamente.

O seguinte código de amostra do Objectivo-C demonstra as melhores práticas para o tratamento de algumas condições comuns de erro:

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

## <a name="conditional-access-and-claims-challenges"></a>Acesso Condicional e desafios de sinistros

Ao obter fichas silenciosamente, a sua aplicação pode receber erros quando um desafio de [acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política de MFA, é exigido por uma API a que está a tentar aceder.

O padrão para lidar com este erro é adquirir interativamente um símbolo usando mSAL. Adquirir um token interactivamente leva o utilizador e dá-lhes a oportunidade de satisfazer a política de Acesso Condicional exigida.

Em certos casos, ao chamar uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Por exemplo, se a política de Acesso Condicional for para ter um dispositivo gerido (Intune) o erro será algo como [AADSTS53000: O seu dispositivo é obrigado a ser gerido para aceder a este recurso](reference-aadsts-error-codes.md) ou algo semelhante. Neste caso, pode passar as reclamações na chamada simbólica de aquisição para que o utilizador seja solicitado a satisfazer a política adequada.

### <a name="net"></a>.NET

Ao chamar uma API que exija acesso condicional a partir de MSAL.NET, a sua aplicação terá de lidar com as exceções ao desafio de reclamação. Isto aparecerá como uma [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) onde a propriedade [Reclamações](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) não estará vazia.

Para lidar com o desafio da reivindicação, terá de usar o `.WithClaim()` método da `PublicClientApplicationBuilder` classe.

### <a name="javascript"></a>JavaScript

Ao obter fichas silenciosamente `acquireTokenSilent`(utilizando ) utilizando MSAL.js, a sua aplicação pode receber erros quando um desafio de [acesso condicional](../azuread-dev/conditional-access-dev-guide.md) alega, como a política de MFA, é exigido por uma API a que está a tentar aceder.

O padrão para lidar com este erro é fazer uma chamada interativa para `acquireTokenPopup` `acquireTokenRedirect` adquirir ficha em MSAL.js, tais como ou como no exemplo seguinte:

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

Adquirir interativamente o token leva o utilizador e dá-lhes a oportunidade de satisfazer a política de Acesso Condicional exigida.

Ao chamar uma API que requer acesso condicional, pode receber um desafio de reclamações no erro da API. Neste caso, pode passar as reclamações devolvidas no erro para o `claimsRequest` campo da `AuthenticationParameters.ts` classe para satisfazer a política adequada. 

Consulte [solicitar reclamações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

A MSAL para iOS e macOS permite-lhe solicitar reclamações específicas em cenários de aquisição interativos e silenciosos.

Para solicitar reclamações `claimsRequest` `MSALSilentTokenParameters` personalizadas, especifique dentro ou `MSALInteractiveTokenParameters`.

Consulte [pedidos de reclamações personalizadas utilizando MSAL para iOS e macOS](request-custom-claims.md) para obter mais informações.

## <a name="retrying-after-errors-and-exceptions"></a>Retentar após erros e exceções

Espera-se que implemente políticas de retenção quando chama mSAL. A MSAL faz chamadas HTTP para o serviço AAD, e podem ocorrer falhas ocasionais, por exemplo, a rede pode descer ou o servidor está sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

MSAL.NET implementa um simples mecanismo de retry-once para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o Servidor token de serviço (STS) está sobrecarregado com muitos pedidos, devolve o erro HTTP 429 `Retry-After` com uma dica sobre quanto tempo até poder tentar novamente no campo de resposta.

### <a name="net"></a>.NET

[MsalServiceSuperfície](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) superfícies `System.Net.Http.Headers.HttpResponseHeaders` como `namedHeaders`uma propriedade . Pode utilizar informações adicionais a partir do código de erro para melhorar a fiabilidade das suas aplicações. No caso descrito, pode `RetryAfterproperty` utilizar o `RetryConditionHeaderValue`(do tipo) e calcular quando voltar a tentar.

Aqui está um exemplo para uma aplicação daemon usando o fluxo de credenciais do cliente. Pode adaptar isto a qualquer um dos métodos para adquirir um símbolo.

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
