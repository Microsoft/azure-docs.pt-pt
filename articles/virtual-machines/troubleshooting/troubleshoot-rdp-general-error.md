---
title: Resolução de problemas de um erro geral do RDP para um VM do Windows em Azure Microsoft Docs
description: Saiba como resolver um erro geral do RDP a um VM do Windows em Azure Microsoft Docs
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
ms.openlocfilehash: f2a1a5f3eaf79a345b0d33f43d260fe6aa15236b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439260"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Resolução de problemas num erro geral do PDR em Azure VM

Este artigo descreve um erro geral que pode experimentar quando errar uma ligação remote desktop Protocol (RDP) a uma Máquina Virtual do Windows (VM) em Azure.

## <a name="symptom"></a>Sintoma

Quando errar uma ligação RDP a um VM da janela em Azure, poderá receber a seguinte mensagem de erro geral:

**O Ambiente de Trabalho Remoto não pode ligar-se ao computador remoto por uma destas razões:**

1. **O acesso remoto ao servidor não está ativado**

2. **O computador remoto está desligado**

3. **O computador remoto não está disponível na rede**

**Certifique-se de que o computador remoto está ligado e ligado à rede e que o acesso remoto está ativado.**

## <a name="cause"></a>Causa

Este problema pode ocorrer devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

O componente RDP é desativado da seguinte forma:

- Ao nível dos componentes
- Ao nível dos ouvintes
- No servidor terminal
- No papel de anfitrião da sessão de desktop remoto

### <a name="cause-2"></a>Causa 2

Os Serviços remotos de Desktop (TermService) não estão a funcionar.

### <a name="cause-3"></a>Causa 3

O ouvinte do PDR está mal configurado.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco de SO do VM afetado como cópia de segurança. Para resolver este problema, utilize o controlo de série ou repare o VM offline.

### <a name="serial-console"></a>Consola de Série

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Passo 1: Abrir a instância CMD na consola serial

1. Aceda à [Consola em Série](serial-console-windows.md) selecionando suporte & consola em série de **resolução de**  >  **problemas (Preview)**. Se a função estiver ativada no VM, pode ligar o VM com sucesso.

2. Criar um novo canal para uma instância CMD. Digite **CMD** para iniciar o canal para obter o nome do canal.

3. Mude para o canal que executa a instância CMD, neste caso deve ser o canal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Passo 2: Verificar os valores das chaves de registo RDP:

1. Verifique se o PDR está desativado por polícias de grupo.

    ```
    REM Get the group policy setting
    reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
    ```
    Se a política de grupo declara que o RDP está desativado (o valor fDenyTSConnections é 0x1), executar o seguinte comando para ativar o serviço TermService. Se a chave de registo não for encontrada, não existe uma política de grupo configurada para desativar o PDR. Pode passar para o próximo passo.

    ```
    REM update the fDenyTSConnections value to enable TermService service
    reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```
    > [!NOTE]
    > Este passo permite temporariamente o serviço TermService. A alteração será reiniciada quando as definições de política de grupo forem atualizadas. Para resolver o problema, é necessário verificar se o serviço TermService é desativado pela política de grupo local ou pela política do grupo de domínios e, em seguida, atualizar as definições de política correspondentemente.
    
2. Verifique a configuração de ligação remota atual.
    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections
    ```
    Se o comando retornar 0x1, o VM não permite a ligação remota. Em seguida, permita a ligação remota utilizando o seguinte comando:
     ```
     reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
     ```
    
1. Verifique a configuração atual do servidor de terminais.

    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
    ```

      Se o comando retornar 0, o servidor do terminal será desativado. Em seguida, ative o servidor do terminal da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. O módulo Terminal Server está definido para drenar o modo se o servidor estiver numa fazenda de servidores terminais (RDS ou Citrix). Verifique o modo atual do módulo Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Se o comando retornar 1, o módulo 'Servidor de Terminal' está definido para o modo de drenagem. Em seguida, coloque o módulo no modo de trabalho da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Verifique se pode ligar-se ao servidor de terminais.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Se o comando retornar 1, não pode ligar-se ao servidor de terminais. Em seguida, ative a ligação da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Verifique a configuração atual do ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Se o comando voltar a 0, o ouvinte RDP está desativado. Em seguida, ative o ouvinte da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Verifique se pode ligar-se ao ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Se o comando retornar 1, não pode ligar-se ao ouvinte RDP. Em seguida, ative a ligação da seguinte forma:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Reinicie a VM.

8. Saia da instância CMD digitando `exit` e, em seguida, prima **Enter** duas vezes.

9. Reinicie o VM digitando `restart` e, em seguida, ligue-o ao VM.

Se o problema ainda acontecer, passe para o passo 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Permitir serviços remotos de desktop

Para obter mais informações, consulte [os Serviços de Ambiente de Trabalho Remoto não estão a começar num Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Repor o ouvinte RDP

Para obter mais informações, consulte [as desconexões de ambientes de trabalho remoto frequentemente em Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Reparação offline

#### <a name="step-1-turn-on-remote-desktop"></a>Passo 1: Ligue o ambiente de trabalho remoto

1. [Fixe o disco DE A uma VM de recuperação](./troubleshoot-recovery-disks-portal-windows.md).
2. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.
3. Certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco de sos anexado.
4. Inicie uma ligação de ambiente de trabalho remoto ao VM de recuperação.
5. Abra uma sessão de solicitação de comando elevada **(Executar como administrador**). Execute os seguintes scripts. Neste script, assumimos que a letra de unidade que é atribuída ao disco de OS anexado é F. Substitua esta letra de unidade pelo valor adequado para o seu VM.

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

6. Se o VM for de domínio, verifique a seguinte chave de registo para ver se existe uma política de grupo que desative o PDR. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Se este valor-chave for definido para 1, significa que o PDR é desativado pela apólice. Para ativar o Ambiente de Trabalho Remoto através da política GPO, altere a seguinte política do controlador de domínio:

   
      **Configuração do computador\Políticas\Modelos administrativos:**

      Definições de política\Componentes do Windows\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remote using Remote Desktop Services
  
1. Retire o disco do VM de resgate.
1. [Criar um novo VM a partir do disco.](../windows/create-vm-specialized.md)

Se o problema ainda acontecer, passe para o passo 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Permitir serviços remotos de desktop

Para obter mais informações, consulte [os Serviços de Ambiente de Trabalho Remoto não estão a começar num Azure VM](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Repor o ouvinte RDP

Para obter mais informações, consulte [as desconexões de ambientes de trabalho remoto frequentemente em Azure VM](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
