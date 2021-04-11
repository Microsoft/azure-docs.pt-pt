---
title: Prepare um VHD do Windows para fazer o upload para o Azure
description: Saiba como preparar um Windows VHD ou VHDX para o fazer o upload para o Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: a177fc7e17dc91a0d57fa6dee87b80921d7fd8f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043585"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD ou um VHDX do Windows para carregamento para o Azure

Antes de carregar uma máquina virtual Windows (VM) das instalações para o Azure, tem de preparar o disco rígido virtual (VHD ou VHDX). O Azure suporta a geração 1 e a geração 2 VMs que estão em formato de ficheiro VHD e que têm um disco de tamanho fixo. O tamanho máximo permitido para o OS VHD numa geração 1 VM é de 2 TB.

Pode converter um ficheiro VHDX em VHD, converter um disco de expansão dinâmica para um disco de tamanho fixo, mas não pode alterar a geração de um VM. Para mais informações, consulte [Devo criar uma geração 1 ou 2 VM em Hiper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) [](../generation-2.md)

Para obter informações sobre a política de suporte para VMs Azure, consulte o [suporte de software do servidor da Microsoft para VMs Azure](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> As instruções deste artigo aplicam-se a:
>
> - A versão de 64 bits do Windows Server 2008 R2 e mais tarde os sistemas operativos Windows Server. Para obter informações sobre a execução de um sistema operativo de 32 bits em Azure, consulte [Suporte para sistemas operativos de 32 bits em VMs Azure](https://support.microsoft.com/help/4021388/).
> - Se qualquer ferramenta de Recuperação de Desastres for utilizada para migrar a carga de trabalho, como a Recuperação do Sítio Azure ou a Azure Migrate, este processo ainda é necessário no So convidado para preparar a imagem antes da migração.

## <a name="system-file-checker"></a> Verificador de Ficheiros do Sistema

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Executar o utilitário de verificação de ficheiros do sistema do Windows antes da generalização da imagem do SISTEMA

O Verificador de Ficheiros do Sistema (SFC) é utilizado para verificar e substituir ficheiros do sistema Windows.

> [!IMPORTANT]
> Utilize uma sessão PowerShell elevada para executar os exemplos neste artigo.

Executar o comando SFC:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Depois de concluída a verificação SFC, instale as Atualizações do Windows e reinicie o computador.

## <a name="set-windows-configurations-for-azure"></a>Set Windows configurations for Azure (Definir configurações do Windows para o Azure)

> [!NOTE]
> A plataforma Azure monta um ficheiro ISO para o DVD-ROM quando um VM do Windows é criado a partir de uma imagem generalizada. Por esta razão, o DVD-ROM deve ser ativado no SO na imagem generalizada. Se for desativado, o Windows VM ficará preso na experiência fora de caixa (OOBE).

1. Remover quaisquer rotas estáticas persistentes na tabela de encaminhamento:

   - Para ver a mesa de encaminhamento, corra `route.exe print` .
   - Consulte a secção Rotas de **Persistência.** Se houver uma rota persistente, use o `route.exe delete` comando para removê-la.

1. Remova o proxy WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Se o VM precisar de trabalhar com um representante específico, adicione uma exceção proxy para o endereço IP Azure[(168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) para que o VM possa ligar-se ao Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Abrir a Parte do Disco:

   ```powershell
   diskpart.exe
   ```

   Desa esta medida, a política san do [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) disco:

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Desajei o tempo universal coordenado (UTC) para o Windows. Além disso, desapeda fique o tipo de arranque do serviço de tempo do Windows **w32time** para **Automatic**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Desa parte para um desempenho elevado:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Certifique-se de que as variáveis ambientais **TEMP** e **TMP** estão definidas para os seus valores predefinidos:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Check the Windows services (Verificar os serviços Windows)

Certifique-se de que cada um dos seguintes serviços Windows está definido para o valor padrão do Windows. Estes serviços são o mínimo que deve ser configurado para garantir a conectividade VM. Para definir as definições de arranque, executar o seguinte exemplo:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar as definições de registo remoto de ambiente de trabalho

Certifique-se de que as seguintes definições estão configuradas corretamente para acesso remoto:

> [!NOTE]
> Se receber uma mensagem de erro durante a `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` execução, pode ignorá-la com segurança. Significa que o domínio não está a configurar essa configuração através de um Objeto de Política de Grupo.

1. O Protocolo remoto de Ambiente de Trabalho (RDP) está ativado:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. A porta RDP é configurada corretamente utilizando a porta padrão de 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Quando implementa um VM, as regras padrão são criadas para a porta 3389. Para alterar o número da porta, faça-o depois de o VM ser implantado em Azure.

1. O ouvinte está a ouvir em todas as interfaces de rede:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Configure o modo de autenticação a nível de rede (NLA) para as ligações RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Desacomprem o valor de manter vivo:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Desactore as opções de reconexão:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Limitar o número de ligações simultâneas:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Remover quaisquer certificados auto-assinados ligados ao ouvinte RDP:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Este código garante que pode ligar quando implementar o VM. Também pode rever estas definições após a implementação do VM em Azure.

1. Se o VM fizer parte de um domínio, verifique as seguintes políticas para se certificar de que as definições anteriores não são revertidas.

    |                 Objetivo                  |                                                                            Política                                                                            |                           Valor                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP está ativado                        | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Componentes\Serviços remotos de ambiente de trabalho\Anfitrião de sessão de secretária remoto\Conexões         | Permitir que os utilizadores se conectem remotamente utilizando o Ambiente de Trabalho Remoto    |
    | Política de grupo da NLA                      | Definições\Modelos administrativos\Componentes\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | Requera a autenticação do utilizador para acesso remoto utilizando o NLA |
    | Configurações vivas                   | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Anfitrião de sessão de secretária remoto\Conexões | Configuura intervalo de ligação de manter-se vivo                   |
    | Reconectar definições                    | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Anfitrião de sessão de secretária remoto\Conexões | Reconectar-se automaticamente                                    |
    | Número limitado de definições de ligação | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Anfitrião de sessão de secretária remoto\Conexões | Número de ligações limite                                |

## <a name="configure-windows-firewall-rules"></a>Configurar as regras do Windows Firewall

1. Ligue o Windows Firewall nos três perfis (domínio, padrão e público):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Executar o exemplo seguinte para permitir o WinRM através dos três perfis de firewall (domínio, privado e público) e ativar o serviço remoto PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Habilitar as seguintes regras de firewall para permitir o tráfego RDP:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Ativar a regra para a partilha de ficheiros e impressoras para que o VM possa responder a pedidos de ping dentro da rede virtual:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Criar uma regra para a rede de plataformas Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Se o VM fizer parte de um domínio, verifique as seguintes políticas AD Azure para se certificar de que as definições anteriores não são revertidas.

    |                 Objetivo                 |                                                                         Política                                                                          |                  Valor                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Ativar os perfis de Firewall do Windows | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Rede\Ligação da rede\Windows Firewall\Perfil de domínio\Firewall   | Proteger todas as ligações de rede         |
    | Ativar pDR                           | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Rede\Ligação da rede\Windows Firewall\Perfil de domínio\Firewall   | Permitir exceções de ambiente de trabalho remoto de entrada |
    |                                      | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Rede\Ligação da rede\Windows Firewall\Perfil Padrão\Firewall windows | Permitir exceções de ambiente de trabalho remoto de entrada |
    | Ativar o ICMP-V4                       | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Rede\Ligação da rede\Windows Firewall\Perfil de domínio\Firewall   | Permitir exceções ao ICMP                   |
    |                                      | Configuração do computador\Políticas\Definições do Windows\Modelos administrativos\Rede\Ligação da rede\Windows Firewall\Perfil Padrão\Firewall windows | Permitir exceções ao ICMP                   |

## <a name="verify-the-vm"></a>Verifique o VM

Certifique-se de que o VM está saudável, seguro e pDR acessível:

1. Para se certificar de que o disco é saudável e consistente, verifique o disco no próximo reinício do VM:

   ```powershell
   chkdsk.exe /f
   ```

   Certifique-se de que o relatório mostra um disco limpo e saudável.

1. Defina as definições de Dados de Configuração de Arranque (BCD).

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. O registo de despejo pode ser útil na resolução de problemas com o Windows. Ativar a recolha de registos de despejo:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Verifique se o repositório de gestão do Windows (WMI) é consistente:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Se o repositório for corrompido, consulte [a WMI: Corrupção repositória ou não](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Certifique-se de que nenhuma outra aplicação está a utilizar a porta 3389. Esta porta é utilizada para o serviço RDP em Azure. Para ver quais portas são utilizadas no VM, `netstat.exe -anob` corra:

   ```powershell
   netstat.exe -anob
   ```

1. Para carregar um Windows VHD que é um controlador de domínio:

   - Siga [estes passos extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

   - Certifique-se de que conhece a palavra-passe do Modo de Restauração dos Serviços de Diretório (DSRM) no caso de ter de iniciar o VM em DSRM. Para obter mais informações, consulte [definir uma palavra-passe DSRM](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Certifique-se de que conhece a conta de administrador incorporada e a palavra-passe. É possível que pretenda redefinir a palavra-passe atual do administrador local e certificar-se de que pode utilizar esta conta para iniciar sôm no Windows através da ligação RDP. Esta permissão de acesso é controlada pelo "Permitir iniciar sessão através de Serviços de Secretária remoto" Objeto de política de grupo. Veja este objeto no Editor de Política do Grupo Local:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Consulte as seguintes políticas AZure AD para se certificar de que não estão a bloquear o acesso rdp:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Consulte a seguinte política de Ad Azure para se certificar de que não estão a remover nenhuma das contas de acesso necessárias:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   A política deve enumerar os seguintes grupos:

   - Administradores

   - Operadores de Cópia de Segurança

   - Todas as pessoas

   - Utilizadores

1. Reinicie o VM para se certificar de que o Windows ainda está saudável e pode ser alcançado através da ligação RDP. Neste ponto, considere criar um VM no seu servidor Hiper-V local para se certificar de que o VM começa completamente. Em seguida, teste para se certificar de que consegue chegar ao VM através de RDP.

1. Remova quaisquer filtros extra de Interface do Condutor de Transporte (TDI). Por exemplo, remova o software que analisa pacotes TCP ou firewalls extra.

1. Desinstale qualquer outro software ou controlador de terceiros relacionado com componentes físicos ou qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows

O ideal é manter a máquina atualizada ao *nível* do patch , se tal não for possível, certifique-se de que as seguintes atualizações estão instaladas. Para obter as últimas atualizações, consulte as páginas de histórico da atualização do Windows: [Windows 10 e Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4009470) e [Windows 7 SP1 e Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Componente        |     Binário     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Armazenamento                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB402272                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB402272                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB402272                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB402272                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Principal                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Serviços de Ambiente de Trabalho Remoto | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Para evitar um reinício acidental durante o fornecimento de VM, recomendamos que todas as instalações do Windows Update estejam terminadas e que não estão pendentes atualizações. Uma forma de o fazer é instalar todas as possíveis atualizações do Windows e reiniciar uma vez antes de executar o `sysprep.exe` comando.

## <a name="determine-when-to-use-sysprep"></a>Determinar quando usar o Sysprep

A Ferramenta de Preparação do Sistema `sysprep.exe` () é um processo que pode ser executado para reiniciar uma instalação do Windows.
A Sysprep proporciona uma experiência "fora da caixa", removendo todos os dados pessoais e repondo vários componentes.

Normalmente corre `sysprep.exe` para criar um modelo a partir do qual pode implementar vários outros VMs que têm uma configuração específica. O modelo é chamado de *imagem generalizada.*

Para criar apenas um VM a partir de um disco, não é preciso usar o Sysprep. Em vez disso, pode criar o VM a partir de uma *imagem especializada.* Para obter informações sobre como criar um VM a partir de um disco especializado, consulte:

- [Criar uma VM a partir de um disco especializado](create-vm-specialized.md)
- [Criar um VM a partir de um disco VHD especializado](./create-vm-specialized-portal.md)

Para criar uma imagem generalizada, é preciso executar o Sysprep. Para obter mais informações, consulte [Como utilizar o Sysprep: Uma introdução](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Nem todas as funções ou aplicações instaladas num computador baseado no Windows suportam imagens generalizadas. Antes de utilizar este procedimento, certifique-se de que a Sysprep suporta o papel do computador. Para obter mais informações, consulte [o suporte do Sysprep para as funções do servidor.](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)

Em particular, a Sysprep exige que as unidades sejam totalmente desencriptadas antes da execução. Se tiver ativado a encriptação no seu VM, desative-a antes de executar o Sysprep.


### <a name="generalize-a-vhd"></a>Generalizar um VHD

>[!NOTE]
> Depois de correr `sysprep.exe` nos passos seguintes, desligue o VM. Não volte a ligá-lo até criar uma imagem dele em Azure.

1. Inscreva-se no Windows VM.
1. Executar uma sessão PowerShell como administrador.
1. Elimine o diretório da pantera (C:\Windows\Panther).
1. Mude o diretório para `%windir%\system32\sysprep` . Em seguida, execute o `sysprep.exe`.
1. Na caixa de diálogo de ferramentas de preparação do **sistema,** selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se** de que a caixa de **verificação Generalize** está selecionada.

    ![Ferramenta de preparação do sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. Nas **opções de encerramento**, selecione **Shutdown**.
1. Selecione **OK**.
1. Quando o Sysprep terminar, desligue o VM. Não use **o Restart** para desligar o VM.

Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar um VM a partir de um disco generalizado, consulte [o Upload a Generalized VHD e use-o para criar um novo VM em Azure](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Um ficheiro *unattend.xml* personalizado não é suportado. Embora suportemos a propriedade **adicional deUnattendContent,** que fornece apenas suporte limitado para adicionar opções [de configuração microsoft-windows-shell](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) no ficheiro *unattend.xml* que o agente de provisionamento Azure utiliza. Pode utilizar, por exemplo, [o Globalcontent adicional](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) para adicionar FirstLogonCommands e LogonCommands. Para obter mais informações, consulte [o exemplo adicional daUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Converter o disco virtual num VHD de tamanho fixo

Utilize um dos métodos nesta secção para converter e redimensionar o seu disco virtual para o formato necessário para o Azure:

1. Faça o back up do VM antes de executar o processo de conversão ou redimensionação do disco virtual.

1. Certifique-se de que o VHD do Windows funciona corretamente no servidor local. Resolva quaisquer erros dentro do próprio VM antes de tentar convertê-lo ou carregá-lo para Azure.

1. Converta o disco virtual para escrever fixo.

1. Redimensionar o disco virtual para satisfazer os requisitos do Azure:

   1. Os discos em Azure devem ter um tamanho virtual alinhado a 1 MiB. Se o seu VHD for uma fração de 1 MiB, terá de redimensionar o disco para um múltiplo de 1 MiB. Os discos que são frações de um MiB causam erros ao criar imagens a partir do VHD carregado. Para verificar o tamanho, pode utilizar o cmdlet PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) para mostrar "Size", que deve ser um múltiplo de 1 MiB em Azure, e "FileSize", que será igual a "Size" mais 512 bytes para o rodapé VHD.
   
   1. O tamanho máximo permitido para o OS VHD com uma geração 1 VM é de 2.048 GiB (2 TiB), 
   1. O tamanho máximo para um disco de dados é de 32.767 GiB (32 TiB).

> [!NOTE]
> - Se estiver a preparar um disco Windows OS depois de se converter num disco fixo e redimensionar, se necessário, crie um VM que utilize o disco. Inicie e inscreva-se no VM e continue com as secções deste artigo para terminar a preparação para o upload.  
> - Se estiver a preparar um disco de dados, poderá parar com esta secção e proceder ao upload do seu disco.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Use o Hyper-V Manager para converter o disco

1. Abra o Hyper-V Manager e selecione o computador local à esquerda. No menu acima da lista de computador, selecione **Action**  >  **Edit Disk**.
1. Na página **'Localizar** o disco rígido virtual', selecione o seu disco virtual.
1. Na página **Escolha Ação,** selecione **Converter Em**  >  **Seguida**.
1. Para converter a partir de VHDX, selecione **VHD**  >  **Next**.
1. Para converter a partir de um disco de expansão dinâmica, selecione **Tamanho Fixo**  >  **Seguinte**.
1. Localize e selecione um caminho para guardar o novo ficheiro VHD.
1. Selecione **Concluir**.

### <a name="use-powershell-to-convert-the-disk"></a>Use o PowerShell para converter o disco

Pode converter um disco virtual utilizando o [cmdlet Convert-VHD](/powershell/module/hyper-v/convert-vhd) em PowerShell. Se precisar de informações sobre a instalação deste cmdlet consulte [instalar a função Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

O exemplo a seguir converte o disco de VHDX para VHD. Também converte o disco de um disco de expansão dinâmica para um disco de tamanho fixo.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Neste exemplo, substitua o valor de **Path** pelo caminho para o disco rígido virtual que pretende converter. Substitua o valor **do DestinationPath** pelo novo caminho e nome do disco convertido.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Use o Gestor Hiper-V para redimensionar o disco

1. Abra o Hyper-V Manager e selecione o computador local à esquerda. No menu acima da lista de computador, selecione **Action**  >  **Edit Disk**.
1. Na página **'Localizar** o disco rígido virtual', selecione o seu disco virtual.
1. Na página **Escolha Ação,** selecione **Expandir A**  >  **seguir**.
1. Na página localizar o **disco rígido virtual,** insira o novo tamanho em GiB > **Next**.
1. Selecione **Concluir**.

### <a name="use-powershell-to-resize-the-disk"></a>Use o PowerShell para redimensionar o disco

Pode redimensionar um disco virtual utilizando o cmdlet [Resize-VHD](/powershell/module/hyper-v/resize-vhd) em PowerShell. Se precisar de informações sobre a instalação deste cmdlet consulte [instalar a função Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

O exemplo a seguir redimensiona o disco de 100,5 MiB para 101 MiB para satisfazer o requisito de alinhamento Azure.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

Neste exemplo, substitua o valor de **Path** pelo caminho para o disco rígido virtual que pretende redimensionar. Substitua o valor de **SizeBytes** pelo novo tamanho em bytes para o disco.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter a partir do formato de disco VMDK VMDK

Se tiver uma imagem VM do Windows no [formato de ficheiro VMDK,](https://en.wikipedia.org/wiki/VMDK)então pode utilizar [o Azure Migrate](../../migrate/server-migrate-overview.md) para converter o VMDK e carregá-lo para Azure.

## <a name="complete-the-recommended-configurations"></a>Complete as configurações recomendadas

As seguintes definições não afetam o upload de VHD. No entanto, recomendamos vivamente que os tenha configurado.

- Instale o [Agente de Máquinas Virtuais Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Em seguida, pode ativar extensões VM. As extensões VM implementam a maior parte da funcionalidade crítica que poderá querer utilizar com os seus VMs. Você precisará das extensões, por exemplo, para redefinir senhas ou configurar RDP. Para mais informações, consulte a visão geral do [Agente de Máquinas Virtuais Azure](../extensions/agent-windows.md).
- Depois de criar o VM em Azure, recomendamos que coloque o ficheiro de página no volume de *unidade temporal* para melhorar o desempenho. Pode configurar a colocação do ficheiro da seguinte forma:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Se um disco de dados for anexado ao VM, a letra do volume de unidade temporal é tipicamente *D*. Esta designação pode ser diferente, dependendo das suas definições e do número de unidades disponíveis.

  - Recomendamos desativar bloqueadores de scripts que possam ser fornecidos por software antivírus. Podem interferir e bloquear os scripts do Agente de Provisionamento do Windows executados quando implementar um novo VM a partir da sua imagem.

## <a name="next-steps"></a>Passos seguintes

- [Faça o upload de uma imagem do Windows VM para Azure para implementações do Gestor de Recursos](upload-generalized-managed.md)
- [Problemas de ativação do Azure Windows VM](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)