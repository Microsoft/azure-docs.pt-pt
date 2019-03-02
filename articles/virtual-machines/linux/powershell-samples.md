---
title: Exemplos do PowerShell de Máquina Virtual do Azure | Documentos da Microsoft
description: Exemplos do PowerShell de Máquina Virtual do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: e094a95fef5e010471dde3e60f2fc9aaaaad6344
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246383"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemplos do PowerShell da Máquina Virtual do Azure

A tabela seguinte inclui ligações para exemplos de scripts do PowerShell que criar e gerir máquinas virtuais do Linux.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar uma VM com o Docker ativado](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, configura esta VM como um anfitrião do Docker e execute um contentor NGINX. |
| [Criar uma VM e execute o script de configuração](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script personalizado do Azure para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script personalizado do Azure para instalar o WordPress. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual ao anexar um disco gerido existente como disco do SO. |
| [Criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo primeiro criar um disco gerido a partir do instantâneo e, em seguida, anexando-o novo disco gerido como disco do SO. |
|**Gerir o armazenamento**||
| [Criar um disco gerido a partir de um VHD no mesmo ou uma subscrição diferente](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como um disco de SO ou a partir de um VHD como um disco de dados, da mesma de dados ou numa subscrição diferente.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Exportar um instantâneo como um VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerido como um VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Criar um instantâneo de um VHD](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um instantâneo a partir de um VHD e, em seguida, utiliza esse instantâneo para criar rapidamente vários discos geridos idênticos.  |
| [Copiar um instantâneo para o mesmo ou numa subscrição diferente](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cópias de instantâneos para o mesmo ou numa subscrição diferente que está na mesma região do instantâneo principal. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com o Log Analytics do Azure](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente do Log Analytics e inscreve a VM numa área de trabalho do Log Analytics.  |
| [Copiar um disco gerido para o mesmo ou numa subscrição diferente](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia um disco gerido para o mesmo ou numa subscrição diferente que esteja na mesma região que o disco gerido principal.
| | |
