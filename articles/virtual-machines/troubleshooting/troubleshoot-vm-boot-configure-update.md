---
title: A startup VM está presa em "Preparar o Windows. Não desligue o computador" em Azure Microsoft Docs
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
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73749634"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A startup VM está presa em "Preparar o Windows. Não desligue o computador" em Azure

Este artigo descreve os ecrãs "Getting Ready" e "Getting Windows ready" que poderá encontrar quando iniciar uma máquina virtual do Windows (VM) no Microsoft Azure. Fornece passos para ajudá-lo a recolher dados para um bilhete de apoio.

 

## <a name="symptoms"></a>Sintomas

Um VM windows não arranca. Quando utilizar **os diagnósticos boot** para obter a imagem do VM, poderá ver que o VM exibe a mensagem "Preparar-se" ou "Preparar o Windows".

![Exemplo de mensagem para Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Normalmente este problema ocorre quando o servidor está a fazer o reboot final após a configuração ter sido alterada. A alteração de configuração pode ser inicializada pelas atualizações do Windows ou pelas alterações nas funções/funcionalidade do servidor. Para o Windows Update, se o tamanho das atualizações for grande, o sistema operativo necessita de mais tempo para reconfigurar as alterações.

## <a name="collect-an-os-memory-dump"></a>Recolher um depósito de memória de OS

Se o problema não for resolvido depois de esperar pelas alterações processando, terá de recolher um ficheiro de despejo de memória e suporte de contacto. Para recolher o ficheiro Dump, siga estes passos:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Fixe o disco OS a um VM de recuperação

1. Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
3. Ambiente de trabalho remoto para o VM de recuperação. 
4. Se o disco OS estiver encriptado, tem de desligar a encriptação antes de passar para o próximo passo. Para obter mais informações, consulte [Desencriptar o disco OS encriptado no VM que não pode arrancar](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. No VM de recuperação, vá para a pasta windows no disco OS anexado. Se a carta de condutor que é atribuída ao disco operativo em anexo for F, tem de ir para F:\Windows.
2. Localize o ficheiro memory.dmp [e,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) em seguida, envie um bilhete de apoio com o ficheiro de despejo. 

Se não encontrar o ficheiro de despejo, desloque o próximo passo para ativar o registo de despejo e a Consola em Série.

### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de despejo e a consola em série

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abrir a sessão de solicitação de comando elevado (Executar como administrador).
2. Execute o seguintes script:

    Neste guião, assumimos que a letra de unidade que é atribuída ao disco operativo em anexo é F.  Substitua-o pelo valor adequado no seu VM.

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

    1. Certifique-se de que há espaço suficiente no disco para alocar tanta memória como a RAM, que depende do tamanho que está a selecionar para este VM.
    2. Se não houver espaço suficiente ou se for uma série VM de grande tamanho (G, GS ou E), poderá então alterar a localização onde este ficheiro será criado e remeter isso para qualquer outro disco de dados que esteja ligado ao VM. Para isso, terá de alterar a seguinte tecla:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Retire o disco OS e, em seguida, religue o disco OS ao VM afetado](../windows/troubleshoot-recovery-disks-portal.md).
4. Inicie o VM e aceda à Consola em Série.
5. Selecione **Enviar Interrupção Não-Máscara (NMI)** para acionar o depósito de memória.
    ![a imagem sobre onde enviar Interrupção Não-Máscara](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Fixe o disco OS a um VM de recuperação novamente, colete ficheiro de despejo.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de recolher o ficheiro de despejo, contacte o suporte da [Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para analisar a causa principal.