---
title: Log nos aplicativos MSAL (biblioteca de autenticação da Microsoft)
titleSuffix: Microsoft identity platform
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
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12badd84bd929bdeb7b60ad6e99d6b3169e5022
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150458"
---
# <a name="logging-in-msal-applications"></a>Registrando em log em aplicativos MSAL

Os aplicativos MSAL (biblioteca de autenticação da Microsoft) geram mensagens de log que podem ajudar a diagnosticar problemas. Um aplicativo pode configurar o registro em log com algumas linhas de código e ter controle personalizado sobre o nível de detalhes e se os dados pessoais e organizacionais serão ou não registrados. Recomendamos que você crie um retorno de chamada de log MSAL e forneça uma maneira para os usuários enviarem logs quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de log

O MSAL fornece vários níveis de detalhes de log:

- Erro: indica que algo deu errado e um erro foi gerado. Use para depuração e identificação de problemas.
- Aviso: não há necessariamente erro ou falha, mas destina-se a problemas de diagnóstico e de identificação.
- Info: o MSAL registrará em log eventos destinados a fins informativos não necessariamente destinados à depuração.
- Verbose: padrão. O MSAL registra em log os detalhes completos do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o agente de log do MSAL não captura dados pessoais ou organizacionais altamente confidenciais. A biblioteca fornece a opção de habilitar o registro em log de dados pessoais e organizacionais se você decidir fazer isso.

## <a name="logging-in-msalnet"></a>Registrando em MSAL.NET

 > [!NOTE]
 > Consulte o [wiki do MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para obter exemplos de registro em log do MSAL.net e muito mais.

No MSAL 3. x, o registro em log é definido por aplicativo na criação do aplicativo usando o modificador do `.WithLogging` Builder. Esse método usa parâmetros opcionais:

- `Level` permite que você decida qual nível de log você deseja. Configurá-lo como erros só receberá erros
- `PiiLoggingEnabled` permite que você registre dados pessoais e organizacionais se definidos como true. Por padrão, isso é definido como false, para que seu aplicativo não Registre dados pessoais.
- `LogCallback` é definido como um delegado que faz o registro em log. Se `PiiLoggingEnabled` for true, esse método receberá as mensagens duas vezes: uma vez com o parâmetro `containsPii` é igual a false e a mensagem sem dados pessoais, e uma segunda vez com o parâmetro `containsPii` igual a true e a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contiver dados pessoais), a mensagem será a mesma.
- `DefaultLoggingEnabled` habilita o log padrão para a plataforma. Por padrão, é false. Se você defini-lo como verdadeiro, ele usará o rastreamento de eventos em aplicativos de desktop/UWP, NSLog no iOS e logcat no Android.

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

## <a name="logging-in-msal-for-android-using-java"></a>Registro em log no MSAL para Android usando Java

Ative o logon na criação do aplicativo Criando um retorno de chamada de log. O retorno de chamada usa estes parâmetros:

- `tag` é uma cadeia de caracteres passada para o retorno de chamada pela biblioteca. Ele é associado à entrada de log e pode ser usado para classificar mensagens de registro em log.
- `logLevel` permite que você decida qual nível de log você deseja. Os níveis de log com suporte são: `Error`, `Warning`, `Info`e `Verbose`.
- `message` é o conteúdo da entrada de log.
- `containsPII` especifica se as mensagens que contêm dados pessoais ou dados organizacionais são registradas. Por padrão, isso é definido como false, para que seu aplicativo não Registre dados pessoais. Se `containsPII` for `true`, esse método receberá as mensagens duas vezes: uma vez com o parâmetro `containsPII` definido como `false` e o `message` sem dados pessoais, e uma segunda vez com o parâmetro `containsPii` definido como `true` e a mensagem poderá conter dados pessoais. Em alguns casos (quando a mensagem não contiver dados pessoais), a mensagem será a mesma.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Por padrão, o MSAL Logger não capturará nenhuma informação de identificação pessoal ou informações de identificação organizacional.
Para habilitar o log de informações de identificação pessoal ou informações de identificação organizacional:

```java
Logger.getInstance().setEnablePII(true);
```

Para desabilitar o registro em log de dados pessoais e dados da organização:

```java
Logger.getInstance().setEnablePII(false);
```

Por padrão, o log em logcat está desabilitado. Para habilitar: 
```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="logging-in-msaljs"></a>Registrando em log no MSAL. js

 Habilite o registro em log no MSAL. js passando um objeto do agente durante a configuração para criar uma instância de `UserAgentApplication`. Este objeto de agente tem as seguintes propriedades:

- `localCallback`: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Implemente o método localCallback dependendo de como você deseja redirecionar os logs.
- `level` (opcional): o nível de log configurável. Os níveis de log com suporte são: `Error`, `Warning`, `Info`e `Verbose`. A predefinição é `Info`.
- `piiLoggingEnabled` (opcional): se definido como true, registra dados pessoais e organizacionais. Por padrão, isso é falso para que seu aplicativo não Registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como console, logcat ou NSLog.
- `correlationId` (opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é RFC4122 versão 4 GUID (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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
| `MSALLogLevelWarning` | :| |
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

A parte da mensagem das mensagens de log MSAL está no formato de `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer IDs de correlação e carimbos de data/hora é útil para rastrear problemas. As informações de carimbo de data e hora e ID de correlação estão disponíveis na mensagem de log. O único local confiável para recuperá-los é de mensagens de registro em log do MSAL.
