---
title: Controlos de segurança para máquinas virtuais Azure Windows
description: Uma lista de controlos de segurança para avaliar máquinas virtuais do Azure Windows
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82101591"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controlos de segurança para máquinas virtuais do Windows

Este artigo documenta os controlos de segurança incorporados nas Máquinas Virtuais do Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Yes | |
| Suporte à injeção VNet| Yes | |
| Suporte de isolamento e firewalling de rede| Yes |  |
| Suporte de túneis forçados| Yes | Consulte [a configuração de túneis forçados utilizando o modelo de implementação do Gestor de Recursos Azure](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | [Monitorize e atualize uma máquina virtual do Windows em Azure](tutorial-monitoring.md). |
| Registo e auditoria de avião de controlo e gestão| Yes |  |
| Registo e auditoria de planos de dados | No |  |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Yes |  |
| Autorização| Yes |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Yes | Consulte [discos virtuais encriptados num VM do Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Yes | A azure Virtual Machines suporta a encriptação [ExpressRoute](/azure/expressroute) e VNet. Ver [encriptação em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Yes | As chaves geridas pelo cliente são um cenário de encriptação Azure suportado; ver [visão geral da encriptação Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Yes | Via HTTPS e TLS. |



## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)
