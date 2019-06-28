---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184129"
---
| **Fornecedor** | **Família de dispositivos** | **Versão de firmware** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (pré-visualização)|
|Cisco | ASA | ASA (*) RouteBased (IKEv2-não-BGP) no caso do ASA abaixo 9.8 |
|Cisco | ASA | RouteBased ASA (IKEv2 - sem BGP), no caso do ASA 9.8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Necessário: NarrowAzureTrafficSelectors (ativar a opção de UsePolicyBasedTrafficSelectors) e CustomAzurePolicies (IKE/IPsec)
>
