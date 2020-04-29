---
title: Controlos de segurança para Hubs de Eventos Azure
description: Este artigo fornece uma lista de controlos de segurança para avaliar os Hubs de Eventos Azure (rede, identidade, proteção de dados, etc.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309511"
---
# <a name="security-controls-for-azure-event-hubs"></a>Controlos de segurança para Hubs de Eventos Azure

Este artigo documenta os controlos de segurança incorporados nos Hubs de Eventos Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte final de serviço| Sim |  |  |
| Suporte à injeção VNet| Não | |  |
| Isolamento da rede e suporte de firewalling| Sim |  |  |
| Apoio de túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | |  |
| Registo e auditoria de planos de controlo e gestão| Sim |  |  |
| Registo e auditoria de planos de dados| Sim |   |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | | [Autorizar o acesso aos Hubs de Eventos Azure,](authorize-access-event-hubs.md)autorizar o acesso aos recursos do [Event Hubs utilizando o Diretório Ativo do Azure,](authorize-access-azure-active-directory.md)autorizando o acesso aos recursos do [Event Hubs utilizando assinaturas de acesso partilhados](authorize-access-shared-access-signature.md) |
| Autorização|  Sim | | [Autenticar uma identidade gerida com o Diretório Ativo azure para aceder aos Recursos](authenticate-managed-identity.md)dos Hubs de Eventos, [autenticar uma aplicação com o Diretório Ativo Azure para aceder aos recursos do Event Hubs,](authenticate-application.md)autenticar o acesso aos recursos do Event [Hubs utilizando assinaturas de acesso partilhado (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft |  Sim | |  |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim. Disponível para clusters dedicados. | Uma chave gerida pelo cliente no Azure KeyVault pode ser usada para encriptar os dados num Hub de Eventos em repouso. | [Configure as chaves geridas pelo cliente para encriptar os dados do Azure Event Hubs em repouso utilizando o portal Azure](configure-customer-managed-key.md) |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | |  |
| Chamadas api encriptadas| Sim |  |  |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | |  |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
