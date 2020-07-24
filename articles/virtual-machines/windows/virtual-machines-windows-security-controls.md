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
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088347"
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
| Suporte de túneis forçados| Yes | Consulte [a configuração de túneis forçados utilizando o modelo de implementação do Gestor de Recursos Azure](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | [Monitorize e atualize uma máquina virtual do Windows em Azure](./tutorial-monitor.md). |
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
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Yes | Consulte [discos virtuais encriptados num VM do Windows](./disk-encryption-overview.md). |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet )| Yes | A azure Virtual Machines suporta a encriptação [ExpressRoute](../../expressroute/index.yml) e VNet. Ver [encriptação em trânsito em VMs](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Yes | As chaves geridas pelo cliente são um cenário de encriptação Azure suportado; ver [visão geral da encriptação Azure](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |
| Chamadas da API encriptadas| Yes | Via HTTPS e TLS. |



## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)
