---
title: Criar e criptografar um conjunto de dimensionamento de máquinas virtuais com modelos de Azure Resource Manager
description: Neste guia de início rápido, você aprende a usar os modelos de Azure Resource Manager para criar e criptografar um conjunto de dimensionamento de máquinas virtuais
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530001"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Criptografar conjuntos de dimensionamento de máquinas virtuais com Azure Resource Manager

Você pode criptografar ou descriptografar conjuntos de dimensionamento de máquinas virtuais do Linux usando modelos de Azure Resource Manager.

## <a name="deploying-templates"></a>Implantando modelos

Primeiro, selecione o modelo que se adapta ao seu cenário.

- [Habilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Habilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Implantar um conjunto de dimensionamento de máquinas virtuais de VMs do Linux com um Jumpbox e habilita a criptografia em conjuntos de dimensionamento de máquinas virtuais do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Implantar um conjunto de dimensionamento de máquinas virtuais de VMs do Windows com um Jumpbox e habilita a criptografia em conjuntos de dimensionamento de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Desabilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Desabilitar a criptografia de disco em um conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Em seguida, siga estas etapas:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Passos seguintes

- [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md)
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o CLI do Azure](disk-encryption-cli.md)
- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
- [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)
- [Usar Azure Disk Encryption com sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais](disk-encryption-extension-sequencing.md)