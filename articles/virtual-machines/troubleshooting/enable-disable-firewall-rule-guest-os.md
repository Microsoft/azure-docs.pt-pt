---
title: Habilitar ou desabilitar uma regra de firewall em um sistema operacional convidado na VM do Azure | Microsoft Docs
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086440"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Habilitar ou desabilitar uma regra de firewall em um SO convidado de VM do Azure

Este artigo fornece uma referência para solucionar uma situação em que você suspeita de que o Firewall do sistema operacional convidado está filtrando o tráfego parcial em uma VM (máquina virtual). Isso pode ser útil pelos seguintes motivos:

*   Se uma alteração foi deliberadamente feita no firewall que fazia com que as conexões RDP falhassem, o uso do recurso de extensão de script personalizado pode resolver o problema.

*   Desabilitar todos os perfis de firewall é uma maneira mais infalível de solucionar problemas do que definir a regra de firewall específica de RDP.

## <a name="solution"></a>Solução

A maneira como você configura as regras de firewall depende do nível de acesso à VM necessária. Os exemplos a seguir usam regras de RDP. No entanto, os mesmos métodos podem ser aplicados a qualquer outro tipo de tráfego apontando para a chave do registro correta.

### <a name="online-troubleshooting"></a>Resolução de problemas online 

#### <a name="mitigation-1-custom-script-extension"></a>Mitigação 1: Extensão de Script Personalizado

1.  Crie seu script usando o modelo a seguir.

    *   Para habilitar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desabilitar uma regra:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Carregue esse script no portal do Azure usando o recurso de [extensão de script personalizado](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Mitigação 2: PowerShell remoto

Se a VM estiver online e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer as atenuações a seguir usando a outra VM.

1.  Na VM de solução de problemas, abra uma janela de console do PowerShell.

2.  Execute os comandos a seguir, conforme apropriado.

    *   Para habilitar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Para desabilitar uma regra:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Mitigação 3: Comandos PSTools

Se a VM estiver online e puder ser acessada em outra VM na mesma rede virtual, você poderá fazer as atenuações a seguir usando a outra VM.

1.  Na VM de solução de problemas, baixe o [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Abra uma instância do CMD e acesse a VM por meio de seu DIP (IP interno). 

    * Para habilitar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Para desabilitar uma regra:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Atenuação 4: Registro remoto

Se a VM estiver online e puder ser acessada em outra VM na mesma rede virtual, você poderá usar o [registro remoto](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) na outra VM.

1.  Na VM de solução de problemas, inicie o editor do registro (regedit. exe) e selecione **arquivo** > **conectar registro de rede**.

2.  Abra a ramificação \System do *computador de destino*e especifique os seguintes valores:

    * Para habilitar uma regra, abra o seguinte valor de registro:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Em seguida, altere **ativo = falso** para **ativo = verdadeiro** na cadeia de caracteres:

        **v 2.22 | Ação = permitir | Ativo = verdadeiro | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**
    
    * Para desabilitar uma regra, abra o seguinte valor de registro:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, altere **ativo = verdadeiro** para **ativo = falso**:
        
        **v 2.22 | Ação = permitir | Ativo = falso | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

3.  Reinicie a VM para aplicar as alterações.

### <a name="offline-troubleshooting"></a>Solução de problemas offline 

Se você não puder acessar a VM por qualquer método, o uso da extensão de script personalizado falhará e você terá que trabalhar no modo OFFLINE trabalhando diretamente por meio do disco do sistema.

Antes de seguir estes passos, tire um instantâneo do disco de sistema da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

1.  [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).

2.  Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

3.  Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Observe que a letra da unidade atribuída ao disco do sistema anexado.

4.  Antes de fazer qualquer alteração, crie uma cópia da pasta \Windows\System32\config, caso uma reversão das alterações seja necessária.

5.  Na VM de solução de problemas, inicie o editor do registro (regedit. exe).

6.  Realce a chave **HKEY_LOCAL_MACHINE** e, em seguida, selecione **arquivo** > **Carregar Hive** no menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Localize e abra o arquivo \windows\system32\config\SYSTEM. 

    > [!Note]
    > Um nome será solicitado. Insira **BROKENSYSTEM**e expanda **HKEY_LOCAL_MACHINE**. Agora, você verá uma chave adicional chamada **BROKENSYSTEM**. Para essa solução de problemas, estamos montando essas seções de problema como **BROKENSYSTEM**.

8.  Faça as seguintes alterações na ramificação BROKENSYSTEM:

    1.  Verifique a chave do registro de **ControlSet** da qual a VM está sendo iniciada. Você verá seu número de chave em HKLM\BROKENSYSTEM\Select\Current.

    2.  Para habilitar uma regra, abra o seguinte valor de registro:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Em seguida, altere **ativo = falso** para **ativo = verdadeiro**.
        
        **v 2.22 | Ação = permitir | Ativo = verdadeiro | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

    3.  Para desabilitar uma regra, abra a seguinte chave do registro:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Em seguida, altere **ativo = verdadeiro** para **ativo = falso**.
        
        **v 2.22 | Ação = permitir | Ativo = falso | Dir = in | Protocolo = 6 | Perfil = domínio | Perfil = privado | Perfil = público | LPort = 3389 | Aplicativo =%SystemRoot%\system32\svchost.exe | SVC = TermService | Nome =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | EmbedCtxt =\@FirewallAPI. dll,-28752 |**

9.  Realce **BROKENSYSTEM**e, em seguida, selecione **arquivo** > **Descarregar Hive** no menu.

10. [Desanexar o disco do sistema e voltar a criar a VM](troubleshoot-recovery-disks-portal-windows.md).

11. Verifique se o problema está resolvido.
