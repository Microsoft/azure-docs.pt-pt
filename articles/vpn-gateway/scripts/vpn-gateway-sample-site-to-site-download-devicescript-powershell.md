---
title: Exemplo de script do Azure PowerShell - Transferir modelo de configuração do dispositivo | Microsoft Docs
description: Este script PowerShell descarrega o modelo de dispositivo VPN para uma ligação em Azure. Saiba mais sobre todos os cmdlets do guião.
services: vpn-gateway
documentationcenter: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: yushwang
ms.openlocfilehash: 77e5291010767368600ef3032306867556181373
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926234"
---
# <a name="download-vpn-device-template-using-powershell"></a>Transferir o modelo do dispositivo VPN com o PowerShell

Este script transfere o modelo de dispositivo VPN para uma ligação

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos criados, utilize o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para eliminar o grupo de recursos. Isto elimina o grupo de recursos e todos os recursos contidos no mesmo.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/az.network/Get-azVirtualNetworkGatewaySupportedVpnDevice) | Lista todos os modelos e versões de dispositivo de VPN disponíveis. |
| [Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/az.network/Get-azVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | Transfere o modelo de configuração da ligação. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/).
