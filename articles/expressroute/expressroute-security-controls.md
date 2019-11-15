---
title: 'Azure ExpressRoute: controles de segurança'
description: Uma lista de verificação de controles de segurança para avaliar o Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079920"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de segurança para o Azure ExpressRoute

Este artigo documenta os controles de segurança internos do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D |  |
| Suporte à injeção de VNet| N/D | |
| Isolamento de rede e suporte de firewall| Sim | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte a túnel forçado| N/D | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Não |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |
| Autorização|  Sim |Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | O ExpressRoute não armazena dados do cliente. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | |
| Chamadas de API criptografadas| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio do NRP (provedor de recursos de rede). |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).