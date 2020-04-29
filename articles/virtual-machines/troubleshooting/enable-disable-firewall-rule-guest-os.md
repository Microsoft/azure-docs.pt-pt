---
title: Ativar ou desativar uma regra de firewall num osso de hóspedes em Azure VM [ Microsoft Docs
description: Saiba como utilizar ferramentas remotas on-line ou offline ou configurações de registo para ativar ou desativar as regras de firewall do OS de um VM Otário remoto.
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
ms.openlocfilehash: e93dbd085ce99b8d555d6b9bb04e7eb6f60de0ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422902"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Ativar ou desativar uma regra de firewall num SO convidado da VM do Azure

Este artigo fornece uma referência para resolver problemas numa situação em que suspeita que a firewall do sistema operativo do hóspede está a filtrar o tráfego parcial numa máquina virtual (VM). Isto pode ser útil pelas seguintes razões:

*   Se foi deliberadamente feita uma alteração na firewall que fez com que as ligações RDP falhassem, a utilização da função de extensão de script personalizado pode resolver o problema.

*   Desativar todos os perfis de firewall é uma forma mais infalível de resolução de problemas do que definir a regra de firewall específica do RDP.

## <a name="solution"></a>Solução

A configuração das regras de firewall depende do nível de acesso ao VM necessário. Os exemplos que se seguem utilizam as regras do PDR. No entanto, os mesmos métodos podem ser aplicados a qualquer outro tipo de tráfego, apontando para a chave de registo correta.

### <a name="online-troubleshooting"></a>Resolução de problemas online 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigação 1: Extensão personalizada do script

1.  Crie o seu script utilizando o seguinte modelo.

    *   Para viabilizar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desativar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Faça upload deste script no portal Azure utilizando a funcionalidade de extensão do [script personalizado.](../extensions/custom-script-windows.md) 

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

Se o VM estiver on-line e puder ser acedido em outro VM na mesma rede virtual, pode então fazer as seguintes atenuações utilizando o outro VM.

1.  No contra-problema vM, abra uma janela de consola PowerShell.

2.  Executar os seguintes comandos, conforme apropriado.

    *   Para viabilizar uma regra:
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

Se o VM estiver on-line e puder ser acedido em outro VM na mesma rede virtual, pode então fazer as seguintes atenuações utilizando o outro VM.

1.  No contra-problema saturado vM, baixe [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância CMD e aceda ao VM através do seu IP Interno (DIP). 

    * Para viabilizar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desativar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Mitigação 4: Registo Remoto

Se o VM estiver on-line e puder ser acedido noutro VM na mesma rede virtual, pode utilizar o [Registo Remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) no outro VM.

1.  No VM de resolução de problemas, inicie o Registry Editor (regedit.exe) e, em seguida, selecione **File** > **Connect Network Registry**.

2.  Abra o ramo TARGET *MACHINE*\SYSTEM e, em seguida, especifique os seguintes valores:

    * Para permitir uma regra, abra o seguinte valor de registo:
    
        *MÁQUINA ALVO*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Em seguida, mude **Ative=FALSE** para **Ative=TRUE** na cadeia:

        **v2.22. Action=Allow[ Ativo=TRUE! Dir=In/ Protocolo=6] Perfil=Domínio! Perfil=Privado! Perfil=Público! LPort=3389/ App=%SystemRoot%\system32\svchost.exe Svc=termservice! Nome=\@FirewallAPI.dll,-28775 Desc=\@FirewallAPI.dll,-28756 InbedCtxt=\@FirewallAPI.dll,-28752/**
    
    * Para desativar uma regra, abra o seguinte valor de registo:
    
        *MÁQUINA ALVO*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, altere **Ativo =TRUE** to **Ative=FALSE:**
        
        **v2.22. Action=Allow[ Ativo=FALSO! Dir=In/ Protocolo=6] Perfil=Domínio! Perfil=Privado! Perfil=Público! LPort=3389/ App=%SystemRoot%\system32\svchost.exe Svc=termservice! Nome=\@FirewallAPI.dll,-28775 Desc=\@FirewallAPI.dll,-28756 InbedCtxt=\@FirewallAPI.dll,-28752/**

3.  Reiniciar o VM para aplicar as alterações.

### <a name="offline-troubleshooting"></a>Resolução de problemas offline 

Se não conseguir aceder ao VM por qualquer método, a utilização da extensão do script personalizado falhará e terá de trabalhar no modo OFFLINE trabalhando diretamente através do disco do sistema.

Antes de seguir estes passos, tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

1.  [Fixe o disco do sistema a um VM](troubleshoot-recovery-disks-portal-windows.md)de recuperação .

2.  Inicie uma ligação remote Desktop ao VM de recuperação.

3.  Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note que a letra de unidade que é atribuída ao disco do sistema anexo.

4.  Antes de efetuar alterações, crie uma cópia da pasta \windows\system32\config no caso de ser necessária uma reversão das alterações.

5.  Na resolução de problemas vM, inicie registry editor (regedit.exe).

6.  Realce a chave **HKEY_LOCAL_MACHINE** e, em seguida, selecione **File** > **Load Hive** do menu.

    ![Regedite](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Localizar e, em seguida, abrir o ficheiro \windows\system32\config\SYSTEM. 

    > [!Note]
    > Foi solicitado um nome. Introduza o **SISTEMA BROKENSYSTEM**e, em seguida, expanda **HKEY_LOCAL_MACHINE**. Agora verá uma chave adicional que se chama **BROKENSYSTEM**. Para esta resolução de problemas, estamos a montar estas colmeias problemáticas como **BROKENSYSTEM**.

8.  Faça as seguintes alterações no ramo BROKENSYSTEM:

    1.  Verifique a chave de registo **do ControlSet** a partir da qual o VM está a começar. Verá o seu número chave no HKLM\BROKENSYSTEM\Select\Current.

    2.  Para permitir uma regra, abra o seguinte valor de registo:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Em seguida, mude **Ative=FALSE** para **Ative=True**.
        
        **v2.22. Action=Allow[ Ativo=TRUE! Dir=In/ Protocolo=6] Perfil=Domínio! Perfil=Privado! Perfil=Público! LPort=3389/ App=%SystemRoot%\system32\svchost.exe Svc=termservice! Nome=\@FirewallAPI.dll,-28775 Desc=\@FirewallAPI.dll,-28756 InbedCtxt=\@FirewallAPI.dll,-28752/**

    3.  Para desativar uma regra, abra a seguinte chave de registo:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parâmetros\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, mude **Ative=True** to **Ative=FALSE**.
        
        **v2.22. Action=Allow[ Ativo=FALSO! Dir=In/ Protocolo=6] Perfil=Domínio! Perfil=Privado! Perfil=Público! LPort=3389/ App=%SystemRoot%\system32\svchost.exe Svc=termservice! Nome=\@FirewallAPI.dll,-28775 Desc=\@FirewallAPI.dll,-28756 InbedCtxt=\@FirewallAPI.dll,-28752/**

9.  Realce **BROKENSYSTEM**, e, em seguida, selecione **File** > **Unload Hive** do menu.

10. [Desmontar o disco do sistema e recriar o VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema está resolvido.
