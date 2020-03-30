---
title: Controlos de segurança
description: Conheça os controlos de segurança utilizados no serviço de backup Azure. Estes controlos ajudam o serviço a prevenir, detetar e responder a vulnerabilidades de segurança.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172144"
---
# <a name="security-controls-for-azure-backup"></a>Controlos de segurança para backup azure

Este artigo documenta os controlos de segurança incorporados no Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte final de serviço| Não |  |  |
| Suporte à injeção VNet| Não |  |  |
| Isolamento da rede e suporte de firewalling| Sim | O túnel forçado é suportado para reforços VM. A escavação forçada não é suportada para cargas de trabalho dentro de VMs. |  |
| Apoio de túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | O Log Analytics é suportado através de registos de recursos. Para mais informações, consulte o [Monitor Azure Backup com cargas de trabalho protegidas utilizando o Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Registo e auditoria de planos de controlo e gestão| Sim | Todas as ações desencadeadas pelo cliente do portal Azure estão registadas em registos de atividade. |  |
| Registo e auditoria de planos de dados| Não | O avião de dados azure Backup não pode ser contactado diretamente.  |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Autenticação| Sim | A autenticação é através do Diretório Ativo Azure. |  |
| Autorização| Sim | São utilizadas funções rBAC criadas e incorporadas. Para mais informações, consulte use o [Controlo de Acesso baseado em funções para gerir os pontos](/azure/backup/backup-rbac-rs-vault)de recuperação de backup do Azure . |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | | Documentação
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Utilizando encriptação do serviço de armazenamento para contas de armazenamento. |  |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Não |  |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| Não |  |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Não | Utilizando HTTPS. |  |
| Chamadas api encriptadas| Sim |  |  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim|  |  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
