---
title: Verificação do sistema de ficheiros ao iniciar um VM Azure. Microsoft Docs
description: Saiba como resolver o problema que a VM mostra o sistema de ficheiros de verificação no arranque Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921492"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows mostra "sistema de ficheiros de verificação" ao iniciar um VM Azure

Este artigo descreve o erro do "Sistema de Ficheiros de Verificação" que poderá encontrar quando iniciar uma Máquina Virtual do Windows (VM) no Microsoft Azure.


## <a name="symptom"></a>Sintoma 

Um VM windows não começa. Quando verificar as imagens da bota nos [diagnósticos da Bota,](boot-diagnostics.md)verá que o processo Check Disk (chkdsk.exe) está a decorrer com uma das seguintes mensagens:

- Unidade de digitalização e reparação (C:)
- Verificação do sistema de ficheiros em C:

## <a name="cause"></a>Causa

Se for encontrado um erro NTFS no sistema de ficheiros, o Windows verificará e reparará a consistência do disco no próximo reinício. Normalmente isto acontece se o VM teve algum recomeço inesperado, ou se o processo de encerramento do VM foi interrompido abruptamente.

## <a name="solution"></a>Solução 

O Windows arrancará normalmente após o processo de Check Disk estar concluído. Se o VM estiver preso no processo check disk, tente executar o Disco de Verificação no VM offline:
1.  Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2.  [Fixe o disco OS a um VM](troubleshoot-recovery-disks-portal-windows.md)de recuperação .  
3.  No VM de recuperação, faça o Check Disk no disco OS anexado. Na amostra seguinte, a letra condutora do disco osso anexado é E: 
        
        chkdsk E: /f
4.  Depois de o Disco de Verificação estar concluído, retire o disco do VM de recuperação e, em seguida, volte a fixar o disco ao VM afetado como um disco OS. Para mais informações, consulte [Troubleshoot a Windows VM, ligando o disco OS a um VM](troubleshoot-recovery-disks-portal-windows.md)de recuperação .
