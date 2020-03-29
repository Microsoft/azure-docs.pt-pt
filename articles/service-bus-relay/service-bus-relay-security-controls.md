---
title: Controlos de segurança para o relé de autocarro saquede teto de serviço azure
description: Estes artigos fornecem uma lista de controlos de segurança incorporados para avaliar o Relé de Autocarros de Serviço Azure.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514022"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Controlos de segurança para o relé de autocarro saquede teto de serviço azure

Este artigo documenta os controlos de segurança incorporados no Azure Service Bus Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte final de serviço| Não |  |   |
| Isolamento da rede e suporte de firewalling| Não |  |   |
| Apoio de túnel forçado| N/D | Relé é o túnel TLS  |   |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | |   |
| Registo e auditoria de planos de controlo e gestão| Sim | Através [do Gestor de Recursos Azure.](../azure-resource-manager/index.yml) |   |
| Registo e auditoria de planos de dados| Sim | Sucesso de ligação/falha e erros e registados.  |   |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | Via SAS. | [Autenticação e autorização do Relé Azure](relay-authentication-and-authorization.md) |
| Autorização|  Sim | Via SAS. | [Autenticação e autorização do Relé Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft |  N/D | O relé é uma tomada web e não persiste dados. |   |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Não | Usa apenas certs Microsoft TLS.  |   |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |   |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | O serviço requer TLS. |   |
| Chamadas api encriptadas| Sim | HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Através [do Gestor de Recursos Azure.](../azure-resource-manager/index.yml)|   |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)