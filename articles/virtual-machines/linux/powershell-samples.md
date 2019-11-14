---
title: Exemplos do PowerShell de máquina virtual do Azure
description: Exemplos do PowerShell de máquina virtual do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 903860dfd1e30f7941770efd759227a1349d41ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035254"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemplos do PowerShell de máquina virtual do Azure

A tabela a seguir inclui links para exemplos de scripts do PowerShell que criam e gerenciam máquinas virtuais do Linux.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar uma VM com o Docker habilitado](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, configura essa VM como um host do Docker e executa um contêiner NGINX. |
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de script personalizado do Azure para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de script personalizado do Azure para instalar o WordPress. |
| [Criar uma VM por meio de um disco do sistema operacional gerenciado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. |
| [Criar uma VM com base em um instantâneo](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual de um instantâneo, primeiro criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do sistema operacional. |
|**Gerir o armazenamento**||
| [Criar um disco gerenciado de um VHD no mesmo ou em uma assinatura diferente](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado de um VHD especializado como um disco do sistema operacional ou de um VHD de dados como um disco de dados, na mesma assinatura ou em outra.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Exportar um instantâneo como um VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerenciado como um VHD para uma conta de armazenamento em uma região diferente. |
| [Exportar o VHD de um disco gerenciado para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento em uma região diferente. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um instantâneo de um VHD e, em seguida, usa esse instantâneo para criar rapidamente vários discos gerenciados idênticos.  |
| [Copiar um instantâneo para a mesma assinatura ou para outra](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia o instantâneo para a mesma ou para uma assinatura diferente que esteja na mesma região que o instantâneo pai. |
|**Monitorizar máquinas virtuais**||
| [Monitorar uma VM com logs de Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente de Log Analytics e registra a VM em um espaço de trabalho Log Analytics.  |
| [Copiar um disco gerenciado para o mesmo ou para uma assinatura diferente](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia um disco gerenciado para o mesmo ou para uma assinatura diferente que esteja na mesma região que o disco gerenciado pai.
| [Coletar detalhes sobre todas as VMs em uma assinatura com o PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um CSV que contém o nome da VM, o nome do grupo de recursos, a região, a rede virtual, a sub-rede, o endereço IP privado, o tipo de so e o endereço IP público das VMs na assinatura fornecida.
| | |
