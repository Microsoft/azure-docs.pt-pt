---
title: Atributos comuns de segurança para o Azure Resource Manager
description: Uma lista de verificação de atributos comuns de segurança para avaliar o Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002263"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atributos de segurança para o Azure Resource Manager

Este artigo documenta os atributos de segurança criados para o Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | HTTPS/TLS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| N/A | O Gestor de recursos do Azure não armazena nenhum conteúdo de cliente, apenas os dados de controle. |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Sim | |
| Chamadas de API encriptadas| Sim | |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não | |
| Suporte de injeção de VNet| Sim | |
| Isolamento de rede e o suporte de firewall| Não |  |
| Suporte de encapsulamento de forçado| Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Não | |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [O Azure Active Directory](/azure/active-directory) com base.|
| Autorização| Sim | |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Expor todas as operações (PUT, POST, DELETE) efetuadas nos seus recursos; de escrita de registos de atividades ver [ver registos de atividades para auditar as ações em recursos](resource-group-audit.md). |
| Auditoria e registo de plano de dados| N/A | |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim |  |
