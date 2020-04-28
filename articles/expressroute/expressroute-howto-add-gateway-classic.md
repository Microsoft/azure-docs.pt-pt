---
title: 'Azure ExpressRoute: Adicione uma porta de entrada para um VNet: clássico'
description: Configure um gateway VNet para um modelo de implementação clássico VNet usando powerShell para uma configuração ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: d7927af5b831dbe3294e1abc3a878e323bf1867e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74928054"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Configure um portal de rede virtual para ExpressRoute usando powerShell (clássico)
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo - Portal Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artigo irá acompanhá-lo através dos passos para adicionar, redimensionar e remover uma porta de entrada de rede virtual (VNet) para um VNet pré-existente. Os passos para esta configuração são especificamente para VNets que foram criados usando o modelo de **implementação clássico** e que serão usados numa configuração ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Acerca dos modelos de implementação do Azure**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Antes de começar
Verifique se instalou os cmdlets Azure PowerShell necessários para esta configuração.

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o portal VNet, pode ligar o seu VNet a um circuito ExpressRoute. Consulte [a Ligação de uma Rede Virtual a um circuito ExpressRoute](expressroute-howto-linkvnet-classic.md).

