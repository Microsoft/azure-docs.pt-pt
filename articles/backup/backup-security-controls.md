---
title: Controlos de segurança
description: Saiba mais sobre os controlos de segurança utilizados no serviço Azure Backup. Estes controlos ajudam o serviço a prevenir, detetar e responder a vulnerabilidades de segurança.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e69053d4646207b34e8df31b949410994c967303
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827295"
---
# <a name="security-controls-for-azure-backup"></a>Controlos de segurança para Azure Backup

Este artigo documenta os controlos de segurança incorporados na Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|--|
| Suporte ao ponto final de serviço| No |  |  |
| Suporte à injeção VNet| No |  |  |
| Isolamento de rede e suporte de firewall| Yes | O túnel forçado é suportado para apoio VM. Os túneis forçados não são suportados por cargas de trabalho dentro de VMs. |  |
| Suporte de túneis forçados| No |  |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação
|---|---|--|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | O Log Analytics é suportado através de registos de recursos. Para obter mais informações, consulte [monitor Azure Backup cargas de trabalho protegidas usando Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Registo e auditoria de avião de controlo e gestão| Yes | Todas as ações desencadeadas pelo cliente a partir do portal Azure são registadas em registos de atividade. |  |
| Registo e auditoria de planos de dados| No | O avião de dados Azure Backup não pode ser contactado diretamente.  |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação
|---|---|--|--|
| Autenticação| Yes | A autenticação é através do Diretório Ativo Azure. |  |
| Autorização| Yes | São utilizadas funções criadas pelo cliente e Azure. Para obter mais informações, consulte [use o Controlo de Acesso Baseado em Funções para gerir os pontos de recuperação de backup do Azure](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Yes | Utilização de encriptação de serviço de armazenamento para contas de armazenamento. |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | No |  |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| No |  |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| No | Utilizando HTTPS. |  |
| Chamadas da API encriptadas| Yes |  |  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação
|---|---|--|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim|  |  |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
