---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85610574"
---
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou com um modelo de Gestor de Recursos. A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Em vez disso, desative a encriptação em todos os discos.

- **Desative a encriptação do disco com a Azure PowerShell:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** 

    1. Clique **em Implementar para Azure** a partir da encriptação do disco [desativar-se no modelo VM do Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Selecione a subscrição, grupo de recursos, localização, VM, tipo de volume, termos legais e acordo.
    3.  Clique em **Comprar** para desativar a encriptação do disco num VM do Windows em execução. 
