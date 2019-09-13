---
title: Controles de segurança para Azure Load Balancer
description: Uma lista de verificação de controles de segurança para avaliar Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e0be6635a0916183e1dfe776bef4c547578383dc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886527"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controles de segurança para Azure Load Balancer

Este artigo documenta os controles de segurança internos do Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/A | |
| Suporte à injeção de VNet| N/A | |
| Isolamento de rede e suporte de firewall| N/A |  |
| Suporte a túnel forçado| N/A | |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |
| Log e auditoria do plano de dados | N/A |  |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| N/A |  |
| Autorização| N/A |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | N/A | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| N/A | |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/A | |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Por meio do [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| N/A |  | 

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).