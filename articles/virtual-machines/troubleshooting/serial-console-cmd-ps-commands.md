---
title: CMD e do PowerShell em VMs do Windows do Azure | Documentos da Microsoft
description: Como utilizar comandos CMD e do PowerShell no SAC em VMs do Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 69aecf3d107000408f98f8ae53968355943c2881
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440816"
---
# <a name="windows-commands---cmd-and-powershell"></a>Comandos do Windows - CMD e o PowerShell

Esta secção inclui comandos de exemplo para realizar tarefas comuns em cenários em que poderá ter de utilizar SAC para aceder à VM do Windows, por exemplo, quando tiver de resolver problemas de falhas de ligação de RDP.

SAC foi incluído em todas as versões do Windows desde o Windows Server 2003, mas está desativada por predefinição. SAC conta com o `sacdrv.sys` driver do kernel, o `Special Administration Console Helper` serviço (`sacsvr`) e o `sacsess.exe` processo. Para obter mais informações, consulte [as definições e ferramentas de serviços de gestão de emergência](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC permite-lhe ligar ao seu sistema operacional em execução por meio da porta serial. Quando inicia o CMD de SAC, `sacsess.exe` inicia `cmd.exe` dentro de seu sistema operacional em execução. Pode ver que tarefa Manager se RDP para a VM ao mesmo tempo estão ligadas a SAC através da funcionalidade de consola de série. O CMD aceder através de SAC é o mesmo `cmd.exe` utilizar quando estiver ligado através de RDP. As mesmas ferramentas e comandos estão disponíveis, incluindo a capacidade de iniciar o PowerShell a partir dessa instância CMD. Essa é uma grande diferença entre SAC e o ambiente de recuperação do Windows (WinRE) em que SAC está informando-o a gerir o seu sistema operacional em execução, em que o WinRE efetua o arranque num sistema operacional diferente, mínimo. Enquanto as VMs do Azure não suportam a capacidade de acessar o WinRE, com a funcionalidade de consola de série, as VMs do Azure podem ser geridas através de SAC.

Uma vez que SAC está limitada a uma memória intermédia de ecrã de 80 x 24 com nenhuma desloque-se novamente, adicione `| more` a comandos para apresentar a página de uma saída de cada vez. Uso `<spacebar>` para ver a página seguinte, ou `<enter>` para ver a linha seguinte.  

`SHIFT+INSERT` é o atalho de colar para a janela de consola de série.

Devido a memória intermédia de tela limitado do SAC, mais comandos podem ser mais fácil digitar o num editor de texto local e, em seguida, colados SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Ver e editar as definições de registo do Windows
### <a name="verify-rdp-is-enabled"></a>Certifique-se de que RDP está ativado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A segunda chave (em \Policies) só existem se estiver configurada a definição de política de grupo relevantes.

### <a name="enable-rdp"></a>Ative o RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A segunda chave (em \Policies) seria apenas necessário se a definição de política de grupo relevantes tinha sido configurada. Valor irá ser reescrito em atualização de política de grupo seguinte se estiver configurado na política de grupo.

## <a name="manage-windows-services"></a>Gerir serviços do Windows

### <a name="view-service-state"></a>Estado do serviço de exibição
`sc query termservice`
###  <a name="view-service-logon-account"></a>Conta de início de sessão do serviço de vista
`sc qc termservice`
### <a name="set-service-logon-account"></a>Definir conta de início de sessão do serviço 
`sc config termservice obj= "NT Authority\NetworkService"`

É necessário um espaço depois do sinal de igual.
### <a name="set-service-start-type"></a>Tipo de início de serviço do conjunto
`sc config termservice start= demand` 

É necessário um espaço depois do sinal de igual. Os valores de início de possíveis incluem `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Dependências do serviço de conjunto
`sc config termservice depend= RPCSS`

É necessário um espaço depois do sinal de igual.
### <a name="start-service"></a>Iniciar serviço
`net start termservice`

ou

`sc start termservice`
### <a name="stop-service"></a>Parar serviço
`net stop termservice`

ou

`sc stop termservice`
## <a name="manage-networking-features"></a>Gerir recursos de rede
### <a name="show-nic-properties"></a>Mostrar propriedades da NIC
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Mostrar propriedades IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Mostrar a configuração de IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Ativar o NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Definir o NIC para utilizar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Para obter mais informações sobre `netsh`, [clique aqui](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

VMs do Azure sempre devem ser configuradas no sistema operacional para utilizar DHCP para obter um endereço IP do convidado. A configuração de IP estática do Azure continua a utilizar DHCP para fornecer o IP estático para a VM.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Ping de porta  
Instalar o cliente telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testar conectividade

`telnet bing.com 80`

Para remover o cliente telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Quando limitado aos métodos disponíveis no Windows, por predefinição, o PowerShell pode ser uma melhor abordagem para testar a conectividade de porta. Consulte a secção de PowerShell abaixo para obter exemplos.
### <a name="test-dns-name-resolution"></a>Testar a resolução de nome DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Mostrar a regra de Firewall do Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Desativar a Firewall do Windows
`netsh advfirewall set allprofiles state off`

Pode utilizar este comando quando a resolução de problemas temporariamente descartar o Firewall do Windows. É ativar na próxima reinicialização ou se a ativar com o comando abaixo. Não pare o serviço de Firewall do Windows (MPSSVC) ou o serviço de motor de filtragem Base (BFE) como forma de descartar o Firewall do Windows. Parar MPSSVC ou BFE resultará em toda a conectividade a ser bloqueada.
### <a name="enable-windows-firewall"></a>Ativar a Firewall do Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gerir utilizadores e grupos
### <a name="create-local-user-account"></a>Criar conta de utilizador local
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Adicionar utilizador local ao grupo local
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Certifique-se de que a conta de utilizador está ativada
`net user <username> | find /i "active"`

VMs do Azure criadas a partir de imagem generalizada terão a conta de administrador local, o nome mudada para o nome especificado durante o aprovisionamento da VM. Portanto, normalmente, não será `Administrator`.
### <a name="enable-user-account"></a>Ativar a conta de utilizador
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Ver propriedades da conta de utilizador
`net user <username>`

Exemplos de linhas de interesse de uma conta de administrador local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Ver grupos locais
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Gerir o registo de eventos do Windows
### <a name="query-event-log-errors"></a>Erros de registo de eventos de consulta
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Alteração `/c:10` para o número pretendido de eventos para retornar ou movê-lo para retornar todos os eventos que corresponde ao filtro.
### <a name="query-event-log-by-event-id"></a>Registo de eventos de consulta por ID de evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Registo de eventos por ID de evento e o fornecedor de consulta
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar o log de eventos por ID de evento e o fornecedor nas últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Utilize `604800000` para procurar novamente 7 dias, em vez de 24 horas.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID de evento, o fornecedor e o EventData nos últimos 7 dias
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visualizar ou remover aplicações instaladas
### <a name="list-installed-applications"></a>Listar instalado aplicações
`wmic product get Name,InstallDate | sort /r | more`

O `sort /r` tipos em ordem decrescente, pela instalar data torna mais fácil ver o que foi recentemente instalado. Uso `<spacebar>` para avançar para a página seguinte da saída, ou `<enter>` para fazer avançar a uma linha.
### <a name="uninstall-an-application"></a>Desinstalar um aplicativo
`wmic path win32_product where name="<name>" call uninstall`

Substitua `<name>` com o nome devolvido no comando acima para a aplicação que pretende remover.

## <a name="file-system-management"></a>Gestão de sistema de ficheiros
### <a name="get-file-version"></a>Obter a versão do ficheiro
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Neste exemplo devolve a versão do ficheiro do controlador NIC virtual, que é netvsc.sys, netvsc63.sys ou netvsc60.sys dependendo da versão do Windows.
### <a name="scan-for-system-file-corruption"></a>Verificar a existência de danos no ficheiro de sistema
`sfc /scannow`

Consulte também [reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Verificar a existência de danos no ficheiro de sistema
`dism /online /cleanup-image /scanhealth`

Consulte também [reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Permissões de ficheiros de exportação para o arquivo de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Guardar permissões de arquivo no ficheiro ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Restaurar permissões de ficheiros a partir do ficheiro ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

O caminho ao usar `/restore` tem de ser a pasta de principal da pasta que especificou quando utilizar `/save`. Neste exemplo, `\RSA` é o principal do `\MachineKeys` pasta especificada no `/save` exemplo acima.
### <a name="take-ntfs-ownership-of-a-folder"></a>Obter a propriedade NTFS de uma pasta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permissões de NTFS recursivamente uma pasta
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Gerir dispositivos
### <a name="remove-non-present-pnp-devices"></a>Remover dispositivos PNP de não presente
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Gerir a política de grupo
### <a name="force-group-policy-update"></a>Forçar a atualização da política de grupo
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Diversas tarefas
### <a name="show-os-version"></a>Mostrar a versão do SO
`ver`

ou 

`wmic os get caption,version,buildnumber /format:list`

ou 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Data de instalação do SO do Vista
`systeminfo | find /i "original"`

ou 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Vista de hora do último arranque
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Fuso horário de vista
`systeminfo | find /i "time zone"`

ou

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Reiniciar o Windows
`shutdown /r /t 0`

Adicionar `/f` irá forçar a aplicativos em execução para fechar sem avisará os utilizadores.
### <a name="detect-safe-mode-boot"></a>Detetar o arranque do modo de segurança
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Comandos do Windows - PowerShell

Para executar o PowerShell no SAC, depois de atingir um prompt de comando, escreva:

`powershell <enter>`

>[!CAUTION]
Remova o módulo de PSReadLine da sessão do PowerShell antes de executar outros comandos do PowerShell. Existe um problema conhecido, onde os caracteres Extras podem ser introduzidos no texto colado da área de transferência se PSReadLine estiver em execução numa sessão do PowerShell no SAC.

Verifique primeiro se PSReadLine é carregado. Ele é carregado por predefinição no Windows Server 2016, Windows 10 e versões posteriores do Windows. Ele apenas estarão presente em versões anteriores do Windows se tivesse sido instalado manualmente. 

Se este comando devolve a uma linha de comandos sem saída, em seguida, o módulo não foi carregado e pode continuar com a sessão do PowerShell na SAC como normal.

`get-module psreadline`

Se o comando acima devolve a versão do módulo de PSReadLine, execute o seguinte comando para descarregá-lo. Este comando não elimina nem desinstalar o módulo, ele descarregará apenas da sessão atual do PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Ver e editar as definições de registo do Windows
### <a name="verify-rdp-is-enabled"></a>Certifique-se de que RDP está ativado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A segunda chave (em \Policies) só existem se estiver configurada a definição de política de grupo relevantes.
### <a name="enable-rdp"></a>Ative o RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A segunda chave (em \Policies) seria apenas necessário se a definição de política de grupo relevantes tinha sido configurada. Valor irá ser reescrito em atualização de política de grupo seguinte se estiver configurado na política de grupo.
## <a name="manage-windows-services"></a>Gerir serviços do Windows
### <a name="view-service-details"></a>Ver detalhes do serviço
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` pode ser utilizado, mas não inclui a conta de início de sessão do serviço. `Get-WmiObject win32-service` faz.
### <a name="set-service-logon-account"></a>Definir conta de início de sessão do serviço
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ao utilizar uma conta de serviço que `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, ou `LocalSystem`, especifique a palavra-passe de conta como o último argumento (oitavo) após o nome da conta.
### <a name="set-service-startup-type"></a>Definir tipo de arranque do serviço
`set-service termservice -startuptype Manual`

`Set-service` aceita `Automatic`, `Manual`, ou `Disabled` para o tipo de arranque.
### <a name="set-service-dependencies"></a>Dependências do serviço de conjunto
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Iniciar serviço
`start-service termservice`
### <a name="stop-service"></a>Parar serviço
`stop-service termservice`
## <a name="manage-networking-features"></a>Gerir recursos de rede
### <a name="show-nic-properties"></a>Mostrar propriedades da NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

ou 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` está disponível no 2012 e posterior, para utilização 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Mostrar propriedades IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Ativar o NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

ou

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` está disponível no 2012 e posterior, para utilização 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Definir o NIC para utilizar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` está disponível no 2012 +. Para 2008R2 utilizar `Get-WmiObject`. VMs do Azure sempre devem ser configuradas no sistema operacional para utilizar DHCP para obter um endereço IP do convidado. A configuração de IP estática do Azure continua a utilizar DHCP para fornecer o IP para a VM.
### <a name="ping"></a>Ping
`test-netconnection`

ou

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sem quaisquer parâmetros tentará fazer ping `internetbeacon.msedge.net`. Está disponível no 2012 +. Para 2008R2 utilizar `Get-WmiObject` como no segundo exemplo.
### <a name="port-ping"></a>Ping de porta
`test-netconnection -ComputerName bing.com -Port 80`

ou

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` está disponível no 2012 +. Para utilização do 2008 R2 `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testar a resolução de nome DNS
`resolve-dnsname bing.com` 

ou 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` está disponível no 2012 +. Para 2008R2 utilizar `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Mostrar a regra de firewall do Windows por nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Mostrar a regra de firewall do Windows por porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

ou

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` está disponível no 2012 +. Para 2008R2 utilizar o `hnetcfg.fwpolicy2` objeto COM. 
### <a name="disable-windows-firewall"></a>Desativar a firewall do Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` está disponível no 2012 +. Para 2008R2 utilizar `netsh advfirewall` como referenciado na secção CMD acima.
## <a name="manage-users-and-groups"></a>Gerir utilizadores e grupos
### <a name="create-local-user-account"></a>Criar conta de utilizador local
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Certifique-se de que a conta de utilizador está ativada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

ou 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` está disponível no 2012 +. Para 2008R2 utilizar `Get-WmiObject`. Este exemplo mostra a conta de administrador local incorporada, que tem sempre o SID `S-1-5-21-*-500`. VMs do Azure criadas a partir de imagem generalizada terão a conta de administrador local, o nome mudada para o nome especificado durante o aprovisionamento da VM. Portanto, normalmente, não será `Administrator`.
### <a name="add-local-user-to-local-group"></a>Adicionar utilizador local ao grupo local
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Ativar a conta de utilizador local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Este exemplo permite que a conta de administrador local incorporada, que tem sempre o SID `S-1-5-21-*-500`. VMs do Azure criadas a partir de imagem generalizada terão a conta de administrador local, o nome mudada para o nome especificado durante o aprovisionamento da VM. Portanto, normalmente, não será `Administrator`.
### <a name="view-user-account-properties"></a>Ver propriedades da conta de utilizador
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

ou 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` está disponível no 2012 +. Para 2008R2 utilizar `Get-WmiObject`. Este exemplo mostra a conta de administrador local incorporada, que tem sempre o SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Ver grupos locais
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` está disponível no 2012 +. Para 2008R2 utilizar `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Gerir o registo de eventos do Windows
### <a name="query-event-log-errors"></a>Erros de registo de eventos de consulta
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Alteração `/c:10` para o número pretendido de eventos para retornar ou movê-lo para retornar todos os eventos que corresponde ao filtro.
### <a name="query-event-log-by-event-id"></a>Registo de eventos de consulta por ID de evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Registo de eventos por ID de evento e o fornecedor de consulta
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar o log de eventos por ID de evento e o fornecedor nas últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Utilize `604800000` para procurar novamente 7 dias, em vez de 24 horas. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID de evento, o fornecedor e o EventData nos últimos 7 dias
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Visualizar ou remover aplicações instaladas
### <a name="list-installed-software"></a>Software de lista instalada
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Desinstalar o software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Gestão de sistema de ficheiros
### <a name="get-file-version"></a>Obter a versão do ficheiro
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Neste exemplo devolve a versão do ficheiro do controlador NIC virtual, que é denominada netvsc.sys, netvsc63.sys ou netvsc60.sys dependendo da versão do Windows.
### <a name="download-and-extract-file"></a>Baixe e extraia o ficheiro
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Este exemplo cria um `c:\bin` pasta, em seguida, transfere e extrai o Sysinternals suite de ferramentas em `c:\bin`.
## <a name="miscellaneous-tasks"></a>Diversas tarefas
### <a name="show-os-version"></a>Mostrar a versão do SO
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Data de instalação do SO do Vista
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Vista de hora do último arranque
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Ver o tempo de atividade do Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Devolve o tempo de atividade como `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, por exemplo `49:16:48:00.00`. 
### <a name="restart-windows"></a>Reiniciar o Windows
`restart-computer`

Adicionar `-force` irá forçar a aplicativos em execução para fechar sem avisará os utilizadores.
## <a name="instance-metadata"></a>Metadados de instância

Pode consultar metadados de instância do Azure a partir na sua VM do Azure para ver os detalhes como osType, localização, vmSize, vmId, nome, resourceGroupName, subscriptionId, privateIpAddress e publicIpAddress.

Consultar os metadados de instância requer conectividade de rede de convidado de bom estado de funcionamento, porque faz uma chamada REST através do anfitrião do Azure para o serviço de metadados de instância. Então, se for capaz de consultar os metadados de instância, que informa que o convidado é capaz de comunicar através da rede a um serviço alojado no Azure.

Para obter mais informações, consulte [serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadados de instância
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Tipo de SO (metadados de instância)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Localização (metadados de instância)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Tamanho (metadados de instância)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID da VM (metadados de instância)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nome da VM (metadados de instância)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nome do grupo de recursos (metadados de instância)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID de subscrição (metadados de instância)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Etiquetas (metadados de instância)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID do grupo de colocação (metadados de instância)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domínio de falhas de plataforma (metadados de instância)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domínio de atualização de plataforma (metadados de instância)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Endereço de IP IPv4 privada (metadados de instância)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Endereço IP público IPv4 (metadados de instância)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Endereço de sub-rede de IPv4 do prefixo (metadados de instância)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Endereço IP IPv6 (metadados de instância)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Endereço MAC (metadados de instância)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Passos Seguintes
* A página de documentação de Windows de consola de série principal está localizada [aqui](serial-console-windows.md).
* Também está disponível para a consola de série [Linux](serial-console-linux.md) VMs.
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).
