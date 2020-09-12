---
title: 'Azure ExpressRoute: Controlos de segurança'
description: Conheça os controlos de segurança no Azure ExpressRoute, que são qualidades ou funcionalidades que ajudam a prevenir, detetar e responder a vulnerabilidades de segurança.
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394912"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlos de segurança para a Azure ExpressRoute

Este artigo documenta os controlos de segurança incorporados no Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| N/D |  |
| Suporte à injeção VNet| N/D | |
| Isolamento de rede e suporte de firewall| Yes | Cada cliente está contido no seu próprio domínio de encaminhamento e escavado para o seu próprio VNet |
| Suporte de túneis forçados| N/D | Através do Protocolo de Gateway fronteiriço (BGP). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | Consulte [a monitorização expressRoute, métricas e alertas](expressroute-monitoring-metrics-alerts.md).|
| Registo e auditoria de avião de controlo e gestão| Yes |  |
| Registo e auditoria de planos de dados| No |   |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Yes | Conta de serviço para Gateway para Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |
| Autorização|  Yes |Conta de serviço para Gateway para Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft |  N/D | A ExpressRoute não armazena os dados dos clientes. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | N/D |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| No | |
| Chamadas da API encriptadas| Yes | Através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Yes | Através do Fornecedor de Recursos de Rede (NRP). |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)