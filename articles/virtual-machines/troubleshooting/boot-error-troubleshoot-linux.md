---
title: Erros de arranque de problemas em Máquinas Virtuais Azure Linux Microsoft Docs
description: Este artigo ajuda a ligá-lo a artigos para resolver erros de arranque em Máquinas Virtuais Azure Linux.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408745"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Problemas de sucação de Bluee Linux Erros de arranque de máquinas virtuais

Este artigo lista os erros comuns de arranque que poderá receber quando iniciar uma máquina virtual Linux (VM) no Microsoft Azure. Para obter mais informações sobre os erros, consulte os artigos na secção **de erros e soluções boot.**

## <a name="boot-errors-and-solutions"></a>Erros e soluções de arranque

* [Resgate GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Passos seguintes

- [Consola em série VM](serial-console-linux.md)

Troubleshoot a Linux VM ligando o disco OS a um VM de recuperação utilizando o Azure:

- [Reparação de VM Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Troca de Discos – isto pode ser automatizado usando qualquer um deles:
- [Scripts de recuperação da concha de potência](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash Scripts de Recuperação](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Portal Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Vídeo de troca de disco:

Se não tem acesso à GRUB veja [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja, como pode facilmente automatizar o procedimento de troca de discos para recuperar o seu VM

## <a name="unofficial-solution"></a>Solução não oficial

A recuperação de um VM também pode ser tentada com o script BETA não suportado [ALAR](https://github.com/malachma/azure-auto-recover)