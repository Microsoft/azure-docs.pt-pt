---
title: A inicialização da VM está presa em "preparando o Windows. Não desligar o computador "no Azure | Microsoft Docs
description: Apresente as etapas para solucionar o problema em que a inicialização da VM está presa em "preparando o Windows. Não desligue o computador."
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
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749634"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A inicialização da VM está presa em "preparando o Windows. Não desligar o computador "no Azure

Este artigo descreve as telas "preparando-se" e "preparando o Windows" que você pode encontrar ao inicializar uma VM (máquina virtual) do Windows no Microsoft Azure. Ele fornece etapas para ajudá-lo a coletar dados para um tíquete de suporte.

 

## <a name="symptoms"></a>Sintomas

Uma VM do Windows não é inicializada. Ao usar o **diagnóstico de inicialização** para obter a captura de tela da VM, você pode ver que a VM exibe a mensagem "preparando-se" ou "preparando o Windows".

![Exemplo de mensagem para o Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Normalmente, esse problema ocorre quando o servidor está fazendo a reinicialização final após a alteração da configuração. A alteração de configuração pode ser inicializada por atualizações do Windows ou pelas alterações nas funções/recurso do servidor. Por Windows Update, se o tamanho das atualizações for grande, o sistema operacional precisará de mais tempo para reconfigurar as alterações.

## <a name="collect-an-os-memory-dump"></a>Coletar um despejo de memória do so

Se o problema não for resolvido depois de aguardar as alterações serem processadas, você precisará coletar um arquivo de despejo de memória e entrar em contato com o suporte. Para coletar o arquivo de despejo, siga estas etapas:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do sistema operacional a uma VM de recuperação

1. Tire um instantâneo do disco do sistema operacional da VM afetada como um backup. Para obter mais informações, consulte [snapshot a Disk](../windows/snapshot-copy-managed-disk.md).
2. [Anexe o disco do sistema operacional a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
3. Área de trabalho remota para a VM de recuperação. 
4. Se o disco do sistema operacional for criptografado, você deverá desligar a criptografia antes de passar para a próxima etapa. Para obter mais informações, consulte [descriptografar o disco do sistema operacional criptografado na VM que não pode ser inicializada](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localizar arquivo de despejo e enviar um tíquete de suporte

1. Na VM de recuperação, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for F, você precisará ir para F:\Windows.
2. Localize o arquivo Memory. dmp e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo. 

Se você não encontrar o arquivo de despejo, mova a próxima etapa para habilitar o log de despejo e o console serial.

### <a name="enable-dump-log-and-serial-console"></a>Habilitar o log de despejo e o console serial

Para habilitar o log de despejo e o console serial, execute o script a seguir.

1. Abra a sessão de prompt de comando com privilégios elevados (executar como administrador).
2. Execute o seguintes script:

    Nesse script, presumimos que a letra da unidade atribuída ao disco do sistema operacional anexado é F.  Substitua-o pelo valor apropriado em sua VM.

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

    1. Verifique se há espaço suficiente no disco para alocar a quantidade de memória como RAM, o que depende do tamanho que você está selecionando para essa VM.
    2. Se não houver espaço suficiente ou se esta for uma VM de tamanho grande (série G, GS ou E), você poderá alterar o local onde esse arquivo será criado e referir-se a qualquer outro disco de dados que esteja anexado à VM. Para fazer isso, será necessário alterar a seguinte chave:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Desanexe o disco do sistema operacional e, em seguida, anexe novamente o disco do sistema operacional à VM afetada](../windows/troubleshoot-recovery-disks-portal.md).
4. Inicie a VM e acesse o console serial.
5. Selecione **Enviar NMI (interrupção não mascarada)** para disparar o despejo de memória.
    ![a imagem sobre onde enviar uma interrupção não mascarável](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Anexe o disco do sistema operacional a uma VM de recuperação novamente, colete o arquivo de despejo.

## <a name="contact-microsoft-support"></a>Contate o suporte da Microsoft

Depois de coletar o arquivo de despejo, entre em contato com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para analisar a causa raiz.