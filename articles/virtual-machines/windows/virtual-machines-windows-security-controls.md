---
title: Controlos de segurança para máquinas virtuais Do Windows Azure
description: Uma lista de controlos de segurança para avaliar as máquinas virtuais do Azure Windows
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101591"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controlos de segurança para máquinas virtuais do Windows

Este artigo documenta os controlos de segurança incorporados nas Máquinas Virtuais do Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | [Monitorize e atualize uma máquina virtual do Windows em Azure](tutorial-monitoring.md). |
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
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Ver [encriptar discos virtuais num VM do Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
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
