---
title: Controles de segurança para o backup do Azure
description: Uma lista de verificação de controles de segurança para avaliar o backup do Azure
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: f44be7556b6d741df93faeeab1dbdfc15bc8ebfd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211813"
---
# <a name="security-controls-for-azure-backup"></a>Controles de segurança para o backup do Azure

Este artigo documenta os controles de segurança incorporados ao backup do Azure. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas | Documentação
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |  |
| Suporte à injeção de VNet| Não |  |  |
| Isolamento de rede e suporte de firewall| Sim | O túnel forçado tem suporte para backup de VM. O túnel forçado não tem suporte para cargas de trabalho em execução dentro de VMs. |  |
| Suporte a túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Há suporte para Log Analytics por meio dos logs de diagnóstico. Consulte [monitorar cargas de trabalho protegidas de backup do Azure usando log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |  |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as ações disparadas pelo cliente da portal do Azure são registradas em logs de atividades. |  |
| Log e auditoria do plano de dados| Não | O plano de dados do backup do Azure não pode ser acessado diretamente.  |  |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |  |
| Autorização| Sim | As funções RBAC criadas e internas do cliente são usadas. Consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação de backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas | | Documentação
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | Sim | Usando a criptografia do serviço de armazenamento para contas de armazenamento. |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não |  |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Não |  |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | Usando HTTPS. |  |
| Chamadas de API criptografadas| Sim |  |  |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas| | Documentação
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).