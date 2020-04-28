---
title: Sessão de registo em aplicativos MSAL [ Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o registo nas aplicações da Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084889"
---
# <a name="logging-in-msal-applications"></a>Registo em aplicações MSAL

As aplicações da Microsoft Authentication Library (MSAL) geram mensagens de registo que podem ajudar a diagnosticar problemas. Uma aplicação pode configurar o registo com algumas linhas de código, e ter controlo personalizado sobre o nível de detalhe e se os dados pessoais e organizacionais são ou não registados. Recomendamos que crie um callback de registo mSAL e forneça uma forma de os utilizadores apresentarem registos quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de exploração madeireira

A MSAL fornece vários níveis de detalhe de exploração madeireira:

- Erro: Indica que algo correu mal e foi gerado um erro. Utilização para depurar e identificar problemas.
- Aviso: Não houve necessariamente um erro ou falha, mas destinam-se a diagnósticos e problemas de localização.
- Informações: A MSAL registará eventos destinados a fins informísticos não destinados necessariamente à depuração.
- Verbose: Padrão. A MSAL regista todos os detalhes do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o madeireiro MSAL não captura dados pessoais ou organizacionais altamente sensíveis. A biblioteca oferece a opção de permitir a marcação de dados pessoais e organizacionais se decidir fazê-lo.

Para mais detalhes sobre o registo da MSAL numa determinada língua, escolha o separador que corresponde à sua língua:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Sessão de exploração madeireira em MSAL.NET

 > [!NOTE]
 > Veja o [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para amostras de MSAL.NET exploração madeireira e muito mais.

Em MSAL 3.x, o registo é definido `.WithLogging` por aplicação na criação de aplicações utilizando o modificador do construtor. Este método requer parâmetros opcionais:

- `Level`permite-lhe decidir qual o nível de exploração que pretende. Defini-lo para Erros só vai ter erros
- `PiiLoggingEnabled`permite-lhe registar dados pessoais e organizacionais se for definido como verdadeiro. Por predefinição, isto está definido como falso, para que a sua aplicação não faça login de dados pessoais.
- `LogCallback`é definido para um delegado que faz a exploração madeireira. Se `PiiLoggingEnabled` for verdade, este método receberá as `containsPii` mensagens duas vezes: uma com o parâmetro é `containsPii` igual a falsa e a mensagem sem dados pessoais, e uma segunda vez com o parâmetro é igual a verdadeiro e a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contenha dados pessoais), a mensagem será a mesma.
- `DefaultLoggingEnabled`permite o registo predefinido para a plataforma. Por defeito é falso. Se o definir de forma verdadeira, utiliza O Rastreio de Eventos em aplicações Desktop/UWP, NSLog no iOS e logcat no Android.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Sessão de login em MSAL para Android usando Java

Ligue o login na criação de apps criando um callback de registo. A chamada leva estes parâmetros:

- `tag`é uma corda passada para a chamada pela biblioteca. Está associado à entrada de registo e pode ser usado para ordenar mensagens de registo.
- `logLevel`permite-lhe decidir qual o nível de exploração que pretende. Os níveis de registo `Error`suportados são: , `Warning`, `Info`e `Verbose`.
- `message`é o conteúdo da entrada de registo.
- `containsPII`especifica se as mensagens que contêm dados pessoais ou dados organizacionais são registadas. Por predefinição, isto está definido como falso, para que a sua aplicação não faça login de dados pessoais. Se `containsPII` `true`for, este método receberá as mensagens duas vezes: `containsPII` uma com o parâmetro definido e `false` `true` `message` sem dados pessoais, e uma segunda vez com o `containsPii` parâmetro definido e a mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contenha dados pessoais), a mensagem será a mesma.

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

Por predefinição, o madeireiro MSAL não capturará nenhuma informação pessoal identificável ou identificável.
Para permitir o registo de informações pessoais identificáveis ou de informações identificáveis organizacionais:

```java
Logger.getInstance().setEnablePII(true);
```

Para desativar o registo de dados pessoais e de organização:

```java
Logger.getInstance().setEnablePII(false);
```

Por predefinição do logcat é desativado. Para permitir:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 Ative o registo em MSAL.js (JavaScript) passando um `UserAgentApplication` objeto de logger durante a configuração para criar uma instância. Este objeto madeireiro tem as seguintes propriedades:

- `localCallback`: uma instância de Callback que pode ser fornecida pelo desenvolvedor para consumir e publicar registos de forma personalizada. Implemente o método LocalCallback dependendo de como pretende redirecionar os registos.
- `level`(opcional): o nível de registo configurável. Os níveis de registo `Error`suportados são: , `Warning`, `Info`e `Verbose`. A predefinição é `Info`.
- `piiLoggingEnabled`(opcional): se for definido como verdadeiro, regista dados pessoais e organizacionais. Por predefinição, isto é falso para que a sua aplicação não faça login de dados pessoais. Os registos de dados pessoais nunca são escritos para saídas padrão como Consola, Logcat ou NSLog.
- `correlationId`(opcional): um identificador único, utilizado para mapear o pedido com a resposta para fins de depuração. Predefinições da versão RFC4122 4 guia (128 bits).

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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL para iOS e macOS logging-ObjC

Desmarque uma chamada para capturar a exploração madeireira mSAL e incorporá-la na exploração madeireira da sua própria aplicação. A assinatura para a chamada é assim:

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

Por predefinição, a MSAL não captura nem regista quaisquer dados pessoais (PII). A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled`ligar, a app assume a responsabilidade de manusear dados altamente sensíveis e seguir os requisitos regulamentares.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Níveis de exploração madeireira

Para definir o nível de registo quando iniciar sessão utilizando MSAL para iOS e macOS, utilize um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desativar toda a exploração madeireira |
| `MSALLogLevelError` | Nível padrão, imprime informação apenas quando ocorrem erros |
| `MSALLogLevelWarning` | Avisos |
| `MSALLogLevelInfo` |  Pontos de entrada da biblioteca, com parâmetros e várias operações de porta-chaves |
|`MSALLogLevelVerbose`     |  Rastreio da API |

Por exemplo:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Formato de mensagem de log

A parte da mensagem das mensagens de registo MSAL está no formato de`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer iDs de correlação e carimbos de tempo são úteis para rastrear problemas. A informação sobre o carimbo de tempo e a correlação de identificação estão disponíveis na mensagem de registo. O único lugar de confiança para recuperá-los é a partir de mensagens de registo da MSAL.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL para iOS e macOS logging-Swift

Desmarque uma chamada para capturar a exploração madeireira mSAL e incorporá-la na exploração madeireira da sua própria aplicação. A assinatura (representada no Objectivo-C) para o callback é a seguinte:

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

Por predefinição, a MSAL não captura nem regista quaisquer dados pessoais (PII). A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled`ligar, a app assume a responsabilidade de manusear dados altamente sensíveis e seguir os requisitos regulamentares.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de exploração madeireira

Para definir o nível de registo quando iniciar sessão utilizando MSAL para iOS e macOS, utilize um dos seguintes valores:

|Nível  |Descrição |
|---------|---------|
| `MSALLogLevelNothing`| Desativar toda a exploração madeireira |
| `MSALLogLevelError` | Nível padrão, imprime informação apenas quando ocorrem erros |
| `MSALLogLevelWarning` | Avisos |
| `MSALLogLevelInfo` |  Pontos de entrada da biblioteca, com parâmetros e várias operações de porta-chaves |
|`MSALLogLevelVerbose`     |  Rastreio da API |

Por exemplo:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Formato de mensagem de log

A parte da mensagem das mensagens de registo MSAL está no formato de`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Por exemplo:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Fornecer iDs de correlação e carimbos de tempo são úteis para rastrear problemas. A informação sobre o carimbo de tempo e a correlação de identificação estão disponíveis na mensagem de registo. O único lugar de confiança para recuperá-los é a partir de mensagens de registo da MSAL.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL para exploração madeireira de Java

A MSAL para Java permite-lhe utilizar a biblioteca de registos que já está a utilizar com a sua aplicação, desde que seja compatível com o SLF4J. MSAL for Java usa a fachada de [exploração simples para Java](http://www.slf4j.org/) (SLF4J) como fachada simples ou abstração para várias estruturas de exploração madeireira, tais como [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) e [Log4j](https://logging.apache.org/log4j/2.x/). O SLF4J permite ao utilizador ligar a estrutura de registo desejada no momento da implantação.

Por exemplo, para utilizar o Logback como estrutura de registo na sua aplicação, adicione a dependência de Logback ao ficheiro Pom Maven para a sua aplicação:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Em seguida, adicione o ficheiro de configuração de Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

O SLF4J liga-se automaticamente ao Logback no momento da implantação. Os registos da MSAL serão escritos na consola.

Para obter instruções sobre como se ligar a outras estruturas de exploração, consulte o [manual SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informações pessoais e de organização

Por predefinição, a exploração madeireira MSAL não captura nem regista quaisquer dados pessoais ou organizacionais. No exemplo seguinte, o registo de dados pessoais ou organizacionais é desligado por defeito:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Ligue a registo de dados `logPii()` pessoais e organizacionais, fixando o construtor de aplicações do cliente. Se ligar o registo de dados pessoais ou organizacionais, a sua aplicação deve assumir a responsabilidade pelo tratamento seguro de dados altamente sensíveis e pelo cumprimento de quaisquer requisitos regulamentares.

No exemplo seguinte, o registo de dados pessoais ou organizacionais está ativado:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL para exploração madeireira Python

O registo em MSAL Python utiliza o `logging.info("msg")` mecanismo padrão de exploração de python, por exemplo, pode configurar a exploração madeireira MSAL da seguinte forma (e vê-la em ação no [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Ativar a exploração de depuração para todos os módulos

Por padrão, a exploração madeireira em qualquer guião python está desligada. Se pretender ativar o depuração de todos os módulos de todo o seu script Python, utilize:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silêncio apenas mSAL logging

Para silenciar apenas o registo da biblioteca MSAL, ao mesmo tempo que permite o depuramento do registo em todos os outros módulos do seu script Python, desligue o madeireiro utilizado pela MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

A MSAL para Python não regista dados pessoais ou dados organizacionais. Não há propriedade para ativar dados pessoais ou de organização registando dentro ou fora.

Pode utilizar a exploração de python padrão para registar o que quiser, mas é responsável pelo manuseamento seguro de dados sensíveis e seguindo os requisitos regulamentares.

Para mais informações sobre a exploração madeireira em Python, consulte o Howto de [Onda de Python.](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)

---
