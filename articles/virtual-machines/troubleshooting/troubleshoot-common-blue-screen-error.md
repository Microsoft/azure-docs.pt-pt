---
title: Erros no ecrã azul ao arrancar um VM Azure. Microsoft Docs
description: Saiba como resolver o problema que o erro do ecrã azul é recebido no arranque. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266939"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows mostra erro de ecrã azul ao iniciar um VM Azure
Este artigo descreve erros de ecrã azul que poderá encontrar quando iniciar uma Máquina Virtual do Windows (VM) no Microsoft Azure. Fornece passos para ajudá-lo a recolher dados para um bilhete de apoio. 


## <a name="symptom"></a>Sintoma 

Um VM windows não começa. Quando verificar as imagens da bota nos [diagnósticos da Bota,](./boot-diagnostics.md)vê uma das seguintes mensagens de erro num ecrã azul:

- o nosso PC deparou-se com um problema e precisa de reiniciar. Estamos apenas a recolher informações de erros, e depois podes recomeçar.
- O seu pc teve um problema e precisa de reiniciar.

Esta secção lista as mensagens de erro comuns que pode encontrar na gestão de VMs:

## <a name="cause"></a>Causa

Pode haver várias razões para ter um erro de paragem. As causas mais comuns são:

- Problema com um motorista
- Ficheiro ou memória do sistema corrompido
- Uma aplicação acede a um setor proibido da memória

## <a name="collect-memory-dump-file"></a>Recolher ficheiro de despejo de memória

Para resolver este problema, você precisaria primeiro de recolher o ficheiro de despejo para o acidente e o suporte de contato com o ficheiro de despejo. Para recolher o ficheiro Dump, siga estes passos:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Fixe o disco OS a um VM de recuperação

1. Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação . 
3. Ambiente de trabalho remoto para o VM de recuperação.

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
4. Inicie o VM para reproduzir o problema, e ntão um ficheiro de despejo será gerado.
5. Fixe o disco DE SO a um VM de recuperação, recolha ficheiro de despejo [e,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) em seguida, envie um bilhete de apoio com o ficheiro de despejo.



