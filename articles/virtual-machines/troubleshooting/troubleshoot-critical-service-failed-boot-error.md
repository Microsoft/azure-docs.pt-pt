---
title: Quando se reinicia uma VM do Azure a falha de serviço crítico | Documentos da Microsoft
description: Aprenda a solucionar o erro "Falha de serviço 0x0000005A crítico", que ocorre quando se reinicia | Documentos da Microsoft
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
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: ffc8bd21468847a71a44be2244c72af7016cd65f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432027"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows mostra "CRÍTICOS serviço falhou" na tela azul quando se reinicia uma VM do Azure
Este artigo descreve o erro "Falha no serviço crítico" que ocorrem quando arranca uma Máquina Virtual do Windows (VM) no Microsoft Azure. Ele fornece passos de resolução de problemas para ajudar a resolver os problemas. 

> [!NOTE] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo descreve a utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptom"></a>Sintoma 

Uma VM do Windows não iniciar. Quando verifica as capturas de ecrã de arranque [diagnósticos de arranque](./boot-diagnostics.md), verá uma das seguintes mensagens de erro numa tela azul:

- "Seu PC Ocorreu um problema e tem de reiniciar. Pode reiniciar. Para obter mais informações sobre este problema e possíveis correções, visite http://windows.com/stopcode. Se chamar um técnico de suporte, dar-lhes estas informações: Pare o código: FALHA DE SERVIÇO CRÍTICO" 
- "Seu PC Ocorreu um problema e tem de reiniciar. Está a ser recolhido apenas algumas informações de erro e, em seguida, podemos será reiniciado para. Se quiser saber mais, pode pesquisar online mais tarde para este erro: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Causa

Existem várias causas para erros de parada. As causas mais comuns são:
- Problema com um driver
- Arquivo de sistema corrompido ou memória
- Aplicativo acessa um setor proibido da memória

## <a name="solution"></a>Solução 

Para resolver este problema, [contacte o suporte e submeter um arquivo de despejo](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), que nos irão ajudar a diagnosticar o problema mais rapidamente, ou tente a seguinte solução de ajuda autónoma.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. Tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do SO a uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md). 
3. Estabelece uma ligação de ambiente de trabalho remoto para a VM de recuperação.

### <a name="enable-dump-logs-and-serial-console"></a>Ativar registos de informação e a consola de série

O registo de despejo e [consola de série](./serial-console-windows.md) irá ajudar-nos fazer ainda mais a resolução de problemas.

Para ativar registos de informação e a consola de série, execute o seguinte script.

1. Abra uma sessão de linha de comandos elevada (executar como administrador).
2. Execute o seguintes script:

    Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F. Deverá substituí-la com o valor apropriado para a sua VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Substituir os controladores não assinados

1. Na VM de recuperação, execute o seguinte comando numa linha de comandos elevada. Este comando define o disco do SO afetado para iniciar no modo seguro na próxima inicialização:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Por exemplo, se o disco do SO que anexou unidade F, execute o seguinte comando:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Desanexar o disco do SO e, em seguida, volte a anexar o disco do SO para a VM afetada](troubleshoot-recovery-disks-portal-windows.md). A VM será arrancada no modo seguro. Se ainda perceber o erro, vá para o passo opcional.
3. Abra o **execute** caixa e execute **verifier** para iniciar a ferramenta de Gerenciador de verificação de Driver.
4. Selecione **selecionar automaticamente os controladores não assinados**e, em seguida, clique em **próxima**.
5. Irá obter a lista de ficheiros de controlador que não assinados. Lembre-se os nomes dos ficheiros.
6. Copie as mesmas versões destes ficheiros a partir de uma VM em funcionamento e, em seguida, substitua estes ficheiros não assinados. 

7. Remova as definições de arranque seguro:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Reinicie a VM. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Opcional: Analisar os registos de informação no modo de despejo de falha

Para analisar os registos de despejo por conta própria, siga estes passos:

1. Anexe o disco do SO a uma VM de recuperação.
2. No disco do SO que anexado, navegue até **\windows\system32\config**. Copie todos os ficheiros de cópia de segurança, caso seja necessária uma reversão.
3. Inicie **Editor de registo** (regedit.exe).
4. Selecione o **HKEY_LOCAL_MACHINE** chave. No menu, selecione **arquivo** > **carregar Hive**.
5. Navegue para o **\windows\system32\config\SYSTEM** pasta no disco do SO que anexados. Para o nome da colmeia, introduza **BROKENSYSTEM**. O novo ramo de registo é apresentado no **HKEY_LOCAL_MACHINE** chave.
6. Navegue até **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** e efetue as seguintes alterações:

    AutoReboot = 0

    CrashDumpEnabled = 2
7.  Selecione **BROKENSYSTEM**. No menu, selecione **arquivo** > **descarregar seção**.
8.  Modificar a configuração de BCD para inicializar em modo de depuração. Execute os seguintes comandos numa linha de comandos elevada:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Desanexar o disco do SO e, em seguida, volte a anexar o disco do SO para a VM afetada](troubleshoot-recovery-disks-portal-windows.md).
10. Arranque da VM para ver se mostra na análise de informação. Localize o ficheiro que não consegue carregar. É necessário substituir este ficheiro com um ficheiro a partir da VM em funcionamento. 

    Segue-se o exemplo de análise de informação. Pode ver que o **falha** no filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: http://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Assim que a VM estiver a trabalhar e arrancar normalmente, remova as definições de informação do Estado de falha de sistema:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
