---
title: Controlos de segurança para Azure Relay
description: Estes artigos fornecem uma lista de controlos de segurança incorporados para avaliar o Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: ce5053366ac1d3536a152610d8ed7f76fad62b84
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919583"
---
# <a name="security-controls-for-azure-relay"></a>Controlos de segurança para Azure Relay

Este artigo documenta os controlos de segurança incorporados no Azure Relay.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte privado de ponto final| Yes |  |   |
| Isolamento de rede e suporte de firewall| Yes |  |   |
| Suporte de túneis forçados| N/D | Relé é o túnel TLS  |   |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | |   |
| Registo e auditoria de avião de controlo e gestão| Yes | Através do [Gestor de Recursos Azure.](../azure-resource-manager/index.yml) |   |
| Registo e auditoria de planos de dados| Yes | Sucesso de ligação /falha e erros e registados.  |   |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Yes | Via SAS. | [Autenticação e autorização do Relé Azure](relay-authentication-and-authorization.md) |
| Autorização|  Yes | Via SAS. | [Autenticação e autorização do Relé Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft |  N/D | O retransmissor é uma tomada web e não persiste em dados. |   |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | No | Utiliza apenas certificados Microsoft TLS.  |   |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |   |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Yes | O serviço requer TLS. |   |
| Chamadas da API encriptadas| Yes | HTTPS. |


## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Yes | Através do [Gestor de Recursos Azure.](../azure-resource-manager/index.yml)|   |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)