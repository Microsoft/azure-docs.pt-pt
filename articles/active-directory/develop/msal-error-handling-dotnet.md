---
title: Lidar com erros e exceções no MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, O Acesso Condicional reclama desafios e retrações em MSAL.NET.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 381416384cacd44bdb1b08801f7b3174c9504d0b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761182"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>Lidar com erros e exceções no MSAL.NET

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>Manipulação de erros em MSAL.NET

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
| [MentedescientExcepção](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Não foi possível identificar o utilizador com sessão registada| A biblioteca não foi capaz de consultar o utilizador atual com sessão iniciada do Windows ou este utilizador não está atado a AD ou Azure AD (os utilizadores do local de trabalho não são suportados). Mitigação 1: no UWP, verifique se a aplicação tem as seguintes capacidades: Autenticação empresarial, Redes Privadas (Cliente e Servidor), Informação da Conta do Utilizador. Mitigação 2: Implemente a sua própria lógica para buscar o nome de utilizador (por exemplo, john@contoso.com ) e use o formulário que toma no nome de `AcquireTokenByIntegratedWindowsAuth` utilizador.|
| [MentedescientExcepção](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Este método baseia-se num protocolo exposto pelo Ative Directory (AD). Se um utilizador foi criado em Azure AD sem suporte AD (utilizador "gerido"), este método falhará. Os utilizadores criados em AD e apoiados por Azure AD (utilizadores "federados") podem beneficiar deste método não interativo de autenticação. Mitigação: Utilize a autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de estado comum devolvidos de MSAL.NET quando telefona `AcquireTokenSilent()` é `MsalError.InvalidGrantError` . Este código de estado significa que a aplicação deve voltar a ligar para a biblioteca de autenticação, mas em modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicações de clientes públicos, tem um desafio em aplicações Web). Isto porque é necessária uma interação adicional do utilizador antes de poder ser emitida uma ficha de autenticação.

A maior parte do tempo, quando `AcquireTokenSilent` falha, é porque a cache simbólica não tem fichas correspondentes ao seu pedido. Os tokens de acesso expiram em 1 hora, e `AcquireTokenSilent` tentarão obter um novo com base num token de atualização (em termos OAuth2, este é o fluxo "Refresh Token"). Este fluxo também pode falhar por várias razões, por exemplo, se um inquilino admin configurar políticas de login mais rigorosas. 

A interação visa fazer com que o utilizador faça uma ação. Algumas dessas condições são fáceis de resolver para os utilizadores (por exemplo, aceitar Termos de Utilização com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa de se ligar a uma rede corporativa específica). Alguns ajudam a autenticação multi-factor do utilizador ou instalam o Microsoft Authenticator no seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` enumeração classificação

A MSAL expõe um `Classification` campo, que pode ler para proporcionar uma melhor experiência ao utilizador. Por exemplo, para dizer ao utilizador que a sua palavra-passe expirou ou que terá de fornecer consentimento para utilizar alguns recursos. Os valores suportados fazem parte do `UiRequiredExceptionClassification` enum:

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
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ao ligar para uma API que requer acesso condicional a partir de MSAL.NET, a sua aplicação terá de lidar com exceções de reclamação. Isto aparecerá como um [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) onde a propriedade [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) não estará vazia.

Para lidar com o desafio de reclamação, terá de usar o `.WithClaim()` método da `PublicClientApplicationBuilder` classe.

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

MSAL.NET implementa um mecanismo simples de retripação de erros com códigos de erro HTTP 500-600.

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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>Próximos passos

Considere permitir o [início de Sessão em MSAL.NET](msal-logging-dotnet.md) para ajudá-lo a diagnosticar e depurar problemas.
