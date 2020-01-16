---
title: Um erro interno ocorre quando você faz uma conexão RDP com as máquinas virtuais do Azure | Microsoft Docs
description: Saiba como resolver problemas de erros internos do RDP no Microsoft Azure. | Documentos da Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: be0f61b1458fa8bd63d85669c7956a789892996a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981331"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Ocorreu um erro interno ao tentar ligar a uma VM do Azure através do ambiente de trabalho remoto

Este artigo descreve um erro que podem ocorrer ao tentar ligar a uma máquina virtual (VM) no Microsoft Azure.
> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../azure-resource-manager/management/deployment-models.md). Este artigo explica como utilizar o modelo de implementação do Resource Manager, que recomendamos que utilize para novas implementações em vez do modelo de implementação clássica.

## <a name="symptoms"></a>Sintomas

É possível ligar a uma VM do Azure utilizando o protocolo de ambiente de trabalho remoto (RDP). A conexão fica preso na seção "Configurando remoto" ou, receberá a seguinte mensagem de erro:

- Erro interno de RDP
- Ocorreu um erro interno
- Este computador não é possível ligar ao computador remoto. Tente ligar novamente. Se o problema persistir, contacte o proprietário do computador remoto ou o administrador da rede


## <a name="cause"></a>Causa

Este problema pode ocorrer pelos seguintes motivos:

- Não não possível aceder as chaves de encriptação RSA locais.
- Protocolo TLS está desativado.
- O certificado está danificado ou expirou.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire um instantâneo do disco do SO da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize a consola de série ou [Repare a VM offline](#repair-the-vm-offline) ao anexar o disco de SO da VM para uma VM de recuperação.


### <a name="use-serial-control"></a>Utilizar o controlo de série

Ligar à [consola de série e a instância do PowerShell aberta](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a consola de série não estiver ativada na sua VM, vá para o [Repare a VM offline](#repair-the-vm-offline) secção.

#### <a name="step-1-check-the-rdp-port"></a>Passo: 1 verificar a porta RDP

1. Numa instância do PowerShell, utilize o [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) para verificar se a porta 8080 é utilizada por outras aplicações:

        Netstat -anob |more
2. Se Termservice.exe é através da porta 8080, vá para o passo 2. Se outro serviço ou aplicativo que não seja Termservice.exe estiver a utilizar a porta 8080, siga estes passos:

    1. Pare o serviço para a aplicação que está a utilizar o serviço 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Inicie o serviço de terminal:

            Start-Service -Name Termservice

2. Se não for possível parar o aplicativo, ou se este método não é aplicável a, altere a porta para RDP:

    1. Altere a porta:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Defina a firewall para a nova porta:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Atualizar o grupo de segurança de rede para a nova porta](../../virtual-network/security-overview.md) na porta RDP portal do Azure.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Passo 2: Definir permissões corretas no certificado autoassinado do RDP

1.  Numa instância do PowerShell, execute os seguintes comandos individualmente para renovar o certificado autoassinado do RDP:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Se não é possível renovar o certificado através deste método, tente renovar o certificado autoassinado do RDP remotamente:

    1. A partir de um VM que tenha conetividade com a VM em funcionamento que está com problemas, tipo **mmc** no **executar** caixa para abrir o Console de gerenciamento Microsoft.
    2. Sobre o **arquivo** menu, selecione **Adicionar/Remover Snap-in**, selecione **certificados**e, em seguida, selecione **adicionar**.
    3. Selecione **contas de computador**, selecione **noutro computador**e, em seguida, adicione o endereço IP do problema VM.
    4. Vá para o **Desktop\Certificates remoto** pasta, o certificado com o botão direito e, em seguida e selecione **eliminar**.
    5. Numa instância do PowerShell a partir da consola de série, reinicie o serviço de configuração do ambiente de trabalho remoto:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Repor a permissão para a pasta MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Reinicie a VM e, em seguida, tente iniciar uma ligação de ambiente de trabalho remoto à VM. Se o erro continuar a ocorrer, vá para o passo seguinte.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Passo 3: Ativar todas as versões suportadas do TLS

O cliente RDP utiliza o TLS 1.0 como o protocolo predefinido. No entanto, isso pode ser alterado para TLS 1.1, que se tornou o novo padrão. Se o TLS 1.1 está desativado na VM, a ligação irá falhar.
1.  Numa instância CMD, ative o protocolo TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Para impedir que a política de AD substitua as alterações, pare temporariamente a atualização da política de grupo:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Reinicie a VM para que as alterações entrem em vigor. Se o problema for resolvido, execute o seguinte comando para voltar a ativar a política de grupo:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Se a alteração é revertida, significa que existe uma política do Active Directory no seu domínio da empresa. Precisa alterar essa política para evitar este problema ocorra novamente.

### <a name="repair-the-vm-offline"></a>Repare a VM Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anexar o disco do SO a uma VM de recuperação

1. [Anexar o disco do SO a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Depois do disco do SO é anexado à VM de recuperação, certifique-se de que o disco é sinalizado de forma **Online** no console de gerenciamento de disco. Tenha em atenção a letra de unidade que está atribuída ao disco do SO anexado.
3. Inicie uma ligação de ambiente de trabalho remoto para a VM de recuperação.

#### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de informação e a consola de série

Para ativar o registo de despejo e consola de série, execute o seguinte script.

1. Abra uma sessão de linha de comandos elevada (**executar como administrador**).
2. Execute o seguintes script:

    Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Repor a permissão para a pasta MachineKeys

1. Abra uma sessão de linha de comandos elevada (**executar como administrador**).
2. Execute o seguinte script. Nesse script, partimos do princípio de que a letra de unidade que está atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Ativar todos com suporte de TLS de versões

1.  Abra uma sessão de linha de comandos elevada (**executar como administrador**) e o execute os seguintes comandos. O seguinte script parte do princípio de que a letra de unidade é atribuída ao disco do SO anexado é F. Substitua esta letra de unidade com o valor apropriado para a sua VM.
2.  Verificação de que o TLS está ativado:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Se a chave não existir, ou seu valor é **0**, ative o protocolo, executando os seguintes scripts:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Ative o NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Desanexar o disco do SO e recriar a VM](../windows/troubleshoot-recovery-disks-portal.md)e, em seguida, verifique se o problema está resolvido.





