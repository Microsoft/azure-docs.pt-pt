---
title: Azul erros de ecrã quando se reinicia uma VM do Azure | Documentos da Microsoft
description: Saiba como resolver o problema que o erro de tela azul é recebido quando se reinicia | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 3d7bdb3f7d3343c5c165636b131f9a5c3a1458fd
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674604"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows mostra a tela azul de erro quando se reinicia uma VM do Azure
Este artigo descreve os erros de tela azul que poderá encontrar ao inicializar uma Máquina Virtual do Windows (VM) no Microsoft Azure. Ele fornece passos para o ajudar a recolher dados para um pedido de suporte. 

> [!NOTE] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve a utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não iniciar. Quando verifica as capturas de ecrã de arranque [diagnósticos de arranque](./boot-diagnostics.md), verá uma das seguintes mensagens de erro numa tela azul:

- nosso PC Ocorreu um problema e tem de reiniciar. Está a ser recolhido apenas algumas informações de erro e, em seguida, pode reiniciar.
- Seu PC Ocorreu um problema e tem de reiniciar.

Esta secção lista as mensagens de erro comuns que poderá encontrar ao gerir VMs:

## <a name="cause"></a>Causa

Pode haver vários motivos, como por que obteria um erro de parada. As causas mais comuns são:

- Problema com um driver
- Arquivo de sistema corrompido ou memória
- Acede a uma aplicação para um setor proibido da memória

## <a name="collect-memory-dump-file"></a>Recolher o arquivo de despejo de memória

Para resolver este problema, precisaria primeiro a reunir o arquivo de despejo de falha e contacte o suporte com o ficheiro de informação. Para recolher o ficheiro de informação, siga estes passos:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. Tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md). 
3. Ambiente de trabalho remoto para a VM de recuperação.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localize o ficheiro de informação e submeter um pedido de suporte

1. Na VM de recuperação, vá para a pasta do windows no disco do SO anexado. Se a letra de unidade que está atribuída ao disco do SO anexado F, terá de ir para F:\Windows.
2. Localize o ficheiro de memory.dmp th e, em seguida [submeter um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de informação. 

Se não encontrar o ficheiro de informação, mova o passo seguinte para ativar o registo de informação e a consola de série.

### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de informação e a consola de série

Para ativar o registo de despejo e consola de série, execute o seguinte script.

1. Abrir sessão de linha de comandos elevada (executar como administrador).
2. Execute o seguintes script:

    Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F.  Substitua-o com o valor apropriado na sua VM.

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

    1. Certifique-se de que existe espaço suficiente no disco para alocar a quantidade de memória como a quantidade de RAM, que depende do tamanho que está a selecionar para esta VM.
    2. Se não existe espaço suficiente ou este é um tamanho grande de VM (série G, GS ou E), em seguida, pode alterar a localização em que esse arquivo será criado e veja que qualquer outro disco de dados que esteja anexado à VM. Para tal, terá de alterar a chave seguinte:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Desanexar o disco do SO e, em seguida, volte a anexar o disco do SO para a VM afetada](../windows/troubleshoot-recovery-disks-portal.md).
4. Iniciar a VM para reproduzir o problema e, em seguida, será gerado um arquivo de despejo.
5. Anexar o disco do SO a uma VM, o ficheiro de recolha de informação, de recuperação e, em seguida [submeter um pedido de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de informação.



