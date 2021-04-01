---
title: Lidar com erros e exceções no MSAL4J
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, Acesso Condicional reclama desafios e retréis em aplicações MSAL4J.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/27/2020
ms.author: marsma
ms.reviewer: saeeda, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8563804a736c37acc9a96eb4a186933507f34200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760681"
---
# <a name="handle-errors-and-exceptions-in-msal-for-java"></a>Lidar com erros e exceções no MSAL para Java

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-java"></a>Manipulação de erros no MSAL para Java

No MSAL para Java, existem três tipos de exceções: `MsalClientException` , e ; todas as que `MsalServiceException` `MsalInteractionRequiredException` herdam de `MsalException` .

- `MsalClientException` é lançado quando ocorre um erro que é local para a biblioteca ou dispositivo.
- `MsalServiceException` é lançado quando o serviço de fichas de segurança (STS) retorna uma resposta de erro ou outro erro de rede ocorre.
- `MsalInteractionRequiredException` é jogado quando a interação da UI é necessária para que a autenticação tenha sucesso.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expõe cabeçalhos HTTP devolvidos nos pedidos ao STS. Aceda-os através de `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um dos códigos de estado comum devolvidos da MSAL para a Java quando telefona `AcquireTokenSilently()` é `InvalidGrantError` . Isto significa que é necessária uma interação adicional do utilizador antes de um token de autenticação poder ser emitido. A sua aplicação deve voltar a ligar para a biblioteca de autenticação, mas em modo interativo, enviando `AuthorizationCodeParameters` ou `DeviceCodeParameters` para aplicações de clientes públicos.

A maior parte do tempo, quando falha, é porque a cache simbólica `AcquireTokenSilently` não tem um símbolo correspondente ao seu pedido. Os tokens de acesso expiram em uma hora, e `AcquireTokenSilently` tentarão obter um novo com base num token de atualização. Em termos OAuth2, este é o fluxo Refresh Token. Este fluxo também pode falhar por várias razões, tais como quando um inquilino admin configura políticas de login mais rigorosas.

Algumas condições que resultam neste erro são fáceis de resolver para os utilizadores. Por exemplo, podem ter de aceitar termos de uso ou o pedido não pode ser preenchido com a configuração atual porque a máquina precisa de se ligar a uma rede corporativa específica.

A MSAL expõe um `reason` campo, que pode utilizar para proporcionar uma melhor experiência ao utilizador. Por exemplo, o `reason` campo pode levá-lo a dizer ao utilizador que a sua palavra-passe expirou ou que terá de fornecer consentimento para utilizar alguns recursos. Os valores suportados fazem parte do  `InteractionRequiredExceptionReason` enum:

| Razão | Significado | Manuseamento recomendado |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Chamada `acquireToken` com parâmetros interativos. |
| `AdditionalAction` | A condição pode ser resolvida através de uma interação corretiva adicional com o sistema fora do fluxo de autenticação interativa. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a tomar. A aplicação de chamadas pode optar por ocultar fluxos que requerem ação adicional se o utilizador não conseguir completar a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida neste momento. Lance o fluxo de autenticação interativa para mostrar uma mensagem explicando a condição. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken` retornará o `UserCanceled` erro depois de o utilizador ler a mensagem e fechar a janela. A aplicação pode optar por ocultar fluxos que resultem em mensagem se o utilizador não beneficiar da mensagem. |
| `ConsentRequired`| O consentimento do utilizador está em falta ou foi revogado. |Ligue `acquireToken` com parâmetros interativos para que o utilizador possa dar o seu consentimento. |
| `UserPasswordExpired` | A senha do utilizador expirou. | Ligue `acquireToken` com parâmetro interativo para que o utilizador possa redefinir a sua palavra-passe. |
| `None` |  Mais detalhes são fornecidos. A condição pode ser resolvida pela interação do utilizador durante o fluxo de autenticação interativa. | Chamada `acquireToken` com parâmetros interativos. |

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passos seguintes

Considere permitir o [início de Sessão em MSAL para java](msal-logging-java.md) para ajudá-lo a diagnosticar e depurar problemas.
