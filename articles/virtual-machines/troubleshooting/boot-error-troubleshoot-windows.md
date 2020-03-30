---
title: A paragem das Máquinas Virtuais Azure está presa nos serviços de reinício, deparagem ou paragem de sistemas de reinício, paragem ou paragem de serviços . Microsoft Docs
description: Este artigo ajuda-o a resolver erros de serviço em Máquinas Virtuais Do Windows Azure.
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
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371368"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Paragem do Azure Windows VM está presa em "Restarting", "Shutdown Down" ou "Stop services"

Este artigo fornece medidas para resolver os problemas de "Reiniciar", "Desligar", ou "Serviços de Paragem" que poderá encontrar quando reiniciar uma máquina virtual do Windows (VM) no Microsoft Azure.

## <a name="symptoms"></a>Sintomas

Quando utilizar [diagnósticos boot](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) para visualizar a imagem do VM, poderá ver que a imagem mostra a mensagem "Reiniciar", "Desligar" ou "Serviços de paragem".

![Reiniciando, desligando e parando os ecrãs de serviços](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Causa

O Windows utiliza o processo de paragem para realizar operações de manutenção do sistema e alterações de processos como atualizações, funções e funcionalidades. Não é aconselhável interromper este processo crítico até que esteja concluído. Dependendo do número de atualizações/alterações e do tamanho do VM, o processo pode demorar muito tempo. Se o processo for interrompido, é possível que o SO se torne corrupto. Só interrompa o processo se estiver a demorar demasiado tempo.

## <a name="solution"></a>Solução

### <a name="collect-a-process-memory-dump"></a>Recolher um depósito de memória do Processo

1. Descarregue a [ferramenta Procdump](http://download.sysinternals.com/files/Procdump.zip) num disco de dados novo ou existente, que está ligado a um VM em funcionamento da mesma região.

2. Retire o disco contendo os ficheiros necessários do VM de funcionamento e prenda o disco ao vM partido. Estamos a chamar a este disco o **disco utilitário.**

Utilize a [Consola em Série](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) para completar os seguintes passos:

1. Abra uma Powershell administrativa e verifique o serviço que está suspenso ao parar.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Numa CMD administrativa, obtenha o PID do serviço suspenso.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Obtenha uma amostra de despejo <STOPPING SERVICE>de memória do processo de suspensão.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Agora mate o processo de suspensão para desbloquear o processo de encerramento.

   ``
   taskkill /PID <PID> /t /f
   ``

Uma vez que o Sistema operativo recomece, se estiver a botas normalmente, certifique-se de que a consistência do OS está boa. Se a corrupção for denunciada, o seguinte comando até que o disco esteja livre de corrupção:

``
dism /online /cleanup-image /restorehealth
``

Se não conseguir recolher um depósito de memória de processo, ou se este problema for recursivo e necessitar de uma análise de causa de raiz, proceda à recolha de um depósito de memória de SO abaixo, a receita para abrir um pedido de suporte.

### <a name="collect-an-os-memory-dump"></a>Recolher um depósito de memória de OS

Se o problema não for resolvido depois de esperar pelas alterações processando, terá de recolher um ficheiro de despejo de memória e suporte de contacto. Para recolher o ficheiro Dump, siga estes passos:

**Fixe o disco OS a um VM de recuperação**

1. Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Fixe o disco OS a um VM](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)de recuperação .

3. Ambiente de trabalho remoto para o VM de recuperação.

4. Se o disco OS estiver encriptado, tem de desligar a encriptação antes de passar para o próximo passo. Para obter mais informações, consulte [Desencriptar o disco OS encriptado no VM que não pode arrancar](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Localize o ficheiro de despejo e envie um bilhete de apoio**

1. No VM de recuperação, vá para a pasta windows no disco OS anexado. Se a carta de condutor que é atribuída ao disco operativo em anexo for F, tem de ir para F:\Windows.

2. Localize o ficheiro memory.dmp [e,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) em seguida, envie um bilhete de apoio com o ficheiro de despejo.

Se não encontrar o ficheiro de despejo, desloque o próximo passo para ativar o registo de despejo e a Consola em Série.

**Ativar o registo de despejo e a consola em série**

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abrir a sessão de solicitação de comando elevado (Executar como administrador).

2. Execute o seguintes script:

   Neste script, assumimos que a letra de unidade atribuída ao disco OS anexado é F. Substitua-a pelo valor adequado no seu VM.

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

4. Se não houver espaço suficiente ou o VM for grande (série G, GS ou E), poderá alterar a localização onde este ficheiro será criado e remeter isso para qualquer outro disco de dados, que está ligado ao VM. Para alterar a localização, tem de alterar a seguinte tecla:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Retire o disco OS e, em seguida, recoloque o disco OS ao VM afetado](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Inicie o VM e aceda à Consola em Série.

7. Selecione Enviar Interrupção Não-Máscara (NMI) para acionar o depósito de memória.

   ![Enviar Interrupção Não-Máscara](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Fixe o disco OS a um VM de recuperação novamente, colete ficheiro de despejo.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de recolher o ficheiro de despejo, contacte o suporte da Microsoft para determinar a causa principal.
