---
title: CMD e PowerShell em VMs Do Windows Azure / Microsoft Docs
description: Como utilizar comandos CMD e PowerShell dentro do SAC em VMs Do Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167031"
---
# <a name="windows-commands---cmd-and-powershell"></a>Comandos Windows - CMD e PowerShell

Esta secção inclui comandos de exemplo para realizar tarefas comuns em cenários onde poderá ser necessário utilizar o SAC para aceder ao seu Windows VM, como quando precisa de resolver falhas de ligação RDP.

O SAC foi incluído em todas as versões do Windows desde o Windows Server 2003, mas está desativado por padrão. A SAC conta com o `sacdrv.sys` condutor de kernel, o serviço `Special Administration Console Helper` (`sacsvr`) e o processo `sacsess.exe`. Para mais informações, consulte [ferramentas e configurações dos Serviços de Gestão de Emergência.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))

O SAC permite-lhe ligar-se ao seu SISTEMA de funcionamento através de uma porta de série. Quando lança o CMD a partir da SAC, `sacsess.exe` lança `cmd.exe` dentro do seu sistema operativo operativo. Pode supor isso no Task Manager se rdP para o seu VM ao mesmo tempo que estiver ligado ao SAC através da funcionalidade de consola em série. O CMD a que acede através do SAC é o mesmo `cmd.exe` que utiliza quando ligado via RDP. Todos os mesmos comandos e ferramentas estão disponíveis, incluindo a capacidade de lançar powerShell a partir dessa instância CMD. Esta é uma grande diferença entre o SAC e o Windows Recovery Environment (WinRE) na qual o SAC está a permitir-lhe gerir o seu sistema operativo operativo, onde o WinRE entra num SISTEMA diferente e mínimo. Embora os VMs Azure não suportem a capacidade de aceder ao WinRE, com a funcionalidade de consola em série, os VMs Azure podem ser geridos via SAC.

Como o SAC está limitado a um tampão de ecrã 80x24 sem deslocação para trás, adicione `| more` aos comandos para exibir a saída uma página de cada vez. Use `<spacebar>` para ver a página seguinte, ou `<enter>` para ver a próxima linha.

`SHIFT+INSERT` é o atalho da pasta para a janela da consola em série.

Devido ao tampão de ecrã limitado da SAC, comandos mais longos podem ser mais fáceis de escrever num editor de texto local e depois colados no SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Ver e editar definições de registo do Windows
### <a name="verify-rdp-is-enabled"></a>Verifique se rdp está ativado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A segunda chave (no âmbito das \Policies) só existirá se a definição de política de grupo relevante estiver configurada.

### <a name="enable-rdp"></a>Ativar RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

A segunda chave (no âmbito das \Policies) só seria necessária se a definição da política de grupo relevante tivesse sido configurada. O valor será reescrito na próxima atualização da política do grupo se estiver configurado na política do grupo.

## <a name="manage-windows-services"></a>Gerir os Serviços Windows

### <a name="view-service-state"></a>Ver estado de serviço
`sc query termservice`
###  <a name="view-service-logon-account"></a>Ver conta de logon de serviço
`sc qc termservice`
### <a name="set-service-logon-account"></a>Definir conta de logon de serviço
`sc config termservice obj= "NT Authority\NetworkService"`

Um espaço é necessário após o sinal igual.
### <a name="set-service-start-type"></a>Definir tipo de início de serviço
`sc config termservice start= demand`

Um espaço é necessário após o sinal igual. Os possíveis valores de início incluem `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`sc config termservice depend= RPCSS`

Um espaço é necessário após o sinal igual.
### <a name="start-service"></a>Iniciar o serviço
`net start termservice`

ou

`sc start termservice`
### <a name="stop-service"></a>Serviço de paragem
`net stop termservice`

ou

`sc stop termservice`
## <a name="manage-networking-features"></a>Gerir funcionalidades de networking
### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`netsh interface show interface`
### <a name="show-ip-properties"></a>Mostrar propriedades IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Mostrar a configuração do IPSec
`netsh nap client show configuration`
### <a name="enable-nic"></a>Ativar nic
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Para mais informações sobre `netsh`, [clique aqui](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Os VMs Azure devem estar sempre configurados no OS de hóspedes para utilizar o DHCP para obter um endereço IP. A definição ip estática Azure ainda utiliza DHCP para dar o IP estático ao VM.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Porto ping
Instale o cliente da telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testar conectividade

`telnet bing.com 80`

Para remover o cliente da rede

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Quando limitado aos métodos disponíveis no Windows por padrão, o PowerShell pode ser uma melhor abordagem para testar a conectividade da porta. Consulte a secção PowerShell abaixo, por exemplo.
### <a name="test-dns-name-resolution"></a>Resolução de nome sanções dns de teste
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Mostrar a regra do Firewall do Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Desativar firewall do Windows
`netsh advfirewall set allprofiles state off`

Pode utilizar este comando quando se resolver problemas para excluir temporariamente o Firewall do Windows. Ativar-se-á no próximo reinício ou quando o ativar utilizando o comando abaixo. Não pare o serviço Windows Firewall (MPSSVC) ou o serviço Base Filtering Engine (BFE) como forma de excluir o Firewall do Windows. Parar MPSSVC ou BFE resultará no bloqueio de toda a conectividade.
### <a name="enable-windows-firewall"></a>Ativar a Firewall do Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gerir os Utilizadores e os Grupos
### <a name="create-local-user-account"></a>Criar conta de utilizador local
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Adicione o utilizador local ao grupo local
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Verifique se a conta do utilizador está ativada
`net user <username> | find /i "active"`

Os VMs azure criados a partir de imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento de VM. Portanto, normalmente não será `Administrator`.
### <a name="enable-user-account"></a>Ativar a conta do utilizador
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Ver propriedades da conta do utilizador
`net user <username>`

Exemplo de linhas de interesse de uma conta de administração local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Ver grupos locais
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Gerir o Registo de Eventos do Windows
### <a name="query-event-log-errors"></a>Erros de registo de eventos de consulta
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Mude `/c:10` ao número desejado de eventos para regressar, ou movê-lo para devolver todos os eventos correspondentes ao filtro.
### <a name="query-event-log-by-event-id"></a>Consulta registro de evento por Id de evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consulta registo de eventos por Id de evento e fornecedor
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consulta registro de evento por Id de evento e fornecedor nas últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para olhar para trás 7 dias em vez de 24 horas.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consulta registo de eventos por Event ID, Fornecedor e EventData nos últimos 7 dias
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Ver ou remover aplicações instaladas
### <a name="list-installed-applications"></a>Lista de aplicações instaladas
`wmic product get Name,InstallDate | sort /r | more`

Os `sort /r` classifica-se por data de instalação para facilitar a ver o que foi recentemente instalado. Utilize `<spacebar>` para avançar para a próxima página de saída, ou `<enter>` para avançar uma linha.
### <a name="uninstall-an-application"></a>Desinstalar uma aplicação
`wmic path win32_product where name="<name>" call uninstall`

Substitua `<name>` pelo nome devolvido no comando acima para a aplicação que pretende remover.

## <a name="file-system-management"></a>Gestão do Sistema de Ficheiros
### <a name="get-file-version"></a>Obtenha versão de arquivo
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Este exemplo devolve a versão de ficheiro do controlador VIRTUAL NIC, que é netvsc.sys, netvsc63.sys ou netvsc60.sys dependendo da versão Windows.
### <a name="scan-for-system-file-corruption"></a>Scan para corrupção de ficheiros do sistema
`sfc /scannow`

Ver também [Reparar uma Imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Scan para corrupção de ficheiros do sistema
`dism /online /cleanup-image /scanhealth`

Ver também [Reparar uma Imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Autorizações de ficheiros de exportação para ficheiros de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Guardar permissões de ficheiros para ficheiro ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Restaurar permissões de ficheiros do ficheiro ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

O caminho ao utilizar `/restore` tem de ser a pasta-mãe da pasta que especificou ao utilizar `/save`. Neste exemplo, `\RSA` é o progenitor da pasta `\MachineKeys` especificada no exemplo `/save` acima.
### <a name="take-ntfs-ownership-of-a-folder"></a>Tome a propriedade ntfs de uma pasta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permissões ntfs para uma pasta de forma recursiva
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Gerir Dispositivos
### <a name="remove-non-present-pnp-devices"></a>Remova dispositivos PNP não presentes
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Gerir a Política de Grupo
### <a name="force-group-policy-update"></a>Atualização da política do grupo de força
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Tarefas diversas
### <a name="show-os-version"></a>Ver versão OS
`ver`

ou

`wmic os get caption,version,buildnumber /format:list`

ou

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Ver data de instalação de OS
`systeminfo | find /i "original"`

ou

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Ver última hora de arranque
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Ver fuso horário
`systeminfo | find /i "time zone"`

ou

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Reiniciar janelas
`shutdown /r /t 0`

Adicionar `/f` forçará as aplicações a executar sem avisar os utilizadores.
### <a name="detect-safe-mode-boot"></a>Detetar bota de modo seguro
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Comandos Windows - PowerShell

Para executar a PowerShell em SAC, depois de chegar a um aviso CMD, escreva:

`powershell <enter>`

> [!CAUTION]
> Retire o módulo PSReadLine da sessão PowerShell antes de executar quaisquer outros comandos PowerShell. Existe uma questão conhecida em que personagens extra podem ser introduzidos em texto colado a partir da área de clipe se o PSReadLine estiver a decorrer numa sessão da PowerShell no SAC.

Primeiro verifique se o PSReadLine está carregado. É carregado por padrão no Windows Server 2016, Windows 10 e versões posteriores do Windows. Só estaria presente em versões anteriores do Windows se tivesse sido instalada manualmente.

Se este comando voltar a um pedido sem saída, então o módulo não foi carregado e pode continuar a utilizar a sessão PowerShell no SAC normalmente.

`get-module psreadline`

Se o comando acima devolver a versão do módulo PSReadLine, execute o seguinte comando para o descarregar. Este comando não elimina nem desinstala o módulo, só o descarrega a partir da atual sessão PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Ver e editar definições de registo do Windows
### <a name="verify-rdp-is-enabled"></a>Verifique se rdp está ativado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A segunda chave (no âmbito das \Policies) só existirá se a definição de política de grupo relevante estiver configurada.
### <a name="enable-rdp"></a>Ativar RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A segunda chave (no âmbito das \Policies) só seria necessária se a definição da política de grupo relevante tivesse sido configurada. O valor será reescrito na próxima atualização da política do grupo se estiver configurado na política do grupo.
## <a name="manage-windows-services"></a>Gerir os Serviços Windows
### <a name="view-service-details"></a>Ver detalhes do serviço
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` pode ser usado, mas não inclui a conta de logon de serviço. `Get-WmiObject win32-service` tem.
### <a name="set-service-logon-account"></a>Definir conta de logon de serviço
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ao utilizar uma conta de serviço que não seja `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, ou `LocalSystem`, especifique a palavra-passe da conta como o último (oitavo) argumento após o nome da conta.
### <a name="set-service-startup-type"></a>Definir tipo de arranque de serviço
`set-service termservice -startuptype Manual`

`Set-service` aceita `Automatic`, `Manual`ou `Disabled` para o tipo de arranque.
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Iniciar o serviço
`start-service termservice`
### <a name="stop-service"></a>Serviço de paragem
`stop-service termservice`
## <a name="manage-networking-features"></a>Gerir funcionalidades de networking
### <a name="show-nic-properties"></a>Mostrar propriedades NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

ou

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` está disponível em 2012+, para a utilização de 2008R2 `Get-WmiObject`.
### <a name="show-ip-properties"></a>Mostrar propriedades IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Ativar nic
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

ou

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` está disponível em 2012+, para a utilização de 2008R2 `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Os VMs Azure devem estar sempre configurados no OS de hóspedes para utilizar o DHCP para obter um endereço IP. A definição ip estática Azure ainda utiliza DHCP para dar o IP ao VM.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> O cmdlet Write-Progress não pode funcionar com este comando. Como mitigação, pode executá`$ProgressPreference = "SilentlyContinue"` na PowerShell para desativar a barra de progresso.

ou

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` sem parâmetros tentará `internetbeacon.msedge.net`. Está disponível em 2012+. Para 2008R2 use `Get-WmiObject` como no segundo exemplo.
### <a name="port-ping"></a>Porto Ping
`test-netconnection -ComputerName bing.com -Port 80`

ou

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` está disponível em 2012+. Para 2008R2 use `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Resolução de nome sanções dns de teste
`resolve-dnsname bing.com`

ou

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` está disponível em 2012+. Para 2008R2 use `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Mostrar a regra da firewall do Windows pelo nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Mostrar a regra da firewall do Windows por porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

ou

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` está disponível em 2012+. Para 2008R2 utilize o `hnetcfg.fwpolicy2` objeto COM.
### <a name="disable-windows-firewall"></a>Desativar firewall windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` está disponível em 2012+. Para 2008R2, `netsh advfirewall` conforme referido na secção CMD acima.
## <a name="manage-users-and-groups"></a>Gerir os Utilizadores e os Grupos
### <a name="create-local-user-account"></a>Criar conta de utilizador local
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Verifique se a conta do utilizador está ativada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

ou

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Este exemplo mostra a conta de administrador local incorporada, que tem sempre SID `S-1-5-21-*-500`. Os VMs azure criados a partir de imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento de VM. Portanto, normalmente não será `Administrator`.
### <a name="add-local-user-to-local-group"></a>Adicione o utilizador local ao grupo local
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Ativar a conta de utilizador local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

Este exemplo permite a conta de administrador local incorporada, que tem sempre sid `S-1-5-21-*-500`. Os VMs azure criados a partir de imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento de VM. Portanto, normalmente não será `Administrator`.
### <a name="view-user-account-properties"></a>Ver propriedades da conta do utilizador
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

ou

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`. Este exemplo mostra a conta de administrador local incorporada, que tem sempre SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Ver grupos locais
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` está disponível em 2012+. Para 2008R2 use `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Gerir o Registo de Eventos do Windows
### <a name="query-event-log-errors"></a>Erros de registo de eventos de consulta
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Mude `/c:10` ao número desejado de eventos para regressar, ou movê-lo para devolver todos os eventos correspondentes ao filtro.
### <a name="query-event-log-by-event-id"></a>Consulta registro de evento por Id de evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consulta registo de eventos por Id de evento e fornecedor
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consulta registro de evento por Id de evento e fornecedor nas últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para olhar para trás 7 dias em vez de 24 horas. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consulta registo de eventos por Event ID, Fornecedor e EventData nos últimos 7 dias
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Ver ou remover aplicações instaladas
### <a name="list-installed-software"></a>Lista de software instalado
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Desinstalar software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Gestão do Sistema de Ficheiros
### <a name="get-file-version"></a>Obtenha versão de arquivo
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Este exemplo devolve a versão de ficheiro do controlador VIRTUAL NIC, que é chamado netvsc.sys, netvsc63.sys ou netvsc60.sys dependendo da versão Windows.
### <a name="download-and-extract-file"></a>Ficheiro de descarregamento e extrato
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Este exemplo cria uma pasta `c:\bin`, depois descarrega e extrai o conjunto de ferramentas Sysinternals para `c:\bin`.
## <a name="miscellaneous-tasks"></a>Tarefas diversas
### <a name="show-os-version"></a>Ver versão OS
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Ver data de instalação de OS
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Ver última hora de arranque
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Ver o tempo de uptime do Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Regressa como `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, por exemplo, `49:16:48:00.00`.
### <a name="restart-windows"></a>Reiniciar janelas
`restart-computer`

Adicionar `-force` forçará as aplicações a executar sem avisar os utilizadores.
## <a name="instance-metadata"></a>Caso de Metadados

Pode consultar metadados de instância azure dentro do seu VM Azure para visualizar detalhes como osType, Localização, vmSize, vmId, nome, recursoGroupName, subscriçãoId, privateIpAddress e publicIpAddress.

A consulta de metadados requer conectividade saudável da rede de hóspedes, porque faz uma chamada REST através do anfitrião Azure para o serviço de metadados de instância. Portanto, se você é capaz de consultar metadados de instância, isso lhe diz que o hóspede é capaz de comunicar através da rede a um serviço hospedado no Azure.

Para mais informações, consulte o [serviço de Metadados De instância sinuosa.](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="instance-metadata"></a>Metadados de instância
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Tipo OS (Metadados de Instância)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Localização (Metadados de Instância)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Tamanho (Metadados de Instância)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID VM (Metadados de Instância)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nome VM (Metadados de Instância)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nome do Grupo de Recursos (Metadados de Instância)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID de subscrição (Metadados de Instância)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tags (Metadados de Instância)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID do Grupo de Colocação (Metadados de Instância)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domínio de falha da plataforma (Metadados de Instância)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domínio de atualização da plataforma (Metadados de Instância)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Endereço IP privado IPv4 (Metadados de Instância)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Endereço IP público IPv4 (Metadados de Instância)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Endereço de sub-rede IPv4 / Prefixo (Metadados de Instância)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Endereço IP IPv6 (Metadados de Instância)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Endereço MAC (Metadados de Instância)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Passos seguintes
* A página de documentação principal da consola em série Windows está localizada [aqui](serial-console-windows.md).
* A consola em série também está disponível para [VMs Linux.](serial-console-linux.md)
* Saiba mais sobre diagnósticos de [arranque.](boot-diagnostics.md)
