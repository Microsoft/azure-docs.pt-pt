---
title: Problemas disparam um erro geral do RDP num VM do Windows em Azure Microsoft Docs
description: Saiba como resolver um erro geral do RDP num Windows VM em Azure Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058008"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Problemas de resolução de um erro geral do PDR no Azure VM

Este artigo descreve um erro geral que pode experimentar quando efauma ligação ao Protocolo de Ambiente de Trabalho Remoto (RDP) a uma Máquina Virtual do Windows (VM) em Azure.

## <a name="symptom"></a>Sintoma

Quando efizer uma ligação RDP a um VM da janela em Azure, poderá receber a seguinte mensagem de erro geral:

**O Ambiente de Trabalho Remoto não pode ligar-se ao computador remoto por uma destas razões:**

1. **O acesso remoto ao servidor não está ativado**

2. **O computador remoto está desligado.**

3. **O computador remoto não está disponível na rede**

**Certifique-se de que o computador remoto está ligado e ligado à rede e que o acesso remoto está ativado.**

## <a name="cause"></a>Causa

Este problema pode ocorrer devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

O componente RDP é desativado da seguinte forma:

- Ao nível dos componentes
- Ao nível do ouvinte
- No servidor de terminais
- No papel de anfitrião da sessão de ambiente de trabalho remoto

### <a name="cause-2"></a>Causa 2

Os Serviços de Ambiente de Trabalho Remoto (TermService) não estão a funcionar.

### <a name="cause-3"></a>Causa 3

O ouvinte rdp está mal configurado.

## <a name="solution"></a>Solução

Para resolver este problema, [volte a colocar o disco do sistema operativo](../windows/snapshot-copy-managed-disk.md)e fixe o disco do sistema operativo a um [VM](troubleshoot-recovery-disks-portal-windows.md)de resgate e, em seguida, siga os passos.

### <a name="serial-console"></a>Consola de Série

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Passo 1: Alugar cmD aberto na consola em série

1. Aceda à [Consola em Série](serial-console-windows.md) selecionando suporte & consola série de resolução > de **problemas****(Pré-visualização)**. Se a funcionalidade estiver ativada no VM, pode ligar o VM com sucesso.

2. Crie um novo canal para uma instância CMD. Digite **CMD** para iniciar o canal para obter o nome do canal.

3. Mude para o canal que executa a instância CMD, neste caso deve ser o canal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Passo 2: Verifique os valores das teclas de registo RDP:

1. Verifique se o PDR é desativado pela polícia.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Se a política de domínio existir, a configuração da política local é substituída.
      - Se a política de domínio afirma que o PDR está desativado (1), então atualize a política de AD a partir do controlador de domínio.
      - Se a política de domínio afirma que o PDR está ativado (0), então não é necessária nenhuma atualização.
      - Se a política de domínio não existir e a política local estipula que o PDR está desativado (1), habilitar o PDR utilizando o seguinte comando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Verifique a configuração atual do servidor de terminais.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Se o comando devolver 0, o servidor de terminais é desativado. Em seguida, ative o servidor de terminais da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. O módulo 'Servidor terminal' está definido para drenar o modo se o servidor estiver numa exploração de servidores de terminais (RDS ou Citrix). Verifique o modo atual do módulo Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Se o comando voltar 1, o módulo do Servidor de Terminais está programado para drenar o modo. Em seguida, coloque o módulo no modo de funcionamento da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Verifique se pode ligar-se ao servidor de terminais.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Se o comando devolver 1, não pode ligar-se ao servidor de terminais. Em seguida, ativar a ligação da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Verifique a configuração atual do ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Se o comando voltar a 0, o ouvinte RDP está desativado. Em seguida, ativar o ouvinte da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Verifique se pode ligar-se ao ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Se o comando voltar 1, não pode ligar-se ao ouvinte RDP. Em seguida, ativar a ligação da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Reinicie a VM.

8. Saia da instância CMD `exit`digitando e, em seguida, pressione **Enter** duas vezes.

9. Reiniciar o VM `restart`digitando e, em seguida, ligue-se ao VM.

Se o problema ainda acontecer, passe para o passo 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Ativar serviços de ambiente de trabalho remoto

Para mais informações, consulte [remote Desktop Services não está a começar num Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Reset RDP ouvinte

Para mais informações, consulte [a desconexão do Ambiente remoto frequentemente em Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Reparação offline

#### <a name="step-1-turn-on-remote-desktop"></a>Passo 1: Ligue o ambiente de trabalho remoto

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Inicie uma ligação remote Desktop ao VM de recuperação.
3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco osso anexado.
4. Inicie uma ligação remote Desktop ao VM de recuperação.
5. Abra uma sessão de solicitação de comando elevada **(Executar como administrador).** Execute os seguintes scripts. Neste script, assumimos que a letra de unidade atribuída ao disco OS anexado é F. Substitua esta carta de unidade com o valor adequado para o seu VM.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Se o VM for de domínio, verifique a seguinte chave de registo para ver se existe uma política de grupo que irá desativar o RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Se este valor-chave for definido para 1, significa que o PDR é desativado pela política. Para ativar o Ambiente de Trabalho Remoto através da política GPO, altere a seguinte política do controlador de domínio:

   
      **Configuração do computador\Políticas\Modelos administrativos:**

      Definições de política\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Permitir que os utilizadores se conectem remotamente utilizando serviços de ambiente de trabalho remotos
  
1. Retire o disco do VM de resgate.
1. [Crie um novo VM a partir do disco.](../windows/create-vm-specialized.md)

Se o problema ainda acontecer, passe para o passo 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Ativar serviços de ambiente de trabalho remoto

Para mais informações, consulte [remote Desktop Services não está a começar num Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Reset RDP ouvinte

Para mais informações, consulte [a desconexão do Ambiente remoto frequentemente em Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
