---
title: Desative o hóspede OS Firewall em Azure VM [ Microsoft Docs
description: Aprenda um método de suver para situações de resolução de problemas em que uma firewall do sistema operativo de hóspedes esteja a filtrar o tráfego parcial ou completo para um VM.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e4cd1595d963330bd5decb366310bf5e97f59bc8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422365"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Desativar a Firewall do SO convidado na VM do Azure

Este artigo fornece uma referência para situações em que suspeita que a firewall do sistema operativo do hóspede está a filtrar o tráfego parcial ou completo para uma máquina virtual (VM). Isto poderia ocorrer se fossem deliberadamente feitas alterações na firewall que fez com que as ligações RDP falhassem.

## <a name="solution"></a>Solução

O processo descrito neste artigo destina-se a ser usado como uma suposição de suposição para que possa focar-se na correção do seu problema real, que é como configurar corretamente as regras da firewall. É uma Aplicação Microsoft Best prática ter o componente Windows Firewall ativado. A configuração das regras de firewall depende do nível de acesso ao VM que é necessário.

### <a name="online-solutions"></a>Soluções Online 

Se o VM estiver on-line e puder ser acedido em outro VM na mesma rede virtual, pode então fazer estas mitigações utilizando o outro VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Mitigação 1: Extensão de script personalizado ou recurso de comando de execução

Se tiver um agente Azure a funcionar, pode utilizar a [extensão do script personalizado](../extensions/custom-script-windows.md) ou a funcionalidade [Comandos de Execução](../windows/run-command.md) (apenas VMs do Gestor de Recursos) para executar remotamente os seguintes scripts.

> [!Note]
> * Se a firewall estiver definida localmente, execute o seguinte script:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Se a firewall for definida através de uma política de Diretório Ativo, pode utilizar o seguinte script para acesso temporário. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   No entanto, assim que a apólice for aplicada novamente, será expulso da sessão remota. A solução permanente para este problema é modificar a política aplicada neste computador.

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

1.  Ligue-se a um VM que está localizado na mesma rede virtual que o VM que não pode alcançar utilizando a ligação RDP.

2.  Abra uma janela de consola PowerShell.

3.  Execute os seguintes comandos:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Se a firewall for definida através de um Objeto de Política de Grupo, este método pode não funcionar porque este comando muda apenas as entradas de registo local. Se houver uma política, anulará esta mudança. 

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: Comandos PSTools

1.  No contra-problema saturado vM, baixe [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância CMD e, em seguida, aceda ao VM através do seu DIP.

3.  Execute os seguintes comandos:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Mitigação 4: Registo Remoto 

Siga estes passos para utilizar o [Registo Remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  No vM de resolução de problemas, inicie o editor de registo, e depois vá ao Registo de Rede de**Rede De Conecteção** **de Ficheiros** > .

2.  Abra o ramo TARGET *MACHINE*\SYSTEM e especifique os seguintes valores:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Reinicie o serviço. Como não pode fazê-lo utilizando o registo remoto, deve utilizar a Consola de Serviço Remoto.

4.  Abra uma instância de **Services.msc**.

5.  Clique em **Serviços (Local)**.

6.  Selecione **Ligar a outro computador**.

7.  Introduza o **endereço IP privado (DIP)** do problema VM.

8.  Reinicie a política local de firewall.

9.  Tente ligar-se ao VM através de RDP novamente a partir do seu computador local.

### <a name="offline-solutions"></a>Soluções Offline 

Se tiver uma situação em que não possa chegar ao VM por qualquer método, a extensão do script personalizado falhará e terá de trabalhar no modo OFFLINE trabalhando diretamente através do disco do sistema. Para tal, siga estes passos:

1.  [Fixe o disco do sistema a um VM](troubleshoot-recovery-disks-portal-windows.md)de recuperação .

2.  Inicie uma ligação remote Desktop ao VM de recuperação.

3.  Certifique-se de que o disco está sinalizado como Online na consola de Gestão de Discos. Repare na carta de unidade atribuída ao disco do sistema anexo.

4.  Antes de efetuar alterações, crie uma cópia da pasta \windows\system32\config no caso de ser necessária uma reversão das alterações.

5.  Na vm de resolução de problemas, inicie o editor de registo (regedit.exe). 

6.  Para este procedimento de resolução de problemas, estamos a montar as colmeias como BROKENSYSTEM e BROKENSOFTWARE.

7.  Realce a chave HKEY_LOCAL_MACHINE e, em seguida, selecione File > Load Hive do menu.

8.  Localize o ficheiro \windows\system32\config\SYSTEM no disco do sistema anexo.

9.  Abra uma instância powerShell elevada e, em seguida, executar os seguintes comandos:

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
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
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

10. [Desmontar o disco do sistema e recriar o VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema está resolvido.
