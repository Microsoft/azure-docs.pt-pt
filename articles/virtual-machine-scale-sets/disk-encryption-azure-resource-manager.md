---
title: Crie e criptografe um conjunto de escala de máquina virtual com modelos de Gestor de Recursos Azure
description: Neste arranque rápido, aprende-se a usar modelos do Gestor de Recursos Azure para criar e encriptar um Conjunto de Escala de Máquina virtual
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c2b49a7e7e14bfbefcca64133ff23fdfabe53e7b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198430"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Criptografe conjuntos de escala de máquinavirtual com Gestor de Recursos Azure

Pode encriptar ou desencriptar conjuntos de escala de máquinas virtuais Do Linux utilizando modelos do Gestor de Recursos Azure.

## <a name="deploying-templates"></a>Modelos de implantação

Primeiro, selecione o modelo que se adequa ao seu cenário.

- [Ativar a encriptação do disco num conjunto de escala de máquina virtual Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Ativar a encriptação do disco num conjunto de escala virtual do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Implemente um conjunto de vMs de escala de máquina virtual de Linux com uma caixa de salto e permite encriptação em conjuntos de escala de máquinas virtuais Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Implemente um conjunto de vMs de escala virtual de máquinas com uma caixa de salto e permite encriptação em conjuntos de escala de máquinas virtuais do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Desative a encriptação do disco num conjunto de escala de máquina virtual Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Desative a encriptação do disco num conjunto de escala virtual do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Em seguida, siga estes passos:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Passos seguintes

- [Encriptação de disco azure para conjuntos de escala de máquina virtual](disk-encryption-overview.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe um conjunto de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
- [Crie e configure um cofre chave para encriptação de disco azure](disk-encryption-key-vault.md)
- [Utilize encriptação de disco azure com sequência de extensão de extensão de conjunto de máquina virtual](disk-encryption-extension-sequencing.md)
