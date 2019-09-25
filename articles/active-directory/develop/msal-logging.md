---
title: Fazendo logon em aplicativos MSAL | Plataforma de identidade da Microsoft
description: Saiba mais sobre o registro em log nos aplicativos da MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268589"
---
# <a name="logging-in-msal-applications"></a>Registrando em log em aplicativos MSAL

Os aplicativos MSAL (biblioteca de autenticação da Microsoft) geram mensagens de log que podem ajudar a diagnosticar problemas. Um aplicativo pode configurar o registro em log com algumas linhas de código e ter controle personalizado sobre o nível de detalhes e se os dados pessoais e organizacionais serão ou não registrados. Recomendamos que você crie um retorno de chamada de log MSAL e forneça uma maneira para os usuários enviarem logs quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de log

O MSAL fornece vários níveis de detalhes de log:

- Erro: Indica que algo deu errado e um erro foi gerado. Use para depuração e identificação de problemas.
- Aviso: Não há, necessariamente, um erro ou uma falha, mas destina-se a problemas de diagnóstico e de identificação.
- Detalhes O MSAL registrará em log eventos destinados a fins informativos, não necessariamente destinados à depuração.
- extensa Predefinição. O MSAL registra em log os detalhes completos do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o agente de log do MSAL não captura dados pessoais ou organizacionais altamente confidenciais. A biblioteca fornece a opção de habilitar o registro em log de dados pessoais e organizacionais se você decidir fazer isso.

## <a name="logging-in-msalnet"></a>Registrando em MSAL.NET

 > [!NOTE]
 > Para obter mais informações sobre MSAL.NET, confira o [wiki do MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Obtenha exemplos de registro em log do MSAL.NET e muito mais.
 
No MSAL 3. x, o registro em log é definido por aplicativo na criação `.WithLogging` do aplicativo usando o modificador de construtor. Esse método usa parâmetros opcionais:

- O *nível* permite que você decida qual nível de log deseja. Configurá-lo como erros só receberá erros
- O *PiiLoggingEnabled* permite que você registre dados pessoais e organizacionais se definido como true. Por padrão, isso é definido como false, para que seu aplicativo não Registre dados pessoais.
- *LogCallback* é definido como um delegado que faz o registro em log. Se *PiiLoggingEnabled* for true, esse método receberá as mensagens duas vezes: uma vez com o parâmetro *containsPii* é igual a false e a mensagem sem dados pessoais, e uma segunda vez com o parâmetro *containsPii* é igual a true e o a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contiver dados pessoais), a mensagem será a mesma.
- *DefaultLoggingEnabled* habilita o log padrão para a plataforma. Por padrão, é false. Se você defini-lo como verdadeiro, ele usará o rastreamento de eventos em aplicativos de desktop/UWP, NSLog no iOS e logcat no Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Registrando em log no MSAL. js

 Você pode habilitar o registro em log no MSAL. js passando um objeto do agente durante a configuração `UserAgentApplication` para criar uma instância. Este objeto de agente tem as seguintes propriedades:

- `localCallback`: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Implemente o método localCallback dependendo de como você deseja redirecionar os logs.

- `level`(opcional): o nível de log configurável. Os níveis de log com suporte são: Erro, aviso, informações, detalhado. O valor padrão é info.

- `piiLoggingEnabled`(opcional): permite que você registre dados pessoais e organizacionais se definido como true. Por padrão, isso é definido como false para que seu aplicativo não Registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como console, logcat ou NSLog. O padrão é definido como false.

- `correlationId`(opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é RFC4122 versão 4 GUID (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Fazendo logon no MSAL para iOS e macOS

Defina um retorno de chamada para capturar o registro em log do MSAL e incorporá-lo no log do seu próprio aplicativo. A assinatura do retorno de chamada tem esta aparência:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Por exemplo:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

Swift
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Informações de identificação pessoal (PII)

Por padrão, o MSAL não captura nem registra nenhuma PII. A biblioteca permite que os desenvolvedores de aplicativos ativem isso por meio de uma propriedade na classe MSALLogger. Ao ativar a PII, o aplicativo assume a responsabilidade por lidar com segurança de dados altamente confidenciais e seguindo os requisitos regulatórios.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

Swift
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de log

Para definir o nível de log ao fazer logon usando o MSAL para iOS e macOS, use um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desabilitar todo o log |
| `MSALLogLevelError` | Nível padrão, imprime informações somente quando ocorrem erros |
| `MSALLogLevelWarning` | Avisos |
| `MSALLogLevelInfo` |  Pontos de entrada de biblioteca, com parâmetros e várias operações de conjunto de chaves |
|`MSALLogLevelVerbose`     |  Rastreio da API       |

Por exemplo:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 Swift
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato de mensagem de log

A parte da mensagem das mensagens de log MSAL está no formato de`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de data/hora é útil para rastrear problemas. As informações de carimbo de data e hora e ID de correlação estão disponíveis na mensagem de log. O único local confiável para recuperá-los é de mensagens de registro em log do MSAL.
