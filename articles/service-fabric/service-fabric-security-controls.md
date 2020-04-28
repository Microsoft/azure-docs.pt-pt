---
title: Controlos de segurança para tecido de serviço Azure
description: Conheça os controlos de segurança da Azure Service Fabric. Inclui uma lista de controlos de segurança incorporados.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645434"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controlos de segurança para tecido de serviço Azure

Este artigo documenta os controlos de segurança incorporados no Tecido de Serviço Azure. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim |  |
| Suporte à injeção VNet| Sim |  |
| Isolamento da rede e suporte de firewalling| Sim | Utilização de grupos de segurança em rede (NSG). |
| Apoio de túnel forçado| Sim | A rede Azure fornece túneis forçados. |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Utilizando o suporte de monitorização azure e apoio de terceiros. |
| Registo e auditoria de planos de controlo e gestão| Sim | Todas as operações de controlo dos aviões passam por processos de auditoria e aprovação. |
| Registo e auditoria de planos de dados| N/D | O cliente é dono do cluster.  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Diretório Ativo Azure. |
| Autorização| Sim | Gestão de identidade e acesso (IAM) para chamadas via SFRP. As chamadas diretamente para o ponto final do cluster suportam duas funções: Utilizador e Administrador. O cliente pode mapear as APIs para qualquer uma das funções. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | O cliente é dono do cluster e da escala de máquina virtual em que o cluster é construído. A encriptação do disco azure pode ser ativada no conjunto de escala de máquina virtual. |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | O cliente é dono do cluster e da escala de máquina virtual em que o cluster é construído. A encriptação do disco azure pode ser ativada no conjunto de escala de máquina virtual. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim |  |
| Chamadas api encriptadas| Sim | As chamadas a API de tecido de serviço são feitas através do Gestor de Recursos Azure. É necessário um token web JSON válido (JWT). |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)