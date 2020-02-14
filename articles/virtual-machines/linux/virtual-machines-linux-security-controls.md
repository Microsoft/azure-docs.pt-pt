---
title: Controlos de segurança para máquinas virtuais Azure Linux - Linux
description: Uma lista de controlos de segurança para avaliar as Máquinas Virtuais Azure Linux
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190578"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Controlos de segurança para máquinas virtuais Linux

Este artigo documenta os controlos de segurança incorporados em Máquinas Virtuais Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim | |
| Suporte à injeção VNet| Sim | |
| Suporte de isolamento de rede e firewalling| Sim |  |
| Apoio de túnel forçado| Sim | Consulte [a configuração de túneis forçados utilizando o modelo](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)de implantação do Gestor de Recursos Azure . |

## <a name="monitoring--logging"></a>Monitorização e exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Consulte [o Monitor e atualize uma máquina virtual Linux em Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
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
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Consulte encriptação de [disco azure para VMs Linux](disk-encryption-overview.md). |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | As Máquinas Virtuais Azure suportam a encriptação [ExpressRoute](/azure/expressroute) e VNet. Ver [encriptação em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | As chaves geridas pelo cliente são um cenário de encriptação Azure suportada; ver [visão geral da encriptação Azure.](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas api encriptadas| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../../security/fundamentals/security-controls.md)
