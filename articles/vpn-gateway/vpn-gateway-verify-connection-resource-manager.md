---
title: 'Gateway de VPN do Azure: verificar uma conexão de gateway'
description: Este artigo mostra como verificar uma conexão de gateway de VPN de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780152"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verificar uma conexão de gateway de VPN

Este artigo mostra como verificar uma conexão de gateway de VPN para os modelos de implantação clássico e do Resource Manager.

## <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Para verificar uma conexão de gateway de VPN para o modelo de implantação do Gerenciador de recursos usando o PowerShell, instale a versão mais recente dos [cmdlets Azure Resource Manager PowerShell](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>CLI do Azure

Para verificar uma conexão de gateway de VPN para o modelo de implantação do Gerenciador de recursos usando CLI do Azure, instale a versão mais recente dos [comandos da CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2,0 ou posterior).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portal do Azure (clássico)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (clássico)

Para verificar sua conexão de gateway de VPN para o modelo de implantação clássico usando o PowerShell, instale as versões mais recentes dos cmdlets Azure PowerShell. Certifique-se de baixar e instalar o módulo de [Gerenciamento de serviços](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) . Use ' Add-AzureAccount ' para fazer logon no modelo de implantação clássico.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
