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
ms.openlocfilehash: 030e2c23d68a3fbbc96dd7591583cb27b650d011
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83200001"
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
| Suporte de túneis forçados| Sim | Consulte [a configuração de túneis forçados utilizando o modelo de implementação do Gestor de Recursos Azure](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Consulte [o Monitor e atualize uma máquina virtual Linux em Azure](/azure/virtual-machines/linux/tutorial-monitoring) e Monitor e [atualize uma máquina virtual do Windows em Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
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
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Sim | A azure Virtual Machines suporta a encriptação [ExpressRoute](/azure/expressroute) e VNet. Ver [encriptação em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | As chaves geridas pelo cliente são um cenário de encriptação Azure suportado; ver Ver [encriptação do disco Azure para conjuntos de escala de máquinas virtuais](disk-encryption-overview.md)|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)
