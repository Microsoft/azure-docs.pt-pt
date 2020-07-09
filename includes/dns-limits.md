---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: 434b79a2b178defd9543e1d3ad087bb5282cb287
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805984"
---
**Zonas públicas de DNS**

| Recurso | Limite |
| --- | --- |
| Zonas públicas de DNS por subscrição |250 <sup>1</sup> |
| Recordes por zona pública de DNS |10.000 <sup>1</sup> |
| Recordes por recorde estabelecidos na zona pública de DNS |20 |
| Número de registos da Alias para um único recurso Azure |20|
| Zonas privadas de DNS por subscrição |1000|
| Conjuntos de recordes por zona privada de DNS |25 000|
| Registos por recorde definidos para zonas privadas de DNS |20|
| Links de rede virtuais por zona privada de DNS |1000|
| Links de redes virtuais por zonas privadas de DNS com registo automático |100|
| Número de zonas privadas de DNS a que uma rede virtual pode ficar ligada com o registo automático ativado |1|
| Número de zonas privadas de DNS uma rede virtual pode ficar ligada |1000|
| Número de consultas dns uma máquina virtual pode enviar para Azure DNS resolver, por segundo |500 <sup>2</sup> |
| Número máximo de consultas dns em fila (resposta pendente) por máquina virtual |200 <sup>2</sup> |

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.

<sup>2</sup> Estes limites são aplicados a cada máquina virtual individual e não ao nível da rede virtual. As consultas de DNS que excedam estes limites são retiradas.
