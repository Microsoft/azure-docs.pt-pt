---
title: Controles de segurança para hubs de eventos do Azure
description: Uma lista de verificação de controles de segurança para avaliar os hubs de eventos do Azure
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219405"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controles de segurança para hubs de eventos do Azure

Este artigo documenta os controles de segurança criados nos hubs de eventos do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |  |
| Suporte à injeção de VNet| Não | |  |
| Isolamento de rede e suporte de firewall| Sim |  |  |
| Suporte a túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | |  |
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |  |
| Log e auditoria do plano de dados| Sim |   |  |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Authentication| Sim | | [Autorizar o acesso aos hubs de eventos do Azure](authorize-access-event-hubs.md), [autorizar o acesso aos recursos dos hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md), [autorizando o acesso aos recursos dos hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md) |
| Autorização|  Sim | | [Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-managed-identity.md), [autenticar um aplicativo com Azure Active Directory para acessar recursos de hubs de eventos](authenticate-application.md), [autenticar o acesso a recursos de hubs de eventos usando o assinaturas de acesso compartilhado (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft |  Sim | |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não |  |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | |  |
| Chamadas de API criptografadas| Sim |  |  |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
