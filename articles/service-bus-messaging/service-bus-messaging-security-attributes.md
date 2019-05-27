---
title: Atributos de segurança comuns para mensagens do Azure Service Bus
description: Uma lista de verificação de atributos comuns de segurança para avaliar a mensagens do Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003115"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atributos de segurança de mensagens do Azure Service Bus

Este artigo documenta os atributos de segurança integrados de mensagens do Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  Sim para servidor encriptação em repouso, por predefinição. | Chaves gerida pelo cliente e a BYOK ainda não são suportadas. Encriptação do lado do cliente é responsabilidade do cliente |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Suporta o mecanismo HTTPS/TLS padrão. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não |   |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | Chamadas de API são feitas através de [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim (apenas no escalão Premium) | Pontos finais de serviço de VNet são suportados para [escalão Premium do Service Bus](service-bus-premium-messaging.md) apenas. |
| Suporte de injeção de VNet| Não | |
| Isolamento de rede e o suporte de firewall| Sim (apenas no escalão Premium) |  |
| Suporte de encapsulamento de forçado| Não |  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | Suportado através de [o Azure Monitor e alertas](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Gerido através de [do Azure Active Directory identidade do serviço gerido](service-bus-managed-service-identity.md); consulte [barramento de serviço de autenticação e autorização](service-bus-authentication-and-authorization.md).|
| Autorização| Sim | Suporta a autorização via [RBAC](service-bus-role-based-access-control.md) (pré-visualização) e o token de SAS; consulte [do Service Bus autenticação e autorização](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Os registos de operações estão disponíveis; ver [registos de diagnóstico do Service Bus](service-bus-diagnostic-logs.md).  |
| Auditoria e registo de plano de dados| Não |  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Suporta o controlo de versões de fornecedor de recursos através da [API do Azure Resource Manager](/rest/api/resources/).|
