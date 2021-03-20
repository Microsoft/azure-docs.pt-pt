---
title: Erros e exceções no MSAL para iOS/macOS
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções no MSAL para iOS/macOS
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763489"
---
# <a name="logging-in-msal-for-iosmacos"></a>Registos no MSAL para iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL para iOS e macOS logging-ObjC

Desaprova uma chamada para capturar o registo msal e incorpore-o na registo da sua própria aplicação. A assinatura para a chamada é assim:

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

### <a name="personal-data"></a>Dados pessoais

Por padrão, a MSAL não captura nem regista quaisquer dados pessoais. A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled` ligar, a aplicação assume a responsabilidade de lidar com segurança dados altamente sensíveis e seguindo os requisitos regulamentares.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Níveis de registo

Para definir o nível de registo quando iniciar sessão utilizando o MSAL para iOS e macOS, utilize um dos seguintes valores:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Desativar todos os registos |
| `MSALLogLevelError` | Nível predefinido, imprime a informação apenas quando ocorrem erros |
| `MSALLogLevelWarning` | Avisos |
| `MSALLogLevelInfo` |  Pontos de entrada na biblioteca, com parâmetros e várias operações de chaveiro |
|`MSALLogLevelVerbose`     |  Rastreio da API |

Por exemplo:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato de mensagem de registo

A parte da mensagem das mensagens de registo MSAL está no formato de `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer identificações de correlação e timetamps são úteis para rastrear problemas. As informações de identificação de tempo e correlação estão disponíveis na mensagem de registo. O único lugar fiável para recuperá-las é a partir de mensagens de registo MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL para iOS e macOS logging-Swift

Desaprova uma chamada para capturar o registo msal e incorpore-o na registo da sua própria aplicação. A assinatura (representada no Objectivo-C) para a chamada é assim:

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

### <a name="personal-data"></a>Dados pessoais

Por padrão, a MSAL não captura nem regista quaisquer dados pessoais. A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled` ligar, a aplicação assume a responsabilidade de lidar com segurança dados altamente sensíveis e seguindo os requisitos regulamentares.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de registo

Para definir o nível de registo quando iniciar sessão utilizando o MSAL para iOS e macOS, utilize um dos seguintes valores:

|Level  |Description |
|---------|---------|
| `MSALLogLevelNothing`| Desativar todos os registos |
| `MSALLogLevelError` | Nível predefinido, imprime a informação apenas quando ocorrem erros |
| `MSALLogLevelWarning` | Avisos |
| `MSALLogLevelInfo` |  Pontos de entrada na biblioteca, com parâmetros e várias operações de chaveiro |
|`MSALLogLevelVerbose`     |  Rastreio da API |

Por exemplo:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato de mensagem de registo

A parte da mensagem das mensagens de registo MSAL está no formato de `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer identificações de correlação e timetamps são úteis para rastrear problemas. As informações de identificação de tempo e correlação estão disponíveis na mensagem de registo. O único lugar fiável para recuperá-las é a partir de mensagens de registo MSAL.

---

## <a name="next-steps"></a>Passos seguintes

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft.](sample-v2-code.md)