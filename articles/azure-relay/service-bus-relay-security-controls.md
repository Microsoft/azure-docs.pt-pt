---
title: Controlos de segurança para o Relé Azure
description: Estes artigos fornecem uma lista de controlos de segurança incorporados para avaliar o Relé Azure.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211464"
---
# <a name="security-controls-for-azure-relay"></a>Controlos de segurança para o Relé Azure

Este artigo documenta os controlos de segurança incorporados no Azure Relay.

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