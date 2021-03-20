---
title: Erro de aplicação do cliente ADAL manuseando as melhores práticas | Rio Azure
description: Fornece orientação de manuseamento de erros e boas práticas para aplicações de clientes ADAL.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383739"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Manipulação de erros das melhores práticas para clientes da Azure Ative Directory Authentication Library (ADAL)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Este artigo fornece orientações sobre o tipo de erros que os desenvolvedores podem encontrar, quando utilizam o ADAL para autenticar os utilizadores. Ao utilizar o ADAL, existem vários casos em que um desenvolvedor pode precisar de intervir e lidar com erros. O manuseamento adequado de erros garante uma grande experiência do utilizador final e limita o número de vezes que o utilizador final precisa de iniciar sing.

Neste artigo, exploramos os casos específicos de cada plataforma suportada pela ADAL, e como a sua aplicação pode lidar corretamente com cada caso. A orientação de erro é dividida em duas categorias mais amplas, com base nos padrões de aquisição simbólicos fornecidos pelas APIs ADAL:

- **AcquireTokenSilent**: O cliente tenta obter um símbolo silenciosamente (sem UI), e pode falhar se a ADAL não tiver sucesso. 
- **AcquireToken**: O Cliente pode tentar a aquisição silenciosa, mas também pode realizar pedidos interativos que requerem login.

> [!TIP]
> É uma boa ideia registar todos os erros e exceções ao usar ADAL e Azure AD. Os registos não só são úteis para compreender a saúde geral da sua aplicação, como também são importantes para depurar problemas mais amplos. Embora a sua aplicação possa recuperar de certos erros, podem sugerir problemas de design mais amplos que requerem alterações de código para resolver. 
> 
> Ao implementar as condições de erro abrangidas por este documento, deverá registar o código de erro e a descrição pelas razões discutidas anteriormente. Consulte a [referência De Erro e registo de registos](#error-and-logging-reference) por exemplos de código de registo. 
>

## <a name="acquiretokensilent"></a>AdquirirTokenSilent

AcquireTokenSilent tenta obter um símbolo com a garantia de que o utilizador final não vê uma Interface de Utilizador (UI). Existem vários casos em que a aquisição silenciosa pode falhar, e precisa de ser tratada através de pedidos interativos ou por um manipulador predefinido. Mergulhamos nas especificidades de quando e como empregar cada caso nas secções que se seguem.

Existe um conjunto de erros gerados pelo sistema operativo, que podem exigir o tratamento de erros específicos da aplicação. Para obter mais informações, consulte a secção de erros do "Sistema Operativo" em [Erro e referência de registo.](#error-and-logging-reference) 

### <a name="application-scenarios"></a>Cenários de aplicações

- [Aplicações de clientes nativos](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) (iOS, Android, .NET Desktop ou Xamarin)
- [Aplicações](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) de cliente web chamando um [recurso](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) (.NET)

### <a name="error-cases-and-actionable-steps"></a>Casos de erro e passos accuáveis

Fundamentalmente, existem dois casos de erros de AcquireTokenSilent:

| Caso | Description |
|------|-------------|
| **Caso 1**: Erro é resolúvel com um sinal interativo | Para erros causados pela falta de fichas válidas, é necessário um pedido interativo. Especificamente, a procura de cache e um token de atualização inválido/expirado requerem uma chamada AcquireToken para resolver.<br><br>Nestes casos, o utilizador final tem de ser solicitado a iniciar sedu. A aplicação pode optar por fazer um pedido interativo imediatamente, após a interação do utilizador final (como carregar num botão de inscrição) ou mais tarde. A escolha depende do comportamento desejado da aplicação.<br><br>Consulte o código na secção seguinte para este caso específico e os erros que o diagnosticam.|
| **Caso 2**: O erro não é resolúvel com um sinal interativo | Para erros de rede e transitórios/temporários, ou outras falhas, a realização de um pedido interativo AcquireToken não resolve o problema. Pedidos de inscrição interativos desnecessários também podem frustrar os utilizadores finais. A ADAL tenta automaticamente uma única repetição para a maioria dos erros nas falhas do AcquireTokenSilent.<br><br>A aplicação do cliente também pode tentar uma nova tentativa em algum momento posterior, mas quando e como é dependente do comportamento da aplicação e da experiência desejada pelo utilizador final. Por exemplo, a aplicação pode fazer uma relemdação AcquireTokenSilent após alguns minutos, ou em resposta a alguma ação do utilizador final. Uma nova tentativa imediata resultará no estrangulamento da aplicação e não deve ser tentada.<br><br>Uma tentativa subsequente de redatório com o mesmo erro não significa que o cliente deve fazer um pedido interativo usando o AcquireToken, uma vez que não resolve o erro.<br><br>Consulte o código na secção seguinte para este caso específico e os erros que o diagnosticam. |

### <a name="net"></a>.NET

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- adquirirTokenSilentAsync(...)
- adquirirTokenSilentSync(...) 
- [prevadido] adquireTokenSilent(...)
- [prevadida] adquireTokenByRefreshToken(...) 

O seu código seria implementado da seguinte forma:

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

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- adquirirTokenSilentSync(...)
- adquirirTokenSilentAsync(...)
- [prevadido] adquireTokenSilent(...)

O seu código seria implementado da seguinte forma:

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

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- adquirirTokenSilentWithResource(...)

O seu código seria implementado da seguinte forma:

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

AcquireToken é o método ADAL padrão usado para obter fichas. Nos casos em que a identidade do utilizador é necessária, a AcquireToken tenta obter um símbolo silenciosamente primeiro, depois exibe UI se necessário (a menos que o PromptBehavior.Nunca seja passado). Nos casos em que a identidade da aplicação é necessária, a AcquireToken tenta obter um token, mas não mostra UI, uma vez que não existe um utilizador final. 

Ao lidar com erros do AcquireToken, o tratamento de erros está dependente da plataforma e do cenário que a aplicação está a tentar alcançar. 

O sistema operativo também pode gerar um conjunto de erros, que requerem o manuseamento de erros dependentes da aplicação específica. Para obter mais informações, consulte "Erros do Sistema Operativo" em [Erro e referência de registo.](#error-and-logging-reference) 

### <a name="application-scenarios"></a>Cenários de aplicações

- Aplicações de clientes nativos (iOS, Android, .NET Desktop ou Xamarin)
- Aplicações web que chamam a um recurso API (.NET)
- Aplicações de uma só página (JavaScript)
- Aplicações de serviço-a-serviço (.NET, Java)
  - Todos os cenários, incluindo em nome de
  - Em nome de cenários específicos

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Casos de erro e etapas acccáveis: Aplicações de clientes nativos

Se estiver a construir uma aplicação de cliente nativo, existem alguns casos de manipulação de erros a considerar que se relacionam com problemas de rede, falhas transitórias e outros erros específicos da plataforma. Na maioria dos casos, uma aplicação não deve realizar retréis imediatos, mas sim esperar pela interação do utilizador final que indique uma sindução. 

Há alguns casos especiais em que uma única repetição pode resolver a questão. Por exemplo, quando um utilizador precisa de ativar dados num dispositivo, ou de ter concluído o download do corretor AZure AD após a falha inicial. 

Em caso de falha, uma aplicação pode apresentar UI para permitir ao utilizador final realizar alguma interação que indique uma nova volta. Por exemplo, se o dispositivo falhou por um erro offline, um botão "Tente iniciar novamente" o que leva a uma redandimento acquireToken em vez de voltar imediatamente a tentar a falha. 

O tratamento de erros em aplicações nativas pode ser definido por dois casos:

|  |  |
|------|-------------|
| **Caso 1:**<br>Erro não retável (na maioria dos casos) | 1. Não tente voltar a tentar. Apresente o UI do utilizador final com base no erro específico que invoca uma nova tentativa (por exemplo, "Tente entrar novamente" ou "Descarregar a aplicação de corretor AD AZure"). |
| **Caso 2:**<br>Erro retável | 1. Realizar uma única repetição, uma vez que o utilizador final pode ter entrado num estado que resulte num sucesso.<br><br>2. Se falhar novamente, apresente a UI do utilizador final com base no erro específico que invoca uma nova tentativa ("Tente entrar novamente", "Download Azure AD broker app", etc.). |

> [!IMPORTANT]
> Se uma conta de utilizador for transmitida à ADAL numa chamada silenciosa e falhar, o pedido interativo subsequente permite que o utilizador final assine através de uma conta diferente. Depois de um AcquireToken bem sucedido utilizando uma conta de utilizador, a aplicação deve verificar se o utilizador inscrito corresponde ao objeto de utilizador local das aplicações. Uma incompatibilidade não gera uma exceção (exceto no Objetivo C), mas deve ser considerada nos casos em que um utilizador seja conhecido localmente antes dos pedidos de autenticação (como uma chamada silenciosa falhada).
>

#### <a name="net"></a>.NET

A seguinte orientação fornece exemplos para o manuseamento de erros em conjunto com todos os Não-Silenciosos AcquireToken(...) Métodos ADAL, *exceto:* 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync(..., UserAssertion,...)   

O seu código seria implementado da seguinte forma:

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
> A ADAL .NET tem uma consideração extra, pois suporta o PromptBehavior.Never, que tem comportamentos como o AcquireTokenSilent.
>

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- adquirirToken (..., PromptBehavior.Never)

O seu código seria implementado da seguinte forma:

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

A seguinte orientação fornece exemplos para o manuseamento de erros em conjunto com todos os Não-Silenciosos AcquireToken(...) Métodos adal. 

O seu código seria implementado da seguinte forma:

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

A seguinte orientação fornece exemplos para o manuseamento de erros em conjunto com todos os Não-Silenciosos AcquireToken(...) Métodos adal. 

O seu código seria implementado da seguinte forma:

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

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Casos de erro e etapas accuáveis: Aplicações web que chamam a uma API de recurso (.NET)

Se estiver a construir uma aplicação web .NET que ligue recebe um token usando um código de autorização para um recurso, o único código necessário é um manipulador predefinido para o caso genérico. 

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenByAuthorizationCodeAsync(...)

O seu código seria implementado da seguinte forma:

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

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Casos de erro e etapas accuáveis: Aplicações de página única (adal.js)

Se estiver a construir uma aplicação de uma página utilizando adal.js com o AcquireToken, o código de manuseamento de erros é semelhante ao de uma chamada silenciosa típica. Especificamente em adal.js, a AcquireToken nunca mostra uma UI. 

A AcquireToken falhada tem os seguintes casos:

|  |  |
|------|-------------|
| **Caso 1:**<br>Resolvível com um pedido interativo | 1. Se o login falhar, não efetue a repetição imediata. Só a repetição após a ação do utilizador provoca uma nova mente.|
| **Caso 2:**<br>Não é reelegível com um pedido interativo. O erro é redapto. | 1. Realizar uma única repetição, uma vez que o comandante do utilizador final entrou num estado que resulta num sucesso.<br><br>2. Se falhar novamente, apresente ao utilizador final uma ação baseada no erro específico que pode invocar uma nova tentativa ("Tente entrar novamente"). |
| **Caso 3:**<br>Não é reelegível com um pedido interativo. O erro não é redapto. | 1. Não tente voltar a tentar. Presentear o utilizador final com uma ação baseada no erro específico que pode invocar uma nova tentativa ("Tente entrar novamente"). |

O seu código seria implementado da seguinte forma:

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

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Casos de erro e etapas accuáveis: aplicações de serviço-a-serviço (apenas.NET)

Se estiver a construir uma aplicação de serviço-a-serviço que utiliza o AcquireToken, existem alguns erros-chave que o seu código deve tratar. O único recurso à falha é devolver o erro à aplicação de chamadas (para casos em nome) ou aplicar uma estratégia de repreensão. 

#### <a name="all-scenarios"></a>Todos os cenários

Para *todos os* cenários de aplicação de serviço-a-serviço, incluindo em nome de:

- Não tente uma repetição imediata. A ADAL tenta uma única repetição para certos pedidos falhados. 
- Apenas continue a tentar novamente depois de um utilizador ou ação de aplicação é solicitado uma nova preensão. Por exemplo, uma aplicação de daemon que funcione em algum intervalo definido deve esperar até ao intervalo seguinte para voltar a tentar.

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

O seu código seria implementado da seguinte forma:

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

#### <a name="on-behalf-of-scenarios"></a>Em nome de cenários

Para *cenários de* aplicação em nome do serviço.

As seguintes orientações fornecem exemplos para o manuseamento de erros em conjunto com os métodos ADAL: 

- AcquireTokenAsync(..., UserAssertion, ...)

O seu código seria implementado da seguinte forma:

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

Construímos uma [amostra completa](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) que demonstra este cenário.

## <a name="error-and-logging-reference"></a>Referência de erro e de registo

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Registar informações pessoais identificáveis & informações organizacionais identificáveis 
Por predefinição, o registo da ADAL não captura nem regista qualquer informação pessoal identificável ou informação organizacional identificável. A biblioteca permite que os desenvolvedores de aplicações liguem isto através de um setter na classe Logger. Ao registar informações pessoais identificáveis ou informações organizacionais identificáveis, a app assume a responsabilidade de lidar com segurança dados altamente sensíveis e de cumprir quaisquer requisitos regulamentares.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>Erros da biblioteca ADAL

Para explorar erros ADAL específicos, o código fonte no [repositório azure-active-directorory-library-for-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) é a melhor referência de erro.

#### <a name="guidance-for-error-logging-code"></a>Orientação para código de registo de erros

ADAL .NET alterações de registo dependendo da plataforma em que está a ser trabalhada. Consulte o [wiki de registo](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) para obter código sobre como ativar a sessão.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>Erros da biblioteca ADAL

Para explorar erros ADAL específicos, o código fonte no [repositório azure-active-directorory-library-for-android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros do Sistema Operativo

Os erros do Sistema Operativo Android são expostos através da AutenticaçãoExcepção em ADAL, são identificáveis como "SERVER_INVALID_REQUEST", podendo ser ainda mais granulares através das descrições de erro. 

Para obter uma lista completa de erros comuns e que medidas tomar quando a sua app ou utilizadores finais os encontrarem, consulte o [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki). 

#### <a name="guidance-for-error-logging-code"></a>Orientação para código de registo de erros

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

Para explorar erros ADAL específicos, o código fonte no [repositório azure-active-director-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) é a melhor referência de erro.

#### <a name="operating-system-errors"></a>Erros do sistema operativo

Podem surgir erros no iOS durante a entrada de informação quando os utilizadores utilizam visualizações web e a natureza da autenticação. Isto pode ser causado por condições como erros TLS, intervalos ou erros de rede:

- Para a Partilha de Direitos, os logins não são persistentes e a cache parece vazia. Pode resolver adicionando a seguinte linha de código ao chaveiro: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Para o conjunto de erros NsUrlDomain, a ação muda dependendo da lógica da aplicação. Consulte a [documentação de referência da NSURLErrorDomain](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) para casos específicos que podem ser manuseados.
- Consulte [as questões comuns do ADAL Obj-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) para a lista de erros comuns mantidos pela equipa ADAL Objective-C.

#### <a name="guidance-for-error-logging-code"></a>Orientação para código de registo de erros

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

### <a name="guidance-for-error-logging-code---javascript"></a>Orientação para código de registo de erros - JavaScript 

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

* [Bibliotecas de autenticação ad Azure][AAD-Auth-Libraries]
* [Cenários de autenticação AD AZure][AAD-Auth-Scenarios]
* [Integração de Aplicações com Diretório Ativo Azure][AAD-Integrating-Apps]

Utilize a secção de comentários que se segue, para fornecer feedback e ajudar-nos a refinar e moldar o nosso conteúdo.

[![Mostra o botão "Iniciar com a Microsoft"][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

