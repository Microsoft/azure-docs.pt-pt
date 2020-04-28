---
title: Controlos de segurança para mensagens de ônibus de serviço Azure
description: Uma lista de controlos de segurança para avaliar as mensagens de autocarro seletivas do Serviço Azure
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75903256"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlos de segurança para mensagens de ônibus de serviço Azure

Este artigo documenta os controlos de segurança incorporados na Mensagem de Autocarro saqueada pela Azure Service.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte final de serviço| Sim (apenas nível premium) | Os pontos finais do serviço VNet são suportados apenas para o [nível Service Bus Premium.](service-bus-premium-messaging.md) |  |
| Suporte à injeção VNet| Não | |  |
| Isolamento da rede e suporte de firewalling| Sim (apenas nível premium) |  |  |
| Apoio de túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Suportado via [Monitor e Alertas Azure.](service-bus-metrics-azure-monitor.md) |  |
| Registo e auditoria de planos de controlo e gestão| Sim | Os registos de operações estão disponíveis.  | [Registos de diagnóstico de ônibus de serviço](service-bus-diagnostic-logs.md) |
| Registo e auditoria de planos de dados| Não |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | Gerido através da Identidade de Serviço Gerido por [Diretório Ativo Azure.](service-bus-managed-service-identity.md)| [Autenticação e autorização](service-bus-authentication-and-authorization.md)do autocarro de serviço. |
| Autorização| Sim | Suporta a autorização através do token [RBAC](authenticate-application.md) e SAS. | [Autenticação e autorização](service-bus-authentication-and-authorization.md)do autocarro de serviço. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft |  Sim, para encriptação do lado do servidor em repouso por padrão. |  |  |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim. | Uma chave gerida pelo cliente no Azure KeyVault pode ser usada para encriptar os dados no espaço de nome do autocarro de serviço em repouso. | [Configure as chaves geridas pelo cliente para encriptar os dados do Ônibus de Serviço Azure em repouso utilizando o portal Azure](configure-customer-managed-key.md)  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |   |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | Suporta o mecanismo PADRÃO HTTPS/TLS. |   |
| Chamadas api encriptadas| Sim | As chamadas API são feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) e https. |   |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Suporta a versão do fornecedor de recursos através da API Gestor [de Recursos Azure](/rest/api/resources/).|   |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
