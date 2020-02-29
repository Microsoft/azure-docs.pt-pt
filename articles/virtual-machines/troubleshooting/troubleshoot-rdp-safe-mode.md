---
title: Não é possível ligar às máquinas de virtuais do Azure remotamente uma vez que a VM arranca no modo de segurança | Documentos da Microsoft
description: Saiba como resolver um problema em que não é possível RDP para uma VM porque a VM arranca no modo seguro. | Documentos da Microsoft
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
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918211"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Não é possível RDP para uma VM porque a VM arranca no modo de segurança

Este artigo mostra como resolver um problema em que não é possível ligar às máquinas de virtuais de Windows do Azure (VMs), porque a VM está configurada para efetuar o arranque no modo seguro.


## <a name="symptoms"></a>Sintomas

Não é possível efetuar uma ligação RDP ou de outras ligações (por exemplo, HTTP) para uma VM no Azure porque a VM está configurada para efetuar o arranque no modo seguro. Quando verificar a imagem nos [diagnósticos](../troubleshooting/boot-diagnostics.md) boot no portal Azure, poderá ver que as botas VM normalmente, mas a interface de rede não está disponível:

![Imagem sobre inferce de rede em modo de segurança](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Causa

O serviço RDP não está disponível no modo de segurança. Apenas serviços e programas essenciais do sistema são carregados quando a VM arranca no modo de segurança. Isto aplica-se para as duas versões diferentes do modo de segurança que são "Mínimo de arranque seguro" e "Arranque seguro com a conectividade".


## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controlo de série para configurar o VM para iniciar o modo normal ou [reparar o VM offline](#repair-the-vm-offline) utilizando um VM de recuperação.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Se a Consola em Série não estiver ativada no seu VM, consulte [a reparação do VM offline](#repair-the-vm-offline).
2. Verifique os dados de configuração de arranque:

        bcdedit /enum

    Se o VM estiver configurado para iniciar o Modo Seguro, verá uma bandeira extra sob a secção de carregador de **botas do Windows** chamada **safeboot**. Se não vir a bandeira do **safeboot,** o VM não está em modo de segurança. Este artigo não é aplicável ao seu cenário.

    A bandeira **safeboot** pode aparecer com os seguintes valores:
   - Mínimo
   - Rede

     Em qualquer um desses dois modos, RDP não será iniciado. Por conseguinte, a correção permanece igual.

     ![Imagem sobre o sinalizador do modo de segurança](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Elimine a bandeira **de safemoade,** para que o VM arranque em modo normal:

        bcdedit /deletevalue {current} safeboot

4. Verifique os dados de configuração da bota para se certificar de que a bandeira **do safeboot** é removida:

        bcdedit /enum

5. Reinicie a VM e, em seguida, verifique se o problema está resolvido.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.
3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Ativar o registo de informação e a consola de série (opcional)

O registo de informação e a consola de série irão ajudar-nos fazer ainda mais de resolução de problemas se não conseguir resolver o problema a solução neste artigo.

Para ativar o registo de despejo e consola de série, execute o seguinte script.

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador).**
2. Execute o seguintes script:

    Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Configure o Windows para iniciar o modo normal

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador).**
2. Verifique os dados de configuração da bota. Nos seguintes comandos, assumimos que a letra de unidade atribuída ao disco OS anexado é F. Substitua esta carta de unidade com o valor adequado para o seu VM.

        bcdedit /store F:\boot\bcd /enum
    Tome nota do nome identificador da partição que tem a pasta **\windows.** Por predefinição, o nome identificador é "Padrão".

    Se o VM estiver configurado para iniciar o Modo Seguro, verá uma bandeira extra sob a secção de carregador de **botas do Windows** chamada **safeboot**. Se não vir a bandeira do **safeboot,** este artigo não se aplica ao seu cenário.

    ![A imagem sobre o identificador de arranque](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Retire a bandeira **safeboot,** para que o VM entre no modo normal:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Verifique os dados de configuração da bota para se certificar de que a bandeira **do safeboot** é removida:

        bcdedit /store F:\boot\bcd /enum
5. [Desmontar o disco OS e recriar o VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se a questão está resolvida.
