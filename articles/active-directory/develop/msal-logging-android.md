---
title: Registar erros e exceções no MSAL para Android.
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções no MSAL para Android.
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
ms.openlocfilehash: b5d11c1bd091e3d7fbe5be87004b91f242e807f5
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763501"
---
# <a name="logging-in-msal-for-android"></a>Registos no MSAL para Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

## <a name="next-steps"></a>Próximos passos

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft)](sample-v2-code.md).