---
title: Não é possível ligar às máquinas de virtuais do Azure remotamente uma vez que a VM arranca no modo de segurança | Documentos da Microsoft
description: Saiba como resolver um problema em que não é possível RDP para uma VM porque a VM arranca no modo seguro. | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 8e108d88282894a7b1bf014146083008bedd483d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095046"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Não é possível RDP para uma VM porque a VM arranca no modo de segurança

Este artigo mostra como resolver um problema em que não é possível ligar às máquinas de virtuais de Windows do Azure (VMs), porque a VM está configurada para efetuar o arranque no modo seguro.

> [!NOTE]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptoms"></a>Sintomas

Não é possível efetuar uma ligação RDP ou de outras ligações (por exemplo, HTTP) para uma VM no Azure porque a VM está configurada para efetuar o arranque no modo seguro. Quando dá entrada a captura de ecrã do [diagnósticos de arranque](../troubleshooting/boot-diagnostics.md) no portal do Azure, poderá ver que a VM arranca normalmente, mas a interface de rede não está disponível:

![Imagem sobre inferce de rede em modo de segurança](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Causa

O serviço RDP não está disponível no modo de segurança. Apenas serviços e programas essenciais do sistema são carregados quando a VM arranca no modo de segurança. Isto aplica-se para as duas versões diferentes do modo de segurança que são "Mínimo de arranque seguro" e "Arranque seguro com a conectividade".


## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize o controle Serial para configurar a VM para efetuar o arranque no modo normal ou [Repare a VM offline](#repair-the-vm-offline) através de uma VM de recuperação.

### <a name="use-serial-control"></a>Utilizar o controlo de série

1. Ligar à [consola de série e Abrir instância CMD](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Se a consola de série não estiver ativada na sua VM, consulte [Repare a VM offline](#repair-the-vm-offline).
2. Verifique os dados de configuração de arranque:

        bcdedit /enum

    Se a VM está configurada para inicializar em modo de segurança, verá um sinalizador extra sob os **carregador de inicialização do Windows** secção denominada **inicialização segura**. Se não vir a **inicialização segura** sinalizador, a VM não está no modo de segurança. Este artigo não é aplicável ao seu cenário.

    O **inicialização segura** sinalizador foi apresentada com os seguintes valores:
   - Mínimo
   - Rede

     Em qualquer um desses dois modos, RDP não será iniciado. Por conseguinte, a correção permanece igual.

     ![Imagem sobre o sinalizador do modo de segurança](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Eliminar a **safemoade** sinalizar, para que a VM será arrancada no modo normal:

        bcdedit /deletevalue {current} safeboot

4. Verifique os dados de configuração de arranque para se certificar de que o **inicialização segura** sinalizador é removido:

        bcdedit /enum

5. Reinicie a VM e, em seguida, verifique se o problema está resolvido.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.
3. Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Ativar o registo de informação e a consola de série (opcional)

O registo de informação e a consola de série irão ajudar-nos fazer ainda mais de resolução de problemas se não conseguir resolver o problema a solução neste artigo.

Para ativar o registo de despejo e consola de série, execute o seguinte script.

1. Abra uma sessão de linha de comandos elevada (**executar como administrador**).
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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Configurar o Windows não arrancou no modo normal

1. Abra uma sessão de linha de comandos elevada (**executar como administrador**).
2. Verifique os dados de configuração de arranque. Os seguintes comandos, partimos do pressuposto que a letra de unidade que está atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.

        bcdedit /store F:\boot\bcd /enum
    Tome nota do nome do identificador da partição que tenha as **\windows** pasta. Por predefinição, o nome do identificador é "Padrão".

    Se a VM está configurada para inicializar em modo de segurança, verá um sinalizador extra sob os **carregador de inicialização do Windows** secção denominada **inicialização segura**. Se não vir a **inicialização segura** sinalizador, este artigo não é aplicável ao seu cenário.

    ![A imagem sobre o identificador de arranque](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remover os **inicialização segura** sinalizar, para que a VM será arrancada no modo normal:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Verifique os dados de configuração de arranque para se certificar de que o **inicialização segura** sinalizador é removido:

        bcdedit /store F:\boot\bcd /enum
5. [Desanexar o disco do SO e recriar a VM](../windows/troubleshoot-recovery-disks-portal.md). Em seguida, verifique se o problema foi resolvido.
