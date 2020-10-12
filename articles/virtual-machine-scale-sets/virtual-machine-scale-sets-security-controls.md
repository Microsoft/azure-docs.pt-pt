---
title: Controlos de segurança para conjuntos de balanças de máquinas virtuais Azure
description: Uma lista de controlos de segurança para avaliar conjuntos de escala de máquina virtual Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029421"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controlos de segurança para conjuntos de balanças de máquinas virtuais Azure

Este artigo documenta os controlos de segurança incorporados em conjuntos de escala de máquina virtual Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Sim | |
| Suporte à injeção VNet| Sim | |
| Suporte de isolamento e firewalling de rede| Sim |  |
| Suporte de túneis forçados| Sim | Consulte [a configuração de túneis forçados utilizando o modelo de implementação do Gestor de Recursos Azure](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Consulte [o Monitor e atualize uma máquina virtual Linux em Azure](../virtual-machines/linux/tutorial-monitor.md) e Monitor e [atualize uma máquina virtual do Windows em Azure](../virtual-machines/windows/tutorial-monitor.md). |
| Registo e auditoria de avião de controlo e gestão| Sim |  |
| Registo e auditoria de planos de dados | Não |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | Consulte [a encriptação do disco Azure para conjuntos de escala de máquinas virtuais](disk-encryption-overview.md). |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Sim | A azure Virtual Machines suporta a encriptação [ExpressRoute](../expressroute/index.yml) e VNet. Ver [encriptação em trânsito em VMs](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | As chaves geridas pelo cliente são um cenário de encriptação Azure suportado; ver Ver [encriptação do disco Azure para conjuntos de escala de máquinas virtuais](disk-encryption-overview.md)|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
