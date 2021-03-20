---
title: Crie e criptografe um conjunto de escala de máquina virtual com modelos de gestor de recursos Azure
description: Neste arranque rápido, você aprende a usar modelos de Gestor de Recursos Azure para criar e encriptar um Conjunto de Escala de Máquina Virtual
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129760"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Criptografe conjuntos de escala de máquina virtual com O Gestor de Recursos Azure

Pode encriptar ou desencriptar conjuntos de escala de máquina virtual Linux utilizando modelos do Gestor de Recursos Azure.

## <a name="deploying-templates"></a>Modelos de implantação

Primeiro, selecione o modelo que se adequa ao seu cenário.

- [Ativar a encriptação do disco num conjunto de escala de máquina virtual linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Ativar a encriptação do disco num conjunto de escala de máquina virtual do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Implemente um conjunto de escala de máquina virtual de Linux VMs com uma caixa de salto e permita encriptação em conjuntos de escala de máquina virtual Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Implemente um conjunto de escala de máquina virtual de VMs do Windows com uma caixa de salto e permita encriptação em conjuntos de escala de máquina virtual do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Desativar a encriptação do disco num conjunto de escala de máquina virtual linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Desativar a encriptação do disco num conjunto de escala de máquina virtual do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Em seguida, siga estes passos:

1. Clique em **Implementar no Azure**.
2. Preencha os campos necessários e, em seguida, concorde com os termos e condições.
3. Clique **em Comprar** para implementar o modelo.

## <a name="next-steps"></a>Passos seguintes

- [Encriptação do disco Azure para conjuntos de escala de máquinas virtuais](disk-encryption-overview.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md)
- [Criptografe conjuntos de escala de máquina virtual usando o Azure PowerShell](disk-encryption-powershell.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure](disk-encryption-key-vault.md)
- [Use encriptação de disco Azure com sequenciação de extensão de conjunto de escala de máquina virtual](disk-encryption-extension-sequencing.md)
