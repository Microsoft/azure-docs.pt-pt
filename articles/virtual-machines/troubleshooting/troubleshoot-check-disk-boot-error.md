---
title: Verificação do sistema de ficheiros ao iniciar um VM Azure| Microsoft Docs
description: Saiba como resolver o problema que o VM mostra o sistema de ficheiros de verificação ao iniciar| Microsoft Docs
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
ms.openlocfilehash: 196f49a72932906e0a21b3c6c534c79d291a845f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632998"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows mostra "sistema de verificação de ficheiros" ao iniciar um VM Azure

Este artigo descreve o erro "Verificar o sistema de ficheiros" que poderá encontrar quando iniciar uma Máquina Virtual do Windows (VM) no Microsoft Azure.


## <a name="symptom"></a>Sintoma 

Um VM do Windows não começa. Quando verificar as imagens de arranque nos [diagnósticos boot,](boot-diagnostics.md)verá que o processo do Disco de Verificação (chkdsk.exe) está a funcionar com uma das seguintes mensagens:

- Unidade de digitalização e reparação (C:)
- Sistema de ficheiros de verificação em C:

## <a name="cause"></a>Causa

Se for encontrado um erro NTFS no sistema de ficheiros, o Windows verificará e reparará a consistência do disco no próximo reinício. Normalmente isto acontece se o VM teve algum reinício inesperado, ou se o processo de paragem de VM foi interrompido abruptamente.

## <a name="solution"></a>Solução 

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

O Windows iniciará normalmente depois de concluído o processo do Disco de Verificação. Se o VM estiver preso no processo do Disco de Verificação, tente executar o disco de verificação no VM offline:
1. Tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco DE A uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).  
3. Na recuperação VM, verifique o disco de verificação no disco oss anexado. Na amostra seguinte, a carta do condutor do disco oss anexado é E: 

    ```console
    chkdsk E: /f
    ```

4. Após a conclusão do Disco de Verificação, retire o disco do VM de recuperação e, em seguida, volte a ligar o disco ao VM afetado como disco de oss. Para obter mais informações, consulte [Troubleshoot a Windows VM, fixando o disco OS a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
