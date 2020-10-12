---
title: Exemplos do PowerShell
description: Lista de amostras PowerShell para VMs
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: b1a154e562db9f8fdd74300bb8eb3d6839cb2ef4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321927"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Amostras Azure VM PowerShell para criar e gerir VMs Linux

A tabela seguinte inclui links para amostras de scripts PowerShell que criam e gerem máquinas virtuais Linux.

| Script | Descrição |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, máquina virtual e todos os recursos relacionados.|
| [Criar uma VM com o Docker ativado](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, configura este VM como hospedeiro Docker e executa um recipiente NGINX. |
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão Azure Custom Script para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão Azure Custom Script para instalar o WordPress. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual anexando um disco gerido existente como disco OS. |
| [Criara uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo, criando primeiro um disco gerido a partir do instantâneo e, em seguida, anexando o novo disco gerido como disco OS. |
|**Manage storage**||
| [Criar um disco gerido a partir de um VHD numa subscrição igual ou diferente](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como disco DE, ou a partir de um VHD de dados como um disco de dados, na mesma ou numa subscrição diferente.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de uma imagem instantânea. |
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Criar um instantâneo a partir de um VHD](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um instantâneo a partir de um VHD e, em seguida, usa esse instantâneo para criar vários discos geridos idênticos rapidamente.  |
| [Copiar um instantâneo para uma subscrição igual ou diferente](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia instantâneo para o mesmo ou uma subscrição diferente que está na mesma região que o instantâneo dos pais. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com os registos do Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e inscreve o VM num espaço de trabalho log analytics.  |
| [Copiar um disco gerido para uma subscrição igual ou diferente](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia um disco gerido para o mesmo ou uma subscrição diferente que está na mesma região que o disco gerido pelo progenitor.
| [Recolher detalhes sobre todas as VMs numa subscrição com o PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um csv que contém o Nome VM, Nome do Grupo de Recursos, Região, Rede Virtual, Sub-rede, Endereço IP Privado, Tipo de OS e Endereço IP Público dos VMs na subscrição fornecida.
| | |
