---
title: Iniciar sessão em apps MSAL ! Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como iniciar sessão nas aplicações microsoft Authentication Library (MSAL).
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
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 2d41b48613ef7ba883a6a51b0fa67407fb730719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87846229"
---
# <a name="logging-in-msal-applications"></a>Registo de aplicações MSAL

As aplicações da Microsoft Authentication Library (MSAL) geram mensagens de registo que podem ajudar a diagnosticar problemas. Uma aplicação pode configurar o registo com algumas linhas de código, e ter controlo personalizado sobre o nível de detalhe e se os dados pessoais e organizacionais são ou não registados. Recomendamos que crie uma chamada de registo MSAL e forneça uma forma de os utilizadores submeterem registos quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de registo

A MSAL fornece vários níveis de detalhes de registo registador:

- Erro: Indica que algo correu mal e foi gerado um erro. Utilizar para depurar e identificar problemas.
- Aviso: Não houve necessariamente um erro ou falha, mas destinam-se a diagnósticos e problemas de localização.
- Informações: A MSAL registará eventos destinados a fins informativos não necessariamente destinados à depuragem.
- Verbose: Padrão. MSAL regista todos os detalhes do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o madeireiro MSAL não captura quaisquer dados pessoais ou organizacionais altamente sensíveis. A biblioteca oferece a opção de ativar dados pessoais e organizacionais se decidir fazê-lo.

Para mais detalhes sobre o registo do MSAL num determinado idioma, escolha o separador correspondente ao seu idioma:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Iniciar sessão em MSAL.NET

 > [!NOTE]
 > Consulte o [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para amostras de MSAL.NET de madeira e muito mais.

No MSAL 3.x, o registo é definido por aplicação na criação de aplicações utilizando o `.WithLogging` modificador de construtores. Este método tem parâmetros opcionais:

- `Level` permite-lhe decidir qual o nível de registo que deseja. Defini-lo para erros só terá erros
- `PiiLoggingEnabled` permite registar dados pessoais e organizacionais se definidos para verdadeiros. Por predefinição, este é definido como falso, para que a sua aplicação não faça login dados pessoais.
- `LogCallback` é definido para um delegado que faz o registo. Se `PiiLoggingEnabled` for verdade, este método receberá as mensagens duas vezes: uma com o `containsPii` parâmetro é igual a falsa e a mensagem sem dados pessoais, e uma segunda vez com o parâmetro é igual a verdadeiro e a `containsPii` mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.
- `DefaultLoggingEnabled` permite a sessão de registo predefinido para a plataforma. Por defeito, é falso. Se o definir como verdadeiro, utiliza o Event Tracing em aplicações desktop/UWP, NSLog no iOS e logcat no Android.

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

## <a name="logging-in-msal-for-android-using-java"></a>Iniciar sessão no MSAL para Android utilizando Java

Ligue o início de sessão na criação de aplicações criando uma chamada de registo. O retorno requer estes parâmetros:

- `tag` é uma corda passada para a chamada pela biblioteca. Está associado à entrada de registo e pode ser usado para classificar mensagens de registo.
- `logLevel` permite-lhe decidir qual o nível de registo que deseja. Os níveis de registo suportados são: `Error` `Warning` , , e `Info` `Verbose` .
- `message` é o conteúdo da entrada de registo.
- `containsPII` especifica se as mensagens que contêm dados pessoais ou dados organizacionais são registadas. Por padrão, isto é definido como falso, para que a sua aplicação não faça login dados pessoais. Se `containsPII` for , este método receberá as `true` mensagens duas vezes: uma com o `containsPII` parâmetro definido para e o sem `false` `message` dados pessoais, e uma segunda vez com o `containsPii` parâmetro definido para e a `true` mensagem pode conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será a mesma.

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

Por padrão, o madeireiro MSAL não capturará nenhuma informação pessoal identificável ou informação organizacional identificável.
Para permitir o registo de informações pessoais identificáveis ou informações organizacionais identificáveis:

```java
Logger.getInstance().setEnablePII(true);
```

Para desativar os dados pessoais e os dados da organização:

```java
Logger.getInstance().setEnablePII(false);
```

Por predefinição, o registo para o logcat é desativado. Para ativar:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

 Ativar o início de sessão em MSAL.js (JavaScript) passando um objeto de madeireira durante a configuração para criar um `UserAgentApplication` exemplo. Este objeto madeirão tem as seguintes propriedades:

- `localCallback`: uma instância de callback que pode ser fornecida pelo desenvolvedor para consumir e publicar registos de forma personalizada. Implemente o método localCallback dependendo da forma como pretende redirecionar os registos.
- `level` (opcional): o nível de registo configurável. Os níveis de registo suportados são: `Error` `Warning` , , e `Info` `Verbose` . A predefinição é `Info`.
- `piiLoggingEnabled` (opcional): se definido como verdadeiro, regista dados pessoais e organizacionais. Por padrão, isto é falso para que a sua aplicação não faça login dados pessoais. Os registos de dados pessoais nunca são escritos para saídas padrão como Consola, Logcat ou NSLog.
- `correlationId` (opcional): um identificador único, utilizado para mapear o pedido com a resposta para efeitos de depuragem. Predefinições para RFC4122 versão 4 guia (128 bits).

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

Por padrão, a MSAL não captura nem regista quaisquer dados pessoais (PII). A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled` ligar, a aplicação assume a responsabilidade de lidar com segurança dados altamente sensíveis e seguindo os requisitos regulamentares.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Níveis de registo

Para definir o nível de registo quando iniciar sessão utilizando o MSAL para iOS e macOS, utilize um dos seguintes valores:

|Nível  |Descrição |
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

Por padrão, a MSAL não captura nem regista quaisquer dados pessoais (PII). A biblioteca permite que os desenvolvedores de aplicações liguem isto através de uma propriedade na classe MSALLogger. Ao `pii.Enabled` ligar, a aplicação assume a responsabilidade de lidar com segurança dados altamente sensíveis e seguindo os requisitos regulamentares.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Níveis de registo

Para definir o nível de registo quando iniciar sessão utilizando o MSAL para iOS e macOS, utilize um dos seguintes valores:

|Nível  |Descrição |
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

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL para registo de java

O MSAL para Java permite-lhe utilizar a biblioteca de registos que já está a utilizar com a sua aplicação, desde que seja compatível com o SLF4J. O MSAL para Java utiliza a [fachada simples de madeira para Java](http://www.slf4j.org/) (SLF4J) como uma fachada simples ou abstração para várias estruturas de registo, tais como [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) e [Log4j](https://logging.apache.org/log4j/2.x/). O SLF4J permite ao utilizador ligar a estrutura de registo desejada no momento de implementação.

Por exemplo, para utilizar o Logback como estrutura de registo na sua aplicação, adicione a dependência de Logback ao ficheiro Maven pom para a sua aplicação:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Em seguida, adicione o ficheiro de configuração do Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

O SLF4J liga-se automaticamente ao Logback no momento da implementação. Os registos MSAL serão escritos na consola.

Para obter instruções sobre como se ligar a outras estruturas de registo, consulte o [manual SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informações pessoais e de organização

Por predefinição, o registo msal não captura ou regista quaisquer dados pessoais ou organizacionais. No exemplo seguinte, o registo de dados pessoais ou organizacionais está desligado por padrão:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Ligue o registo de dados pessoais e organizacionais definindo `logPii()` o construtor de aplicações do cliente. Se ligar o registo de dados pessoais ou organizacionais, a sua aplicação deve assumir a responsabilidade de lidar com dados altamente sensíveis e cumprir quaisquer requisitos regulamentares.

No exemplo seguinte, é ativado o registo de dados pessoais ou organizacionais:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL para registo de python

O registo em MSAL Python utiliza o mecanismo padrão de registo python, por `logging.info("msg")` exemplo, pode configurar a exploração madeireira MSAL da seguinte forma (e vê-la em ação no [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Ativar a registo de depuragem para todos os módulos

Por predefinição, o registo em qualquer script Python é desligado. Se quiser ativar a exploração de depuragem para todos os módulos em todo o seu script Python, utilize:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Silêncio apenas registo msal

Para silenciar apenas o registo da biblioteca MSAL, ao mesmo tempo que permite o registo de depuração em todos os outros módulos do seu script Python, desligue o madeireiro utilizado pela MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

A MSAL para Python não regista dados pessoais ou dados organizacionais. Não há propriedade para tornar os dados pessoais ou organizacionais que iniciam sessão.

Pode utilizar o registo padrão de Python para registar o que quiser, mas é responsável por lidar com segurança dados sensíveis e seguir os requisitos regulamentares.

Para obter mais informações sobre o login em Python, consulte o  [PYTHON's Logging HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
