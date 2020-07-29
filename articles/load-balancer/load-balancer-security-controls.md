---
title: Controlos de segurança para o balançador de carga Azure
description: Uma lista de controlos de segurança para avaliar o Balanceador de Carga
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214906"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controlos de segurança para o balançador de carga Azure

Este artigo documenta os controlos de segurança incorporados no Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte de isolamento e firewalling de rede| N/D |  |
| Suporte de túneis forçados| N/D | |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Consulte [os registos do Monitor Azure para o balanceador de carga básico público](load-balancer-monitor-log.md). |
| Registo e auditoria de avião de controlo e gestão| Sim | Consulte [os registos do Monitor Azure para o balanceador de carga básico público](load-balancer-monitor-log.md). |
| Registo e auditoria de planos de dados | N/D |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| N/D |  |
| Autorização| N/D |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| N/D | |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | N/D | |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Sim | Através do [Gestor de Recursos Azure](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| N/D |  | 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)