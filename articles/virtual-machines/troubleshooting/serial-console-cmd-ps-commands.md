---
title: CMD e PowerShell em VMs do Windows do Azure | Microsoft Docs
description: Como usar comandos CMD e PowerShell no SAC em VMs do Windows do Azure
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 77fe6f1ce416df049928697d2c166e2aba0abfe2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935221"
---
# <a name="windows-commands---cmd-and-powershell"></a>Comandos do Windows – CMD e PowerShell

Esta seção inclui comandos de exemplo para executar tarefas comuns em cenários em que talvez seja necessário usar o SAC para acessar a VM do Windows, como quando você precisa solucionar problemas de falhas de conexão de RDP.

O SAC foi incluído em todas as versões do Windows desde o Windows Server 2003, mas está desabilitado por padrão. `sacdrv.sys` O SAC conta com o driver de kernel, o `Special Administration Console Helper` serviço`sacsvr`() e o `sacsess.exe` processo. Para obter mais informações, consulte [ferramentas e configurações dos serviços de gerenciamento de emergência](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

O SAC permite que você se conecte ao sistema operacional em execução por meio da porta serial. Quando você inicia o cmd do SAC `sacsess.exe` , `cmd.exe` o é iniciado em seu sistema operacional em execução. Você pode ver que, no Gerenciador de tarefas, se você RDP para sua VM ao mesmo tempo em que está conectado ao SAC por meio do recurso de console serial. O cmd que você acessa via SAC é o `cmd.exe` mesmo usado quando conectado via RDP. Todos os mesmos comandos e ferramentas estão disponíveis, incluindo a capacidade de iniciar o PowerShell a partir dessa instância CMD. Essa é uma grande diferença entre o SAC e o ambiente de recuperação do Windows (WinRE), pois o SAC permite que você gerencie seu sistema operacional em execução, em que o WinRE é inicializado em um sistema operacional mínimo diferente. Embora as VMs do Azure não ofereçam suporte à capacidade de acessar o WinRE, com o recurso do console serial, as VMs do Azure podem ser gerenciadas por meio do SAC.

Como o SAC é limitado a um buffer de tela 80x24 sem rolagem, `| more` adicione a comandos para exibir a saída de uma página por vez. Use `<spacebar>` para ver a próxima página ou `<enter>` para ver a próxima linha.  

`SHIFT+INSERT`é o atalho colar para a janela do console serial.

Devido ao buffer de tela limitado do SAC, os comandos mais longos podem ser mais fáceis de digitar em um editor de texto local e, em seguida, colados no SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar configurações do registro do Windows
### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A segunda chave (em \Policies) só existirá se a configuração de política de grupo relevante estiver configurada.

### <a name="enable-rdp"></a>Habilitar RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A segunda chave (em \Policies) só seria necessária se a configuração de política de grupo relevante tivesse sido configurada. O valor será reescrito na próxima atualização de política de grupo se estiver configurado na diretiva de grupo.

## <a name="manage-windows-services"></a>Gerenciar serviços do Windows

### <a name="view-service-state"></a>Exibir estado do serviço
`sc query termservice`
###  <a name="view-service-logon-account"></a>Exibir conta de logon de serviço
`sc qc termservice`
### <a name="set-service-logon-account"></a>Definir conta de logon de serviço 
`sc config termservice obj= "NT Authority\NetworkService"`

Um espaço é necessário após o sinal de igual.
### <a name="set-service-start-type"></a>Definir tipo de início do serviço
`sc config termservice start= demand` 

Um espaço é necessário após o sinal de igual. Os valores iniciais possíveis `boot`incluem `system` `auto`, `demand` ,,`delayed-auto`,,. `disabled`
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`sc config termservice depend= RPCSS`

Um espaço é necessário após o sinal de igual.
### <a name="start-service"></a>Iniciar serviço
`net start termservice`

ou

`sc start termservice`
### <a name="stop-service"></a>Parar serviço
`net stop termservice`

ou

`sc stop termservice`
## <a name="manage-networking-features"></a>Gerenciar recursos de rede
### <a name="show-nic-properties"></a>Mostrar propriedades da NIC
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Mostrar configuração de IPSec
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Habilitar NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Para obter mais informações `netsh`sobre, [clique aqui](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

As VMs do Azure sempre devem ser configuradas no sistema operacional convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa DHCP para fornecer o IP estático para a VM.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Ping da porta  
Instalar o cliente Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testar conectividade

`telnet bing.com 80`

Para remover o cliente Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Quando limitado a métodos disponíveis no Windows por padrão, o PowerShell pode ser uma abordagem melhor para testar a conectividade de porta. Consulte a seção do PowerShell abaixo para obter exemplos.
### <a name="test-dns-name-resolution"></a>Testar resolução de nome DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Mostrar regra de firewall do Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Desabilitar o Firewall do Windows
`netsh advfirewall set allprofiles state off`

Você pode usar esse comando ao solucionar problemas para descartar temporariamente o Firewall do Windows. Ele será habilitado na próxima reinicialização ou quando você habilitá-lo usando o comando a seguir. Não pare o serviço de firewall do Windows (MPSSVC) ou o serviço do mecanismo de filtragem base (BFE) como uma forma de descartar o Firewall do Windows. Parar o MPSSVC ou BFE fará com que toda a conectividade seja bloqueada.
### <a name="enable-windows-firewall"></a>Habilitar o Firewall do Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
### <a name="create-local-user-account"></a>Criar conta de usuário local
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Adicionar usuário local ao grupo local
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Verificar se a conta de usuário está habilitada
`net user <username> | find /i "active"`

As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Em geral, não `Administrator`será.
### <a name="enable-user-account"></a>Habilitar conta de usuário
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Exibir Propriedades da conta de usuário
`net user <username>`

Exemplo de linhas de interesse de uma conta de administrador local:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Exibir grupos locais
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
### <a name="query-event-log-errors"></a>Consultar erros do log de eventos
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos que correspondem ao filtro.
### <a name="query-event-log-by-event-id"></a>Consultar o log de eventos por ID do evento
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consultar o log de eventos por ID de evento e provedor
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar o log de eventos por ID de evento e provedor nas últimas 24 horas
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para procurar 7 dias em vez de 24 horas.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e EventData nos últimos 7 dias
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
### <a name="list-installed-applications"></a>Listar aplicativos instalados
`wmic product get Name,InstallDate | sort /r | more`

A `sort /r` data de instalação é classificada em ordem decrescente por para facilitar a visualização do que foi instalado recentemente. Use `<spacebar>` para avançar para a próxima página de saída ou `<enter>` para avançar uma linha.
### <a name="uninstall-an-application"></a>Desinstalar um aplicativo
`wmic path win32_product where name="<name>" call uninstall`

Substitua `<name>` pelo nome retornado no comando acima para o aplicativo que você deseja remover.

## <a name="file-system-management"></a>Gerenciamento do sistema de arquivos
### <a name="get-file-version"></a>Obter versão do arquivo
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Este exemplo retorna a versão do arquivo do driver NIC virtual, que é netvsc. sys, netvsc63. sys ou netvsc60. sys, dependendo da versão do Windows.
### <a name="scan-for-system-file-corruption"></a>Verificar se há danos no arquivo do sistema
`sfc /scannow`

Consulte também [reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Verificar se há danos no arquivo do sistema
`dism /online /cleanup-image /scanhealth`

Consulte também [reparar uma imagem do Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Exportar permissões de arquivo para arquivo de texto
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Salvar permissões de arquivo no arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Restaurar permissões de arquivo do arquivo ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

O caminho ao usar `/restore` precisa ser a pasta pai da pasta que você especificou ao usar `/save`o. Neste exemplo, `\RSA` é o pai `\MachineKeys` da pasta especificada no `/save` exemplo acima.
### <a name="take-ntfs-ownership-of-a-folder"></a>Usar a propriedade NTFS de uma pasta
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Conceder permissões NTFS a uma pasta recursivamente
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Gerenciar dispositivos
### <a name="remove-non-present-pnp-devices"></a>Remover dispositivos PNP não presentes
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Gerenciar Política de Grupo
### <a name="force-group-policy-update"></a>Forçar atualização de política de grupo
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Tarefas diversas
### <a name="show-os-version"></a>Mostrar versão do so
`ver`

ou 

`wmic os get caption,version,buildnumber /format:list`

ou 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Exibir data de instalação do so
`systeminfo | find /i "original"`

ou 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Exibir hora da última inicialização
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Exibir fuso horário
`systeminfo | find /i "time zone"`

ou

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Reiniciar o Windows
`shutdown /r /t 0`

A `/f` adição forçará a execução de aplicativos que serão fechados sem aviso de usuários.
### <a name="detect-safe-mode-boot"></a>Detectar inicialização no modo de segurança
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Comandos do Windows – PowerShell

Para executar o PowerShell no SAC, depois de acessar um prompt CMD, digite:

`powershell <enter>`

> [!CAUTION]
> Remova o módulo PSReadLine da sessão do PowerShell antes de executar qualquer outro comando do PowerShell. Há um problema conhecido em que caracteres extras podem ser introduzidos no texto colado da área de transferência se PSReadLine estiver em execução em uma sessão do PowerShell no SAC.

Primeiro verifique se PSReadLine está carregado. Ele é carregado por padrão no Windows Server 2016, Windows 10 e versões posteriores do Windows. Ele só estaria presente em versões anteriores do Windows se ele tivesse sido instalado manualmente. 

Se esse comando retornar a um prompt sem saída, o módulo não foi carregado e você poderá continuar usando a sessão do PowerShell no SAC normalmente.

`get-module psreadline`

Se o comando acima retornar a versão do módulo PSReadLine, execute o comando a seguir para descarregá-lo. Esse comando não exclui nem desinstala o módulo, ele apenas o descarrega da sessão atual do PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Exibir e editar configurações do registro do Windows
### <a name="verify-rdp-is-enabled"></a>Verificar se o RDP está habilitado
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A segunda chave (em \Policies) só existirá se a configuração de política de grupo relevante estiver configurada.
### <a name="enable-rdp"></a>Habilitar RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A segunda chave (em \Policies) só seria necessária se a configuração de política de grupo relevante tivesse sido configurada. O valor será reescrito na próxima atualização de política de grupo se estiver configurado na diretiva de grupo.
## <a name="manage-windows-services"></a>Gerenciar serviços do Windows
### <a name="view-service-details"></a>Exibir detalhes do serviço
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`pode ser usado, mas não inclui a conta de logon do serviço. `Get-WmiObject win32-service`existe.
### <a name="set-service-logon-account"></a>Definir conta de logon de serviço
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ao usar uma conta de serviço diferente `NT AUTHORITY\LocalService`de `NT AUTHORITY\NetworkService`, ou `LocalSystem`, especifique a senha da conta como o último argumento (oitavo) após o nome da conta.
### <a name="set-service-startup-type"></a>Definir tipo de inicialização de serviço
`set-service termservice -startuptype Manual`

`Set-service`aceita `Automatic`, `Manual` ou`Disabled` para o tipo de inicialização.
### <a name="set-service-dependencies"></a>Definir dependências de serviço
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Iniciar serviço
`start-service termservice`
### <a name="stop-service"></a>Parar serviço
`stop-service termservice`
## <a name="manage-networking-features"></a>Gerenciar recursos de rede
### <a name="show-nic-properties"></a>Mostrar propriedades da NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

ou 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`está disponível em 2012 +, para uso `Get-WmiObject`do 2008R2.
### <a name="show-ip-properties"></a>Mostrar propriedades de IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Habilitar NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

ou

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`está disponível em 2012 +, para uso `Get-WmiObject`do 2008R2.
### <a name="set-nic-to-use-dhcp"></a>Definir NIC para usar DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`está disponível em 2012 +. Para uso `Get-WmiObject`2008R2. As VMs do Azure sempre devem ser configuradas no sistema operacional convidado para usar o DHCP para obter um endereço IP. A configuração de IP estático do Azure ainda usa DHCP para fornecer o IP para a VM.
### <a name="ping"></a>Ping
`test-netconnection`

ou

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`sem nenhum parâmetro, tentará executar `internetbeacon.msedge.net`ping. Ele está disponível em 2012 +. Para 2008R2, `Get-WmiObject` use como no segundo exemplo.
### <a name="port-ping"></a>Ping da porta
`test-netconnection -ComputerName bing.com -Port 80`

ou

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`está disponível em 2012 +. Para uso 2008R2`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testar resolução de nome DNS
`resolve-dnsname bing.com` 

ou 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`está disponível em 2012 +. Para uso `System.Net.DNS`2008R2.
### <a name="show-windows-firewall-rule-by-name"></a>Mostrar regra de firewall do Windows por nome
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Mostrar regra de firewall do Windows por porta
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

ou

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`está disponível em 2012 +. Para 2008R2, use `hnetcfg.fwpolicy2` o objeto com. 
### <a name="disable-windows-firewall"></a>Desabilitar o Firewall do Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`está disponível em 2012 +. Para 2008R2, `netsh advfirewall` use como referenciado na seção cmd acima.
## <a name="manage-users-and-groups"></a>Gerenciar usuários e grupos
### <a name="create-local-user-account"></a>Criar conta de usuário local
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Verificar se a conta de usuário está habilitada
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

ou 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`está disponível em 2012 +. Para uso `Get-WmiObject`2008R2. Este exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Em geral, não `Administrator`será.
### <a name="add-local-user-to-local-group"></a>Adicionar usuário local ao grupo local
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Habilitar conta de usuário local
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Este exemplo habilita a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`. As VMs do Azure criadas a partir da imagem generalizada terão a conta de administrador local renomeada para o nome especificado durante o provisionamento da VM. Em geral, não `Administrator`será.
### <a name="view-user-account-properties"></a>Exibir Propriedades da conta de usuário
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

ou 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`está disponível em 2012 +. Para uso `Get-WmiObject`2008R2. Este exemplo mostra a conta de administrador local interna, que sempre tem o SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Exibir grupos locais
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`está disponível em 2012 +. Para uso `Get-WmiObject`2008R2.
## <a name="manage-the-windows-event-log"></a>Gerenciar o log de eventos do Windows
### <a name="query-event-log-errors"></a>Consultar erros do log de eventos
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Altere `/c:10` para o número desejado de eventos a serem retornados ou mova-o para retornar todos os eventos que correspondem ao filtro.
### <a name="query-event-log-by-event-id"></a>Consultar o log de eventos por ID do evento
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Consultar o log de eventos por ID de evento e provedor
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Consultar o log de eventos por ID de evento e provedor nas últimas 24 horas
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Use `604800000` para procurar 7 dias em vez de 24 horas. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Consultar o log de eventos por ID do evento, provedor e EventData nos últimos 7 dias
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Exibir ou remover aplicativos instalados
### <a name="list-installed-software"></a>Listar software instalado
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Desinstalar software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Gerenciamento do sistema de arquivos
### <a name="get-file-version"></a>Obter versão do arquivo
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Este exemplo retorna a versão do arquivo do driver NIC virtual, chamado netvsc. sys, netvsc63. sys ou netvsc60. sys, dependendo da versão do Windows.
### <a name="download-and-extract-file"></a>Baixar e extrair arquivo
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Este exemplo cria uma `c:\bin` pasta e, em seguida, baixa e extrai o pacote de ferramentas `c:\bin`do Sysinternals para o.
## <a name="miscellaneous-tasks"></a>Tarefas diversas
### <a name="show-os-version"></a>Mostrar versão do so
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Exibir data de instalação do so
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Exibir hora da última inicialização
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Exibir tempo de atividade do Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Retorna o tempo `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`de atividade como `49:16:48:00.00`, por exemplo. 
### <a name="restart-windows"></a>Reiniciar o Windows
`restart-computer`

A `-force` adição forçará a execução de aplicativos que serão fechados sem aviso de usuários.
## <a name="instance-metadata"></a>Metadados da instância

Você pode consultar os metadados de instância do Azure de dentro de sua VM do Azure para exibir detalhes como osType, Location, vmSize, vmId, Name, resourceGroupName, SubscriptionId, privateIpAddress e publicIpAddress.

A consulta de metadados de instância requer conectividade de rede de convidado íntegra, pois faz uma chamada REST pelo host do Azure para o serviço de metadados de instância. Portanto, se você for capaz de consultar metadados de instância, isso informará que o convidado é capaz de se comunicar pela rede a um serviço hospedado pelo Azure.

Para obter mais informações, consulte [serviço de metadados de instância do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Metadados da instância
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Tipo de so (metadados da instância)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Local (metadados da instância)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Tamanho (metadados da instância)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>ID da VM (metadados da instância)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nome da VM (metadados da instância)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nome do grupo de recursos (metadados da instância)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>ID da assinatura (metadados da instância)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Marcas (metadados da instância)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>ID do grupo de posicionamento (metadados da instância)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domínio de falha da plataforma (metadados da instância)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domínio de atualização da plataforma (metadados da instância)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Endereço IP privado IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Endereço IP público IPv4 (metadados da instância)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Endereço/prefixo da sub-rede IPv4 (metadados da instância)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Endereço IP IPv6 (metadados da instância)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Endereço MAC (metadados da instância)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Passos seguintes
* A página principal da documentação do Windows do console serial está localizada [aqui](serial-console-windows.md).
* Também está disponível para a consola de série [Linux](serial-console-linux.md) VMs.
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md).
