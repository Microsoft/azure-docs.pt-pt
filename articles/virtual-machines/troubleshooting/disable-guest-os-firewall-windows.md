---
title: Desativar o convidado Firewall do SO na VM do Azure | Documentos da Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820030"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Desativar a Firewall do SO convidado na VM do Azure

Este artigo fornece uma referência para situações em que suspeitar de que a firewall do sistema operativo convidado está a filtrar o tráfego parcial ou completado para uma máquina virtual (VM). Isto pode acontecer se deliberadamente foram feitas alterações à firewall que causou a ligações de RDP efetuar a ativação.

## <a name="solution"></a>Solução

O processo descrito neste artigo destina-se para ser utilizado como uma solução alternativa para que possa se concentrar em corrigir o problema real, o que é como configurar as regras de firewall corretamente. It\rquote s uma prática recomendada pela Microsoft para ter o componente de Firewall do Windows ativado. Como configurar o firewall regras \cf3 depende do nível de acesso para o s that\rquote VM necessário.

### <a name="online-solutions"></a>Soluções online 

Se a VM está online e pode ser acessada em outra VM na mesma rede virtual, pode fazer essas atenuações utilizando a outra VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Atenuação 1: Recurso personalizado de extensão de Script ou comando executado

Se tiver um agente do Azure em funcionamento, pode utilizar [extensão de Script personalizado](../extensions/custom-script-windows.md) ou o [executar comandos](../windows/run-command.md) funcionalidade (apenas para VMs do Resource Manager) para executar os seguintes scripts remotamente.

> [!Note]
> * Se a firewall está definida localmente, execute o seguinte script:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Se a firewall está definida por meio de uma política do Active Directory, pode utilizar execute o seguinte script para acesso temporário. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   No entanto, assim que a política é aplicada novamente, vai ser iniciada fora a sessão remota. A correção permanente para este problema é modificar a política que é aplicada neste computador.

#### <a name="mitigation-2-remote-powershell"></a>Atenuação 2: PowerShell remoto

1.  Ligar a uma VM que está localizada na mesma rede virtual da VM que não é possível aceder ao utilizar a ligação RDP.

2.  Abra uma janela de consola do PowerShell.

3.  Execute os seguintes comandos:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Se a firewall está definida por meio de um objeto de política de grupo, este método pode não funcionar porque este comando altera apenas as entradas de registo local. Se uma política estiver em vigor, irá substituir esta alteração. 

#### <a name="mitigation-3-pstools-commands"></a>Atenuação 3: Comandos do PSTools

1.  A VM de resolução de problemas, transferi [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância CMD e, em seguida, aceder à VM através do respetivo DIP.

3.  Execute os seguintes comandos:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Atenuação 4: Registo remoto 

Siga estas etapas para usar [registo remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Na VM de resolução de problemas, inicie o editor de registo e, em seguida, aceda a **arquivo** > **registo de rede ligar**.

2.  Abra o *máquina de destino*\SYSTEM ramificação e especifique os seguintes valores:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Reinicie o serviço. Porque não é possível fazê-lo através do registo remoto, é necessário utilizar a consola do serviço de remover.

4.  Abra uma instância de **Services. msc**.

5.  Clique em **serviços (Local)**.

6.  Selecione **ligar a outro computador**.

7.  Introduza o **endereço de IP privado (DIP)** do problema VM.

8.  Reinicie a política de local firewall.

9.  Tente ligar novamente à VM através do RDP do computador local.

### <a name="offline-solutions"></a>Soluções offline 

Se tiver uma situação em que não é possível alcançar a VM por qualquer método, extensão de Script personalizado irá falhar e terá de trabalhar no modo OFFLINE, ao trabalhar diretamente com o disco do sistema. Para tal, siga estes passos:

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Certifique-se de que o disco é sinalizado como Online no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída para o disco do sistema anexado.

4.  Antes de fazer quaisquer alterações, crie uma cópia da pasta \windows\system32\config no caso de uma reversão das alterações necessária.

5.  Na VM de resolução de problemas, inicie o editor de registo (regedit.exe). 

6.  Para este procedimento de resolução de problemas, podemos montar os hives como BROKENSYSTEM e BROKENSOFTWARE.

7.  Realce a chave HKEY_LOCAL_MACHINE e, em seguida, selecione ficheiro > carregar Hive no menu.

8.  Localize o ficheiro de \windows\system32\config\SYSTEM no disco do sistema anexado.

9.  Abra uma instância elevada do PowerShell e, em seguida, execute os seguintes comandos:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Desanexar o disco do sistema e voltar a criar a VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema está resolvido.
