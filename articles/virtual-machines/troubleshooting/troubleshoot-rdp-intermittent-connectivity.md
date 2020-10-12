---
title: O Ambiente de Trabalho Remoto desliga-se frequentemente em Azure VM/ Microsoft Docs
description: Saiba como resolver desconexões frequentes de ambientes de trabalho remoto em Azure VM.
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
ms.openlocfilehash: 60be7c234a0166331c35eb6528eae11bfbbf518f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074323"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Remote Desktop disconnects frequently in Azure VM (O Ambiente de Trabalho Remoto desliga-se muitas vezes na VM do Azure)

Este artigo explica como resolver desconexões frequentes a uma máquina virtual Azure (VM) através do Protocolo de Ambiente de Trabalho Remoto RDP.


## <a name="symptom"></a>Sintoma

Enfrenta problemas intermitentes de conectividade RDP durante as suas sessões. Pode inicialmente ligar-se ao VM, mas depois a ligação cai.

## <a name="cause"></a>Causa

Este problema pode ocorrer se o ouvinte RDP estiver mal configurado. Normalmente, este problema ocorre num VM que utiliza uma imagem personalizada.

## <a name="solution"></a>Solução

Antes de seguir estes passos, [tire uma foto do disco de SO](../windows/snapshot-copy-managed-disk.md) do VM afetado como cópia de segurança. 

Para resolver este problema, utilize o controlo de série ou [repare o VM offline,](#repair-the-vm-offline) fixando o disco de so do VM a um VM de recuperação.

### <a name="serial-control"></a>Controlo em série

1. Ligue-se à [Consola em Série e abra a instância CMD](./serial-console-windows.md). Em seguida, executar os seguintes comandos para redefinir as configurações RDP. Se a Consola em Série não estiver ativada no seu VM, vá para o próximo passo.
2. Baixe a camada de segurança RDP para 0. Nesta definição, as comunicações entre o servidor e o cliente utilizam a encriptação RDP nativa.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

3. Baixe o nível de encriptação para a definição mínima para permitir a ligação dos clientes RDP.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

4. Desaveri o RDP para carregar a configuração do utilizador do computador cliente.

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

5. Ativar o controlo de Keep-Alive RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

6. Definir o controlo de religação RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

7. Definir o controlo de tempo de sessão RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

8. Definir o controlo de tempo de desconexão RDP: 

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f` `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

9. Definir o controlo de tempo de ligação RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

10. Desave o controlo de tempo inativo da sessão RDP:

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f`

11. Definir o controlo "Limitar as ligações máximas simultâneas":

    `REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f`

12. Reinicie o VM e tente novamente ligá-lo utilizando RDP.

### <a name="repair-the-vm-offline"></a>Reparar o VM offline

1. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Depois do disco OS ser ligado ao VM de recuperação, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de sos anexado.
3. No disco OS que anexou, navegue na pasta **\windows\system32\config.** Copie todos os ficheiros desta pasta como cópia de segurança, caso seja necessário um reversão.
4. Inicie o Editor de registo (regedit.exe).
5. Selecione a **tecla HKEY_LOCAL_MACHINE.** No menu, selecione **File**  >  **Load Hive**:
6. Navegue na pasta **\windows\system32\config\SYSTEM** no disco OS que anexou. Para o nome da colmeia, **insira BROKENSYSTEM**. A nova colmeia de registo é exibida sob a tecla **HKEY_LOCAL_MACHINE.** Em seguida, carregue a colmeia de software **\windows\system32\config\SOFTWARE** sob a tecla **HKEY_LOCAL_MACHINE.** Para o nome do software de colmeia, **insira BROKENSOFTWARE**. 
7. Abra uma janela de pedido de comando elevado **(Executar como administrador)** e executar comandos nos passos restantes para redefinir as configurações RDP. 
8. Baixe a Camada de Segurança RDP para 0 de modo a que as comunicações entre o servidor e o cliente utilizem a encriptação RDP nativa:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f`

9. Baixe o nível de encriptação para a definição mínima para permitir que os clientes RDP legados se conectem:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f`

10. Desaveri o RDP para carregar a configuração do utilizador da máquina do cliente.

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f`

11. Ativar o controlo de Keep-Alive RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f`

12. Definir o controlo de religação RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f`

13. Definir o controlo de tempo de sessão RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f`

14. Definir o controlo de tempo de desconexão RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f`

15. Definir o controlo de tempo de ligação RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f`

16. Desave o controlo de tempo inativo da sessão RDP:

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f`

17. Definir o controlo "Limitar as ligações máximas simultâneas":

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

    `REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f`

18. Reinicie o VM e tente novamente ligá-lo utilizando RDP.

## <a name="need-help"></a>Precisa de ajuda? 
Contacte o suporte. Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
