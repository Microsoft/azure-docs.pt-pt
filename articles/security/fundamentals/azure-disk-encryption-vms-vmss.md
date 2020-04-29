---
title: Encriptação de disco azure para máquinas virtuais e conjuntos de escala de máquinas virtuais
description: Este artigo fornece uma visão geral da Encriptação do Disco Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062114"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Encriptação de disco azure para máquinas virtuais e conjuntos de escala de máquinas virtuais

A encriptação azure Disk pode ser aplicada tanto a máquinas virtuais Linux como Windows, bem como a conjuntos de escala de máquinas virtuais. 

## <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

Os seguintes artigos fornecem orientação para encriptar máquinas virtuais Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual da Encriptação do Disco Azure

- [Descrição geral do Azure Disk Encryption para máquinas virtuais do Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Cenários do Azure Disk Encryption em VMs do Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criar e encriptar uma VM do Linux com a CLI do Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Linux com o Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Criar e encriptar uma VM do Linux com o portal do Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Extensão de encriptação do disco azure Schema para Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Scripts de exemplo do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Perguntas frequentes acerca do Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação de disco azure com Azure AD (versão anterior)

- [Visão geral da encriptação do disco azure com Anúncio Azure para máquinas virtuais Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Encriptação de disco azure com cenários de AD Azure em VMs Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows

Os seguintes artigos fornecem orientações para encriptar máquinas virtuais do Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual da Encriptação do Disco Azure

- [Descrição geral do Azure Disk Encryption para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Cenários do Azure Disk Encryption em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criar e encriptar uma VM do Windows com a CLI do Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Criar e encriptar uma VM do Windows com o Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Criar e encriptar uma VM do Windows com o portal do Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Extensão de encriptação do disco azure Schema para Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Scripts de exemplo do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Perguntas frequentes acerca do Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Encriptação de disco azure com Azure AD (versão anterior)

- [Visão geral da encriptação do disco azure com Anúncio Azure para máquinas virtuais Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Encriptação de disco azure com cenários de AD Azure em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Os seguintes artigos fornecem orientações para encriptar conjuntos de escala de máquinas virtuais.

- [Visão geral da encriptação do disco azure para conjuntos de escala de máquina virtual](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Criptografe um conjunto de escala de máquina virtual usando o Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Criptografe um conjunto](../../virtual-machine-scale-sets/disk-encryption-powershell.md)de escala de máquina virtual utilizando o Azure Powershell .
- [Criptografe um conjunto de escala de máquina virtual usando o Gestor de Recursos Azure](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Crie e configure um cofre chave para encriptação de disco azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Utilize encriptação de disco azure com sequência de extensão de extensão de conjunto de máquina virtual](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação azure](encryption-overview.md)
- [Encriptação de dados inativa](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)
