---
title: Ativar encriptação do disco Azure para conjuntos de escala de máquinas virtuais
description: Este artigo fornece instruções para permitir a encriptação do disco Azure do Microsoft para conjuntos de escala de máquinas virtuais
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83195115"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Encriptação do disco Azure para conjuntos de escala de máquinas virtuais

A Azure Disk Encryption fornece encriptação de volume para o SISTEMA e discos de dados das suas máquinas virtuais, ajudando a proteger e salvaguardar os seus dados para cumprir compromissos de segurança organizacional e conformidade. Para saber mais, consulte [encriptação do disco Azure: Linux VMs](../virtual-machines/linux/disk-encryption-overview.md) e [Encriptação do Disco Azure: VMs windows](../virtual-machines/windows/disk-encryption-overview.md)  

A encriptação do disco Azure também pode ser aplicada em conjuntos de escala de máquina virtual Windows e Linux, nestes casos:
- Conjuntos de escala criados com discos geridos. A encriptação do disco Azure não é suportada para conjuntos de escala de disco nativos (ou não geridos).
- Volumes de SO e de dados em conjuntos de escala do Windows.
- Volumes de dados em conjuntos de escala de Linux. Atualmente, a encriptação do disco OS NÃO é suportada para conjuntos de escala Linux.

Pode aprender os fundamentos da Encriptação do Disco Azure para conjuntos de escala de máquinas virtuais em apenas alguns minutos com o [Criptografar uma balança de máquina virtual usando o Azure CLI](disk-encryption-cli.md) ou [o Criptografar uma escala de máquina virtual usando os tutoriais Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Passos seguintes

- [Criptografe conjuntos de escala de máquina virtual usando o Gestor de Recursos Azure](disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure](disk-encryption-key-vault.md)
- [Use encriptação de disco Azure com sequenciação de extensão de conjunto de escala de máquina virtual](disk-encryption-extension-sequencing.md)
