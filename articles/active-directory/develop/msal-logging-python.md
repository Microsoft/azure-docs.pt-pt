---
title: Registar erros e exceções no MSAL para Python
titleSuffix: Microsoft identity platform
description: Saiba como registar erros e exceções no MSAL para Python
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
ms.openlocfilehash: 560caa7e29ce12b58e151a1362aaf2c662646f13
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954805"
---
# <a name="logging-in-msal-for-python"></a>Registos no MSAL para Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

Para obter mais informações sobre o login em Python, consulte o  [Python's Logging: how-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Passos seguintes

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft.](sample-v2-code.md)

---