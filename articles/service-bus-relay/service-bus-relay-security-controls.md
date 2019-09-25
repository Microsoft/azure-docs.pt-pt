---
title: Controles de segurança para retransmissão do barramento de serviço do Azure
description: Uma lista de verificação de controles de segurança para avaliar a retransmissão do barramento de serviço do Azure
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219979"
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

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Authentication| Sim | Via SAS. | [Autenticação e autorização de retransmissão do Azure](relay-authentication-and-authorization.md) |
| Autorização|  Sim | Via SAS. | [Autenticação e autorização de retransmissão do Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft |  N/A | A retransmissão é um soquete da Web e não mantém os dados. |   |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | Usa somente certificados TLS da Microsoft.  |   |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |   |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O serviço requer TLS. |   |
| Chamadas de API criptografadas| Sim | HTTPS. |


## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).