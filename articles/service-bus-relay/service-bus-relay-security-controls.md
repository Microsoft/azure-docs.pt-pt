---
title: Controles de segurança para retransmissão do barramento de serviço do Azure
description: Este artigo fornece uma lista de verificação de controles de segurança internos para avaliar a retransmissão do barramento de serviço do Azure.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514022"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controles de segurança para retransmissão do barramento de serviço do Azure

Este artigo documenta os controles de segurança criados na retransmissão do barramento de serviço do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte ao ponto de extremidade de serviço| Não |  |   |
| Isolamento de rede e suporte de firewall| Não |  |   |
| Suporte a túnel forçado| N/A | Retransmissão é o túnel TLS  |   |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |   |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Log e auditoria do plano de dados| Sim | Êxito na conexão/falha e erros e registrados.  |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | Via SAS. | [Autenticação e autorização de retransmissão do Azure](relay-authentication-and-authorization.md) |
| Autorização|  Sim | Via SAS. | [Autenticação e autorização de retransmissão do Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/A | A retransmissão é um soquete da Web e não mantém os dados. |   |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Usa somente certificados TLS da Microsoft.  |   |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |   |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O serviço requer TLS. |   |
| Chamadas de API criptografadas| Sim | HTTPS. |


## <a name="configuration-management"></a>Gestão de configurações

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).