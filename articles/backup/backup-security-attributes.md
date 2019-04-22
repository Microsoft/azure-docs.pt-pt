---
title: Atributos comuns de segurança para o Azure Backup
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure Backup
services: backup
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 93dd5372bffb278894987cd3cc2b8322cbc5e577
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679692"
---
# <a name="common-security-attributes-for-azure-backup"></a>Atributos comuns de segurança para o Azure Backup

A segurança integra todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comuns incorporados no Azure Backup. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | A utilizar a encriptação do serviço de armazenamento para as contas de armazenamento. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Não | Através de HTTPS. |
| A manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Não |  |
| Chamadas de API encriptadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não |  |
| suporte de Injeção de vNET| Não |  |
| Isolamento de rede / suporte de firewall| Sim | O túnel forçado é suportado para cópia de segurança VM. O túnel forçado não é suportado para cargas de trabalho em execução em VMs. |
| Suporte para protocolo de túnel forçado | Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | O log Analytics é suportado através de registos de diagnóstico. Ver [cargas de trabalho utilizar o Log Analytics de protegidas de cópia de segurança do Azure Monitor](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

## <a name="iam-support"></a>Suporte IAM

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Gestão de acesso - autenticação| Sim | A autenticação é através do Azure Active Directory. |
| Gestão de acesso - autorização| Sim | Cliente criada e funções RBAC incorporadas são usadas. Ver [Use Role-Based o controlo de acesso para gerir pontos de recuperação de cópia de segurança do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


## <a name="audit-trail"></a>Registo de Auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Planear a gestão/controlo e auditoria do registo| Sim | Todas as ações de cliente acionada no portal do Azure são registadas nos registos de atividade. |
| Registo e auditoria do plano de dados| Não | Não é possível aceder diretamente ao plano de dados de cópia de segurança do Azure.  |

## <a name="configuration-management"></a>Gestão da Configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim|  |