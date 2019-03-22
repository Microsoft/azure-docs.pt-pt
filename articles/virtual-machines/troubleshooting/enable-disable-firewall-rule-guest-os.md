---
title: Ativar ou desativar uma regra de firewall num sistema operacional convidado na VM do Azure | Documentos da Microsoft
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
ms.openlocfilehash: ed3d89bc15f960947a48ac4364bd14f3fdf50cc2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853074"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Ativar ou desativar uma regra de firewall num SO de convidado de VM do Azure

Este artigo fornece uma referência para resolução de problemas de uma situação em que suspeitar de que a firewall do sistema operativo convidado está a filtrar o tráfego parcial numa máquina virtual (VM). Isto pode ser útil pelos seguintes motivos:

*   Se deliberadamente foi feita uma alteração à firewall que causou a ligações de RDP efetuar a ativação, usando o recurso de extensão de Script personalizado pode resolver o problema.

*   Desabilitar todos os perfis de firewall é uma forma mais segura de resolução de problemas que definir a regra de firewall do RDP específicas.

## <a name="solution"></a>Solução

Como configurar as regras de firewall depende do nível de acesso à VM que é necessário. Os exemplos seguintes utilizam regras RDP. No entanto, os mesmos métodos podem ser aplicados a qualquer outro tipo de tráfego ao apontar para a chave de registo correto.

### <a name="online-troubleshooting"></a>Resolução de problemas online 

#### <a name="mitigation-1-custom-script-extension"></a>Atenuação 1: Extensão de Script Personalizado

1.  Crie o script com o modelo seguinte.

    *   Para ativar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desativar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Carregar este script no portal do Azure com o [extensão de Script personalizado](../extensions/custom-script-windows.md) funcionalidade. 

#### <a name="mitigation-2-remote-powershell"></a>Atenuação 2: PowerShell remoto

Se a VM está online e pode ser acessada em outra VM na mesma rede virtual, pode efetuar as atenuações de seguir utilizando a outra VM.

1.  Na VM de resolução de problemas, abra uma janela de consola do PowerShell.

2.  Execute os comandos seguintes, conforme apropriado.

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

#### <a name="mitigation-3-pstools-commands"></a>Atenuação 3: Comandos do PSTools

Se a VM está online e pode ser acessada em outra VM na mesma rede virtual, pode efetuar as atenuações de seguir utilizando a outra VM.

1.  A VM de resolução de problemas, transferi [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância CMD e aceder à VM através de seu IP (DIP interno). 

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

#### <a name="mitigation-4-remote-registry"></a>Atenuação 4: Registo remoto

Se a VM está online e pode ser acessada em outra VM na mesma rede virtual, pode utilizar [registo remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na outra VM.

1.  Na VM de resolução de problemas, inicie o Editor de registo (regedit.exe) e, em seguida, selecione **arquivo** > **registo de rede ligar**.

2.  Abra o *máquina de destino*\SYSTEM ramificação e, em seguida, especifique os seguintes valores:

    * Para ativar uma regra, abra o valor de registo seguinte:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Em seguida, altere **Active Directory = FALSE** ao **Active Directory = TRUE** na cadeia de caracteres:

        **v2.22 | Ação = permitir | Active Directory = TRUE | Dir = In | Protocolo = 6 | Perfil = domínio | Perfil = privada | Perfil público de = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nome =\@Firewallapi,-28775 | Desc =\@Firewallapi,-28756 | EmbedCtxt =\@Firewallapi,-28752 |**
    
    * Para desativar uma regra, abra o valor de registo seguinte:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, altere **Active Directory = TRUE** ao **Active Directory = FALSE**:
        
        **v2.22 | Ação = permitir | Active Directory = FALSE | Dir = In | Protocolo = 6 | Perfil = domínio | Perfil = privada | Perfil público de = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nome =\@Firewallapi,-28775 | Desc =\@Firewallapi,-28756 | EmbedCtxt =\@Firewallapi,-28752 |**

3.  Reinicie a VM para aplicar as alterações.

### <a name="offline-troubleshooting"></a>Resolução de problemas offline 

Se não conseguir aceder a VM por qualquer método, usando a extensão de Script personalizado irá falhar e terá de trabalhar no modo OFFLINE, ao trabalhar diretamente com o disco do sistema.

Antes de seguir estes passos, tire um instantâneo do disco de sistema da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção que a unidade de letra que é atribuída para o disco do sistema anexado.

4.  Antes de fazer quaisquer alterações, crie uma cópia da pasta \windows\system32\config no caso de uma reversão das alterações necessária.

5.  Na VM de resolução de problemas, inicie o Editor de registo (regedit.exe).

6.  Realce o **HKEY_LOCAL_MACHINE** da chave e, em seguida, selecione **ficheiro** > **carregar Hive** no menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Localize e, em seguida, abra o ficheiro de \windows\system32\config\SYSTEM. 

    > [!Note]
    > São-lhe pedido para um nome. Introduza **BROKENSYSTEM**e, em seguida, expanda **HKEY_LOCAL_MACHINE**. Agora verá uma chave adicional denominada **BROKENSYSTEM**. Para esta solução de problemas, podemos montar esses hives do problema como **BROKENSYSTEM**.

8.  Efetue as seguintes alterações no ramo BROKENSYSTEM:

    1.  Verifique qual **ControlSet** a VM é a partir de chave de registo. Verá o respetivo número de chave na hklm\brokensystem\select\current.

    2.  Para ativar uma regra, abra o valor de registo seguinte:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Em seguida, altere **Active Directory = FALSE** ao **Active Directory = True**.
        
        **v2.22 | Ação = permitir | Active Directory = TRUE | Dir = In | Protocolo = 6 | Perfil = domínio | Perfil = privada | Perfil público de = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nome =\@Firewallapi,-28775 | Desc =\@Firewallapi,-28756 | EmbedCtxt =\@Firewallapi,-28752 |**

    3.  Para desativar uma regra, abra a seguinte chave de registo:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, altere **Active Directory = True** ao **Active Directory = FALSE**.
        
        **v2.22 | Ação = permitir | Active Directory = FALSE | Dir = In | Protocolo = 6 | Perfil = domínio | Perfil = privada | Perfil público de = | LPort = 3389 | App=%systemroot%\system32\svchost.exe| SVC = termservice | Nome =\@Firewallapi,-28775 | Desc =\@Firewallapi,-28756 | EmbedCtxt =\@Firewallapi,-28752 |**

9.  Realçar **BROKENSYSTEM**e, em seguida, selecione **ficheiro** > **descarregar seção** no menu.

10. [Desanexar o disco do sistema e voltar a criar a VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema está resolvido.
