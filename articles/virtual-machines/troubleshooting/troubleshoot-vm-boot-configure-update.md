---
title: A startup VM está presa em "Getting Windows ready. Não desligue o computador" em Azure | Microsoft Docs
description: Introduza os passos para resolver o problema em que a startup VM está presa em "Getting Windows ready. Não desligue o computador."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: f3e4d51b4d41fa0dc23e9b12ac0251c14215de5c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633015"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A startup VM está presa em "Getting Windows ready. Não desligue o computador" em Azure

Este artigo descreve os ecrãs "Preparar-se" e "Preparar o Windows" que poderá encontrar quando iniciar uma máquina virtual Windows (VM) no Microsoft Azure. Fornece passos para ajudá-lo a recolher dados para um bilhete de apoio.

 

## <a name="symptoms"></a>Sintomas

Um VM windows não é iniciado. Quando utilizar **diagnósticos boot** para obter a imagem do VM, poderá ver que o VM apresenta a mensagem "Preparar-se" ou "Preparar o Windows".

![Exemplo de mensagem para Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Normalmente, este problema ocorre quando o servidor está a fazer o reboot final após a configuração ter sido alterada. A alteração de configuração pode ser inicializada por atualizações do Windows ou pelas alterações nas funções/funcionalidade do servidor. Para o Windows Update, se o tamanho das atualizações fosse grande, o sistema operativo necessita de mais tempo para reconfigurar as alterações.

## <a name="collect-an-os-memory-dump"></a>Recolha um depósito de memória de SO

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Se o problema não se resolver depois de esperar pelas alterações ao processo, terá de recolher um ficheiro de despejo de memória e suporte de contacto. Para recolher o ficheiro de despejo, siga estes passos:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Ligue o disco de SO a um VM de recuperação

1. Tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
3. Ambiente de trabalho remoto para o VM de recuperação. 
4. Se o disco DE estiver encriptado, deve desligar a encriptação antes de passar para o passo seguinte. Para obter mais informações, consulte [desencriptar o disco de OS encriptado no VM que não pode arrancar](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. No VM de recuperação, vá à pasta do windows no disco oss anexado. Se a carta do controlador que é atribuída ao disco de oss anexado for F, tem de ir para F:\Windows.
2. Localize o ficheiro .dmp memória e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo. 

Se não conseguir encontrar o ficheiro de despejo, mova o próximo passo para ativar o registo de despejo e a Consola em Série.

### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de despejo e a consola em série

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abrir sessão de pedido de comando elevado (Executar como administrador).
2. Execute o seguintes script:

    Neste script, assumimos que a letra de unidade que é atribuída ao disco de SO anexado é F.  Substitua-o pelo valor adequado no seu VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Certifique-se de que há espaço suficiente no disco para alocar tanta memória como a RAM, o que depende do tamanho que está a selecionar para este VM.
    2. Se não houver espaço suficiente ou se este for um VM de grande tamanho (série G, GS ou E), poderá então alterar a localização onde este ficheiro será criado e encaminhá-lo para qualquer outro disco de dados que esteja ligado ao VM. Para isso, terá de alterar a seguinte tecla:
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [Retire o disco de SO e, em seguida, re-prenda o disco de SO ao VM afetado](./troubleshoot-recovery-disks-portal-windows.md).
4. Inicie o VM e aceda à Consola em Série.
5. Selecione **Enviar Interrupção Não Mascarada (NMI)** para ativar o despejo de memória.
    ![a imagem sobre onde enviar Interrupção Não-Mascarada](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Fixe o disco DE A uma VM de recuperação novamente, colete o ficheiro de despejo.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de recolher o ficheiro de despejo, contacte o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para analisar a causa raiz.
