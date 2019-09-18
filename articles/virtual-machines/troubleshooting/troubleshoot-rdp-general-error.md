---
title: Solucionar problemas de um erro geral de RDP para uma VM do Windows no Azure | Microsoft Docs
description: Saiba como solucionar problemas de um erro geral de RDP para uma VM do Windows no Azure | Microsoft Docs
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
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058008"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Solucionar um erro geral de RDP na VM do Azure

Este artigo descreve um erro geral que pode ocorrer quando você faz uma conexão de protocolo RDP (RDP) para uma VM (máquina virtual) do Windows no Azure.

## <a name="symptom"></a>Sintoma

Quando você faz uma conexão RDP com uma VM de janela no Azure, você pode receber a seguinte mensagem de erro geral:

**Ambiente de trabalho remoto não é possível ligar ao computador remoto para um dos seguintes motivos:**

1. **Acesso remoto para o servidor não está ativado**

2. **O computador remoto está desativado**

3. **O computador remoto não está disponível na rede**

**Certificar-se de que o computador remoto está ativado e ligado à rede e que o acesso remoto está ativado.**

## <a name="cause"></a>Causa

Esse problema pode ocorrer devido às seguintes causas:

### <a name="cause-1"></a>Causa 1

O componente RDP é desabilitado da seguinte maneira:

- No nível de componente
- No nível do ouvinte
- No servidor de terminal
- Na função Host da Sessão da Área de Trabalho Remota

### <a name="cause-2"></a>Causa 2

O Serviços de Área de Trabalho Remota (TermService) não está em execução.

### <a name="cause-3"></a>Causa 3

O ouvinte RDP está configurado incorretamente.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md)e [anexe o disco do sistema operacional a uma VM de resgate](troubleshoot-recovery-disks-portal-windows.md)e siga as etapas.

### <a name="serial-console"></a>Consola de Série

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Passo 1: Abra a instância CMD no Console serial

1. Acesse o [console serial](serial-console-windows.md) selecionando **suporte &**  > solução**de problemas console serial (versão prévia)** . Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância CMD. Digite **cmd** para iniciar o canal para obter o nome do canal.

3. Alterne para o canal que executa a instância CMD, neste caso, deve ser o canal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Passo 2: Verifique os valores das chaves do registro do RDP:

1. Verifique se o RDP está desabilitado por políticas.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Se a política de domínio existir, a configuração na política local será substituída.
      - Se a política de domínio indicar que o RDP está desabilitado (1), atualize a política do AD do controlador de domínio.
      - Se a política de domínio indicar que o RDP está habilitado (0), nenhuma atualização será necessária.
      - Se a política de domínio não existir e a política local indicar que o RDP está desabilitado (1), habilite o RDP usando o seguinte comando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Verifique a configuração atual do servidor de terminal.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Se o comando retornar 0, o servidor de terminal será desabilitado. Em seguida, habilite o servidor de terminal da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. O módulo Terminal Server será definido para o modo de descarga se o servidor estiver em um farm de servidores de terminal (RDS ou Citrix). Verifique o modo atual do módulo Terminal Server.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Se o comando retornar 1, o módulo Terminal Server será definido como modo de descarga. Em seguida, defina o módulo para o modo de trabalho da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Verifique se você pode se conectar ao servidor de terminal.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Se o comando retornar 1, você não poderá se conectar ao servidor de terminal. Em seguida, habilite a conexão da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Verifique a configuração atual do ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Se o comando retornar 0, o ouvinte RDP será desabilitado. Em seguida, habilite o ouvinte da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Verifique se você pode se conectar ao ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Se o comando retornar 1, você não poderá se conectar ao ouvinte RDP. Em seguida, habilite a conexão da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Reinicie a VM.

8. Saia da instância cmd digitando `exit`e pressione **Enter** duas vezes.

9. Reinicie a VM digitando `restart`e, em seguida, conecte-se à VM.

Se o problema ainda ocorrer, vá para a etapa 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Habilitar serviços de área de trabalho remota

Para obter mais informações, consulte [serviços de área de trabalho remota não está iniciando em uma VM do Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Redefinir ouvinte RDP

Para obter mais informações, consulte [área de trabalho remota desconexões frequentemente na VM do Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Reparo offline

#### <a name="step-1-turn-on-remote-desktop"></a>Passo 1: Ativar Área de Trabalho Remota

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.
3. Certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
4. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.
5. Abra uma sessão de linha de comandos elevada (**executar como administrador**). Execute os scripts a seguir. Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.

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

6. Se a VM estiver ingressada no domínio, verifique a seguinte chave do registro para ver se há uma política de grupo que desabilitará o RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Se esse valor de chave for definido como 1, isso significa que o RDP está desabilitado pela política. Para habilitar Área de Trabalho Remota por meio da política de GPO, altere a seguinte política do controlador de domínio:

   
      **Modelos de configuração de computador:**

      Política definitions\Windows de área de trabalho do remota do remoto \ sessão do Host\Connections\Allow usuários para se conectar remotamente usando Serviços de Área de Trabalho Remota
  
1. Desanexe o disco da VM de resgate.
1. [Crie uma nova VM a partir do disco](../windows/create-vm-specialized.md).

Se o problema ainda ocorrer, vá para a etapa 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Passo 2: Habilitar serviços de área de trabalho remota

Para obter mais informações, consulte [serviços de área de trabalho remota não está iniciando em uma VM do Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Passo 3: Redefinir ouvinte RDP

Para obter mais informações, consulte [área de trabalho remota desconexões frequentemente na VM do Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte

Se você ainda precisar de ajuda, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o problema rapidamente.
