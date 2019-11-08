---
title: Exemplo de Script do Azure PowerShell – Encriptar uma VM do Windows | Microsoft Docs
description: Exemplo de Script do Azure PowerShell – Encriptar uma VM do Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: b97292fb0a832f6eec1096d2274556d162e67ca7
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748644"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Encriptar uma máquina virtual do Windows com o Azure PowerShell

Este script cria um Azure Key Vault seguro, as chaves de encriptação, o principal do serviço Azure Active Directory e uma máquina virtual (VM) do Windows. A VM é encriptada com a chave de encriptação a partir do Key Vault e das credenciais do principal do serviço.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Limpar a implementação

Execute o seguinte comando para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos para criar a implementação. Cada item na tabela liga a documentação específica do comando.

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | Cria um Azure Key Vault para armazenar dados seguros, tais como chaves de encriptação. |
| [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Cria uma chave de encriptação no Key Vault. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | Cria um principal do serviço Azure Active Directory para autenticar e controlar o acesso às chaves de encriptação de forma segura. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Define as permissões no Key Vault para conceder ao principal do serviço acesso às chaves de encriptação. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Cria a máquina virtual e liga-a à placa de rede, à rede virtual, à sub-rede e ao grupo de segurança de rede. Este comando também abre a porta 80 e define as credenciais administrativas. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Obtém as informações necessárias no Key Vault |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | Ativa a encriptação numa VM com as credenciais do principal do serviço e a chave de encriptação. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Mostra o estado do processo de encriptação da VM. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos no grupo. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o módulo do Azure PowerShell, veja [Documentação do Azure PowerShell](/powershell/azure/overview).

Pode ver exemplos adicionais de scripts do PowerShell da máquina virtual na [Documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
