---
title: 'Azure ExpressRoute: Controlos de segurança'
description: Uma lista de controlos de segurança para avaliar o Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079920"
---
# <a name="security-controls-for-azure-expressroute"></a>Controlos de segurança para Azure ExpressRoute

Este artigo documenta os controlos de segurança incorporados na Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| N/D |  |
| Suporte à injeção VNet| N/D | |
| Isolamento da rede e suporte de firewalling| Sim | Cada cliente está contido no seu próprio domínio de encaminhamento e escavado para o seu próprio VNet |
| Apoio de túnel forçado| N/D | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Consulte [a monitorização, métricas e alertas expressRoute.](expressroute-monitoring-metrics-alerts.md)|
| Registo e auditoria de planos de controlo e gestão| Sim |  |
| Registo e auditoria de planos de dados| Não |   |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Conta de serviço para Gateway para Microsoft (GWM) (controlador); Mesmo no tempo (JIT) acesso para Dev e OP. |
| Autorização|  Sim |Conta de serviço para Gateway para Microsoft (GWM) (controlador); Mesmo no tempo (JIT) acesso para Dev e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft |  N/D | ExpressRoute não armazena dados do cliente. |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | N/D |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Não | |
| Chamadas api encriptadas| Sim | Através do Gestor de [Recursos Azure](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Através do Fornecedor de Recursos de Rede (NRP). |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)