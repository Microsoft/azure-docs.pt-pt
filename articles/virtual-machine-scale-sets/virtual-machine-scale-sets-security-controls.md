---
title: Controles de segurança para conjuntos de dimensionamento de máquinas virtuais do Azure
description: Uma lista de verificação de controles de segurança para avaliar os conjuntos de dimensionamento de máquinas virtuais do Azure
services: virtual-machine-scale-sets
ms.service: virtual-machine-scale-sets
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d7e69e8ad0c5b14ac7ed8b941a7949f4f675812
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176760"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controles de segurança para conjuntos de dimensionamento de máquinas virtuais do Azure

Este artigo documenta os controles de segurança criados nos conjuntos de dimensionamento de máquinas virtuais do Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Sim | Consulte [Configurar o túnel forçado usando o modelo de implantação Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitorar e atualizar uma máquina virtual Linux no Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorar e atualizar uma máquina virtual do Windows no Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados | Não |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Consulte [como criptografar uma máquina virtual do Linux no Azure](/azure/virtual-machines/linux/disk-encryption-linux) e [criptografar discos virtuais em uma VM do Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | As máquinas virtuais do Azure dão suporte à criptografia de [ExpressRoute](/azure/expressroute) e VNet. Consulte [criptografia em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia do Azure com suporte; consulte [visão geral da criptografia do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/A | |
| Chamadas de API criptografadas| Sim | Via HTTPS e SSL. |

## <a name="configuration-management"></a>Gestão de configurações

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).