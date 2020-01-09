---
title: A reinicialização de máquinas virtuais do Azure está paralisada na reinicialização, no desligamento ou na interrupção dos serviços | Microsoft Docs
description: Este artigo ajuda você a solucionar problemas de erros de serviço no Azure Máquinas Virtuais do Windows.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: db7b26402170236843891799738088b9229e4693
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477411"
---
# <a name="azure-windows-vm-restart-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>A reinicialização de VM do Windows do Azure está presa em "reinicializando", "desligando" ou "parando serviços"

Este artigo fornece etapas para resolver os problemas de mensagens de "reinicialização", "desligamento" ou "interrupção de serviços" que você pode encontrar ao reinicializar uma VM (máquina virtual) do Windows no Microsoft Azure.

## <a name="symptoms"></a>Sintomas

Ao usar o [diagnóstico de inicialização](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para exibir a captura de tela da VM, você pode ver que a captura de tela exibe a mensagem "reiniciando", "desligando" ou "interrompendo serviços".

![Reiniciando, desligando e interrompendo telas de serviços](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

O Windows usa o processo de desligamento para executar operações de manutenção do sistema e processa alterações como atualizações, funções e recursos. Não é recomendável interromper esse processo crítico até que ele seja concluído. Dependendo do número de atualizações/alterações e do tamanho da VM, o processo pode levar muito tempo. Se o processo for interrompido, é possível que o sistema operacional fique corrompido. Interrompa o processo apenas se ele estiver demorando demais.

## <a name="solution"></a>Solução

### <a name="collect-a-process-memory-dump"></a>Coletar um despejo de memória do processo

1. Baixe a [ferramenta Procdump](http://download.sysinternals.com/files/Procdump.zip) em um disco de dados novo ou existente, que é anexado a uma VM em funcionamento da mesma região.

2. Desanexe o disco que contém os arquivos necessários da VM de trabalho e anexe o disco à VM quebrada. Estamos ligando para esse disco o **disco do utilitário**.

Use o [console serial](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) para concluir as seguintes etapas:

1. Abra um PowerShell administrativo e verifique o serviço suspenso após a interrupção.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Em um CMD Administrativo, obtenha o PID do serviço suspenso.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Obtenha um exemplo de despejo de memória do processo suspenso <STOPPING SERVICE>.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Agora, encerre o processo suspenso para desbloquear o processo de desligamento.

   ``
   taskkill /PID <PID> /t /f
   ``

Depois que o sistema operacional for iniciado novamente, se ele for inicializado normalmente, apenas verifique se a consistência do sistema operacional está ok. Se a corrupção for relatada, execute o seguinte comando até que o disco esteja corrompido:

``
dism /online /cleanup-image /restorehealth
``

Se você não conseguir coletar um despejo de memória de processo ou se esse problema for recursivo e precisar de uma análise de causa raiz, continue com a coleta de um despejo de memória do so abaixo, continue para abrir uma solicitação de suporte.

### <a name="collect-an-os-memory-dump"></a>Coletar um despejo de memória do so

Se o problema não for resolvido depois de aguardar as alterações serem processadas, você precisará coletar um arquivo de despejo de memória e entrar em contato com o suporte. Para coletar o arquivo de despejo, siga estas etapas:

**Anexar o disco do sistema operacional a uma VM de recuperação**

1. Tire um instantâneo do disco do sistema operacional da VM afetada como um backup. Para obter mais informações, consulte [instantâneo de um disco](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Anexar o disco do SO a uma VM de recuperação](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Área de trabalho remota para a VM de recuperação.

4. Se o disco do sistema operacional for criptografado, você deverá desligar a criptografia antes de passar para a próxima etapa. Para obter mais informações, consulte [descriptografar o disco do sistema operacional criptografado na VM que não pode ser inicializada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Localizar arquivo de despejo e enviar um tíquete de suporte**

1. Na VM de recuperação, vá para a pasta do Windows no disco do sistema operacional anexado. Se a letra do driver atribuída ao disco do sistema operacional anexado for F, você precisará ir para F:\Windows.

2. Localize o arquivo Memory. dmp e, em seguida, [envie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o arquivo de despejo.

Se você não encontrar o arquivo de despejo, mova a próxima etapa para habilitar o log de despejo e o console serial.

**Habilitar o log de despejo e o console serial**

Para ativar o registo de despejo e consola de série, execute o seguinte script.

1. Abra a sessão de prompt de comando com privilégios elevados (executar como administrador).

2. Execute o seguintes script:

   Nesse script, presumimos que a letra da unidade atribuída ao disco do sistema operacional anexado é F. Substitua-a pelo valor apropriado em sua VM.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Verifique se há espaço suficiente no disco para alocar a quantidade de memória como RAM, o que depende do tamanho que você está selecionando para essa VM.

4. Se não houver espaço suficiente ou a VM for grande (G, GS ou série E), você poderá alterar o local em que esse arquivo será criado e referir-se a qualquer outro disco de dados, que é anexado à VM. Para alterar o local, você deve alterar a seguinte chave:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Desanexe o disco do sistema operacional e, em seguida, anexe novamente o disco do sistema operacional à VM afetada](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Inicie a VM e acesse o console serial.

7. Selecione Enviar NMI (interrupção não mascarada) para disparar o despejo de memória.

   ![Enviar interrupção não mascarável](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Anexe o disco do sistema operacional a uma VM de recuperação novamente, colete o arquivo de despejo.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de coletar o arquivo de despejo, entre em contato com o suporte da Microsoft para determinar a causa raiz.
