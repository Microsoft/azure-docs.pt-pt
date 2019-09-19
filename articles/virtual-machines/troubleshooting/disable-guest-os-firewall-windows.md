---
title: Desabilitar o Firewall do sistema operacional convidado na VM do Azure | Microsoft Docs
description: ''
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
ms.openlocfilehash: 9ae8620b803fa9a911f44840a5fff5d190a316a1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086537"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Desativar a Firewall do SO convidado na VM do Azure

Este artigo fornece uma referência para situações em que você suspeita que o Firewall do sistema operacional convidado está filtrando o tráfego parcial ou completo para uma VM (máquina virtual). Isso pode ocorrer se as alterações foram deliberadamente feitas no firewall que fizeram com que as conexões RDP falhassem.

## <a name="solution"></a>Solução

O processo descrito neste artigo destina-se a ser usado como uma solução alternativa para que você possa se concentrar na correção de seu problema real, que é como configurar corretamente as regras de firewall. It\rquote s uma prática recomendada da Microsoft para que o componente Firewall do Windows esteja habilitado. A maneira como você configura as regras de firewall \cf3 depende do nível de acesso para a VM that\rquote s necessária.

### <a name="online-solutions"></a>Soluções online 

Se a VM estiver online e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer essas atenuações usando a outra VM.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Mitigação 1: Extensão de script personalizado ou recurso de executar comando

Se você tiver um agente do Azure em funcionamento, poderá usar a [extensão de script personalizado](../extensions/custom-script-windows.md) ou o recurso [executar comandos](../windows/run-command.md) (somente VMs do Gerenciador de recursos) para executar remotamente os scripts a seguir.

> [!Note]
> * Se o firewall estiver definido localmente, execute o seguinte script:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Se o firewall estiver definido por meio de uma política de Active Directory, você poderá usar executar o script a seguir para acesso temporário. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   No entanto, assim que a política for aplicada novamente, você será inicializado da sessão remota. A correção permanente para esse problema é modificar a política aplicada neste computador.

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

1.  Conecte-se a uma VM que está localizada na mesma rede virtual que a VM que você não pode acessar usando a conexão RDP.

2.  Abra uma janela de consola do PowerShell.

3.  Execute os seguintes comandos:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Se o firewall for definido por meio de um objeto Política de Grupo, esse método poderá não funcionar porque esse comando altera apenas as entradas do Registro local. Se uma política estiver em vigor, ela substituirá essa alteração. 

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: Comandos PSTools

1.  Na VM de solução de problemas, baixe o [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância do CMD e, em seguida, acesse a VM por meio de seu DIP.

3.  Execute os seguintes comandos:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Atenuação 4: Registro remoto 

Siga estas etapas para usar o [registro remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  Na VM de solução de problemas, inicie o editor do registro e vá para **arquivo** > **conectar registro de rede**.

2.  Abra a ramificação \System do *computador de destino*e especifique os seguintes valores:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Reinicie o serviço. Como não é possível fazer isso usando o registro remoto, você deve usar remover console de serviço.

4.  Abra uma instância de **Services. msc**.

5.  Clique em **Serviços (local)** .

6.  Selecione **conectar a outro computador**.

7.  Insira o **endereço IP privado (DIP)**  da VM com problema.

8.  Reinicie a política de firewall local.

9.  Tente se conectar à VM por meio do RDP novamente no computador local.

### <a name="offline-solutions"></a>Soluções offline 

Se você tiver uma situação em que não consiga acessar a VM por qualquer método, a extensão de script personalizado falhará e você terá que trabalhar no modo OFFLINE trabalhando diretamente pelo disco do sistema. Para tal, siga estes passos:

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Verifique se o disco está sinalizado como online no console de gerenciamento de disco. Observe a letra da unidade atribuída ao disco do sistema anexado.

4.  Antes de fazer qualquer alteração, crie uma cópia da pasta \Windows\System32\config, caso uma reversão das alterações seja necessária.

5.  Na VM de solução de problemas, inicie o editor do registro (regedit. exe). 

6.  Para este procedimento de solução de problemas, estamos montando os hives como BROKENSYSTEM e BROKENSOFTWARE.

7.  Realce a chave HKEY_LOCAL_MACHINE e, em seguida, selecione Arquivo > Carregar Hive no menu.

8.  Localize o arquivo \windows\system32\config\SYSTEM no disco do sistema anexado.

9.  Abra uma instância do PowerShell com privilégios elevados e execute os seguintes comandos:

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
