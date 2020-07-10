---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165668"
---
O fuso horário padrão utilizado com as expressões CRON é Tempo Universal Coordenado (UTC). Para ter a sua expressão CRON baseada em outro fuso horário, crie uma definição de aplicação para a sua aplicação de função chamada `WEBSITE_TIME_ZONE` . 

O valor desta definição depende do sistema operativo e do plano em que a sua aplicação de função é executado.

|Sistema operativo |Planear |Valor |
|-|-|-|
| **Windows** |Todos | Desa cos assim o valor do fuso horário pretendido, tal como indicado no [Índice de Fuso Horário da Microsoft]. https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) |
| **Linux** |Premium<br/>Dedicada |Desa esta medida de valor para o nome do fuso horário pretendido, tal como indicado na [base de dados tz](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`não é atualmente apoiado no plano de consumo de Linux.

Por exemplo, *o Tempo Padrão Oriental* (Windows) ou *América/New_York* (Linux) é UTC-05:00. Para ter o seu temporizador disparar às 10:00 AM EST todos os dias, utilize a seguinte expressão NCRONTAB que explica o fuso horário UTC:

```
"0 0 15 * * *"
``` 

Ou crie uma configuração de aplicativo para a sua aplicação de função denominada `WEBSITE_TIME_ZONE` , defina o valor para `Eastern Standard Time` (Windows) ou `America/New_York` (Linux) e, em seguida, use a seguinte expressão NCRONTAB: 

```
"0 0 10 * * *"
``` 

Quando `WEBSITE_TIME_ZONE` utilizar, o tempo é ajustado para alterações de tempo no fuso horário específico, como o horário de verão. 
