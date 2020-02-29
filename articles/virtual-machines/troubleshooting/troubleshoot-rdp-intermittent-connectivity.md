---
title: O Ambiente de Trabalho Remoto desliga-se frequentemente no Azure VM. Microsoft Docs
description: Aprenda a resolver desconexões frequentes de desktop remoto em Azure VM.
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
ms.openlocfilehash: c22a401a6b25f7bb2c27a10e52214fa42ac6089b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918228"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>O ambiente de trabalho remoto desliga-se frequentemente em Azure VM

Este artigo explica como resolver as desconexões frequentes a uma máquina virtual Azure (VM) através do Protocolo de Ambiente de Trabalho Remoto RDP).


## <a name="symptom"></a>Sintoma

Você enfrenta problemas intermitentes de conectividade RDP durante as suas sessões. Inicialmente pode ligar-se ao VM, mas depois a ligação cai.

## <a name="cause"></a>Causa

Este problema pode ocorrer se o Ouvinte RDP estiver mal configurado. Tipicamente, este problema ocorre num VM que usa uma imagem personalizada.

## <a name="solution"></a>Solução

Antes de seguir estes passos, [tire uma foto do disco osso](../windows/snapshot-copy-managed-disk.md) do VM afetado como cópia de segurança. 

Para resolver este problema, utilize o controlo em série ou [repare o VM offline,](#repair-the-vm-offline) fixando o disco DE Os do VM a um VM de recuperação.

### <a name="serial-control"></a>Controlo de série

1. Ligue-se à consola em série e abra a [instância CMD](./serial-console-windows.md). Em seguida, execute os seguintes comandos para redefinir as configurações RDP. Se a Consola em Série não estiver ativada no seu VM, vá para o próximo passo.
2. Baixe a Camada de Segurança RDP para 0. Nesta definição, as comunicações entre o servidor e o cliente utilizam a encriptação rdp nativa.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Reduza o nível de encriptação para a definição mínima para permitir que os clientes rdp legados se conectem.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Despôs rdP para carregar a configuração do utilizador do computador cliente.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Ativar o controlo RDP Keep-Alive:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Detete o comando RDP Reconnect:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Detete o controlo do tempo de sessão rdp:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Detete o controlo do tempo de deconexão RDP: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Detete o controlo do tempo de ligação RDP:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Detete o controlo de tempo inativo da sessão rdp:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Detete o comando "Limitar as ligações máximas simultâneas":

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Reinicie o VM e tente voltar a ligá-lo utilizando RDP.

### <a name="repair-the-vm-offline"></a>Repare a VM offline

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Depois de o disco OS estar ligado ao VM de recuperação, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. No disco OS que anexou, navegue para a pasta **\windows\system32\config.** Copie todos os ficheiros desta pasta como cópia de segurança, caso seja necessário um reversão.
4. Iniciar editor de registo (regedit.exe).
5. Selecione a tecla **HKEY_LOCAL_MACHINE.** No menu, selecione **File** > **Load Hive:**
6. Navegue na pasta **\windows\system32\config\SYSTEM** no disco OS que anexou. Para o nome da colmeia, introduza **BROKENSYSTEM**. A nova colmeia de registo é exibida sob a chave **HKEY_LOCAL_MACHINE.** Em seguida, carregue a colmeia de software **\windows\system32\config\SOFTWARE** sob a chave **HKEY_LOCAL_MACHINE.** Para o nome do software da colmeia, **introduza BROKENSOFTWARE**. 
7. Abra uma janela de comando elevado (**Executar como administrador)** e executar comandos nos passos restantes para redefinir as configurações rdp. 
8. Baixe a Camada de Segurança RDP para 0 de modo a que as comunicações entre o servidor e o cliente utilizem a encriptação rdp nativa:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Reduza o nível de encriptação para a definição mínima para permitir que os clientes rdp legados se conectem:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Despôs rdP para carregar a configuração do utilizador da máquina cliente.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Ativar o controlo RDP Keep-Alive:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Detete o comando RDP Reconnect:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Detete o controlo do tempo de sessão rdp:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Detete o controlo do tempo de deconexão RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Detete o controlo do tempo de ligação RDP:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Defina o controlo de tempo de marcha lenta da sessão RDP: REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Detete o comando "Limitar as ligações máximas simultâneas":

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Reinicie o VM e tente voltar a ligá-lo utilizando RDP.

## <a name="need-help"></a>Precisa de ajuda? 
Contacte o suporte. Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.





