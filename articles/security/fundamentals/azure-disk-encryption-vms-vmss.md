---
title: Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais
description: Saiba mais sobre a encriptação do disco Azure para máquinas virtuais (VMs) e conjuntos de escala VM. A encriptação do disco Azure funciona tanto para Os VMs Linux como para o Windows VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 21194bf2fe76a7eb0ee034d4a502c20ee3032dd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543678"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais

A encriptação do disco Azure pode ser aplicada tanto para máquinas virtuais Linux como para Windows, bem como para conjuntos de escala de máquinas virtuais. 

## <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

Os seguintes artigos fornecem orientação para encriptar máquinas virtuais Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual da Encriptação do Disco Azure

- [Descrição geral do Azure Disk Encryption para máquinas virtuais do Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Cenários do Azure Disk Encryption em VMs do Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criar e encriptar uma VM do Linux com a CLI do Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Linux com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Criar e encriptar uma VM do Linux com o portal do Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Esquema de extensão de encriptação de disco Azure para Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Scripts de exemplo do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Perguntas frequentes acerca do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação do disco Azure com Azure AD (versão anterior)

- [Visão geral da encriptação do disco Azure com Azure AD para máquinas virtuais Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Encriptação do disco Azure com cenários AD Azure em VMs Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows

Os seguintes artigos fornecem orientação para encriptar máquinas virtuais do Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual da Encriptação do Disco Azure

- [Descrição geral do Azure Disk Encryption para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Cenários do Azure Disk Encryption em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criar e encriptar uma VM do Windows com a CLI do Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Windows com o Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Criar e encriptar uma VM do Windows com o portal do Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Esquema de extensão de encriptação de disco Azure para windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Scripts de exemplo do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Perguntas frequentes acerca do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação do disco Azure com Azure AD (versão anterior)

- [Visão geral da encriptação do disco Azure com Azure AD para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Encriptação do disco Azure com cenários AD Azure em VMs windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Os seguintes artigos fornecem orientações para encriptar conjuntos de escala de máquina virtual.

- [Visão geral da encriptação do disco Azure para conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Criptografe conjuntos de escala de máquina virtual usando o Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Criptografe conjuntos de escala de máquina virtual usando Azure Powershell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Criptografe conjuntos de escala de máquina virtual usando o Gestor de Recursos Azure](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Use encriptação de disco Azure com sequenciação de extensão de conjunto de escala de máquina virtual](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da encriptação do Azure](encryption-overview.md)
- [Encriptação de dados inativos](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)
