---
title: Controlos de segurança para Azure VPN Gateway
description: Uma lista de controlos de segurança para avaliar o Gateway Azure VPN
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82127860"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controlos de segurança para Azure VPN Gateway

Este artigo documenta os controlos de segurança incorporados no Gateway Azure VPN.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte de isolamento e firewalling de rede| Yes | Gateways VPN são instâncias VM dedicadas para cada rede virtual de clientes  |
| Suporte de túneis forçados| Yes |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | Consulte [alerta de registo de monitor](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)  &  [Azure Azure Monitor Metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Registo e auditoria de avião de controlo e gestão| Yes | Registo de atividade do Gestor de Recursos Azure. |
| Registo e auditoria de planos de dados | Yes | [Registos de atividade do monitor Azure](../azure-resource-manager/management/view-activity-logs.md) para registo de conectividade VPN e auditoria. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Yes | [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerir o serviço e configurar o gateway Azure VPN. |
| Autorização| Yes | Autorização de Apoio via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | N/D | VPN gateway transit dados do cliente, NÃO armazena dados do cliente |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Yes | Gateway VPN encripta pacotes de clientes entre gateways Azure VPN e clientes VPN (S2S) ou clientes VPN (P2S). As portas VPN também suportam encriptação VNet-to-VNet. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | No | As chaves pré-partilhadas especificadas pelo cliente são encriptadas em repouso; mas ainda não integrado com a CMK. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Yes | Através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Yes | Para operações de gestão, o estado de uma configuração de gateway Azure VPN pode ser exportado como um modelo de Gestor de Recursos Azure e ver versão ao longo do tempo. |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
