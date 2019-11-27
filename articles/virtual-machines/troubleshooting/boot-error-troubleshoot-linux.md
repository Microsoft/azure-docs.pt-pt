---
title: Solucionar problemas de erros de inicialização no Azure Máquinas Virtuais do Linux | Microsoft Docs
description: Este artigo ajuda a vincular os artigos para solucionar problemas de erros de inicialização no Azure Máquinas Virtuais do Linux.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408745"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Solucionar erros de inicialização do Azure Máquinas Virtuais do Linux

Este artigo lista os erros de inicialização comuns que você pode receber ao iniciar uma VM (máquina virtual) do Linux no Microsoft Azure. Para obter mais informações sobre os erros, consulte os artigos na seção **erros e soluções de inicialização** .

## <a name="boot-errors-and-solutions"></a>Erros e soluções de inicialização

* [Resgate de GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Passos Seguintes

- [Console serial da VM](serial-console-linux.md)

Solucione problemas de uma VM Linux anexando o disco do sistema operacional a uma VM de recuperação usando o Azure:

- [Reparo de VM do Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Troca de disco – isso pode ser automatizado usando:
- [Scripts de recuperação do Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Scripts de recuperação bash](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Portal do Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Vídeo de permuta de disco:

Se você não tiver acesso ao GRUB Assista a [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja como é possível automatizar facilmente o procedimento de permuta de disco para recuperar sua VM

## <a name="unofficial-solution"></a>Solução não oficial

A recuperação de uma VM também pode ser tentada com o script BETA sem suporte [alar](https://github.com/malachma/azure-auto-recover)