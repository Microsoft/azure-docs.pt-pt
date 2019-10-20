---
title: Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais
description: Este artigo fornece uma visão geral de Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599958"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption para máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais

A criptografia de disco do Azure pode ser aplicada a máquinas virtuais Linux e Windows, bem como a conjuntos de dimensionamento de máquinas virtuais. 

## <a name="linux-virtual-machines"></a>Máquinas Virtuais do Linux

Os artigos a seguir fornecem orientação para criptografar máquinas virtuais do Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual do Azure Disk Encryption

- [Visão geral de Azure Disk Encryption para máquinas virtuais do Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Cenários de Azure Disk Encryption em VMs Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criar e criptografar uma VM Linux com CLI do Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM Linux com Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Criar e criptografar uma VM do Linux com o portal do Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption scripts de exemplo](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Solução de problemas Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption perguntas frequentes](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption com o Azure AD (versão anterior)

- [Visão geral de Azure Disk Encryption com o Azure AD para máquinas virtuais do Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption com cenários do Azure AD em VMs do Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows

Os artigos a seguir fornecem orientação para criptografar máquinas virtuais do Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Versão atual do Azure Disk Encryption

- [Visão geral de Azure Disk Encryption para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Cenários de Azure Disk Encryption em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criar e criptografar uma VM do Windows com CLI do Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Windows com Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Criar e criptografar uma VM do Windows com o portal do Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption scripts de exemplo](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Solução de problemas Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption perguntas frequentes](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption com o Azure AD (versão anterior)

- [Visão geral do Azure Disk Encryption com o Azure AD para máquinas virtuais do Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption com cenários do Azure AD em VMs do Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais

Os artigos a seguir fornecem orientações para criptografar conjuntos de dimensionamento de máquinas virtuais.

- [Visão geral de Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o CLI do Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre de chaves para Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Usar Azure Disk Encryption com sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da encriptação do Azure](encryption-overview.md)
- [Encriptação de dados inativa](encryption-atrest.md)
- [Melhores práticas de segurança e encriptação de dados](data-encryption-best-practices.md)
