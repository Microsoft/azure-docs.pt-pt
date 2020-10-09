---
title: Controlos de segurança para mensagens de autocarro da Azure Service
description: Uma lista de controlos de segurança para avaliar as mensagens de autocarro da Azure Service
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341625"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlos de segurança para mensagens de autocarro da Azure Service

Este artigo documenta os controlos de segurança incorporados nas mensagens de autocarro da Azure Service.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte ao ponto final de serviço| Sim (apenas nível premium) | Os pontos finais do serviço VNet são suportados apenas para [o nível Service Bus Premium.](service-bus-premium-messaging.md) |  |
| Suporte à injeção VNet| Não | |  |
| Isolamento de rede e suporte de firewall| Sim (apenas nível premium) |  |  |
| Suporte de túneis forçados| Não |  |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Suportado através do [Monitor e Alertas Azure](service-bus-metrics-azure-monitor.md). |  |
| Registo e auditoria de avião de controlo e gestão| Sim | Os registos de operações estão disponíveis.  | [Registos de diagnóstico de ônibus de serviço](service-bus-diagnostic-logs.md) |
| Registo e auditoria de planos de dados| Não |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | Gerido através da [Identidade de Serviço Gerido pelo Diretório Ativo Azure](service-bus-managed-service-identity.md).| [Autenticação e autorização do Service Bus](service-bus-authentication-and-authorization.md). |
| Autorização| Sim | Suporta a autorização através [do símbolo RBAC](authenticate-application.md) e SAS. | [Autenticação e autorização do Service Bus](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft |  Sim para encriptação do lado do servidor em repouso por predefinição. |  |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim. | Uma chave gerida pelo cliente em Azure KeyVault pode ser usada para encriptar os dados no Service Bus Namespace em repouso. | [Configure as chaves geridas pelo cliente para encriptar os dados do Azure Service Bus em repouso utilizando o portal Azure](configure-customer-managed-key.md)  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |   |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | Suporta o mecanismo HTTPS/TLS padrão. |   |
| Chamadas da API encriptadas| Sim | As chamadas API são e feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |   |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | Suporta a versão do fornecedor de recursos através da [API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
