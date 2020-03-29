---
title: Controlos de segurança
description: Uma lista de controlos de segurança incorporados para avaliar o serviço de Gestor de Recursos Azure.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485627"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlos de segurança para O Gestor de Recursos Azure

Este artigo documenta os controlos de segurança incorporados no Gestor de Recursos Azure.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | HTTPS/TLS. |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | N/D | O Azure Resource Manager não armazena conteúdos de clientes, apenas dados de controlo. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| Sim | |
| Chamadas api encriptadas| Sim | |

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Não | |
| Suporte à injeção VNet| Sim | |
| Isolamento da rede e suporte de firewalling| Não |  |
| Apoio de túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Não | |
| Registo e auditoria de planos de controlo e gestão| Sim | Os registos de atividade expõem todas as operações de escrita (PUT, POST, DELETE) realizadas nos seus recursos; ver [Ver registos de atividade para auditar ações sobre recursos](view-activity-logs.md). |
| Registo e auditoria de planos de dados| N/D | |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [Azure Ative Directory](/azure/active-directory) baseado.|
| Autorização| Sim | |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim |  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../../security/fundamentals/security-controls.md)