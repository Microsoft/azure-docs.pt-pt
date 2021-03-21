---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2020
ms.author: glenga
ms.openlocfilehash: 7d1bf8dd2d1c8feab8b051a8edad7d5e570ee11b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025489"
---
O fuso horário padrão utilizado com as expressões CRON é Tempo Universal Coordenado (UTC). Para ter a sua expressão CRON baseada em outro fuso horário, crie uma definição de aplicação para a sua aplicação de função chamada `WEBSITE_TIME_ZONE` . 

O valor desta definição depende do sistema operativo e do plano em que a sua aplicação de função é executado.

|Sistema operativo |Planear |Valor |
|-|-|-|
| **Windows** |Todos | Desapedahe o valor do fuso horário pretendido como dado pela segunda linha de cada par dado pelo comando Windows `tzutil.exe /L` |
| **Linux** |Premium<br/>Dedicada |Desa esta medida de valor para o nome do fuso horário pretendido, tal como indicado na [base de dados tz](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE` não é atualmente apoiado no plano de consumo de Linux.

Por exemplo, o Horário Oriental nos EUA (representado por `Eastern Standard Time` (Windows) ou `America/New_York` (Linux)) utiliza atualmente UTC-05:00 durante o horário padrão e UTC-04:00 durante o dia. Para ter um gatilho de temporizador disparado às 10:00 horas de sábado, crie uma definição de aplicação para a sua aplicação de função denominada `WEBSITE_TIME_ZONE` , defina o valor para `Eastern Standard Time` (Windows) ou `America/New_York` (Linux) e, em seguida, use a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando utilizar `WEBSITE_TIME_ZONE` o tempo é ajustado para alterações de tempo no fuso horário específico, incluindo o horário de verão e alterações no tempo padrão.
