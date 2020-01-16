---
title: Erros de tela azul ao inicializar uma VM do Azure | Microsoft Docs
description: Saiba como solucionar o problema que o erro de tela azul é recebido ao inicializar | Microsoft Docs
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
ms.openlocfilehash: d9732f232c5a7ec1e551f42fe0e8187e382aec6e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981876"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>O Windows mostra o erro de tela azul ao inicializar uma VM do Azure
Este artigo descreve os erros de tela azul que você pode encontrar ao inicializar uma VM (máquina virtual) do Windows no Microsoft Azure. Ele fornece etapas para ajudá-lo a coletar dados para um tíquete de suporte. 

> [!NOTE] 
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../azure-resource-manager/management/deployment-models.md). Este artigo descreve como usar o modelo de implantação do Gerenciador de recursos, que é recomendável usar para novas implantações em vez do modelo de implantação clássico.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não é iniciada. Ao verificar as capturas de tela de inicialização em [diagnósticos de inicialização](./boot-diagnostics.md), você verá uma das seguintes mensagens de erro em um ecrã azul:

- nosso PC encontrou um problema e precisa ser reiniciado. Estamos apenas coletando algumas informações de erro e, em seguida, você pode reiniciar.
- Seu PC encontrou um problema e precisa ser reiniciado.

Esta seção lista as mensagens de erro comuns que você pode encontrar ao gerenciar VMs:

## <a name="cause"></a>Causa

Pode haver várias razões pelas quais você obteria um erro de parada. As causas mais comuns são:

- Problema com um driver
- Arquivo ou memória do sistema corrompido
- Um aplicativo acessa um setor proibido da memória

## <a name="collect-memory-dump-file"></a>Coletar arquivo de despejo de memória

Para resolver esse problema, você precisaria primeiro coletar o arquivo de despejo para a falha e contatar o suporte com o arquivo de despejo. Para coletar o arquivo de despejo, siga estas etapas:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. Tire um instantâneo do disco do sistema operacional da VM afetada como um backup. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md). 
3. Área de trabalho remota para a VM de recuperação.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Localizar arquivo de despejo e enviar um tíquete de suporte

1. Na VM de recuperação, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for F, você precisará ir para F:\Windows.
2. Localize o arquivo Memory. dmp e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo. 

Se você não encontrar o arquivo de despejo, mova a próxima etapa para habilitar o log de despejo e o console serial.

### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de informação e a consola de série

Para ativar o registo de despejo e consola de série, execute o seguinte script.

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
4. Inicie a VM para reproduzir o problema. em seguida, um arquivo de despejo será gerado.
5. Anexe o disco do sistema operacional a uma VM de recuperação, colete o arquivo de despejo e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo.



