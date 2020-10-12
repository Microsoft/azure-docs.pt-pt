---
title: Exemplos da CLI do Azure
description: Amostras de CLI para máquinas virtuais Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63c420a7ae082cbf38b529e71d8324550a84b7bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89319173"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Exemplos da CLI do Azure para máquinas virtuais do Linux

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| Script | Descrição |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual Linux com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria várias máquinas virtuais numa configuração altamente disponível e equilibrada de carga. |
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão Azure Custom Script para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão Azure Custom Script para instalar o WordPress. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual anexando um disco gerido existente como disco OS. |
| [Criara uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo, criando primeiro um disco gerido a partir de instantâneo e, em seguida, anexando o novo disco gerido como disco OS. |
|**Manage storage**||
| [Criar um disco gerido a partir de um VHD](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado como disco de SO ou a partir de um VHD de dados como disco de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de uma imagem instantânea. |
| [Copiar discos geridos para uma subscrição igual ou diferente](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | As cópias conseguiram o disco para a mesma subscrição ou subscrição diferente, mas na mesma região que o disco gerido pelos pais. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento em diferentes regiões. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento em diferentes regiões. |
| [Copiar instantâneo para uma subscrição igual ou diferente](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia instantâneo para a mesma subscrição ou subscrição diferente, mas na mesma região que o instantâneo dos pais. |
|**Máquinas virtuais da rede**||
| [Proteger o tráfego de rede entre as máquinas virtuais](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados, e um grupo interno e externo de segurança de rede (NSG). |
|**Máquinas virtuais seguras**||
| [Encriptar uma VM e os discos de dados](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um Cofre de Chave Azure, chave de encriptação e principal de serviço, em seguida, encripta um VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com os registos do Azure Monitor](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente Log Analytics e inscreve o VM num espaço de trabalho log Analytics.  |
|**Máquinas virtuais de resolução de problemas**||
| [Resolver problemas de um disco do sistema operativo de VMs](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monta o disco do sistema operativo a partir de um VM como um disco de dados num segundo VM. |
| | |
