---
title: Controles de segurança para Azure Resource Manager
description: Uma lista de verificação de controles de segurança para avaliar Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 97fd6611d7e2a2787b865365c4c7579f89f17d01
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886717"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controles de segurança para Azure Resource Manager

Este artigo documenta os controles de segurança internos do Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | Sim |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | HTTPS/TLS. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/A | O Azure Resource Manager não armazena nenhum conteúdo do cliente, só controla os dados. |
| Criptografia em nível de coluna (serviços de dados do Azure)| Sim | |
| Chamadas de API criptografadas| Sim | |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte a túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Não | |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Os logs de atividades expõem todas as operações de gravação (PUT, POST e DELETE) executadas em seus recursos; consulte [Exibir logs de atividades para auditar ações em recursos](resource-group-audit.md). |
| Log e auditoria do plano de dados| N/A | |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Baseado em [Azure Active Directory](/azure/active-directory) .|
| Autorização| Sim | |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).