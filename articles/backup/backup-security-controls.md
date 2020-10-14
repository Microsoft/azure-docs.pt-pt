---
title: Controlos de segurança
description: Saiba mais sobre os controlos de segurança utilizados no serviço Azure Backup. Estes controlos ajudam o serviço a prevenir, detetar e responder a vulnerabilidades de segurança.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 40bd35bdf83d336aebd37cdda0a0b59164ac9f7a
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055890"
---
# <a name="security-controls-for-azure-backup"></a>Controlos de segurança para Azure Backup

Este artigo documenta os controlos de segurança incorporados na Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|--|
| Suporte ao ponto final de serviço| No |  |  |
| Suporte à injeção VNet| No |  |  |
| Isolamento de rede e suporte de firewall| Yes | |  |
| Suporte de túneis forçados para Azure VMs | Yes  |  |  |
| Suporte de túneis forçado para aplicações que desemcorram dentro dos VMs do Azure| No  |  |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação
|---|---|--|--|
| Suporte de monitorização Azure (como analítica de log, insights de aplicações)| Yes | O Log Analytics é suportado através de registos de recursos. Para obter mais informações, consulte [monitor Azure Backup cargas de trabalho protegidas usando Log Analytics](backup-azure-diagnostics-mode-data-model.md). |  |
| Registo e auditoria de avião de controlo e gestão| Yes | Todas as ações desencadeadas pelo cliente a partir do portal Azure são registadas em registos de atividade. |  |
| Registo e auditoria de planos de dados| No | O avião de dados Azure Backup não pode ser contactado diretamente.  |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação
|---|---|--|--|
| Autenticação| Yes | A autenticação é através do Diretório Ativo Azure. |  |
| Autorização| Yes | São utilizadas funções criadas pelo cliente e Azure. Para obter mais informações, consulte [use Role-Based Access Control para gerir pontos de recuperação de backup do Azure](./backup-rbac-rs-vault.md). |  |

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
| Suporte de gestão de configuração (versão da configuração, e assim por diante)| Sim|  |  |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
