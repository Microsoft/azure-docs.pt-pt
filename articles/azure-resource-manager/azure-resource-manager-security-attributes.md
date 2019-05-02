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
ms.openlocfilehash: ebc39dcd9fe921c794add48cc677a799841cbb78
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943631"
---
# <a name="common-security-attributes-for-azure-resource-manager"></a>Atributos comuns de segurança para o Azure Resource Manager

A segurança integra todos os aspectos de um serviço do Azure. Este artigo documenta os atributos de segurança comuns, criados para o Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim |  |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim | HTTPS/TLS. |
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

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | [O Azure Active Directory](/azure/active-directory) com base.|
| Autorização| Sim | |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Expor todas as operações (PUT, POST, DELETE) efetuadas nos seus recursos; de escrita de registos de atividades ver [ver registos de atividades para auditar as ações em recursos](resource-group-audit.md). |
| Auditoria e registo de plano de dados| N/A | |

## <a name="configuration-management"></a>Gestão da configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim |  |
