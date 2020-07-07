---
title: 'Azure ExpressRoute: Controlos de segurança'
description: Uma lista de controlos de segurança para a avaliação do Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74079920"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlos de segurança para a Azure ExpressRoute

Este artigo documenta os controlos de segurança incorporados no Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| N/D |  |
| Suporte à injeção VNet| N/D | |
| Isolamento de rede e suporte de firewall| Sim | Cada cliente está contido no seu próprio domínio de encaminhamento e escavado para o seu próprio VNet |
| Suporte de túneis forçados| N/D | Através do Protocolo de Gateway fronteiriço (BGP). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Consulte [a monitorização expressRoute, métricas e alertas](expressroute-monitoring-metrics-alerts.md).|
| Registo e auditoria de avião de controlo e gestão| Sim |  |
| Registo e auditoria de planos de dados| Não |   |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Conta de serviço para Gateway para Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |
| Autorização|  Sim |Conta de serviço para Gateway para Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft |  N/D | A ExpressRoute não armazena os dados dos clientes. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | N/D |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Não | |
| Chamadas da API encriptadas| Sim | Através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | Através do Fornecedor de Recursos de Rede (NRP). |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)