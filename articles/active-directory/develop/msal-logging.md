---
title: O registo em aplicativos de MSAL | Azure
description: Saiba mais sobre o registo em aplicativos do Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544100"
---
# <a name="logging"></a>Registo
Aplicações Microsoft Authentication Library (MSAL) para gerar mensagens de registo que podem ajudar a diagnosticar problemas e fornecem detalhes. Uma aplicação pode configurar o registo com algumas linhas de código e tem controle personalizado sobre o nível de detalhe e se é ou não os dados pessoais e organizacionais são registados. É recomendado que defina um retorno de chamada de registo de MSAL e fornecer uma maneira para os utilizadores enviem registos quando estiver a ter problemas de autenticação.

## <a name="logging-levels"></a>Níveis de registo

Agente de log da MSAL permite vários níveis de detalhe para ser captura:

- Erro: Indica um problema e que foi gerado um erro. Utilize para depuração e identificação de problemas.
- Aviso: Eventos de pergunta e a aplicação tem de obter mais informações sobre. Há ainda não necessariamente foi um erro ou falha, mas se destina a problemas de diagnóstico e apontando.
- INFO: A MSAL irá registar eventos que se destina a fins informativos, não necessariamente a de depuração.
- verboso: Predefinição. A MSAL será uma grande quantidade de informações de registo e dar a todos os detalhes sobre o comportamento que biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais
Por predefinição, o agente a MSAL não captura dados altamente confidenciais pessoais ou organizacionais. A biblioteca fornece a opção para ativar o registo de dados pessoais e organizacionais se optar por fazê-lo.

## <a name="logging-in-msalnet"></a>Iniciar sessão MSAL.NET
No MSAL 3.x, o registo é definido por aplicação através da criação de aplicações a `.WithLogging` modificador builder. Este método usa parâmetros opcionais:

- *Nível* permite-lhe decidir qual nível de registo que pretende. Defini-la como erros apenas obterá erros
- *PiiLoggingEnabled* permite-lhe registar dados de pessoais e organizacionais se definido como true. Por predefinição está definida como false, para que seu aplicativo não regista os dados pessoais.
- *LogCallback* está definido como um delegado que faz o Registro em log. Se *PiiLoggingEnabled* for true, este método irá receber as mensagens de duas vezes: uma vez com o *containsPii* parâmetro é igual a false e a mensagem sem dados pessoais e uma segunda vez com o *containsPii* parâmetro é igual a TRUE e a mensagem poderá conter dados pessoais. Em alguns casos (quando a mensagem não contém dados pessoais), a mensagem será o mesmo.
- *DefaultLoggingEnabled* permite que o registo predefinido para a plataforma. Por predefinição é false. Se definido como VERDADEIRO, ele utiliza o rastreio de eventos em aplicativos de área de trabalho/UWP, NSLog no iOS e logcat no Android.

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
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Iniciar sessão msal

 Pode ativar o registo no msal passando um objeto logger durante a configuração para a criação de um `UserAgentApplication` instância. Este objeto logger tem as seguintes propriedades:

- *localCallback*: uma instância de retorno de chamada que pode ser fornecida pelo desenvolvedor para consumir e publicar os registos de maneira personalizada. Implemente o método localCallback dependendo de como deseja redirecionar os registos.

- *nível* (opcional): o nível de registo configuráveis. Os níveis de registo suportados são: Erro, aviso, verboso, informações. Valor predefinido é de informações.

- *piiLoggingEnabled* (opcional): permite-lhe registar dados de pessoais e organizacionais se definido como true. Por predefinição está definida como false, de modo que seu aplicativo não regista os dados pessoais. Registos de dados pessoais nunca são escritos para saídas padrão, como o Console, Logcat ou NSLog. Predefinição está definida como false.

- *correlationId* (opcional): um identificador exclusivo, utilizado para mapear a solicitação com a resposta para fins de depuração. Predefinição é guid de versão 4 RFC4122 (128 bits).

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
