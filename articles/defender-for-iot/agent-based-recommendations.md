---
title: Recomendações baseadas em agente
description: Conheça o conceito de recomendações de segurança e como são usados para dispositivos IoT.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784990"
---
# <a name="security-recommendations-for-iot-devices"></a>Recomendações de segurança para dispositivos IoT

O Defender for IoT digitaliza os seus recursos Azure e dispositivos IoT e fornece recomendações de segurança para reduzir a sua superfície de ataque.
As recomendações de segurança são exequíveis e visam ajudar os clientes a cumprir as melhores práticas de segurança.

Neste artigo, encontrará uma lista de recomendações, que podem ser ativadas nos seus dispositivos IoT.

## <a name="agent-based-recommendations"></a>Recomendações baseadas em agente

As recomendações do dispositivo fornecem insights e sugestões para melhorar a postura de segurança do dispositivo.

| Gravidade | Name | Origem de dados | Description |
|--|--|--|--|
| Médio | Portas abertas no dispositivo | Defensor-IoT-micro-agente clássico| Um ponto final de escuta foi encontrado no dispositivo. |
| Médio | Política de firewall permissiva encontrada numa das correntes. | Defensor-IoT-micro-agente clássico| Política de firewall permitida encontrada (INPUT/OUTPUT). A política de firewall deve negar todo o tráfego por defeito e definir regras para permitir a comunicação necessária de/para o dispositivo. |
| Médio | Foi encontrada regra de firewall permissiva na cadeia de entrada | Defensor-IoT-micro-agente clássico| Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas. |
| Médio | Foi encontrada regra de firewall permissiva na cadeia de saída | Defensor-IoT-micro-agente clássico| Foi encontrada uma regra na firewall que contém um padrão permissivo para uma ampla gama de endereços IP ou portas. |
| Médio | A validação da linha de base do sistema operativo falhou | Defensor-IoT-micro-agente clássico| O dispositivo não está em conformidade com as [referências do CIS Linux](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Recomendações operacionais baseadas em agentes

As recomendações operacionais fornecem insights e sugestões para melhorar a configuração do agente de segurança.

| Gravidade | Name | Origem de dados | Description |
|--|--|--|--|
| Baixo | Agente envia mensagens não utetilizadas | Defensor-IoT-micro-agente clássico | 10% ou mais de mensagens de segurança foram menores do que 4 KB durante as últimas 24 horas. |
| Baixo | Configuração de twin de segurança não é ideal | Defensor-IoT-micro-agente clássico | A configuração de dois de segurança não é a ideal. |
| Baixo | Conflito de configuração de dupla segurança | Defensor-IoT-micro-agente clássico | Os conflitos foram identificados na configuração de dois de segurança. |

## <a name="next-steps"></a>Passos seguintes

- Visão [geral](overview.md) do serviço defender para ioT
- Saiba como aceder aos [seus dados de segurança](how-to-security-data-access.md)
- Saiba mais sobre [a investigação de um dispositivo](how-to-investigate-device.md)
