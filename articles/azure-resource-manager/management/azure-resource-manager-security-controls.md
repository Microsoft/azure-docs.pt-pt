---
title: Controlos de segurança
description: Uma lista de controlos de segurança incorporados para avaliar o serviço Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: bb8742c38fae88dc1fd1fd1ec175b248f30df3a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054462"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controlos de segurança para o Gestor de Recursos Azure

Este artigo documenta os controlos de segurança incorporados no Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | HTTPS/TLS. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | N/D | O Azure Resource Manager não armazena nenhum conteúdo do cliente, apenas controla dados. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| Sim | |
| Chamadas da API encriptadas| Sim | |

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Não | |
| Suporte à injeção VNet| Sim | |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte de túneis forçados| Não |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Não | |
| Registo e auditoria de avião de controlo e gestão| Sim | Os registos de atividade expõem todas as operações de escrita (PUT, POST, DELETE) realizadas nos seus recursos; ver [Ver registos de atividade para auditar ações sobre recursos](view-activity-logs.md). |
| Registo e auditoria de planos de dados| N/D | |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [Azure Ative Directory](../../active-directory/index.yml) baseado.|
| Autorização| Sim | |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim |  |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)
