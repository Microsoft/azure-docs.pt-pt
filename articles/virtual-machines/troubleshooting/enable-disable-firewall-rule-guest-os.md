---
title: Ativar ou desativar uma regra de firewall num so convidado em Azure VM ; Microsoft Docs
description: Aprenda a utilizar ferramentas remotas on-line ou offline ou configurações de registo para ativar ou desativar as regras de firewall do OS do hóspede num Azure VM remoto.
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
ms.openlocfilehash: 17616a223292ec07186b0a3fba264400423977ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87058755"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Ativar ou desativar uma regra de firewall num SO convidado da VM do Azure

Este artigo fornece uma referência para a resolução de problemas de uma situação em que suspeita que a firewall do sistema operativo de hóspedes está a filtrar o tráfego parcial numa máquina virtual (VM). Isto pode ser útil pelas seguintes razões:

*   Se foi deliberadamente feita uma alteração na firewall que fez com que as ligações RDP falhassem, a utilização da funcionalidade de Extensão de Script Personalizado pode resolver o problema.

*   Desativar todos os perfis de firewall é uma forma mais infalível de resolução de problemas do que definir a regra de firewall específica do RDP.

## <a name="solution"></a>Solução

A configuração das regras de firewall depende do nível de acesso ao VM que é necessário. Os exemplos a seguir utilizam as regras RDP. No entanto, os mesmos métodos podem ser aplicados a qualquer outro tipo de tráfego apontando para a chave de registo correta.

### <a name="online-troubleshooting"></a>Resolução de problemas on-line 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigação 1: Extensão do script personalizado

1.  Crie o seu script utilizando o seguinte modelo.

    *   Para ativar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desativar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Faça upload deste script no portal Azure utilizando a funcionalidade [de Extensão de Script Personalizado.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

Se o VM estiver online e puder ser acedido em outro VM na mesma rede virtual, pode fazer as seguintes mitigações utilizando o outro VM.

1.  No VM de resolução de problemas, abra uma janela da consola PowerShell.

2.  Executar os seguintes comandos, conforme apropriado.

    *   Para ativar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Para desativar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: Comandos PSTools

Se o VM estiver online e puder ser acedido em outro VM na mesma rede virtual, pode fazer as seguintes mitigações utilizando o outro VM.

1.  No VM de resolução de problemas, descarregue [PSTools](/sysinternals/downloads/pstools).

2.  Abra uma instância CMD e aceda ao VM através do seu IP Interno (DIP). 

    * Para ativar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desativar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Mitigação 4: Registo remoto

Se o VM estiver online e puder ser acedido em outro VM na mesma rede virtual, pode utilizar o [Registo Remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) no outro VM.

1.  No VM de resolução de problemas, inicie o Editor de Registo (regedit.exe) e, em seguida, selecione Registo de Rede de Ligação **de**  >  **Ficheiros**.

2.  Abra a *máquina alvo*\ramo SISTEMA e, em seguida, especifique os seguintes valores:

    * Para ativar uma regra, abra o seguinte valor de registo:
    
        *MÁQUINA ALVO*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Em seguida, altere **Ative=FALSE** para **Ative=TRUE** na cadeia:

        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`
    
    * Para desativar uma regra, abra o seguinte valor de registo:
    
        *MÁQUINA ALVO*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, **altere Ative =TRUE** para **Ative=FALSE**:
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

3.  Reinicie o VM para aplicar as alterações.

### <a name="offline-troubleshooting"></a>Resolução de problemas offline 

Se não conseguir aceder ao VM por qualquer método, a utilização da Extensão de Script Personalizado falhará e terá de funcionar em modo OFFLINE, funcionando diretamente através do disco do sistema.

Antes de seguir estes passos, tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).

1.  [Ligue o disco do sistema a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.

3.  Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note que a letra de acionamento que é atribuída ao disco do sistema anexo.

4.  Antes de escoar quaisquer alterações, crie uma cópia da pasta \windows\system32\config no caso de ser necessário um reversão das alterações.

5.  No VM de resolução de problemas, inicie o Editor de Registo (regedit.exe).

6.  Realce a **tecla HKEY_LOCAL_MACHINE** e, em seguida, selecione a Colmeia de Carga de **Ficheiros**  >  **Load Hive** no menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Localize e, em seguida, abra o ficheiro \windows\system32\config\SYSTEM. 

    > [!Note]
    > É solicitado um nome. Insira **o BROKENSYSTEM**e, em seguida, expanda **HKEY_LOCAL_MACHINE**. Verá agora uma chave adicional chamada **BROKENSYSTEM**. Para esta resolução de problemas, estamos a montar estas colmeias problemáticas como **BROKENSYSTEM**.

8.  Es faça as seguintes alterações no ramo BROKENSYSTEM:

    1.  Verifique qual a chave de registo **do ControlSet** da qual o VM está a começar. Verá o seu número-chave em HKLM\BROKENSYSTEM\Select\Current.

    2.  Para ativar uma regra, abra o seguinte valor de registo:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Em seguida, altere **Ative=FALSE** para **Ative=True**.
        
        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

    3.  Para desativar uma regra, abra a seguinte chave de registo:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, **altere Ative=True** to **Ative=FALSE**.
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

9.  Realce **o BROKENSYSTEM**e, em seguida, selecione A Colmeia de Descarregamento de **File**  >  **Ficheiros** a partir do menu.

10. [Retire o disco do sistema e recobri o VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se a questão está resolvida.
