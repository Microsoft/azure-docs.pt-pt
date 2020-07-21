---
title: Criar VM a partir de instantâneo (Windows) - Amostra PowerShell
description: Amostra de script Azure PowerShell - Crie um VM a partir de um instantâneo
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7f8e7f5e758c916cf7e6b96ab38607ee722152b1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509560"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>Criar uma máquina virtual a partir de um instantâneo com PowerShell (Windows)

Este script cria uma máquina virtual a partir de um instantâneo de um disco do SO. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Limpar a implementação 

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para obter propriedades instantâneas, criar um disco gerido a partir de instantâneo e criar um VM. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtém uma foto com o nome instantâneo. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria uma configuração de disco. Esta configuração é usada com o processo de criação do disco. |
| [Novo AzDisco](/powershell/module/az.compute/new-azdisk) | Cria um disco gerido. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Anexa o disco gerido como disco DE à máquina virtual |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [Novo AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria uma interface de rede. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
