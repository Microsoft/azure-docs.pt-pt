---
title: Controles de segurança para o Azure ExpressRoute
description: Uma lista de verificação de controles de segurança para avaliar o Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ab73ba6dd4b78d3cd0be5f4c7f7a502e5c58e08
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886502"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de segurança para o Azure ExpressRoute

Este artigo documenta os controles de segurança internos do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A |  |
| Suporte à injeção de VNet| N/A | |
| Isolamento de rede e suporte de firewall| Sim | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte a túnel forçado| N/A | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Não |   |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |
| Autorização|  Sim |Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft |  N/A | O ExpressRoute não armazena dados do cliente. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/A |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | |
| Chamadas de API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio do NRP (provedor de recursos de rede). |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).