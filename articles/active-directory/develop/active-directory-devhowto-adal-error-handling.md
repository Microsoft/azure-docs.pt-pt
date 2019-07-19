---
title: Práticas recomendadas de tratamento de erros para clientes ADAL (biblioteca de autenticação Azure Active Directory)
description: Fornece diretrizes de tratamento de erros e práticas recomendadas para aplicativos cliente ADAL.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1d478bbb2f8645703299c8fe37c2117f492c3f8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324818"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Práticas recomendadas de tratamento de erros para clientes ADAL (biblioteca de autenticação Azure Active Directory)

Este artigo fornece orientação sobre o tipo de erros que os desenvolvedores podem encontrar, ao usar a ADAL para autenticar usuários. Ao usar a ADAL, há vários casos em que um desenvolvedor pode precisar entrar e tratar erros. O tratamento de erros adequado garante uma ótima experiência do usuário final e limita o número de vezes que o usuário final precisa entrar.

Neste artigo, exploraremos os casos específicos para cada plataforma com suporte da ADAL e como seu aplicativo pode lidar com cada caso adequadamente. As diretrizes de erro são divididas em duas categorias mais amplas, com base nos padrões de aquisição de token fornecidos pelas APIs de ADAL:

- **AcquireTokenSilent**: O cliente tenta obter um token silenciosamente (sem interface do usuário) e pode falhar se a ADAL não for bem-sucedida. 
- **AcquireToken**: O cliente pode tentar a aquisição silenciosa, mas também pode executar solicitações interativas que exigem entrada.

> [!TIP]
> É uma boa ideia registrar todos os erros e exceções ao usar a ADAL e o Azure AD. Os logs não são úteis para compreender a integridade geral do seu aplicativo, mas também são importantes durante a depuração de problemas mais amplos. Embora seu aplicativo possa se recuperar de determinados erros, eles podem indicar problemas de design mais amplos que exigem alterações de código para resolver. 
> 
> Ao implementar as condições de erro abordadas neste documento, registre o código de erro e a descrição pelos motivos discutidos anteriormente. Consulte o [erro e a referência de log](#error-and-logging-reference) para obter exemplos de código de registro em log. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

O AcquireTokenSilent tenta obter um token com a garantia de que o usuário final não vê uma interface do usuário (IU). Há vários casos em que a aquisição silenciosa pode falhar e precisa ser tratada por meio de solicitações interativas ou por um manipulador padrão. Nós nos aprofundamos nas especificações de quando e como empregar cada caso nas seções a seguir.

Há um conjunto de erros gerados pelo sistema operacional, o que pode exigir tratamento de erros específico ao aplicativo. Para obter mais informações, consulte a seção de erros "sistema operacional" em [referência de erro e log](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicações

- Aplicativos [cliente nativos](developer-glossary.md#native-client) (Ios, Android, .net desktop ou Xamarin)
- Aplicativos [cliente Web](developer-glossary.md#web-client) chamando um [recurso](developer-glossary.md#resource-server) (.net)

### <a name="error-cases-and-actionable-steps"></a>Casos de erro e etapas acionáveis

Fundamentalmente, há dois casos de erros de AcquireTokenSilent:

| Casos | Descrição |
|------|-------------|
| **Caso 1**: O erro pode ser resolvido com uma entrada interativa | Para erros causados por uma falta de tokens válidos, uma solicitação interativa é necessária. Especificamente, a pesquisa de cache e um token de atualização inválido/expirado exigem uma chamada AcquireToken para resolver.<br><br>Nesses casos, o usuário final precisa ser solicitado a entrar. O aplicativo pode optar por fazer uma solicitação interativa imediatamente, após a interação do usuário final (como pressionar um botão de entrada) ou posterior. A escolha depende do comportamento desejado do aplicativo.<br><br>Consulte o código na seção a seguir para esse caso específico e os erros que o diagnosticam.|
| **Caso 2**: O erro não pode ser resolvido com uma entrada interativa | Para erros de rede e transitórios/temporários ou outras falhas, a execução de uma solicitação AcquireToken interativa não resolve o problema. Prompts de entrada interativa desnecessárias também podem frustrar os usuários finais. A ADAL tenta automaticamente uma única repetição para a maioria dos erros em falhas de AcquireTokenSilent.<br><br>O aplicativo cliente também pode tentar uma nova tentativa posteriormente, mas quando e como fazer isso depende do comportamento do aplicativo e da experiência do usuário final desejada. Por exemplo, o aplicativo pode fazer uma nova tentativa de AcquireTokenSilent após alguns minutos ou em resposta a alguma ação do usuário final. Uma repetição imediata resultará no aplicativo que está sendo limitado e não deverá ser tentada.<br><br>Uma nova tentativa subsequente falha com o mesmo erro não significa que o cliente deve fazer uma solicitação interativa usando AcquireToken, pois não resolve o erro.<br><br>Consulte o código na seção a seguir para esse caso específico e os erros que o diagnosticam. |

### <a name="net"></a>.NET

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- acquireTokenSilentAsync(…)
- acquireTokenSilentSync(…) 
- [preterido] acquireTokenSilent (...)
- [preterido] acquireTokenByRefreshToken (...) 

Seu código seria implementado da seguinte maneira:

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

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- acquireTokenSilentSync(…)
- acquireTokenSilentAsync(...)
- [preterido] acquireTokenSilent (...)

Seu código seria implementado da seguinte maneira:

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

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- acquireTokenSilentWithResource(…)

Seu código seria implementado da seguinte maneira:

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

AcquireToken é o método ADAL padrão usado para obter tokens. Nos casos em que a identidade do usuário é necessária, o AcquireToken tenta primeiro obter um token silenciosamente e, em seguida, exibe a interface do usuário, se necessário (a menos que PromptBehavior. nunca seja passado). Nos casos em que a identidade do aplicativo é necessária, AcquireToken tenta obter um token, mas não mostra a interface do usuário, pois não há nenhum usuário final. 

Ao lidar com erros de AcquireToken, o tratamento de erros depende da plataforma e do cenário que o aplicativo está tentando atingir. 

O sistema operacional também pode gerar um conjunto de erros, que exigem o tratamento de erros dependente do aplicativo específico. Para obter mais informações, consulte "erros do sistema operacional" em [referência de erro e registro em log](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Cenários de aplicações

- Aplicativos cliente nativos (iOS, Android, .NET desktop ou Xamarin)
- Aplicativos Web que chamam uma API de recurso (.NET)
- Aplicativos de página única (JavaScript)
- Aplicativos de serviço a serviço (.NET, Java)
  - Todos os cenários, incluindo em nome de
  - Cenários específicos em nome de

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casos de erro e etapas acionáveis: Aplicativos cliente nativos

Se você estiver criando um aplicativo cliente nativo, há alguns casos de tratamento de erros a serem considerados relacionados a problemas de rede, falhas transitórias e outros erros específicos da plataforma. Na maioria dos casos, um aplicativo não deve executar repetições imediatas, mas sim aguardar a interação do usuário final que solicita uma entrada. 

Há alguns casos especiais em que uma única repetição pode resolver o problema. Por exemplo, quando um usuário precisa habilitar dados em um dispositivo ou concluído o download do Azure AD Broker após a falha inicial. 

Em casos de falha, um aplicativo pode apresentar a IU para permitir que o usuário final execute alguma interação que solicite uma nova tentativa. Por exemplo, se o dispositivo falhar para um erro offline, um botão "tentar entrar novamente" solicitando uma nova tentativa de AcquireToken em vez de repetir a falha imediatamente. 

O tratamento de erros em aplicativos nativos pode ser definido por dois casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Erro sem nova tentativa (maioria dos casos) | 1. Não tente repetição imediata. Apresente a interface do usuário final com base no erro específico que invoca uma nova tentativa ("tentar entrar novamente", "baixar o aplicativo Azure AD Broker", etc.). |
| **Caso 2**:<br>Erro com nova tentativa | 1. Execute uma única tentativa, pois o usuário final pode ter inserido um estado que resulta em um êxito.<br><br>2. Se a repetição falhar, apresente a interface do usuário final com base no erro específico que invoca uma nova tentativa ("tentar entrar novamente", "baixar o aplicativo Azure AD Broker", etc.). |

> [!IMPORTANT]
> Se uma conta de usuário for passada para a ADAL em uma chamada silenciosa e falhar, a solicitação interativa subsequente permitirá que o usuário final entre usando uma conta diferente. Após uma AcquireToken bem-sucedida usando uma conta de usuário, o aplicativo deve verificar se o usuário conectado corresponde ao objeto de usuário local dos aplicativos. Uma incompatibilidade não gera uma exceção (exceto no objetivo C), mas deve ser considerada em casos em que um usuário é conhecido localmente antes das solicitações de autenticação (como uma chamada silenciosa com falha).
>

#### <a name="net"></a>.NET

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com todas as AcquireToken não silenciosas (...) Métodos de ADAL, *exceto*: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion,…)   

Seu código seria implementado da seguinte maneira:

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
> O ADAL .NET tem uma consideração extra, pois dá suporte a PromptBehavior. Never, que tem comportamento como AcquireTokenSilent.
>

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- acquireToken (..., PromptBehavior. Never)

Seu código seria implementado da seguinte maneira:

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

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com todas as AcquireToken não silenciosas (...) Métodos ADAL. 

Seu código seria implementado da seguinte maneira:

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

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com todas as AcquireToken não silenciosas (...) Métodos ADAL. 

Seu código seria implementado da seguinte maneira:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casos de erro e etapas acionáveis: Aplicativos Web que chamam uma API de recurso (.NET)

Se você estiver criando um aplicativo Web .NET que chama obter um token usando um código de autorização para um recurso, o único código necessário é um manipulador padrão para o caso genérico. 

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenByAuthorizationCodeAsync(…)

Seu código seria implementado da seguinte maneira:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casos de erro e etapas acionáveis: Aplicativos de página única (Adal. js)

Se você estiver criando um aplicativo de página única usando Adal. js com AcquireToken, o código de tratamento de erro será semelhante ao de uma chamada silenciosa típica. Especificamente, no Adal. js, o AcquireToken nunca mostra uma interface do usuário. 

Um AcquireToken com falha tem os seguintes casos:

|  |  |
|------|-------------|
| **Caso 1**:<br>Resolvível com uma solicitação interativa | 1. Se o logon () falhar, não execute a repetição imediata. Só tente novamente depois que a ação do usuário solicitar uma nova tentativa.|
| **Caso 2**:<br>Não resolvida com uma solicitação interativa. Erro com nova tentativa. | 1. Execute uma única tentativa, pois a principal do usuário final inseriu um estado que resulta em um êxito.<br><br>2. Se a repetição falhar, apresente ao usuário final uma ação com base no erro específico que pode invocar uma nova tentativa ("tentar entrar novamente"). |
| **Caso 3**:<br>Não resolvida com uma solicitação interativa. O erro não é repetido. | 1. Não tente repetição imediata. Apresente ao usuário final uma ação com base no erro específico que pode invocar uma nova tentativa ("tentar entrar novamente"). |

Seu código seria implementado da seguinte maneira:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casos de erro e etapas acionáveis: aplicativos de serviço a serviço (somente .NET)

Se você estiver criando um aplicativo de serviço a serviço que usa AcquireToken, haverá alguns erros de chave que seu código deve manipular. O único recurso para falha é retornar o erro de volta para o aplicativo de chamada (para casos em nome de) ou aplicar uma estratégia de repetição. 

#### <a name="all-scenarios"></a>Todos os cenários

Para *todos os* cenários de aplicativo de serviço a serviço, incluindo em nome de:

- Não tente uma repetição imediata. A ADAL tenta uma única repetição para determinadas solicitações com falha. 
- Só continue tentando novamente depois que uma ação de usuário ou aplicativo for solicitada uma nova tentativa. Por exemplo, um aplicativo daemon que funciona em algum intervalo definido deve aguardar até que o próximo intervalo tente novamente.

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenAsync(…, IClientAssertionCertification, …)
- AcquireTokenAsync(…,ClientCredential, …)
- AcquireTokenAsync(…,ClientAssertion, …)
- AcquireTokenAsync(…,UserAssertion, …)

Seu código seria implementado da seguinte maneira:

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

#### <a name="on-behalf-of-scenarios"></a>Cenários em nome de

Para cenários de aplicativo *em nome de* serviço a serviço.

As diretrizes a seguir fornecem exemplos de tratamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenAsync (..., userdeclaration,...)

Seu código seria implementado da seguinte maneira:

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

Criamos um [exemplo completo](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) que demonstra esse cenário.

## <a name="error-and-logging-reference"></a>Referência de log e erro

### <a name="logging-personal-identifiable-information-pii--organizational-identifiable-information-oii"></a>Registrando informações de identificação pessoal (PII) & informações de identificação organizacional (OII)
Por padrão, o registro em log da ADAL não captura nem registra nenhuma PII ou OII. A biblioteca permite que os desenvolvedores de aplicativos ativem isso por meio de um setter na classe de agente. Ao ativar PII ou OII, o aplicativo assume a responsabilidade por lidar com segurança de dados altamente confidenciais e obedecer a quaisquer requisitos regulatórios.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Erros da biblioteca ADAL

Para explorar erros de ADAL específicos, o código-fonte no [repositório Azure-ActiveDirectory-library-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) é a melhor referência de erro.

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de log de erros

O log do ADAL .NET é alterado dependendo da plataforma em que está trabalhando. Consulte o [wiki de registro em log](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) para obter código sobre como habilitar o registro em log.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Erros da biblioteca ADAL

Para explorar erros de ADAL específicos, o código-fonte no [repositório Azure-ActiveDirectory-library-for-Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros do sistema operacional

OS erros do sistema operacional Android são expostos por meio da Autenticaçãoexception na ADAL, são identificáveis como "SERVER_INVALID_REQUEST" e podem ser ainda mais granulares por meio das descrições de erro. 

Para obter uma lista completa de erros comuns e quais etapas executar quando seu aplicativo ou os usuários finais os encontram, consulte o [wiki do Adal para Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de log de erros

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

#### <a name="adal-library-errors"></a>Erros da biblioteca ADAL

Para explorar erros de ADAL específicos, o código-fonte no [repositório Azure-ActiveDirectory-library-for-ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros do sistema operacional

os erros do iOS podem surgir durante a entrada quando os usuários usam exibições da Web e a natureza da autenticação. Isso pode ser causado por condições como erros de SSL, tempos limite ou erros de rede:

- Para o compartilhamento de direitos, os logons não são persistentes e o cache aparece vazio. Você pode resolver adicionando a seguinte linha de código ao conjunto de chaves:`[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Para o conjunto de erros NsUrlDomain, a ação muda dependendo da lógica do aplicativo. Consulte a [documentação de referência do NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) para instâncias específicas que podem ser tratadas.
- Confira [problemas comuns de Adal obj-c](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) para obter a lista de erros comuns mantidos pela equipe da Adal Objective-c.

#### <a name="guidance-for-error-logging-code"></a>Diretrizes para código de log de erros

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

### <a name="guidance-for-error-logging-code---javascript"></a>Diretrizes para código de log de erros-JavaScript 

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

* [Guia do desenvolvedor do Azure AD][AAD-Dev-Guide]
* [Bibliotecas de autenticação do Azure AD][AAD-Auth-Libraries]
* [Cenários de autenticação do Azure AD][AAD-Auth-Scenarios]
* [Integrando aplicativos com o Azure Active Directory][AAD-Integrating-Apps]

Use a seção de comentários a seguir para fornecer comentários e nos ajudar a refinar e formatar nosso conteúdo.

[![Mostra o botão "Entrar com a conta da Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

