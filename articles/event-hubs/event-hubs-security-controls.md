---
title: Controlos de segurança para hubs de eventos Azure
description: Este artigo fornece uma lista de controlos de segurança para a avaliação dos Hubs de Eventos Azure (rede, identidade, proteção de dados, etc.).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85315402"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controlos de segurança para hubs de eventos Azure

Este artigo documenta os controlos de segurança incorporados nos Hubs de Eventos Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte ao ponto final de serviço| Sim |  |  |
| Suporte à injeção VNet| Não | |  |
| Isolamento de rede e suporte de firewall| Sim |  |  |
| Suporte de túneis forçados| Não |  |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | |  |
| Registo e auditoria de avião de controlo e gestão| Sim |  |  |
| Registo e auditoria de planos de dados| Sim |   |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | | [Autorizar o acesso aos Hubs de Eventos Azure,](authorize-access-event-hubs.md) [autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md), [autorizando o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md) |
| Autorização|  Sim | | [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos do Centro de Eventos](authenticate-managed-identity.md), [Autenticar uma aplicação com o Azure Ative Directory para aceder aos recursos do Event Hubs,](authenticate-application.md) [Autenticar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft |  Sim | |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim. Disponível para clusters dedicados. | Uma chave gerida pelo cliente no Azure KeyVault pode ser usada para encriptar os dados de um Centro de Eventos em repouso. | [Configure as chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso utilizando o portal Azure](configure-customer-managed-key.md) |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | |  |
| Chamadas da API encriptadas| Sim |  |  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | |  |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
