---
title: Janelas de amostras Azure CLI
description: Janelas de amostras Azure CLI
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4868254ffc0448562ff6115a4e6bb030e4353ee4
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083314"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemplos da CLI do Azure para máquinas virtuais do Windows

A tabela seguinte inclui links para scripts de bash construídos usando o AZURE CLI que implementa máquinas virtuais windows.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual do Windows com uma configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um grupo de recursos, máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada em carga. |
| [Criar um VM e executar script de configuração](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Script Personalizado Azure para instalar o IIS. |
| [Criar uma configuração VM e executar dSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Configuração do Estado Pretendido (DSC) para instalar o IIS. |
|**Manage storage**||
| [Criar um disco gerido a partir de um VHD](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como um disco OS ou a partir de um VHD de dados como disco de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Copiar disco gerido para a mesma ou diferente subscrição](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | As cópias geriam o disco para uma subscrição igual ou diferente, mas na mesma região que o disco gerido pelos pais. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento em diferentes regiões. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento em diferentes regiões. |
| [Copiar instantâneo para a mesma ou diferente subscrição](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cópias snapshot para assinatura igual ou diferente, mas na mesma região que o instantâneo dos pais. |
|**Máquinas virtuais da rede**||
| [Proteger o tráfego de rede entre as máquinas virtuais](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados, e um grupo de segurança de rede interna e externa (NSG). |
|**Máquinas virtuais seguras**||
| [Encriptar uma VM e os discos de dados](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria um Cofre de Chave Azure, chave de encriptação e diretor de serviço, em seguida, encripta um VM. |
|**Monitorizar máquinas virtuais**||
| [Monitor a VM com Monitor Azure](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e inscreve o VM num espaço de trabalho de Log Analytics.  |
| | |
