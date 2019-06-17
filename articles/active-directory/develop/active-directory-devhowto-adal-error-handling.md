---
title: Erro ao processar as melhores práticas para clientes do Azure Active Directory Authentication Library (ADAL)
description: Fornece orientações e melhores práticas para aplicações de cliente da ADAL de tratamento de erros.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 652175e99c800b8e4aa69c639f0bdb9aba838987
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544624"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Erro ao processar as melhores práticas para clientes do Azure Active Directory Authentication Library (ADAL)

Este artigo fornece orientações sobre o tipo de erros que os desenvolvedores podem ser encontrados, ao utilizar a ADAL para autenticar os utilizadores. Ao utilizar a ADAL, existem vários casos em que um desenvolvedor precisa para avançar e lidar com erros. Tratamento de erros adequado garante uma experiência de utilizador final excelente e limita o número de vezes que o utilizador final tem de iniciar sessão.

Neste artigo, vamos explorar os casos específicos para cada plataforma suportada por ADAL e como seu aplicativo pode manipular cada caso corretamente. A documentação de orientação de erro é dividida em duas categorias mais amplas, com base nos padrões de aquisição do token fornecidos pelas APIs da ADAL:

- **AcquireTokenSilent**: Cliente tenta obter um token silenciosamente (nenhuma interface do Usuário) e pode falhar se ADAL for concluído com êxito. 
- **AcquireToken**: Cliente poderá tentar aquisição silenciosa, mas também pode efetuar pedidos interativos que necessitem de início de sessão.

> [!TIP]
> É uma boa idéia para registar todos os erros e exceções quando utilizar a ADAL e o Azure AD. Os registos não são apenas útil para compreender o estado de funcionamento geral da sua aplicação, mas também são importantes ao depurar problemas mais amplos. Enquanto seu aplicativo pode recuperar de determinados erros, eles podem indicar problemas de design mais abrangente que necessitem de alterações de código para resolver. 
> 
> Quando implementar as condições de erro abrangidas neste documento, deve se conectar o código de erro e a descrição pelos motivos citados anteriormente. Consulte a [referência de registo de erros e](#error-and-logging-reference) para obter exemplos de código de Registro. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent tenta obter um token com a garantia de que o utilizador final não vê uma Interface de utilizador (IU). Existem vários casos em que a aquisição silenciosa pode falhar e tem de ser processada através de pedidos interativos ou por um manipulador de predefinição. Nós nos aprofundamos as especificidades de quando e como empregar a cada caso nas secções que se seguem.

Existe um conjunto de erros gerados pelo sistema operativo, que poderão necessitar de específico ao aplicativo de processamento de erros. Para obter mais informações, consulte a secção de erros de "Sistema operativo" em [referência de registo de erros e](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicações

- [Cliente nativo](developer-glossary.md#native-client) aplicativos (iOS, Android, ambiente de trabalho do .NET ou Xamarin)
- [Cliente Web](developer-glossary.md#web-client) aplicativos que chamam um [recurso](developer-glossary.md#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Casos de erro e passos acionáveis

Fundamentalmente, há dois casos de erros de AcquireTokenSilent:

| Caso | Descrição |
|------|-------------|
| **Caso 1**: Erro pode ser resolvido com uma interativo início de sessão | Para erros causados por uma falta de tokens válidos, um pedido de interativo é necessário. Especificamente, pesquisa de cache e um token de atualização inválido/expirado exigem uma chamada de AcquireToken para resolver.<br><br>Nestes casos, o utilizador final tem de ser pedido para iniciar sessão. O aplicativo pode optar por fazer uma solicitação interativa imediatamente, depois de interação do utilizador final (por exemplo, pressionando um botão de início de sessão) ou posterior. A escolha depende do comportamento desejado do aplicativo.<br><br>Consulte o código na seção a seguir para este caso específico e os erros que diagnosticá-lo.|
| **Caso 2**: Erro não pode ser resolvido com uma interativo início de sessão | Para a rede e erros transitórios/temporária ou outras falhas, efetuar um pedido de AcquireToken interativo não resolve o problema. Desnecessários início de sessão pedidos interativos também podem frustrar utilizadores finais. ADAL tenta automaticamente uma repetição única para a maioria dos erros de falhas de AcquireTokenSilent.<br><br>O aplicativo cliente também pode tentar uma repetição em algum momento posterior, mas quando e como fazer isso depende o comportamento do aplicativo e a experiência de utilizador final desejado. Por exemplo, o aplicativo pode fazer uma repetição AcquireTokenSilent após alguns minutos, ou em resposta a alguma ação do utilizador final. Uma repetição imediata irá resultar no aplicativo a ser limitado e não deve ser tentada.<br><br>Uma falha com o mesmo erro de repetição subsequente não significa que o cliente deve fazer uma solicitação de interativa usando o AcquireToken, como não resolver o erro.<br><br>Consulte o código na seção a seguir para este caso específico e os erros que diagnosticá-lo. |

### <a name="net"></a>.NET

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- acquireTokenSilent(...) [preterido]
- [deprecated] acquireTokenByRefreshToken(…) 

O código seria implementado da seguinte forma:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- acquireTokenSilent(...) [preterido]

O código seria implementado da seguinte forma:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- acquireTokenSilentWithResource(…)

O código seria implementado da seguinte forma:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken é o método ADAL do padrão usado na obtenção de tokens. Em casos em que a identidade de utilizador é necessária, AcquireToken tenta obter um token silenciosamente primeiro, em seguida, exibe a interface do Usuário se necessário (a menos que é passado PromptBehavior.Never). Em casos onde é necessária a identidade da aplicação, AcquireToken tenta obter um token, mas não mostra a interface do Usuário porque não existe nenhum utilizador final. 

Quando o tratamento de erros de AcquireToken, tratamento de erros é dependente de plataforma e o cenário a aplicação está a tentar alcançar. 

O sistema operacional também pode gerar um conjunto de erros, que requerem tratamento depende do aplicativo específico de erros. Para obter mais informações, consulte "Erros de sistema operacional" no [referência de registo de erros e](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicações

- Aplicações de cliente nativo (iOS, Android, ambiente de trabalho do .NET ou Xamarin)
- Aplicações Web que chamam um recurso de API (.NET)
- Aplicativos de página única (JavaScript)
- Aplicativos de serviços (.NET, Java)
  - Todos os cenários, incluindo em-nome-de
  - Em-nome-de cenários específicos

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casos de erro e passos acionáveis: Aplicações de cliente nativo

Se estiver a criar uma aplicação cliente nativa, existem alguns casos de manipulação de erro a ter em consideração que se relacionam com a problemas de rede, as falhas transitórias e outros erros específicos da plataforma. Na maioria dos casos, um aplicativo não deve realiza as repetições imediatas, mas em vez disso, aguarde a interação do utilizador final que solicita um início de sessão. 

Existem alguns casos especiais em que uma repetição única pode resolver o problema. Por exemplo, quando um utilizador tem de ativar a dados num dispositivo ou concluir o agente do Azure AD transferir depois da falha inicial. 

No caso de falha, um aplicativo pode apresentar a interface do Usuário para permitir que o utilizador final executar alguma interação que lhe pede uma repetição. Por exemplo, se o dispositivo falhou para um erro offline, um botão "Experimente voltar a iniciar sessão" pedir um AcquireToken tentar novamente em vez de repetir imediatamente a falha. 

Erro ao processar em aplicativos nativos pode ser definido por dois casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Erro sem repetição (a maioria dos casos) | 1. Não tente repetição imediata. Apresente o utilizador final a que interface do Usuário com base no erro específico que invoca uma repetição ("Tentar novamente para início de sessão", "Aplicação de Mediador de transferir o Azure AD", etc.). |
| **Caso 2**:<br>Erro de repetição | 1. Execute uma repetição única visto que o utilizador final pode ter a entrou num Estado que resulta num sucesso.<br><br>2. Se falhar a repetição, apresentam o utilizador final a que interface do Usuário com base no erro específico que invoca uma repetição ("Tentar novamente para início de sessão", "Aplicação de Mediador de transferir o Azure AD", etc.). |

> [!IMPORTANT]
> Se uma conta de utilizador é passada para a ADAL uma chamada silenciosa e falhar, o pedido subsequente interativo permite que o utilizador final que inicie sessão com uma conta diferente. Após um AcquireToken com êxito através de uma conta de utilizador, a aplicação tem de verificar que o utilizador com sessão iniciada corresponde ao objeto de utilizador local os aplicativos. Um erro de correspondência não gera uma exceção (exceto no Objective C), mas devem ser consideradas em casos em que um utilizador é conhecido localmente antes dos pedidos de autenticação (como uma falha de chamada silenciosa).
>

#### <a name="net"></a>.NET

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com todos os não-silencioso AcquireToken(...) Métodos da ADAL, *exceto*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

O código seria implementado da seguinte forma:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> .NET da ADAL tem uma consideração adicional, por oferecer suporte a PromptBehavior.Never, que tem o comportamento como AcquireTokenSilent.
>

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- acquireToken(…, PromptBehavior.Never)

O código seria implementado da seguinte forma:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com todos os não-silencioso AcquireToken(...) Métodos da ADAL. 

O código seria implementado da seguinte forma:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com todos os não-silencioso AcquireToken(...) Métodos da ADAL. 

O código seria implementado da seguinte forma:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casos de erro e passos acionáveis: Aplicações Web que chamam um recurso de API (.NET)

Se estiver a criar uma aplicação web .NET que chama obtém um token com um código de autorização para um recurso, o único código necessário é um manipulador padrão para o caso de genérico. 

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

O código seria implementado da seguinte forma:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casos de erro e passos acionáveis: Aplicativos de única página (adal.js)

Se estiver criando um aplicativo de página única com adal.js AcquireToken, o código de tratamento de erro é semelhante de uma chamada típica de silenciosa. Especificamente adal.js, AcquireToken nunca mostra uma interface do Usuário. 

Uma falha AcquireToken tem os seguintes casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Resolvido por uma solicitação interativa | 1. Se login() falhar, não efetue repetição imediata. Apenas Repita após qualquer ação do utilizador pede uma repetição.|
| **Caso 2**:<br>Não Resolvable por uma solicitação interativa. Erro é repetição. | 1. Execute uma repetição única visto que o utilizador final principais introduziu um Estado que resulta num sucesso.<br><br>2. Se falhar a repetição, apresentar o utilizador final com uma ação com base no erro específico que pode invocar uma repetição ("tentar voltar a iniciar sessão"). |
| **Caso 3**:<br>Não Resolvable por uma solicitação interativa. Erro não é recuperável. | 1. Não tente repetição imediata. Apresentar o utilizador final com uma ação com base no erro específico que pode invocar uma repetição ("tentar voltar a iniciar sessão"). |

O código seria implementado da seguinte forma:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casos de erro e passos acionáveis: serviço para serviço de aplicações (apenas para o .NET)

Se estiver criando um aplicativo de serviço para serviço que usa AcquireToken, existem alguns erros de chave que deve processar o seu código. O único recurso a falha é para devolver o erro para a aplicação de chamada (para casos em-nome-de) ou aplicar uma estratégia de repetição. 

#### <a name="all-scenarios"></a>Todos os cenários

Para *todos os* cenários de aplicativo de serviços, incluindo em-nome-de:

- Não tente uma repetição imediata. ADAL tentativas de uma única para determinados repetir pedidos falharam. 
- Apenas continue a repetir após uma ação do utilizador ou aplicação pede uma repetição. Por exemplo, um aplicativo de daemon que funcionam em determinado intervalo de conjunto deve aguardar até o próximo intervalo de repetição.

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

O código seria implementado da seguinte forma:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Em-nome-de cenários

Para *em-nome-de* cenários de aplicativos de serviço para serviço.

As seguintes orientações fornecem exemplos de tratamento de erros em conjunto com os métodos da ADAL: 

- AcquireTokenAsync(…, UserAssertion, …)

O código seria implementado da seguinte forma:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

Criamos um [exemplo completo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) que demonstra este cenário.

## <a name="error-and-logging-reference"></a>Registo de erros e referência

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Informações de identificação pessoal (PII) de registo & informações de identificação organizacionais (OII)
Por predefinição, o registo da ADAL não capturar ou quaisquer PII ou OII de registo. A biblioteca permite que os programadores de aplicações ativar esta opção por meio de um setter na classe Logger. Ao ativar de PII ou OII, a aplicação assume a responsabilidade por com segurança tratamento de dados altamente confidenciais e a conformidade com quaisquer requisitos de regulamentação.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar os erros específicos da ADAL, o código-fonte no [repositório azure-activedirectory-library-para-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) é a referência de erro melhor.

#### <a name="guidance-for-error-logging-code"></a>Orientações para o código de registo de erro

Alterações de registo da ADAL .NET consoante a plataforma a ser executada. Consulte a [wiki de Registro em log](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) para código sobre como ativar o registo.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar os erros específicos da ADAL, o código-fonte no [repositório azure-activedirectory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) é a referência de erro melhor.

#### <a name="operating-system-errors"></a>Erros de sistema operativo

Android erros de sistema operacional são expostos por meio de AuthenticationException na ADAL, são identificáveis, como "SERVER_INVALID_REQUEST" e podem ser ainda mais granulares, as descrições de erro. 

Para obter uma lista completa de erros comuns e que passos deve para efetuar quando a aplicação ou os utilizadores finais depara-las, consulte a [Wiki Android da ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Orientações para o código de registo de erro

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>Erros de biblioteca ADAL

Para explorar os erros específicos da ADAL, o código-fonte no [repositório azure-activedirectory-library-para-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) é a referência de erro melhor.

#### <a name="operating-system-errors"></a>Erros de sistema operativo

erros de iOS surgem durante o início de sessão quando os utilizadores utilizam vistas web e a natureza de autenticação. Isto pode ser causado por condições como erros SSL, tempos limite ou erros de rede:

- Para a partilha de elegibilidade, inícios de sessão não são persistentes e a cache parece vazia. Pode resolver, adicionando a seguinte linha de código para a porta-chaves: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Para o conjunto de NsUrlDomain de erros, as alterações de ação, dependendo da lógica de aplicação. Consulte a [documentação de referência de NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) para instâncias específicas que podem ser processadas.
- Ver [ADAL problemas comuns de Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) para obter a lista dos erros comuns mantidos pela equipa do ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Orientações para o código de registo de erro

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Orientações para o código de registo de erros - JavaScript 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```
## <a name="related-content"></a>Conteúdo relacionado

* [Guia do programador AD do Azure][AAD-Dev-Guide]
* [Bibliotecas de autenticação do Azure AD][AAD-Auth-Libraries]
* [Cenários de autenticação do Azure AD][AAD-Auth-Scenarios]
* [Integrar aplicações com o Azure Active Directory][AAD-Integrating-Apps]

Utilize a secção de comentários que se segue, para fornecer comentários e ajude-na refinar e moldar o nosso conteúdo.

[![Inicie sessão no botão][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

