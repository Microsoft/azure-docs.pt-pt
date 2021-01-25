---
title: Registar erros e exceções na MSAL.js
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções em MSAL.js
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
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763496"
---
# <a name="logging-in-msaljs"></a>Registos no MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Configurar o início de sessão em MSAL.js

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
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Próximos passos

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft)](sample-v2-code.md).