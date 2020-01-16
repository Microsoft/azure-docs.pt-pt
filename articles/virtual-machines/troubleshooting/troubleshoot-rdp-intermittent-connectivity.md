---
title: Área de Trabalho Remota desconexões com frequência na VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas de desconexões frequentes do Área de Trabalho Remota na VM do Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 636973110e11770e33c635e312c86b25110705da
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981340"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Área de Trabalho Remota desconexões frequentemente na VM do Azure

Este artigo explica como solucionar problemas de desconexões frequentes para uma VM (máquina virtual) do Azure por meio de protocolo RDP RDP).

> [!NOTE] 
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação do Gerenciador de recursos. Recomendamos que você use esse modelo para novas implantações em vez de usar o modelo de implantação clássico.

## <a name="symptom"></a>Sintoma

Você enfrenta problemas intermitentes de conectividade de RDP durante suas sessões. Inicialmente, você pode se conectar à VM, mas a conexão cai.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se o ouvinte RDP estiver configurado incorretamente. Normalmente, esse problema ocorre em uma VM que usa uma imagem personalizada.

## <a name="solution"></a>Solução

Antes de seguir essas etapas, [tire um instantâneo do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) da VM afetada como um backup. 

Para solucionar esse problema, use o controle serial ou [Repare a VM offline](#repair-the-vm-offline) anexando o disco do sistema operacional da VM a uma VM de recuperação.

### <a name="serial-control"></a>Controle de série

1. Ligar à [consola de série e Abrir instância CMD](./serial-console-windows.md). Em seguida, execute os comandos a seguir para redefinir as configurações de RDP. Se o console serial não estiver habilitado em sua VM, vá para a próxima etapa.
2. Reduza a camada de segurança RDP para 0. Nessa configuração, as comunicações entre servidor e cliente usam a criptografia RDP nativa.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Reduza o nível de criptografia para a configuração mínima para permitir que clientes RDP herdados se conectem.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Defina o RDP para carregar a configuração de usuário do computador cliente.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Habilitar o controle Keep-Alive RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Defina o controle de reconexão de RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Definir o controle de tempo de sessão RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Definir o controle de tempo de desconexão de RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Definir o controle de tempo de conexão RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Definir o controle de tempo ocioso da sessão RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Defina o controle "limitar o máximo de conexões simultâneas":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Reinicie a VM e tente se conectar a ela usando o RDP.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Depois do disco do SO é anexado à VM de recuperação, certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. No disco do sistema operacional que você anexou, navegue até a pasta **\Windows\System32\config** . Copie todos os arquivos desta pasta como um backup, caso uma reversão seja necessária.
4. Inicie o Editor de registo (regedit.exe).
5. Selecione a chave de **HKEY_LOCAL_MACHINE** . No menu, selecione **arquivo** > **Carregar Hive**:
6. Navegue até a pasta **\windows\system32\config\SYSTEM** no disco do sistema operacional que você anexou. Para o nome do hive, insira **BROKENSYSTEM**. O novo hive do registro é exibido sob a chave **HKEY_LOCAL_MACHINE** . Em seguida, carregue o hive do software **\windows\system32\config\SOFTWARE** na chave **HKEY_LOCAL_MACHINE** . Para o nome do software do hive, insira **BROKENSOFTWARE**. 
7. Abra uma janela de prompt de comandos com privilégios elevados (**Executar como administrador**) e execute comandos nas etapas restantes para redefinir as configurações de RDP. 
8. Reduza a camada de segurança de RDP para 0 para que as comunicações entre o servidor e o cliente usem a criptografia RDP nativa:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Reduza o nível de criptografia para a configuração mínima para permitir que clientes RDP herdados se conectem:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Defina o RDP para carregar a configuração de usuário do computador cliente.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Habilitar o controle Keep-Alive RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Defina o controle de reconexão de RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Definir o controle de tempo de sessão RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Definir o controle de tempo de desconexão de RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Definir o controle de tempo de conexão RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Defina o controle de tempo ocioso da sessão RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp"/v ' fInheritMaxIdleTime '/t REG_DWORD/d 1/f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Defina o controle "limitar o máximo de conexões simultâneas":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Reinicie a VM e tente se conectar a ela usando o RDP.

## <a name="need-help"></a>Precisa de ajuda? 
Contacte o suporte. Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.





