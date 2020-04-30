---
title: Prepare um Windows VHD para fazer upload para o Azure
description: Saiba como preparar um Windows VHD ou VHDX para o enviar para o Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.author: genli
ms.openlocfilehash: c83850ea479e115121da8eb049db4a01befe7f89
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201079"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Prepare a Windows VHD or VHDX to upload to Azure  (Preparar um VHD ou um VHDX do Windows para carregamento para o Azure)

Antes de fazer o upload de uma máquina virtual do Windows (VM) de instalações para O Azure, tem de preparar o disco rígido virtual (VHD ou VHDX). O Azure suporta tanto os VMs de geração 1 como de geração 2 que estão em formato de ficheiro VHD e que têm um disco de tamanho fixo. O tamanho máximo permitido para o VHD é de 2 TB.

Numa geração 1 VM, pode converter um sistema de ficheiros VHDX em VHD. Também pode converter um disco em expansão dinâmica para um disco de tamanho fixo. Mas não se pode mudar a geração de um VM. Para mais informações, consulte [Devo criar uma geração 1 ou 2 VM em Hyper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) [Support for generation 2 VMs on Azure](generation-2.md)

Para obter informações sobre a política de suporte para VMs Azure, consulte o suporte do software do [servidor microsoft para VMs Azure](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> As instruções deste artigo aplicam-se a:
>
> - A versão de 64 bits do Windows Server 2008 R2 e posteriorsistemaoperativo do Windows Server. Para obter informações sobre a execução de um sistema operativo de 32 bits em Azure, consulte [suporte para sistemas operativos de 32 bits em VMs Azure](https://support.microsoft.com/help/4021388/).
> - Se alguma ferramenta de recuperação de desastres for utilizada para migrar a carga de trabalho, como a Azure Site Recovery ou a Azure Migrate, este processo ainda é necessário no Os convidado para preparar a imagem antes da migração.

## <a name="system-file-checker"></a> Verificador de Ficheiros do Sistema

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Executar utilitário de verificador de ficheiros do sistema Windows antes da generalização da imagem do OS

O Verificador de Ficheiros do Sistema (SFC) é utilizado para verificar e substituir ficheiros do sistema Windows.

> [!IMPORTANT]
> Utilize uma sessão powerShell elevada para executar os exemplos neste artigo.

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

Depois de a varredura do SFC estar concluída, instale as Atualizações do Windows e reinicie o computador.

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Converter o disco virtual num VHD de tamanho fixo

Utilize um dos métodos desta secção para converter o seu disco virtual no formato necessário para o Azure:

1. Faça o back up do VM antes de executar o processo de conversão de disco virtual.

1. Certifique-se de que o VHD do Windows funciona corretamente no servidor local. Resolva quaisquer erros dentro do próprio VM antes de tentar convertê-lo ou carregá-lo para o Azure.

1. Tamanho VHD:

   1. Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco cru para um VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1 MB antes da conversão.
      Frações de um megabyte causam erros ao criar imagens a partir do VHD carregado.

   1. O tamanho máximo permitido para o VHD osso é de 2 TB.

Depois de converter o disco, crie um VM que utilize o disco. Inicie e inicie o vM para terminar de prepará-lo para o upload.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Use o Gestor De Hiper-V para converter o disco

1. Abra o Hyper-V Manager e selecione o seu computador local à esquerda. No menu acima da lista de computadores, selecione **Action** > **Edit Disk**.
1. Na página Localizar discos **rígidos virtuais,** selecione o disco virtual.
1. Na página **Escolha Ação,** selecione **Converter** > **Seguinte**.
1. Para converter a partir de VHDX, selecione **VHD** > **Next**.
1. Para converter a partir de um disco em expansão dinâmica, selecione **Tamanho** > Fixo**Seguinte**.
1. Localize e selecione um caminho para salvar o novo ficheiro VHD.
1. Selecione **Concluir**.

### <a name="use-powershell-to-convert-the-disk"></a>Use powerShell para converter o disco

Pode converter um disco virtual utilizando o cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) no PowerShell.

O exemplo seguinte converte o disco de VHDX para VHD. Também converte o disco de um disco em expansão dinâmica para um disco de tamanho fixo.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Neste exemplo, substitua o valor por **Path** pelo caminho para o disco rígido virtual que pretende converter. Substitua o valor para **DestinationPath** pelo novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter a partir do formato de disco VMware VMDK

Se tiver uma imagem VM do Windows no formato de [ficheiro VMDK,](https://en.wikipedia.org/wiki/VMDK)utilize o [Conversor](https://www.microsoft.com/download/details.aspx?id=42497) de Máquina Virtual da Microsoft para convertê-la em formato VHD. Para mais informações, consulte [Como converter um VMware VMDK em Hyper-VHD](/archive/blogs/timomta/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd).

## <a name="set-windows-configurations-for-azure"></a>Set Windows configurations for Azure (Definir configurações do Windows para o Azure)

> [!NOTE]
> A plataforma Azure monta um ficheiro ISO para o DVD-ROM quando um VM windows é criado a partir de uma imagem generalizada. Por esta razão, o DVD-ROM deve ser ativado no SISTEMA na imagem generalizada. Se estiver desativado, o Windows VM ficará preso a uma experiência fora da caixa (OOBE).

1. Remova quaisquer rotas estáticas persistentes no quadro de encaminhamento:

   - Para ver a mesa `route.exe print`de encaminhamento, corra.
   - Verifique a secção Rotas de **Persistência.** Se houver uma rota persistente, `route.exe delete` use o comando para removê-lo.

1. Remova o proxy WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Se o VM precisar de trabalhar com um representante específico, adicione uma exceção de procuração para o endereço IP Azure[(168.63.129.16](/azure/virtual-network/what-is-ip-address-168-63-129-16)) para que o VM possa ligar-se ao Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Open DiskPart:

   ```powershell
   diskpart.exe
   ```

   Desloque a [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11))política san do disco para:

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Detete o tempo de tempo universal coordenado (UTC) para windows. Além disso, detete o tipo de arranque do serviço de tempo Windows **w32time** para **Automatic:**

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Detete o perfil de potência para um alto desempenho:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Certifique-se de que as variáveis ambientais **TEMP** e **TMP** estão definidas para os seus valores predefinidos:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Check the Windows services (Verificar os serviços Windows)

Certifique-se de que cada um dos seguintes serviços Windows está definido para o valor predefinido do Windows. Estes serviços são o mínimo que deve ser configurado para garantir a conectividade VM. Para definir as definições de arranque, execute o seguinte exemplo:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar definições de registo remoto de desktop

Certifique-se de que as seguintes definições estão corretamente configuradas para acesso remoto:

> [!NOTE]
> Se receber uma mensagem `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>`de erro durante a execução, pode ignorá-la com segurança. Significa que o domínio não está a definir essa configuração através de um Objeto de Política de Grupo.

1. O Protocolo de Ambiente de Trabalho Remoto (RDP) está ativado:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. A porta RDP é configurada corretamente utilizando a porta padrão de 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Quando se implementa um VM, as regras predefinidas são criadas para a porta 3389. Para alterar o número da porta, faça-o depois de o VM ser implantado em Azure.

1. O ouvinte está a ouvir em todas as interfaces da rede:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Configure o modo de autenticação ao nível da rede (NLA) para as ligações RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Detete o valor de manutenção viva:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Definir as opções de religação:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Limitar o número de ligações simultâneas:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Remova quaisquer certificados auto-assinados ligados ao ouvinte rdp:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Este código garante que pode ligar-se quando implementa o VM. Também pode rever estas definições depois de o VM ser implantado no Azure.

1. Se o VM fizer parte de um domínio, verifique as seguintes políticas para se certificar de que as definições anteriores não são revertidas.

    |                 Objetivo                  |                                                                            Política                                                                            |                           Valor                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RdP está ativado                        | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Componentes\Serviços remotos de ambiente de trabalho\Remote Desktop Session Host\Connections         | Permitir que os utilizadores se conectem remotamente utilizando o Ambiente de Trabalho Remoto    |
    | Política do grupo NLA                      | Definições\Modelos Administrativos\Componentes\Serviços de ambiente de trabalho remoto\Remote Desktop Session Host\Security                                                    | Exigir a autenticação do utilizador para acesso remoto utilizando o NLA |
    | Definições de manter-se vivas                   | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Remote Desktop Session Host\Connections | Configure intervalo de ligação keep-alive                   |
    | Reconectar as definições                    | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Remote Desktop Session Host\Connections | Reconectar automaticamente                                    |
    | Número limitado de definições de ligação | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Componentes do Windows\Serviços remotos de ambiente de trabalho\Remote Desktop Session Host\Connections | Número limite de ligações                                |

## <a name="configure-windows-firewall-rules"></a>Configure as regras do Windows Firewall

1. Ligue o Windows Firewall nos três perfis (domínio, standard e público):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Execute o seguinte exemplo para permitir o WinRM através dos três perfis de firewall (domínio, privado e público), e ativar o serviço remoto PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Ativar as seguintes regras de firewall para permitir o tráfego rdp:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Ativar a regra de partilha de ficheiros e impressoras para que o VM possa responder aos pedidos de ping dentro da rede virtual:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Criar uma regra para a rede da plataforma Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Se o VM fizer parte de um domínio, verifique as seguintes políticas de AD Azure para se certificar de que as definições anteriores não são revertidas.

    |                 Objetivo                 |                                                                         Política                                                                          |                  Valor                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Ativar os perfis de Firewall do Windows | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Ligação de rede\Windows Firewall\Perfil\Windows Firewall   | Proteja todas as ligações de rede         |
    | Ativar RDP                           | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Ligação de rede\Windows Firewall\Perfil\Windows Firewall   | Permitir exceções à entrada remota do ambiente de trabalho |
    |                                      | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Ligação de rede\Windows Firewall\Perfil padrão\Firewall | Permitir exceções à entrada remota do ambiente de trabalho |
    | Ativar o ICMP-V4                       | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Ligação de rede\Windows Firewall\Perfil\Windows Firewall   | Permitir exceções ao ICMP                   |
    |                                      | Configuração do computador\Policies\Windows Settings\Modelos Administrativos\Ligação de rede\Windows Firewall\Perfil padrão\Firewall | Permitir exceções ao ICMP                   |

## <a name="verify-the-vm"></a>Verifique o VM

Certifique-se de que o VM é saudável, seguro e rdp acessível:

1. Para se certificar de que o disco é saudável e consistente, verifique o disco no próximo reinício do VM:

   ```powershell
   chkdsk.exe /f
   ```

   Certifique-se de que o relatório mostra um disco limpo e saudável.

1. Detete as definições de Dados de Configuração de Arranque (BCD).

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

1. O registo de despejo pode ser útil para resolver problemas com problemas com o Windows. Ativar a recolha de registos de despejo:

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

1. Verifique se o repositório de instrumentação de gestão do Windows (OMI) é consistente:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Se o repositório for corrompido, consulte [o WMI: Corrupção repositória ou não](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Certifique-se de que nenhuma outra aplicação está a utilizar a porta 3389. Esta porta é utilizada para o serviço RDP em Azure. Para ver quais as portas utilizadas `netstat.exe -anob`no VM, corra:

   ```powershell
   netstat.exe -anob
   ```

1. Para fazer o upload de um VHD do Windows que é um controlador de domínio:

   - Siga [estes passos extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

   - Certifique-se de que conhece a senha de restauro do Modo de Restauração de Serviços de Diretório (DSRM) caso tenha de iniciar o VM em DSRM. Para mais informações, consulte [Definir uma senha DSRM](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Certifique-se de conhecer a conta de administrador incorporado e a palavra-passe. É possível que pretenda redefinir a senha atual do administrador local e certifique-se de que pode utilizar esta conta para iniciar sessão no Windows através da ligação RDP. Esta permissão de acesso é controlada pelo Objeto de Política de Grupo "Permitir iniciar sessão através de Serviços de Ambiente de Trabalho Remoto". Veja este objeto no Editor de Política do Grupo Local:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Consulte as seguintes políticas da AD Azure para se certificar de que não estão a bloquear o acesso ao PDR:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Consulte a seguinte política da AD Azure para se certificar de que não estão a remover nenhuma das contas de acesso necessárias:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   A política deve enumerar os seguintes grupos:

   - Administradores

   - Operadores de Cópia de Segurança

   - Todos os utilizadores

   - Utilizadores

1. Reinicie o VM para se certificar de que o Windows ainda está saudável e pode ser alcançado através da ligação RDP. Neste ponto, considere criar um VM no seu servidor Hiper-V local para se certificar de que o VM começa completamente. Em seguida, teste para se certificar de que pode chegar ao VM através de RDP.

1. Remova quaisquer filtros extra de interface do controlador de transporte (TDI). Por exemplo, remova o software que analisa pacotes de TCP ou firewalls extras. Para rever isto mais tarde, pode fazê-lo depois de o VM ser implantado em Azure.

1. Desinstale qualquer outro software ou controlador de terceiros relacionado com componentes físicos ou qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows

Idealmente, deve manter a máquina atualizada ao *nível*do patch . Se tal não for possível, certifique-se de que as seguintes atualizações estão instaladas. Para obter as mais recentes atualizações, consulte as páginas de histórico da atualização do Windows: [Windows 10 e Windows Server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 e Windows Server 2012 R2](https://support.microsoft.com/help/4009470) e [Windows 7 SP1 e Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Componente        |     Binário     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Armazenamento                 | discos.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
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
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB40227222                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Principal                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Serviços de Ambiente de Trabalho Remoto | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termodd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
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
> Para evitar um reboot acidental durante o fornecimento de VM, recomendamos que todas as instalações do Windows Update estejam concluídas e que não estejam pendentes atualizações. Uma das formas de o fazer é instalar todas as `sysprep.exe` possíveis atualizações do Windows e reiniciar uma vez antes de executar o comando.

### <a name="determine-when-to-use-sysprep"></a>Determine quando usar sysprep

A Ferramenta`sysprep.exe`de Preparação do Sistema é um processo que pode ser executado para redefinir uma instalação do Windows.
A Sysprep proporciona uma experiência "fora da caixa", removendo todos os dados pessoais e repondo vários componentes.

Você normalmente `sysprep.exe` corre para criar um modelo a partir do qual você pode implementar vários outros VMs que têm uma configuração específica. O modelo é chamado de *imagem generalizada.*

Para criar apenas um VM a partir de um disco, não é preciso usar sysprep. Em vez disso, pode criar o VM a partir de uma *imagem especializada.* Para obter informações sobre como criar um VM a partir de um disco especializado, consulte:

- [Criar uma VM a partir de um disco especializado](create-vm-specialized.md)
- [Criar um VM a partir de um disco VHD especializado](/azure/virtual-machines/windows/create-vm-specialized-portal)

Para criar uma imagem generalizada, precisa executar sysprep. Para mais informações, consulte [Como utilizar o Sysprep: Uma introdução](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Nem todas as funções ou aplicações instaladas num computador baseado no Windows suportam imagens generalizadas. Antes de utilizar este procedimento, certifique-se de que a Sysprep suporta o papel do computador. Para mais informações, consulte o [suporte sysprep para funções](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)de servidor .

### <a name="generalize-a-vhd"></a>Generalizar um VHD

>[!NOTE]
> Depois de `sysprep.exe` correr nos seguintes passos, desligue o VM. Não volte a ligá-lo até criar uma imagem dele em Azure.

1. Inscreva-se no VM do Windows.
1. Execute uma sessão powerShell como administrador.
1. Mude o diretório para `%windir%\system32\sysprep`. Em seguida, execute o `sysprep.exe`.
1. Na caixa de diálogo da ferramenta de preparação do **sistema,** selecione **Enter System Out-of-Box Experience (OOBE) e certifique-se**de que a caixa de verificação **Generalize** está selecionada.

    ![Ferramenta de preparação do sistema](media/prepare-for-upload-vhd-image/syspre.png)
1. Em **Opções de Encerramento,** selecione **Shutdown**.
1. Selecione **OK**.
1. Quando o Sysprep terminar, desligue o VM. Não utilize **o Restart** para desligar o VM.

Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar um VM a partir de um disco generalizado, consulte [o Upload de um VHD generalizado e use-o para criar um novo VM em Azure](sa-upload-generalized.md).

>[!NOTE]
> Um ficheiro personalizado *unattend.xml* não é suportado. Embora suportemos a propriedade **adicional UnattendContent,** que fornece apenas suporte limitado para adicionar opções [de configuração microsoft-windows-shell-no](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) ficheiro *unattend.xml* que o agente de provisionamento Azure utiliza. Pode utilizar, por exemplo, [adicionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) para adicionar FirstLogonCommands e LogonCommands. Para mais informações, consulte [o exemplo de UnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="complete-the-recommended-configurations"></a>Complete as configurações recomendadas

As seguintes definições não afetam o upload do VHD. No entanto, recomendamos vivamente que os configure.

- Instale o [Agente de Máquinavirtual Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Em seguida, pode ativar extensões VM. As extensões VM implementam a maior parte da funcionalidade crítica que poderá querer utilizar com os seus VMs. Você precisará das extensões, por exemplo, para redefinir senhas ou configurar RDP. Para mais informações, consulte a visão geral do [Agente De MáquinaVirtual Azure](../extensions/agent-windows.md).
- Depois de criar o VM em Azure, recomendamos que coloque o ficheiro de página no volume de *unidade temporal* para melhorar o desempenho. Pode configurar a colocação do ficheiro da seguinte forma:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Se um disco de dados estiver ligado ao VM, a letra do volume de acionamento temporal é tipicamente *D*. Esta designação pode ser diferente, dependendo das suas definições e do número de unidades disponíveis.

  - Recomendamos que os bloqueadores de scripts sejam fornecidos pelo software antivírus. Podem interferir e bloquear os scripts do Windows Provisioning Agent executados quando implementar um novo VM a partir da sua imagem.

## <a name="next-steps"></a>Passos seguintes

- [Faça upload de uma imagem VM do Windows para Azure para implementações de Gestor de Recursos](upload-generalized-managed.md)
- [Problemas de sessão de problemas de ativação do Windows VM do Windows](troubleshoot-activation-problems.md)
