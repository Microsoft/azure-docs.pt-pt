---
title: Ocorre um erro interno quando se faz uma ligação RDP às Máquinas Virtuais Azure [ Microsoft Docs
description: Saiba como resolver erros internos do RDP no Microsoft Azure.] Microsoft Docs
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
ms.openlocfilehash: 8046e4f42db50db15c840a13b95ae1f3620a8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266926"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>An internal error occurs when you try to connect to an Azure VM through Remote Desktop (Um erro interno ocorre quando se tenta ligar a uma VM do Azure através do Ambiente de Trabalho Remoto)

Este artigo descreve um erro que pode experimentar quando tenta ligar-se a uma máquina virtual (VM) no Microsoft Azure.


## <a name="symptoms"></a>Sintomas

Não é possível ligar-se a um VM Azur e utilizando o protocolo de ambiente de trabalho remoto (RDP). A ligação fica presa na secção "Configurar o Controlo Remoto", ou recebe a seguinte mensagem de erro:

- Erro interno do RDP
- Ocorreu um erro interno
- Este computador não pode ser ligado ao computador remoto. Tente ligar-se de novo. Se o problema continuar, contacte o proprietário do computador remoto ou o seu administrador de rede


## <a name="cause"></a>Causa

Esta questão pode ocorrer pelas seguintes razões:

- As chaves de encriptação RSA locais não podem ser acedidas.
- O protocolo TLS está desativado.
- O certificado é corrompido ou expirado.

## <a name="solution"></a>Solução

Antes de seguir estes passos, tire uma foto do disco osso do VM afetado como cópia de segurança. Para mais informações, consulte [snapshot um disco](../windows/snapshot-copy-managed-disk.md).

Para resolver este problema, utilize a Consola em Série ou [repare o VM offline,](#repair-the-vm-offline) fixando o disco OS do VM a um VM de recuperação.


### <a name="use-serial-control"></a>Utilizar o controlo em série

Ligue-se à consola em série e abra a [instância PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Se a Consola em Série não estiver ativada no seu VM, vá à [reparação da secção de offline VM.](#repair-the-vm-offline)

#### <a name="step-1-check-the-rdp-port"></a>Passo: 1 Verifique a porta RDP

1. Num caso powerShell, utilize o [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) para verificar se a porta 8080 é utilizada por outras aplicações:

        Netstat -anob |more
2. Se termservice.exe estiver usando a porta 8080, vá para o passo 2. Se outro serviço ou aplicação que não o Termservice.exe estiver a utilizar a porta 8080, siga estes passos:

    1. Pare o serviço para a aplicação que está a utilizar o serviço 3389:

            Stop-Service -Name <ServiceName> -Force

    2. Inicie o serviço de terminais:

            Start-Service -Name Termservice

2. Se a aplicação não puder ser interrompida, ou se este método não se aplicar a si, altere a porta para RDP:

    1. Alterar a porta:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Detete a firewall para a nova porta:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Atualize o grupo de segurança da rede para o novo porto](../../virtual-network/security-overview.md) no portal Azure RDP.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Passo 2: Definir permissões corretas no certificado auto-assinado rdp

1.  Num caso powerShell, execute os seguintes comandos um a um para renovar o certificado auto-assinado RDP:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Se não conseguir renovar o certificado utilizando este método, tente renovar remotamente o certificado auto-assinado RDP:

    1. A partir de um VM de trabalho que tem conectividade com o VM que está a ter problemas, digite **mmc** na caixa **Run** para abrir a Consola de Gestão da Microsoft.
    2. No menu **'Ficheiro',** selecione **Adicionar/Remover snap-in,** selecione **Certificados,** e depois selecione **Adicionar**.
    3. Selecione **contas de computador,** selecione **Outro Computador**e, em seguida, adicione o endereço IP do problema VM.
    4. Vá à pasta **Remote Desktop\Certificates,** clique no certificado e, em seguida, selecione **Delete**.
    5. Numa instância PowerShell da Consola em Série, reinicie o serviço de configuração de ambiente de trabalho remoto:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Redefinir a permissão para a pasta MachineKeys.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Reinicie o VM e, em seguida, tente iniciar uma ligação remota de ambiente de trabalho ao VM. Se o erro ainda ocorrer, vá para o próximo passo.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Passo 3: Ativar todas as versões TLS suportadas

O cliente RDP usa tLS 1.0 como protocolo predefinido. No entanto, isto pode ser alterado para TLS 1.1, que se tornou o novo padrão. Se o TLS 1.1 estiver desativado no VM, a ligação falhará.
1.  Num caso CMD, ativar o protocolo TLS:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Para evitar que a política da AD sobrepor as alterações, pare temporariamente a atualização da política do grupo:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Reinicie o VM de modo a que as alterações entrem em vigor. Se a questão for resolvida, execute o seguinte comando para reativar a política do grupo:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Se a mudança for revertida, significa que há uma política de Diretório Ativo no domínio da sua empresa. É preciso mudar essa política para evitar que este problema volte a ocorrer.

### <a name="repair-the-vm-offline"></a>Reparar o VM Offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Fixe o disco OS a um VM de recuperação

1. [Fixe o disco OS a um VM](../windows/troubleshoot-recovery-disks-portal.md)de recuperação .
2. Depois de o disco OS estar ligado ao VM de recuperação, certifique-se de que o disco está sinalizado como **Online** na consola de Gestão de Discos. Note a letra de unidade que é atribuída ao disco osso anexado.
3. Inicie uma ligação remote Desktop ao VM de recuperação.

#### <a name="enable-dump-log-and-serial-console"></a>Ativar o registo de despejo e a consola em série

Para ativar o registo de despejo e a Consola em Série, execute o seguinte script.

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador).**
2. Execute o seguintes script:

    Neste script, assumimos que a letra de unidade atribuída ao disco OS anexado é F. Substitua esta carta de unidade com o valor adequado para o seu VM.

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

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Redefinir a permissão para a pasta MachineKeys

1. Abra uma sessão de solicitação de comando elevada **(Executar como administrador).**
2. Execute o seguinte script. Neste script, assumimos que a letra de unidade atribuída ao disco OS anexado é F. Substitua esta carta de unidade com o valor adequado para o seu VM.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Ativar todas as versões TLS suportadas

1.  Abra uma sessão de solicitação de comando elevada (**Executar como administrador)** e executar os seguintes comandos. O seguinte script pressupõe que a letra do condutor é atribuída ao disco OS anexado é F. Substitua esta letra de unidade com o valor adequado para o seu VM.
2.  Verifique quais TLS estão ativados:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Se a chave não existir, ou o seu valor for **0,** ative o protocolo executando os seguintes scripts:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Ativar nLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Desmontar o disco OS e recriar o VM,](../windows/troubleshoot-recovery-disks-portal.md)e depois verificar se o problema está resolvido.





