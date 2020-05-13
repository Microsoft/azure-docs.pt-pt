---
title: Ativar encriptação de disco azure para conjuntos de escala de máquina virtual
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para conjuntos de escala de máquina virtual
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195115"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Encriptação de disco azure para conjuntos de escala de máquina virtual

A Encriptação do Disco Azure fornece encriptação de volume para o OS e discos de dados das suas máquinas virtuais, ajudando a proteger e salvaguardar os seus dados para cumprir os compromissos de segurança organizacional e conformidade. Para saber mais, consulte [encriptação do disco Azure: VMs Linux](../virtual-machines/linux/disk-encryption-overview.md) e Encriptação de [Disco Azure: VMs do Windows](../virtual-machines/windows/disk-encryption-overview.md)  

A encriptação do disco azure também pode ser aplicada a conjuntos de escala de máquinas virtuais Windows e Linux, nestes casos:
- Conjuntos de escala criados com discos geridos. A encriptação do Disco Azure não é suportada para conjuntos de escala de disco nativo (ou não geridos).
- OS e volumes de dados em conjuntos de escala windows.
- Volumes de dados em conjuntos de escala linux. A encriptação do disco OS NÃO é suportada atualmente para conjuntos de escala Linux.

Pode aprender os fundamentos da Encriptação de Disco Azure para conjuntos de escala de máquinas virtuais em apenas alguns minutos com o Encrypt um conjunto de escala de [máquina virtual utilizando o Azure CLI](disk-encryption-cli.md) ou o Conjunto de escala de máquina virtual Encrypt a utilizando os tutoriais [Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Passos seguintes

- [Criptografe um conjunto de escala de máquina virtual usando o Gestor de Recursos Azure](disk-encryption-azure-resource-manager.md)
- [Crie e configure um cofre chave para encriptação de disco azure](disk-encryption-key-vault.md)
- [Utilize encriptação de disco azure com sequência de extensão de extensão de conjunto de máquina virtual](disk-encryption-extension-sequencing.md)
