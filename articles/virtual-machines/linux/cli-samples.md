---
title: Exemplos da CLI do Azure
description: Exemplos da CLI do Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 76803f8c3a703071eb733c5cfde65482ffd07f40
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970084"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Amostras Azure CLI para máquinas virtuais Linux

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual Linux com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Criar um VM e executar script de configuração](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Script Personalizado Azure para instalar o NGINX. |
| [Criar um VM com wordPress instalado](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão do Script Personalizado Azure para instalar o WordPress. |
| [Criar um VM a partir de um disco de Os gerido](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual fixando um disco gerido existente como disco OS. |
| [Criar um VM a partir de um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo, criando primeiro um disco gerido a partir de instantâneo e, em seguida, anexando o novo disco gerido como disco OS. |
|**Gerir o armazenamento**||
| [Criar disco gerido a partir de um VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como um disco OS ou a partir de um VHD de dados como disco de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Copiar disco gerido para a mesma ou diferente subscrição](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | As cópias geriam o disco para uma subscrição igual ou diferente, mas na mesma região que o disco gerido pelos pais. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento em diferentes regiões. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento em diferentes regiões. |
| [Copiar instantâneo para a mesma ou diferente subscrição](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cópias snapshot para assinatura igual ou diferente, mas na mesma região que o instantâneo dos pais. |
|**Máquinas virtuais da rede**||
| [Tráfego seguro de rede entre máquinas virtuais](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados, e um grupo de segurança de rede interna e externa (NSG). |
|**Máquinas virtuais seguras**||
| [Criptografe um VM e discos de dados](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um Cofre de Chave Azure, chave de encriptação e diretor de serviço, em seguida, encripta um VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorize um VM com registos do Monitor Azure](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e inscreve o VM num espaço de trabalho de Log Analytics.  |
|**Máquinas virtuais de resolução de problemas**||
| [Problemas de resolução de um disco do sistema operativo VMs](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monta o disco do sistema operativo a partir de um VM como um disco de dados num segundo VM. |
| | |
