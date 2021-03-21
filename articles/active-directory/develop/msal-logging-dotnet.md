---
title: Registo de erros e exceções na MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções em MSAL.NET
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
ms.openlocfilehash: da36ce0a956e0c3ed369a676960bdb9b5c5b1199
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954839"
---
# <a name="logging-in-msalnet"></a>Registos no MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Configurar o início de sessão em MSAL.NET

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

> [!TIP]
 > Consulte o [wiki MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) para amostras de MSAL.NET de madeira e muito mais.

## <a name="next-steps"></a>Passos seguintes

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft.](sample-v2-code.md)