---
title: Controles de segurança para o gateway de VPN do Azure
description: Uma lista de verificação de controles de segurança para avaliar o gateway de VPN do Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886802"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controles de segurança para o gateway de VPN do Azure

Este artigo documenta os controles de segurança incorporados ao gateway de VPN do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A | |
| Suporte à injeção de VNet| N/A | |
| Isolamento de rede e suporte de firewall| Sim | Gateways de VPN são instâncias de VM dedicadas para cada rede virtual de cliente  |
| Suporte a túnel forçado| Sim |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [Azure monitor logs de diagnóstico/alerta](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor métricas/alerta](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades Azure Resource Manager. |
| Log e auditoria do plano de dados | Sim | [Azure monitor logs de diagnóstico](../azure-resource-manager/resource-group-audit.md) para auditoria e log de conectividade de VPN. |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerenciar o serviço e configurar o gateway de VPN do Azure. |
| Autorização| Sim | Suporte à autorização via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | N/A | Dados de cliente de trânsito de gateway de VPN, não armazena dados do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O gateway de VPN criptografa os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte à criptografia de VNet para VNet. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | As chaves pré-compartilhadas especificadas pelo cliente são criptografadas em repouso; Mas não integrado com o CMK ainda. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e https  |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de gateway de VPN do Azure pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. | 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).