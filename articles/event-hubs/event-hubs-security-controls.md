---
title: Controles de segurança para hubs de eventos do Azure
description: Uma lista de verificação de controles de segurança para avaliar os hubs de eventos do Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3f02315012840b20dc4aa8639ade954f23a5526e
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886458"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controles de segurança para hubs de eventos do Azure

Este artigo documenta os controles de segurança criados nos hubs de eventos do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Não | |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Sim |   |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | |
| Autorização|  Sim | |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft |  Sim | |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | |
| Chamadas de API criptografadas| Sim |  |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
