---
title: Amostras de powershell da máquina virtual Azure
description: Amostras de powershell da máquina virtual Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321757"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Amostras Azure Virtual Machine PowerShell

A tabela seguinte fornece ligações a amostras de script PowerShell que criam e gerem máquinas virtuais do Windows (VMs).

| Script | Descrição |
|---|---|
|**Criar máquinas virtuais**||
| [Crie rapidamente uma máquina virtual](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual, e todos os recursos relacionados, com um mínimo de solicitações.|
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada em carga.|
| [Crie um VM e execute um script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão Azure Custom Script para instalar o IIS. |
| [Crie um VM e execute uma configuração DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Configuração do Estado Azure Desired (DSC) para instalar o IIS. |
| [Faça upload de um VHD e crie VMs](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Envia um ficheiro VHD local para Azure, cria uma imagem a partir do VHD e, em seguida, cria um VM a partir dessa imagem. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual anexando um disco gerido existente como disco OS. |
| [Criara uma VM a partir de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo, criando primeiro um disco gerido a partir do instantâneo e, em seguida, anexando o novo disco gerido como disco OS. |
|**Manage storage**||
| [Criar um disco gerido a partir de um VHD numa subscrição igual ou diferente](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como disco DE, ou a partir de um VHD de dados como um disco de dados, na mesma ou numa subscrição diferente.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de uma imagem instantânea. |
| [Copiar um disco gerido para uma subscrição igual ou diferente](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia um disco gerido para o mesmo ou uma subscrição diferente que está na mesma região que o disco gerido pelo progenitor.
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Criar um instantâneo a partir de um VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um instantâneo a partir de um VHD e, em seguida, usa esse instantâneo para criar vários discos geridos idênticos rapidamente.  |
| [Copiar um instantâneo para uma subscrição igual ou diferente](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia instantâneo para o mesmo ou uma subscrição diferente que está na mesma região que o instantâneo dos pais. |
|**Máquinas virtuais seguras**||
| [Encriptar uma VM e os respetivos discos de dados](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Cria um cofre de chave Azure, uma chave de encriptação e um principal de serviço, e depois encripta um VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorize um VM com monitor Azure](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente Azure Log Analytics e inscreve o VM num espaço de trabalho log analytics.  |
| [Recolher detalhes sobre todas as VMs numa subscrição com o PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um csv que contém o Nome VM, Nome do Grupo de Recursos, Região, Rede Virtual, Sub-rede, Endereço IP Privado, Tipo de OS e Endereço IP Público dos VMs na subscrição fornecida.
| | |
