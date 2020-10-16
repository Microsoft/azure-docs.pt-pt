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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)