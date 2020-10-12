---
title: O encerramento das máquinas virtuais Azure está preso nos serviços de recorese, desligar ou parar Microsoft Docs
description: Este artigo ajuda-o a resolver erros de serviço em Máquinas Virtuais Azure Windows.
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
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526763"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>O encerramento do Azure Windows VM está preso em "Reiniciar", "Desligar" ou "Parar serviços"

Este artigo fornece medidas para resolver os problemas de "Reiniciar", "Desligar" ou "Parar serviços" que poderá encontrar quando reiniciar uma máquina virtual do Windows (VM) no Microsoft Azure.

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, poderá ver que a imagem exibe a mensagem "Reiniciar", "Desligar" ou "Parar os serviços".

![Reiniciar, desligar e parar os ecrãs de serviços](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

O Windows utiliza o processo de paragem para executar operações de manutenção do sistema e alterações de processos como atualizações, funções e funcionalidades. Não é aconselhável interromper este processo crítico até que esteja concluído. Dependendo do número de atualizações/alterações e do tamanho do VM, o processo pode demorar muito tempo. Se o processo for interrompido, é possível que o SO se torne corrupto. Só interrompe o processo se estiver a demorar demasiado tempo.

## <a name="solution"></a>Solução

### <a name="collect-a-process-memory-dump"></a>Recolha um despejo de memória de processo

1. Descarregue [a ferramenta Procdump](http://download.sysinternals.com/files/Procdump.zip) num disco de dados novo ou existente, que está ligado a um VM em funcionamento da mesma região.

2. Retire o disco que contém os ficheiros necessários do VM de funcionamento e prenda o disco ao seu VM avariado. Estamos a chamar a este disco o **disco utility.**

Utilize [a Consola em Série](./serial-console-windows.md) para completar os seguintes passos:

1. Abra uma PowerShell administrativa e verifique o serviço que deixa de responder após a paragem.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Num CMD administrativo, obtenha o PID do serviço sem resposta.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Obtenha uma amostra de despejo de memória do processo sem <STOPPING SERVICE> resposta.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Agora, desligue o processo sem resposta para desbloquear o processo de paragem.

   ``
   taskkill /PID <PID> /t /f
   ``

Uma vez que o SO recomeça, se arrancar normalmente, certifique-se de que a consistência do SO está bem. Se a corrupção for reportada, executar o seguinte comando até que o disco seja livre de corrupção:

``
dism /online /cleanup-image /restorehealth
``

Se não conseguir recolher um despejo de memória de processo, ou se este problema for recursivo e necessitar de uma análise de causa raiz, proceda à recolha de um depósito de memória de SO abaixo, proceda a abrir um pedido de apoio.

### <a name="collect-an-os-memory-dump"></a>Recolha um depósito de memória de SO

Se o problema não se resolver depois de esperar pelas alterações ao processo, terá de recolher um ficheiro de despejo de memória e suporte de contacto. Para recolher o ficheiro de despejo, siga estes passos:

**Ligue o disco de SO a um VM de recuperação**

1. Tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

2. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).

3. Ambiente de trabalho remoto para o VM de recuperação.

4. Se o disco DE estiver encriptado, deve desligar a encriptação antes de passar para o passo seguinte. Para obter mais informações, consulte [desencriptar o disco de OS encriptado no VM que não pode arrancar](./troubleshoot-bitlocker-boot-error.md#solution).

**Localize o ficheiro de despejo e envie um bilhete de apoio**

1. No VM de recuperação, vá à pasta do windows no disco oss anexado. Se a carta do controlador que é atribuída ao disco de oss anexado for F, tem de ir para F:\Windows.

2. Localize o ficheiro memory.dmp e, em seguida, [envie um bilhete de apoio](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) com o ficheiro de despejo.

Se não conseguir encontrar o ficheiro de despejo, mova o próximo passo para ativar o registo de despejo e a Consola em Série.

**Ativar o registo de despejo e a consola em série**

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abrir sessão de pedido de comando elevado (Executar como administrador).

2. Execute o seguintes script:

   Neste script, assumimos que a letra de unidade que é atribuída ao disco de SO anexado é F. Substitua-a pelo valor apropriado no seu VM.

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

3. Verifique se há espaço suficiente no disco para alocar tanta memória como a RAM, que depende do tamanho que está a selecionar para este VM.

4. Se não houver espaço suficiente ou o VM for grande (série G, GS ou E), poderá alterar a localização onde este ficheiro será criado e encaminhá-lo para qualquer outro disco de dados, que esteja ligado ao VM. Para alterar a localização, tem de alterar a seguinte tecla:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Retire o disco de OS e, em seguida, recoloque o disco de OS no VM afetado](./troubleshoot-recovery-disks-portal-windows.md).

6. Inicie o VM e aceda à Consola em Série.

7. Selecione Enviar Interrupção Não Mascarada (NMI) para ativar o despejo de memória.

   ![Enviar interrupção não-mascarada](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Fixe o disco DE A uma VM de recuperação novamente, colete o ficheiro de despejo.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de recolher o ficheiro de despejo, contacte o suporte da Microsoft para determinar a causa principal.
