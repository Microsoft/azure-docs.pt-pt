---
title: Erros no ecrã azul ao iniciar um VM Azure Microsoft Docs
description: Saiba como resolver o problema de que o erro do ecrã azul é recebido no arranque. Microsoft Docs
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
ms.openlocfilehash: b382efc4d283d64ce0f833bde9104fa2e3bc973a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088550"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows mostra erro do ecrã azul ao iniciar um VM Azure
Este artigo descreve erros de ecrã azul que poderá encontrar quando iniciar uma Máquina Virtual do Windows (VM) no Microsoft Azure. Fornece passos para ajudá-lo a recolher dados para um bilhete de apoio. 


## <a name="symptom"></a>Sintoma 

Um VM do Windows não começa. Quando verifica as imagens de arranque nos [diagnósticos boot,](./boot-diagnostics.md)vê uma das seguintes mensagens de erro num ecrã azul:

- o nosso PC teve um problema e precisa de reiniciar. Estamos só a recolher algumas informações de erro, e depois podes recomeçar.
- O seu PC teve um problema e precisa de reiniciar.

Esta secção lista as mensagens de erro comuns que pode encontrar ao gerir os VMs:

## <a name="cause"></a>Causa

Pode haver várias razões para ter um erro de paragem. As causas mais comuns são:

- Problema com um motorista
- Ficheiro ou memória do sistema corrompido
- Uma aplicação acede a um setor proibido da memória

## <a name="collect-memory-dump-file"></a>Recolher ficheiro de despejo de memória

Para resolver este problema, você precisaria primeiro de recolher o ficheiro de despejo para o acidente e suporte de contato com o ficheiro de despejo. Para recolher o ficheiro de despejo, siga estes passos:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Ligue o disco de SO a um VM de recuperação

1. Tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md). 
3. Ambiente de trabalho remoto para o VM de recuperação.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de despejo e envie um bilhete de apoio

1. No VM de recuperação, vá à pasta do windows no disco oss anexado. Se a carta do controlador que é atribuída ao disco de oss anexado for F, tem de ir para F:\Windows.
2. Localize o ficheiro memory.dmp e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo. 

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

    ```config-reg
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

    reg unload HKLM\BROKENSYSTEM
    ```

3. [Retire o disco de SO e, em seguida, re-prenda o disco de SO ao VM afetado](./troubleshoot-recovery-disks-portal-windows.md).
4. Inicie o VM para reproduzir o problema, em seguida, um ficheiro de despejo será gerado.
5. Fixe o disco DE A um VM de recuperação, recolha o ficheiro de despejo e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo.
