---
title: Exemplos da CLI do Azure Windows | Documentos da Microsoft
description: Exemplos da CLI do Azure Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: abc38d315bc879a06ecd9a9bf7188c15533c018a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535013"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Máquinas de virtuais de amostras de CLI para Windows do Azure

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure que implementar as máquinas virtuais do Windows.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual do Windows com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais de elevada disponibilidade](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Criar uma VM e execute o script de configuração](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script personalizado do Azure para instalar o IIS. |
| [Criar uma VM e execute a configuração de DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Azure Desired State Configuration (DSC) para instalar o IIS. |
|**Gerir o armazenamento**||
| [Criar disco gerido a partir de um VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado, como um disco do SO ou a partir de um VHD, como um disco de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Copiar o disco gerido para uma subscrição idêntica ou diferente](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cópias geridas disco para uma subscrição idêntica ou diferente, mas na mesma região que o elemento principal de disco gerido. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Copiar o instantâneo para uma subscrição idêntica ou diferente](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Instantâneo de cópias para uma subscrição idêntica ou diferente, mas na mesma região do instantâneo principal. |
|**Máquinas de virtuais de rede**||
| [Proteger o tráfego de rede entre máquinas virtuais](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e um grupos de segurança de rede interna e externa (NSG). |
|**Proteger máquinas virtuais**||
| [Encriptar uma VM e discos de dados](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um Azure Key Vault, a chave de encriptação e o principal de serviço, em seguida, criptografa uma VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com o Azure Monitor](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente do Log Analytics e inscreve a VM numa área de trabalho do Log Analytics.  |
| | |
