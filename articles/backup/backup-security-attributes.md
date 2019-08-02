---
title: Atributos de segurança para o backup do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o backup do Azure
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: dacurwin
ms.openlocfilehash: e6ea701840b201c2794d81be5019d57ca531cd00
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688510"
---
# <a name="security-attributes-for-azure-backup"></a>Atributos de segurança para o backup do Azure

Este artigo documenta os atributos de segurança internos do backup do Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso (como criptografia do lado do servidor, criptografia do lado do servidor com chaves gerenciadas pelo cliente e outros recursos de criptografia)| Sim | Usando a criptografia do serviço de armazenamento para contas de armazenamento. |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | Usando HTTPS. |
| Tratamento de chave de criptografia (CMK, BYOK, etc.)| Não |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Não |  |
| Chamadas de API criptografadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |
| Suporte à injeção de VNet| Não |  |
| Isolamento de rede e suporte de firewall| Sim | O túnel forçado tem suporte para backup de VM. O túnel forçado não tem suporte para cargas de trabalho em execução dentro de VMs. |
| Suporte a túnel forçado| Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Há suporte para Log Analytics por meio dos logs de diagnóstico. Consulte [monitorar cargas de trabalho protegidas de backup do Azure usando log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) para obter mais informações. |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | As funções RBAC criadas e internas do cliente são usadas. Consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação de backup do Azure](/azure/backup/backup-rbac-rs-vault) para obter mais informações. |


## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as ações disparadas pelo cliente da portal do Azure são registradas em logs de atividades. |
| Log e auditoria do plano de dados| Não | O plano de dados do backup do Azure não pode ser acessado diretamente.  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim|  |