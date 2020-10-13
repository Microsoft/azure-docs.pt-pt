---
title: Não é possível ligar-se remotamente às Máquinas Virtuais Azure porque as botas VM entram em Modo Seguro Microsoft Docs
description: Saiba como resolver um problema em que não pode pDR a um VM porque o VM entra em Modo De Segurança.! Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: d424cccc0a50198f3ca8c6c040afb87f44282d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86508904"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Não é pDR a um VM porque as botas VM entram em Modo Seguro

Este artigo mostra como resolver um problema em que não é possível ligar-se a Máquinas Virtuais Azure Windows (VMs) porque o VM está configurado para iniciar o modo de segurança.


## <a name="symptoms"></a>Sintomas

Não é possível efecer uma ligação RDP ou outras ligações (como HTTP) a um VM em Azure porque o VM está configurado para iniciar o modo de segurança. Quando verificar a imagem no [diagnóstico Boot](../troubleshooting/boot-diagnostics.md) no portal Azure, poderá ver que as botas VM normalmente, mas a interface de rede não está disponível:

![Imagem sobre inferir rede em Modo Seguro](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Causa

O serviço RDP não está disponível em Modo de Segurança. Apenas os programas e serviços essenciais do sistema são carregados quando as botas VM entram em Modo De Segurança. Isto aplica-se às duas versões diferentes do Modo De Segurança que são "Safe Boot minimal" e "Safe Boot with connectivity".


## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco de SO do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para configurar o VM para iniciar o modo normal ou [reparar o VM offline](#repair-the-vm-offline) utilizando um VM de recuperação.

### <a name="use-serial-control"></a>Utilizar o controlo em série

1. Ligue-se à [Consola em Série e abra a instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Se a Consola em Série não estiver ativada no seu VM, consulte [a reparação offline do VM](#repair-the-vm-offline).
2. Verifique os dados de configuração da bota:

    ```console
    bcdedit /enum
    ```

    Se o VM estiver configurado para iniciar o modo de segurança, verá uma bandeira extra sob a secção **de carregador de botas do Windows** chamada **safeboot**. Se não vir a bandeira **de segurança,** o VM não está em Modo de Segurança. Este artigo não se aplica ao seu cenário.

    A bandeira **safeboot** pode aparecer com os seguintes valores:
   - Mínimo
   - Rede

     Em qualquer um destes dois modos, o PDR não será iniciado. Portanto, a correção permanece a mesma.

     ![Imagem sobre a bandeira do modo de segurança](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Elimine a bandeira **de safemoade,** para que o VM entre em modo normal:

    ```console
    bcdedit /deletevalue {current} safeboot
    ```

4. Verifique os dados de configuração da bota para se certificar de que a bandeira **de safeboot** é removida:

    ```console
    bcdedit /enum
    ```

5. Reinicie o VM e, em seguida, verifique se o problema está resolvido.

### <a name="repair-the-vm-offline"></a>Reparar o VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Ligue o disco de SO a um VM de recuperação

1. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.
3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de sos anexado.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Ativar o registo de despejo e a consola em série (opcional)

O registo de despejo e a Consola em Série ajudar-nos-ão a fazer mais resolução de problemas se o problema não puder ser resolvido pela solução neste artigo.

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador**).
2. Execute o seguintes script:

    Neste script, assumimos que a letra de unidade que é atribuída ao disco de OS anexado é F. Substitua esta letra de unidade pelo valor adequado para o seu VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Configure as Janelas para iniciar em modo normal

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador**).
2. Verifique os dados de configuração da bota. Nos seguintes comandos, assumimos que a letra de unidade que é atribuída ao disco de OS anexado é F. Substitua esta letra de unidade pelo valor adequado para o seu VM.

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

    Tome nota do nome identificador da partição que tem a pasta **\windows.** Por predefinição, o nome identificador é "Predefinido".

    Se o VM estiver configurado para iniciar o modo de segurança, verá uma bandeira extra sob a secção **de carregador de botas do Windows** chamada **safeboot**. Se não vir a bandeira **de safeboot,** este artigo não se aplica ao seu cenário.

    ![A imagem sobre o identificador de botas](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Retire a bandeira **de segurança,** para que o VM entre em modo normal:

    ```console
    bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
    ```

4. Verifique os dados de configuração da bota para se certificar de que a bandeira **de safeboot** é removida:

    ```console
    bcdedit /store F:\boot\bcd /enum
    ```

5. [Retire o disco de SO e recrie o VM](./troubleshoot-recovery-disks-portal-windows.md). Então verifique se a questão está resolvida.
