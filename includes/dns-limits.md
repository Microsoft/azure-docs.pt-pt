---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94329600"
---
**Zonas públicas de DNS**

| Recurso | Limite |
| --- | --- |
| Zonas públicas de DNS por subscrição |250 <sup>1</sup> |
| Recordes por zona pública de DNS |10.000 <sup>1</sup> |
| Recordes por recorde estabelecidos na zona pública de DNS |20 |
| Número de registos da Alias para um único recurso Azure |20|

<sup>1</sup> Se precisar de aumentar estes limites, contacte o Suporte Azure.

**Zonas privadas de DNS**

| Recurso | Limite |
| --- | --- |
| Zonas privadas de DNS por subscrição |1000|
| Conjuntos de recordes por zona privada de DNS |25 000|
| Registos por recorde definidos para zonas privadas de DNS |20|
| Links de rede virtuais por zona privada de DNS |1000|
| Links de redes virtuais por zonas privadas de DNS com registo automático |100|
| Número de zonas privadas de DNS a que uma rede virtual pode ficar ligada com o registo automático ativado |1|
| Número de zonas privadas de DNS uma rede virtual pode ficar ligada |1000|
| Número de consultas dns uma máquina virtual pode enviar para Azure DNS resolver, por segundo |1000 <sup>1</sup> |
| Número máximo de consultas dns em fila (resposta pendente) por máquina virtual |200 <sup>1</sup> |

<sup>1</sup> Estes limites são aplicados a cada máquina virtual individual e não ao nível da rede virtual. As consultas de DNS que excedam estes limites são retiradas.
