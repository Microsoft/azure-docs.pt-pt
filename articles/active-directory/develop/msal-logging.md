---
title: Fazendo logon em aplicativos MSAL | Azure
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
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f52ca8062ed1ed196a67d25385b712451afe8ae
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532722"
---
# <a name="logging"></a>Registo
Aplicativos da MSAL (biblioteca de autenticação da Microsoft) para gerar mensagens de log que podem ajudar a diagnosticar problemas e fornecer detalhes. Um aplicativo pode configurar o registro em log com algumas linhas de código e ter controle personalizado sobre o nível de detalhes e se os dados pessoais e organizacionais serão ou não registrados. É recomendável que você defina um retorno de chamada de log MSAL e forneça uma maneira para os usuários enviarem logs quando estiverem tendo problemas de autenticação.

## <a name="logging-levels"></a>Níveis de log

O agente de log do MSAL permite que vários níveis de detalhes sejam capturados:

- Erro: Indica que algo deu errado e um erro foi gerado. Use para depuração e identificação de problemas.
- Aviso: Eventos que são perguntas e o aplicativo precisam de mais informações sobre o. Não há necessariamente um erro ou falha, mas destina-se a problemas de diagnóstico e de identificação.
- Detalhes O MSAL registrará em log eventos destinados a fins informativos, não necessariamente destinados à depuração.
- Extensa Predefinição. O MSAL registrará uma grande quantidade de informações e fornecerá detalhes completos sobre o comportamento da biblioteca.

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

- *localCallback*: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar logs de maneira personalizada. Implemente o método localCallback dependendo de como você deseja redirecionar os logs.

- *nível* do (opcional): o nível de log configurável. Os níveis de log com suporte são: Erro, aviso, informações, detalhado. O valor padrão é info.

- *piiLoggingEnabled* (opcional): permite que você registre dados pessoais e organizacionais se definido como true. Por padrão, isso é definido como false para que seu aplicativo não Registre dados pessoais. Logs de dados pessoais nunca são gravados em saídas padrão como console, logcat ou NSLog. O padrão é definido como false.

- CorrelationId (opcional): um identificador exclusivo, usado para mapear a solicitação com a resposta para fins de depuração. O padrão é RFC4122 versão 4 GUID (128 bits).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
