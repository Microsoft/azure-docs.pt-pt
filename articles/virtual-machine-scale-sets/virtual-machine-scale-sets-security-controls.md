---
title: Controlos de segurança para conjuntos de escala de máquinas virtuais azure
description: Uma lista de controlos de segurança para avaliar conjuntos de escala de máquinas virtuais Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 030e2c23d68a3fbbc96dd7591583cb27b650d011
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200001"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controlos de segurança para conjuntos de escala de máquinas virtuais azure

Este artigo documenta os controlos de segurança incorporados em conjuntos de escala de máquinas virtuais Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim | |
| Suporte à injeção VNet| Sim | |
| Suporte de isolamento de rede e firewalling| Sim |  |
| Apoio de túnel forçado| Sim | Consulte [a configuração de túneis forçados utilizando o modelo](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)de implantação do Gestor de Recursos Azure . |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Consulte [o Monitor e atualize uma máquina virtual Linux em Azure](/azure/virtual-machines/linux/tutorial-monitoring) e Monitor e [atualize uma máquina virtual windows em Azure](/azure/virtual-machines/windows/tutorial-monitoring). |
| Registo e auditoria de planos de controlo e gestão| Sim |  |
| Registo e auditoria de planos de dados | Não |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Consulte encriptação de [disco azure para conjuntos](disk-encryption-overview.md)de escala de máquina virtual . |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | As Máquinas Virtuais Azure suportam a encriptação [ExpressRoute](/azure/expressroute) e VNet. Ver [encriptação em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | As chaves geridas pelo cliente são um cenário de encriptação Azure suportada; ver Ver Encriptação de [disco azure para conjuntos](disk-encryption-overview.md) de escala de máquina virtual|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas api encriptadas| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
