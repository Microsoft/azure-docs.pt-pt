---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "77111237"
---
| **Fornecedor** | **Família de dispositivos** | **Versão do firmware** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (Pré-visualização)|
|Cisco | ASA | ASA ( * ) RouteBased (IKEv2- No BGP) para ASA abaixo de 9.8 |
|Cisco | ASA | Rota asaBased (IKEv2 - Sem BGP) para ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RotaBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Obrigatório: NarrowAzureTrafficSelectors (ativar a opção UsePolicyBasedTrafficSelectors) e CustomAzurePolicies (IKE/IPsec)
>
