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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578758"
---
# <a name="logging-in-msal-for-python"></a>Registos no MSAL para Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL para registo de python

O registo no MSAL for Python aproveita o [módulo de registo na biblioteca padrão Python.](https://docs.python.org/3/library/logging.html) Pode configurar a registo MSAL da seguinte forma (e vê-la em ação no [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Ativar a registo de depuragem para todos os módulos

Por predefinição, o registo em qualquer script Python é desligado. Se pretender ativar a exploração de verbose para **todos os** módulos Python no seu script, utilize `logging.basicConfig` com um nível de `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Isto irá imprimir todas as mensagens de registo dadas ao módulo de registo para a saída padrão.

### <a name="configure-msal-logging-level"></a>Configure o nível de registo MSAL

Pode configurar o nível de registo do fornecedor de registos MSAL para Python utilizando o `logging.getLogger()` método com o nome do madeireira `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Configurar o registo MSAL com a Azure App Insights

Os troncos de python são dados a um manipulador de registos, que por padrão é o `StreamHandler` . Para enviar registos MSAL para um Insights de Aplicação com uma Chave de Instrumentação, utilize o `AzureLogHandler` fornecido pela `opencensus-ext-azure` biblioteca.

Para instalar, `opencensus-ext-azure` adicione o pacote do `opencensus-ext-azure` PyPI às suas dependências ou instalação de pip:

```console
pip install opencensus-ext-azure
```

Em seguida, altere o manipulador predefinido do `"msal"` prestador de registos para uma instância de com uma chave de `AzureLogHandler` instrumentação definida na `APP_INSIGHTS_KEY` variável ambiente:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Dados pessoais e organizacionais em Python

A MSAL para Python não regista dados pessoais ou dados organizacionais. Não há propriedade para tornar os dados pessoais ou organizacionais que iniciam sessão.

Pode utilizar o registo padrão de Python para registar o que quiser, mas é responsável por lidar com segurança dados sensíveis e seguir os requisitos regulamentares.

Para obter mais informações sobre o login em Python, consulte o  [Python's Logging: how-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Passos seguintes

Para obter mais amostras de código, consulte [as amostras de código da plataforma de identidade da Microsoft.](sample-v2-code.md)
