---
title: Exemplo do Script do PowerShell do Azure – Criar uma VM ao anexar um disco gerido como disco do SO | Microsoft Docs
description: Exemplo do Script do PowerShell do Azure – Criar uma VM ao anexar um disco gerido como disco do SO
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: b0f0a5ea7be482ffe1509f7a610a8075bf124356
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081239"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Criar uma máquina virtual através de um disco de SO gerido existente com o PowerShell

Este script cria uma máquina virtual ao anexar um disco gerido existente como disco do SO. Utilize este script nos cenários anteriores:
* Criar uma VM a partir de um disco de SO gerido existente copiado de um disco gerido numa subscrição diferente
* Criar uma VM a partir de um disco gerido existente criado de um ficheiro VHD especializado 
* Criar uma VM a partir de um disco de SO gerido existente criado de um instantâneo 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para obter as propriedades do disco gerido, anexar um disco gerido a uma nova VM e criar uma VM. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/Get-AzDisk) | Obtém o objeto de disco com base no nome e no grupo de recursos de um disco. A propriedade de ID do objeto do disco devolvido é utilizada para anexar o disco a uma VM nova |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Esta configuração inclui informações como o nome da VM, sistema operativo e credenciais administrativas. A configuração é utilizada durante a criação da VM. |
| [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) | Anexa um disco gerido com a propriedade de Id do disco como disco do SO para uma nova máquina virtual |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | Cria uma interface de rede. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

Para imagens do Marketplace, use [set-AzVMPlan](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) para definir as informações do plano.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
