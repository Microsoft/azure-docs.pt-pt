---
title: Amostras de PowerShell de máquina virtual azure
description: Amostras de PowerShell de máquina virtual azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 03d27e1e72eaa5f5dee8cfa9062a56c6dd45007a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82100027"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Amostras de PowerShell de máquina virtual Azure

A tabela seguinte fornece links para amostras de script PowerShell que criam e gerem máquinas virtuais do Windows (VMs).

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Crie rapidamente uma máquina virtual](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados, com um mínimo de solicitações.|
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada em carga.|
| [Crie um VM e execute um script de configuração](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Script Personalizado Azure para instalar o IIS. |
| [Criar um VM e executar uma configuração DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Configuração do Estado Pretendido (DSC) para instalar o IIS. |
| [Faça upload de um VHD e crie VMs](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Envia um ficheiro VHD local para O Azure, cria uma imagem a partir do VHD e, em seguida, cria um VM a partir dessa imagem. |
| [Criar um VM a partir de um disco de Os gerido](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual fixando um disco gerido existente como disco OS. |
| [Criar um VM a partir de um instantâneo](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo, criando primeiro um disco gerido a partir do instantâneo e, em seguida, anexando o novo disco gerido como disco OS. |
|**Manage storage**||
| [Criar um disco gerido a partir de um VHD na mesma ou numa subscrição diferente](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como um disco OS, ou a partir de um VHD de dados como um disco de dados, na mesma ou numa subscrição diferente.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Copiar um disco gerido para o mesmo ou uma subscrição diferente](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia um disco gerido para o mesmo ou uma subscrição diferente que está na mesma região que o disco gerido pelo progenitor.
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Criar um instantâneo a partir de um VHD](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um instantâneo a partir de um VHD e depois usa esse instantâneo para criar vários discos geridos idênticos rapidamente.  |
| [Copiar um instantâneo para a mesma ou uma subscrição diferente](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copia instantâneo para a mesma ou uma subscrição diferente que está na mesma região que o instantâneo dos pais. |
|**Máquinas virtuais seguras**||
| [Encriptar uma VM e os respetivos discos de dados](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Cria um cofre de chaves Azure, uma chave de encriptação e um diretor de serviço, e depois encripta um VM. |
|**Monitorizar máquinas virtuais**||
| [Monitor a VM com Monitor Azure](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente Azure Log Analytics e inscreve o VM num espaço de trabalho de Log Analytics.  |
| [Colete detalhes sobre todos os VMs numa subscrição com a PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um cSV que contém o nome VM, nome do grupo de recursos, região, rede virtual, subnet, endereço IP privado, tipo OS e endereço IP público dos VMs na subscrição fornecida.
| | |
