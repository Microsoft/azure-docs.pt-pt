---
title: Atributos comuns de segurança para os Hubs de eventos do Azure
description: Uma lista de verificação de atributos comuns de segurança para avaliar os Hubs de eventos do Azure
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbaldwin
ms.openlocfilehash: 50f19d50f211972c2042c69a1e3177ffb4a47624
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247203"
---
# <a name="common-security-attributes-for-azure-event-hubs"></a>Atributos comuns de segurança para os Hubs de eventos do Azure

Este artigo documenta os atributos comuns de segurança incorporados ao Event Hubs do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  Sim | |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de Vnet</li><li>Encriptação de VNet a VNet</ul>| Sim | |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim |  |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim |  |
| suporte de injeção de vNET| Não | |
| Isolamento de rede e o suporte de firewall| Sim |  |
| Suporte de encapsulamento de forçado| Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | |
| Autorização|  Sim | |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim |  |
| Auditoria e registo de plano de dados| Sim |   |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | |
