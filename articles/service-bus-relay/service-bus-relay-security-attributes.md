---
title: Atributos comuns de segurança para o reencaminhamento do Azure Service Bus
description: Uma lista de verificação de atributos comuns de segurança para avaliar o reencaminhamento do Azure Service Bus
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000153"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atributos de segurança de reencaminhamento do Azure Service Bus

Este artigo documenta os atributos de segurança incorporados no reencaminhamento do Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>|  N/A | Reencaminhamento é um soquete de web e não persiste dados. |
| Encriptação em trânsito:<ul><li>Express route encriptação</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | O serviço precisa TLS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Não | Utiliza certificados de Microsoft TLS apenas.  |
| Encriptação de nível de coluna (Serviços de dados do Azure)| N/A | |
| Chamadas de API encriptadas| Sim | HTTPS. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Não |  |
| Isolamento de rede e o suporte de firewall| Não |  |
| Suporte de encapsulamento de forçado| N/A | O reencaminhamento é o túnel TLS  |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Através de SAS. |
| Autorização|  Sim | Através de SAS. |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml). |
| Auditoria e registo de plano de dados| Sim | Êxito de ligação / falhas e erros e registado.  |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Sim | Por meio [o Azure Resource Manager](../azure-resource-manager/index.yml).|
