---
title: Controles de segurança para o Azure Service Fabric
description: Uma lista de verificação de controles de segurança para avaliar o Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886776"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controles de segurança para o Azure Service Fabric

Este artigo documenta os controles de segurança criados no Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Usando NSG (grupos de segurança de rede). |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o suporte ao monitoramento do Azure e o suporte de terceiros. |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações do plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Log e auditoria do plano de dados| N/A | O cliente possui o cluster.  |

## <a name="identity"></a>identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | A autenticação é por meio de Azure Active Directory. |
| Autorização| Sim | IAM (gerenciamento de acesso e identidade) para chamadas via SFRP. As chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: Usuário e administrador. O cliente pode mapear as APIs para qualquer função. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: Chaves gerenciadas pela Microsoft | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim |  |
| Chamadas de API criptografadas| Sim | Service Fabric chamadas à API são feitas por meio de Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="configuration-management"></a>Gestão de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).