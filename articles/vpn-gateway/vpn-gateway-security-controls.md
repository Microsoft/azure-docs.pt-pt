---
title: Controlos de segurança para Azure VPN Gateway
description: Uma lista de controlos de segurança para avaliar o Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972282"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controlos de segurança para Azure VPN Gateway

Este artigo documenta os controlos de segurança incorporados no Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte de isolamento de rede e firewalling| Sim | Os gateways VPN são instâncias VM dedicadas para cada rede virtual de cliente  |
| Apoio de túnel forçado| Sim |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Consulte [o Azure Monitor Diagnostics Logs/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor Metrics/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Registo e auditoria de planos de controlo e gestão| Sim | Registo de Atividades do Gestor de Recursos Azure. |
| Registo e auditoria de planos de dados | Sim | [Registos](../azure-resource-manager/management/view-activity-logs.md) de diagnóstico do Monitor Azure para registos de conectividade VPN e auditoria. |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [Diretório Azure Ative](../active-directory/fundamentals/active-directory-whatis.md) para gerir o serviço e configurar o gateway Azure VPN. |
| Autorização| Sim | Autorização de Apoio via [RBAC.](../role-based-access-control/overview.md) |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | N/D | Dados do cliente do cliente de trânsito de gateway VPN, NÃO armazenam dados do cliente |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | O gateway VPN encripta pacotes de clientes entre gateways Azure VPN e dispositivos VPN de clientes no local (S2S) ou clientes VPN (P2S). Os gateways VPN também suportam a encriptação VNet-to-VNet. |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Não | As chaves pré-partilhadas especificadas pelo cliente são encriptadas em repouso; mas ainda não integrado com a CMK. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas api encriptadas| Sim | Através do Gestor de [Recursos Azure](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Para operações de gestão, o estado de uma configuração de gateway Azure VPN pode ser exportado como um modelo de Gestor de Recursos Azure e versão ao longo do tempo. |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
