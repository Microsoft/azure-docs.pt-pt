---
title: Controlos de segurança para a Azure Service Fabric
description: Saiba mais sobre os controlos de segurança da Azure Service Fabric. Inclui uma lista de verificação de controlos de segurança incorporados.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645434"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controlos de segurança para a Azure Service Fabric

Este artigo documenta os controlos de segurança incorporados no Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Sim |  |
| Suporte à injeção VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Utilizando grupos de segurança de rede (NSG). |
| Suporte de túneis forçados| Sim | A ligação em rede Azure fornece túneis forçados. |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Utilizando o suporte de monitorização Azure e o suporte de terceiros. |
| Registo e auditoria de avião de controlo e gestão| Sim | Todas as operações de controlo do avião passam por processos de auditoria e aprovação. |
| Registo e auditoria de planos de dados| N/D | O cliente é dono do cluster.  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | A autenticação é através do Diretório Ativo Azure. |
| Autorização| Sim | Gestão de identidade e acesso (IAM) para chamadas via SFRP. As chamadas diretamente para o ponto final do cluster suportam duas funções: Utilizador e Administrador. O cliente pode mapear as APIs para qualquer um dos papéis. |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | O cliente é dono do cluster e da balança de máquinas virtual em que o cluster é construído. A encriptação do disco azul pode ser ativada no conjunto de escala de máquina virtual. |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | O cliente é dono do cluster e da balança de máquinas virtual em que o cluster é construído. A encriptação do disco azul pode ser ativada no conjunto de escala de máquina virtual. |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim |  |
| Chamadas da API encriptadas| Sim | As chamadas API do Tecido de Serviço são es feitas através do Azure Resource Manager. É necessário um token web JSON válido (JWT). |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)