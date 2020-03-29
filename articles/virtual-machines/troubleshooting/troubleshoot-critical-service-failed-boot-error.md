---
title: SERVIÇO CRÍTICO FALHOU ao arrancar um Azure VM / Microsoft Docs
description: Saiba como resolver o erro "0x00000005A-CRITICAL SERVICE FAILED" que ocorre ao iniciar o arranque [ serviço crítico de 000000005" que ocorre ao iniciar o arranque [ serviço crítico] que ocorre ao iniciar o arranque [ serviço crítico] Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: 54ba87b681a055bb46b81ca81d2bcdd103491f27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921458"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Windows mostra "SERVIÇO CRÍTICO FALHADO" no ecrã azul ao arrancar um VM Azure
Este artigo descreve o erro "CRITICAL SERVICE FAILED" que poderá experimentar ao iniciar uma Máquina Virtual do Windows (VM) no Microsoft Azure. Fornece medidas de resolução de problemas para ajudar a resolver os problemas. 


## <a name="symptom"></a>Sintoma 

Um VM windows não começa. Quando verificar as imagens da bota nos [diagnósticos da Bota,](./boot-diagnostics.md)vê uma das seguintes mensagens de erro num ecrã azul:

- "O seu PC teve um problema e precisa de recomeçar. Pode recomeçar. Para mais informações sobre este assunto https://windows.com/stopcodee possíveis correções, visite . Se ligar para uma pessoa de apoio, dê-lhes esta informação: Stop code: CRITICAL SERVICE FAILED" 
- "O seu PC teve um problema e precisa de recomeçar. Estamos apenas a recolher informações de erros, e depois recomeçamos para ti. Se quiser saber mais, pode pesquisar online mais tarde por este erro: CRITICAL_SERVICE_FAILED"

## <a name="cause"></a>Causa

Há várias causas para erros de paragem. As causas mais comuns são:
- Problema com um motorista
- Ficheiro ou memória do sistema corrompido
- Candidatura acede a um setor proibido da memória

## <a name="solution"></a>Solução 

Para resolver este problema, contacte o [suporte e submeta um ficheiro](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file)de despejo , que nos ajudará a diagnosticar o problema mais rapidamente, ou tente a seguinte solução de autoajuda.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Fixe o disco OS a um VM de recuperação

1. Tire uma foto do disco operativo do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Fixe o disco OS a um VM](./troubleshoot-recovery-disks-portal-windows.md)de recuperação . 
3. Estabeleça uma ligação remota de ambiente de trabalho ao VM de recuperação.

### <a name="enable-dump-logs-and-serial-console"></a>Ativar registos de despejo e consola em série

O registo de despejo e a [Consola em Série](./serial-console-windows.md) ajudar-nos-ão a resolver mais problemas.

Para ativar os registos de despejo e a Consola em Série, execute o seguinte script.

1. Abra uma sessão de solicitação de comando elevada (executar como administrador).
2. Execute o seguintes script:

    Neste guião, assumimos que a carta de unidade que é atribuída ao disco operativo anexado é F. Deve substituí-lo pelo valor adequado para o seu VM.

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

### <a name="replace-the-unsigned-drivers"></a>Substitua os condutores não assinados

1. No VM de recuperação, corra o seguinte comando a partir de um pedido de comando elevado. Este comando define o disco OS afetado para iniciar o modo de segurança na bota seguinte:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Por exemplo, se o disco OS que anexou for acionado F, execute o seguinte comando:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Retire o disco OS e, em seguida, religue o disco OS ao VM afetado](troubleshoot-recovery-disks-portal-windows.md). O VM iniciará o modo Seguro. Se ainda sentir o erro, vá ao passo opcional.
3. Abra a caixa **de execução** e execute o **verificador** para iniciar a ferramenta Do Gestor de Verificadores de Condutor.
4. Selecione **automaticamente selecione controladores não assinados**e, em seguida, clique em **Seguinte**.
5. Receberá a lista dos ficheiros do motorista que não estão assinados. Lembre-se dos nomes dos ficheiros.
6. Copie as mesmas versões destes ficheiros a partir de um VM em funcionamento e, em seguida, substitua estes ficheiros não assinados. 

7. Retire as definições de arranque de segurança:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Reinicie a VM. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Opcional: Analise os registos de despejo no modo dump crash

Para analisar os troncos de despejo, siga estes passos:

1. Anexe o disco do SO a uma VM de recuperação.
2. No disco OS que anexou, navegue para **\windows\system32\config**. Copie todos os ficheiros como cópia de segurança no caso de ser necessário um retrocesso.
3. Iniciar **editor de registo** (regedit.exe).
4. Selecione a tecla **HKEY_LOCAL_MACHINE.** No menu, selecione **File** > **Load Hive**.
5. Navegue na pasta **\windows\system32\config\SYSTEM** no disco OS que anexou. Para o nome da colmeia, introduza **BROKENSYSTEM**. A nova colmeia de registo é exibida sob a chave **HKEY_LOCAL_MACHINE.**
6. Navegue para **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl** e efaça as seguintes alterações:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Selecione **BrokenSYSTEM**. No menu, selecione **File** > **Unload Hive**.
8.  Modifique a configuração BCD para iniciar o modo de depuração. Executar os seguintes comandos a partir de um pedido de comando elevado:

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
9. [Retire o disco OS e, em seguida, religue o disco OS ao VM afetado](troubleshoot-recovery-disks-portal-windows.md).
10. Arranque o VM para ver se mostra análise de despejo. Encontre o ficheiro que não carrega. Tem de substituir este ficheiro por um ficheiro da VM em funcionamento. 

    Segue-se a amostra da análise do despejo. Pode ver que o **INE** está em filecrypt.sys: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

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
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Uma vez que o VM esteja a funcionar e a arrancar normalmente, retire as definições de dump Crash:

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
