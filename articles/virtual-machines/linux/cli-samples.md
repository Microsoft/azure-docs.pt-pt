---
title: Exemplos da CLI do Azure | Microsoft Docs
description: Exemplos da CLI do Azure
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
ms.custom: mvc
ms.openlocfilehash: 57de195ad70930389e28885e1016d11fb870e6fb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092084"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Exemplos de CLI do Azure para máquinas virtuais do Linux

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual do Linux com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais altamente disponíveis](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Criar uma VM e executar o script de configuração](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de script personalizado do Azure para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e usa a extensão de script personalizado do Azure para instalar o WordPress. |
| [Criar uma VM por meio de um disco do sistema operacional gerenciado](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. |
| [Criar uma VM com base em um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual de um instantâneo, primeiro criando um disco gerenciado do instantâneo e, em seguida, anexando o novo disco gerenciado como disco do sistema operacional. |
|**Gerir o armazenamento**||
| [Criar um disco gerenciado com base em um VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado de um VHD especializado como um disco do sistema operacional ou de um VHD de dados como um disco de dados.  |
| [Criar um disco gerenciado com base em um instantâneo](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerenciado com base em um instantâneo. |
| [Copiar o disco gerenciado para a mesma assinatura ou outra](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia o disco gerenciado para uma assinatura igual ou diferente, mas na mesma região que o disco gerenciado pai. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerenciado como VHD para uma conta de armazenamento em uma região diferente. |
| [Exportar o VHD de um disco gerenciado para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento em uma região diferente. |
| [Copiar instantâneo para a mesma assinatura ou outra](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copia o instantâneo para uma assinatura igual ou diferente, mas na mesma região que o instantâneo pai. |
|**Máquinas virtuais de rede**||
| [Proteger o tráfego de rede entre máquinas virtuais](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e um NSG (grupos de segurança de rede) internos e externos. |
|**Proteger máquinas virtuais**||
| [Criptografar uma VM e discos de dados](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um Azure Key Vault, uma chave de criptografia e uma entidade de serviço e, em seguida, criptografa uma VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorar uma VM com logs de Azure Monitor](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente de Log Analytics e registra a VM em um espaço de trabalho Log Analytics.  |
|**Solucionar problemas de máquinas virtuais**||
| [Solucionar problemas de um disco do sistema operacional de VMs](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monta o disco do sistema operacional de uma VM como um disco de dados em uma segunda VM. |
| | |
