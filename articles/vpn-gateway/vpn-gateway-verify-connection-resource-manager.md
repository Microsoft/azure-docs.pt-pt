---
title: 'Gateway Azure VPN: Verifique uma ligação de gateway'
description: Este artigo mostra-lhe como verificar uma ligação virtual VPN Gateway de rede virtual.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 93a7c0bb15c17b6939d6d53018e4fed84b1ae020
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984570"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verifique uma ligação VPN Gateway

Este artigo mostra-lhe como verificar uma ligação de gateway VPN para os modelos clássicos e gestores de recursos.

## <a name="azure-portal"></a>Portal do Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Para verificar uma ligação de gateway VPN para o modelo de implementação do Gestor de Recursos utilizando o PowerShell, instale a versão mais recente dos [cmdlets PowerShell do Gestor de Recursos Azure](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>CLI do Azure

Para verificar uma ligação de gateway VPN para o modelo de implementação do Gestor de Recursos utilizando o Azure CLI, instale a versão mais recente dos [comandos CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 ou mais tarde).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portal do Azure (clássico)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (clássico)

Para verificar a sua ligação de gateway VPN para o modelo de implementação clássico utilizando o PowerShell, instale as versões mais recentes dos cmdlets Azure PowerShell. Certifique-se de que descarrega e instala o módulo [de Gestão de Serviços.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Utilize o 'Add-AzureAccount' para iniciar sessão no modelo clássico de implementação.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Próximos passos

* Pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter os passos.
