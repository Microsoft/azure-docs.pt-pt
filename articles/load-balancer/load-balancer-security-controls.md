---
title: Controlos de segurança para o Equilíbrio de Carga Azure
description: Uma lista de controlos de segurança para avaliar o Balancer de Carga
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214906"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controlos de segurança para o Equilíbrio de Carga Azure

Este artigo documenta os controlos de segurança incorporados no Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte de isolamento de rede e firewalling| N/D |  |
| Apoio de túnel forçado| N/D | |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Consulte [os registos do Monitor Azure para o equilíbrio](load-balancer-monitor-log.md)de carga básico público . |
| Registo e auditoria de planos de controlo e gestão| Sim | Consulte [os registos do Monitor Azure para o equilíbrio](load-balancer-monitor-log.md)de carga básico público . |
| Registo e auditoria de planos de dados | N/D |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| N/D |  |
| Autorização| N/D |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| N/D | |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | N/D | |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas api encriptadas| Sim | Através do Gestor de [Recursos Azure.](../azure-resource-manager/index.yml) |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| N/D |  | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)