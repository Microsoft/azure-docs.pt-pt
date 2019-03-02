---
title: Exemplos da CLI do Azure | Microsoft Docs
description: Exemplos da CLI do Azure
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
ms.custom: mvc
ms.openlocfilehash: 4ff0d644ee6e5d5548f1efa5a4647c79d9b05089
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245595"
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Exemplos da CLI do Azure para máquinas virtuais do Linux

A tabela seguinte inclui ligações para scripts bash criados com a CLI do Azure.

| | |
|---|---|
|**Criar máquinas virtuais**||
| [Criar uma máquina virtual](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual Linux com configuração mínima. |
| [Criar uma máquina virtual totalmente configurada](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um grupo de recursos, uma máquina virtual e todos os recursos relacionados.|
| [Criar máquinas virtuais de elevada disponibilidade](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria várias máquinas virtuais numa elevada disponibilidade e a configuração de balanceamento de carga. |
| [Criar uma VM e execute o script de configuração](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script personalizado do Azure para instalar o NGINX. |
| [Criar uma VM com o WordPress instalado](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual e utiliza a extensão de Script personalizado do Azure para instalar o WordPress. |
| [Criar uma VM a partir de um disco de SO gerido](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual ao anexar um disco gerido existente como disco do SO. |
| [Criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual a partir de um instantâneo primeiro criar um disco gerido a partir de instantâneo e, em seguida, anexando-o novo disco gerido como disco do SO. |
|**Gerir o armazenamento**||
| [Criar disco gerido a partir de um VHD](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um VHD especializado, como um disco do SO ou a partir de um VHD, como um disco de dados.  |
| [Criar um disco gerido a partir de um instantâneo](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um disco gerido a partir de um instantâneo. |
| [Copiar o disco gerido para uma subscrição idêntica ou diferente](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cópias geridas disco para uma subscrição idêntica ou diferente, mas na mesma região que o elemento principal de disco gerido. 
| [Exportar um instantâneo como VHD para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta um instantâneo gerido como VHD para uma conta de armazenamento numa região diferente. |
| [Exportar o VHD de um disco gerido para uma conta de armazenamento](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporta o VHD subjacente de um disco gerido para uma conta de armazenamento numa região diferente. |
| [Copiar o instantâneo para uma subscrição idêntica ou diferente](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Instantâneo de cópias para uma subscrição idêntica ou diferente, mas na mesma região do instantâneo principal. |
|**Máquinas de virtuais de rede**||
| [Proteger o tráfego de rede entre máquinas virtuais](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria duas máquinas virtuais, todos os recursos relacionados e um grupos de segurança de rede interna e externa (NSG). |
|**Proteger máquinas virtuais**||
| [Encriptar uma VM e discos de dados](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria um Azure Key Vault, a chave de encriptação e o principal de serviço, em seguida, criptografa uma VM. |
|**Monitorizar máquinas virtuais**||
| [Monitorizar uma VM com o Log Analytics do Azure](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Cria uma máquina virtual, instala o agente do Log Analytics e inscreve a VM numa área de trabalho do Log Analytics.  |
|**Resolver problemas relacionados com máquinas virtuais**||
| [Resolver problemas de um disco de sistema operativo de VMs](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Monta o disco do sistema operativo a partir de uma VM como um disco de dados numa segunda VM. |
| | |
