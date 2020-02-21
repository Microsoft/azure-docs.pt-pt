---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 2/14/2020
ms.author: rohink
ms.openlocfilehash: b674f8e31eb61328f60bb24866f73d02653b655f
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77495030"
---
**Zonas públicas de DNS**

| Recurso | Limite predefinido |
| --- | --- |
| Zonas Públicas dNS por subscrição |250 <sup>1</sup> |
| Recorde seleções por zona pública de DNS |10.000 <sup>1</sup> |
| Recordes por recorde estabelecidos na zona pública de DNS |20 |
| Número de registos da Alias para um único recurso Azure |20|
| Zonas privadas de DNS por subscrição |1000|
| Recorde seleções por zona privada de DNS |25000|
| Recordes por recorde estabelecidos para zonas privadas de DNS |20|
| Links de rede virtual por zona privada de DNS |1000|
| Ligações de Redes Virtuais por zonas privadas de DNS com registo automático ativado |100|
| Número de zonas privadas de DNS a que uma rede virtual pode ficar ligada com registo automático ativado |1|
| Número de zonas privadas de DNS uma rede virtual pode ficar ligada |1000|
| Número de consultas de DNS que uma máquina virtual pode enviar para O DNS Azure resolver, por segundo |500 <sup>2</sup> |
| Número máximo de consultas de DNS em fila (resposta pendente) por máquina virtual |200 <sup>2</sup> |

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.

<sup>2</sup> Estes limites são aplicados a todas as máquinas virtuais individuais e não ao nível da rede virtual. As consultas de DNS que excedem estes limites são retiradas.