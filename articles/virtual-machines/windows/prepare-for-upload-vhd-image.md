---
title: Preparar um VHD do Windows para carregar para o Azure | Documentos da Microsoft
description: Como preparar um VHD do Windows ou o VHDX antes de carregar para o Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/13/2018
ms.author: genli
ms.openlocfilehash: 0988902e0a2154f2935a01ddcfb6a460be693df3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093808"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Preparar um VHD do Windows ou o VHDX para carregar para o Azure
Antes de carregar um Windows máquinas virtuais (VM) no local para o Microsoft Azure, tem de preparar o disco rígido virtual (VHD ou VHDX). O Azure suporta **apenas as VMs de geração 1** que estejam no formato de ficheiro VHD e que tem um disco de tamanho fixo. O tamanho máximo permitido para o VHD é 1,023 GB. Pode converter uma geração de VHD e um disco de expansão dinâmica com tamanho fixo do sistema de ficheiros de 1 VM a partir do VHDX. Mas não é possível alterar a geração de uma VM. Para obter mais informações, consulte [devo criar uma geração 1 ou 2 VM no Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Para obter mais informações sobre a política de suporte para VM do Azure, consulte [suporte de software de servidor Microsoft para as VMs do Azure do Microsoft](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> As instruções neste artigo aplicam-se para a versão de 64 bits do Windows Server 2008 R2 e o sistema de operativo de servidor Windows posterior. Para obter informações sobre a versão de 32 bits em execução do sistema operacional no Azure, consulte [suporte para sistemas de operativos de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Converter o disco virtual para o VHD e o disco de tamanho fixo 
Se precisar converter seu disco virtual para o formato necessário para o Azure, utilize um dos métodos nesta secção. Cópia de segurança da VM antes de executar o processo de conversão do disco virtual e certifique-se de que o VHD do Windows funciona corretamente no servidor local. Resolva os eventuais erros dentro da VM em si antes de tentar converter ou carregá-lo para o Azure.

Depois de converter o disco, crie uma VM que utiliza o disco convertido. Iniciar e inicie sessão na VM para concluir a preparação da VM para carregamento.

### <a name="convert-disk-using-hyper-v-manager"></a>Converter disco utilizando o Gestor de Hyper-V
1. Abra o Gestor de Hyper-V e selecione o seu computador local à esquerda. No menu acima da lista de computador, clique em **ação** > **editar disco**.
2. Sobre o **localizar disco rígido Virtual** ecrã, localize e selecione o seu disco virtual.
3. Sobre o **selecionar ação** ecrã e, em seguida, selecione **converter** e **seguinte**.
4. Se precisar converter de VHDX, selecione **VHD** e, em seguida, clique em **próxima**.
5. Se precisar de converter a partir de um disco de expansão dinâmica, selecione **fixos de tamanho** e, em seguida, clique em **próxima**.
6. Localize e selecione um caminho para guardar o novo ficheiro VHD.
7. Clique em **Concluir**.

>[!NOTE]
>Os comandos neste artigo devem ser executados numa sessão elevada do PowerShell.

### <a name="convert-disk-by-using-powershell"></a>Converter o disco com o PowerShell
Pode converter um disco virtual ao utilizar o [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) comando no Windows PowerShell. Selecione **executar como administrador** quando inicia o PowerShell. 

O seguinte comando de exemplo converte de VHDX em VHD e de um disco de expansão dinâmica de tamanho fixo:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Neste comando, substitua o valor para "-caminho" com o caminho para o disco rígido virtual que pretende converter e o valor de "-DestinationPath" com o novo caminho e nome do disco convertido.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converter de formato de disco VMDK do VMware
Se tiver uma imagem de VM do Windows na [formato de ficheiro VMDK](https://en.wikipedia.org/wiki/VMDK), convertê-lo a um VHD com o [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). Para obter mais informações, consulte o artigo de blogue [como converter um VMDK do VMware em Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Definir configurações do Windows para o Azure

Na VM que pretende carregar para o Azure, executar todos os comandos nas etapas a seguir a partir de um [janela de linha de comandos elevada](https://technet.microsoft.com/library/cc947813.aspx):

1. Remova qualquer rota estática persistente na tabela de encaminhamento:
   
   * Para ver a tabela de rotas, execute `route print` no prompt de comando.
   * Verifique os **rotas de persistência** secções. Se existir uma rota persistente, utilize o **route delete** comando para removê-lo.
2. Remova o proxy de WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Se a VM precisa trabalhar com qualquer proxy específico, tem de adicionar uma exceção de proxy para o endereço IP do Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), por isso, a VM tem conectividade para o Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Defina a política de SAN de disco para [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    Na janela de linha de comandos aberta, escreva os seguintes comandos:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Definir o tempo de hora Universal Coordenada (UTC) para Windows e o tipo de arranque do serviço de hora do Windows (w32time) para **automaticamente**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Defina o perfil de energia o **elevado desempenho**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Certifique-se de que as variáveis de ambiente **TEMP** e **TMP** estão definidas para os valores predefinidos:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Verificar os serviços do Windows
Certifique-se de que cada um dos seguintes serviços do Windows está definida para o **valores predefinidos do Windows**. Estes são os números mínimo de serviços que devem ser configurados para se certificar de que a VM tem conectividade. Para repor as definições de arranque, execute os seguintes comandos:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Atualizar definições de registo de ambiente de trabalho remoto
Certifique-se de que as seguintes definições estão configuradas corretamente para conexão de área de trabalho remoto:

>[!Note] 
>Poderá receber uma mensagem de erro ao executar o **ItemProperty de conjunto-caminho ' HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal serviços - name &lt;nome do objeto&gt; -valor &lt;valor&gt;**  nestes passos. A mensagem de erro pode ser ignorada com segurança. Apenas significa que o domínio não está a enviar essa configuração por meio de um objeto de diretiva de grupo.
>
>

1. Protocolo RDP (Remote Desktop) está ativado:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. A porta RDP está corretamente definida (predefinição a porta 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Quando implementa uma VM, as regras predefinidas são criadas em relação a porta 3389. Se quiser alterar o número de porta, fazê-lo Depois da VM é implementada no Azure.

3. O serviço de escuta está à escuta em cada interface de rede:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Configure o modo de autenticação de nível de rede para as ligações de RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Defina o valor de ligação keep-alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Voltar a ligar:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Limite o número de ligações simultâneas:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Se existirem quaisquer certificados autoassinados vinculados ao serviço de escuta RDP, removê-los:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Isso é certificar-se de que pode ligar-se no início ao implementar a VM. Poderá também ver isso num estágio posterior depois da VM é implementada no Azure, se necessário.

9. Se a VM for parte de um domínio, verifique todas as definições seguintes para se certificar de que as definições anteriores não são revertidas. As políticas que devem ser verificadas são os seguintes:
    
    | Objetivo                                     | Política                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP está ativado                           | Host\Connections de sessão de ambiente de trabalho do computador configurações Settings\Administrative Templates\Components\Remote Services\Remote área de trabalho         | Permitir que os utilizadores liguem remotamente utilizando o ambiente de trabalho remoto                                  |
    | Política de grupo do NLA                         | Host\Security de sessão de ambiente de trabalho do Settings\Administrative Templates\Components\Remote Services\Remote área de trabalho                                                    | Exigir autenticação de utilizador para ligações remotas utilizando a autenticação de nível de rede |
    | Manter as definições de Alive                      | Computador configurações Settings\Administrative administrativos\Componentes Components\Remote Desktop Services\Remote sessão de ambiente de trabalho Host\Connections | Configurar o intervalo de ligação keep-alive                                                 |
    | Definições de restabelecimento de ligação                       | Computador configurações Settings\Administrative administrativos\Componentes Components\Remote Desktop Services\Remote sessão de ambiente de trabalho Host\Connections | Restabelecimento de ligação automática                                                                   |
    | Limitar o número de configurações de conexão | Computador configurações Settings\Administrative administrativos\Componentes Components\Remote Desktop Services\Remote sessão de ambiente de trabalho Host\Connections | Limitar o número de ligações                                                              |

## <a name="configure-windows-firewall-rules"></a>Configurar regras de Firewall do Windows
1. Ative a Firewall do Windows em três perfis (domínio, Standard e público):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Execute o seguinte comando no PowerShell para permitir o WinRM por três perfis de firewall (domínio, privadas e públicas) e ativar o serviço de remota do PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Ative as seguintes regras de firewall permitir o tráfego RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Ative a regra de impressora partilha de ficheiros e para que a VM pode responder a um comando ping dentro da rede Virtual:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Se a VM for parte de um domínio, verifique as definições seguintes para se certificar de que as definições anteriores não são revertidas. As políticas do AD que devem ser verificadas são os seguintes:

    | Objetivo                                 | Política                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Ativar os perfis de Firewall do Windows | Computador configurações Settings\Administrative administrativos Connection\Windows Firewall\Domain Profile\Windows Firewall   | Proteger todas as ligações de rede         |
    | Ative o RDP                           | Computador configurações Settings\Administrative administrativos Connection\Windows Firewall\Domain Profile\Windows Firewall   | Permitir entrada de ambiente de trabalho remoto de exceções |
    |                                      | Computador configurações Settings\Administrative administrativos Connection\Windows Firewall\Standard Profile\Windows Firewall | Permitir entrada de ambiente de trabalho remoto de exceções |
    | Ativar o ICMP V4                       | Computador configurações Settings\Administrative administrativos Connection\Windows Firewall\Domain Profile\Windows Firewall   | Permitir exceções de ICMP                   |
    |                                      | Computador configurações Settings\Administrative administrativos Connection\Windows Firewall\Standard Profile\Windows Firewall | Permitir exceções de ICMP                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Certifique-se de que a VM está em bom estado, segura e acessível com RDP 
1. Para certificar-se de que o disco está em bom estado e consistente, execute uma operação de verificação de disco na próxima reinicialização da VM:

    ```PowerShell
    Chkdsk /f
    ```
    Certifique-se de que o relatório mostra um disco limpo e bom estado de funcionamento.

2. Configure as definições de dados de configuração de arranque (BCD). 

    > [!Note]
    > Certifique-se de que executar estes comandos numa janela elevada do PowerShell.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. O registo de informação pode ser útil para solucionar problemas de falha do Windows. Ative a recolha de informação de registo:

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Certifique-se de que o repositório de Instrumentations de gestão do Windows é consistente. Para efetuar isto, execute o seguinte comando:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Se o repositório está corrompido, consulte o artigo [WMI: Existência de danos no repositório, ou não](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Certifique-se de que qualquer outro aplicativo não está a utilizar a porta 3389. Esta porta é utilizada para o serviço RDP no Azure. Pode executar **netstat - anob** para ver as portas em que são utilizadas na VM:

    ```PowerShell
    netstat -anob
    ```

6. Se o VHD do Windows que pretende carregar um controlador de domínio, em seguida, siga estes passos:

    1. Siga [estes passos Extras](https://support.microsoft.com/kb/2904015) para preparar o disco.

    1. Certifique-se de que sabe a palavra-passe do DSRM no caso de ter que iniciar a VM no DSRM em algum momento. Talvez queira consulte esta ligação para definir o [palavra-passe DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Certifique-se de que a conta de administrador interno e a palavra-passe são conhecidas para. Poderá repor a palavra-passe de administrador local atual e certifique-se de que pode utilizar esta conta para iniciar sessão no Windows através da ligação de RDP. Esta permissão de acesso é controlado pelo objeto de diretiva de grupo "Permitir início de sessão através dos serviços de ambiente de trabalho remoto". Pode ver este objeto no Editor de políticas de grupo de Local em:

    Configuração de computador Windows Settings\Local Policies\User Rights Assignment

8. As políticas de verificação do AD seguinte para se certificar de que não estão a bloquear o acesso RDP através de RDP ou não pertence a rede:

    - Acesso de configuração Windows \ direitos de utilizador\negar do computador a este computador da rede

    - Computador configuração Windows \ direitos de utilizador\negar início de sessão através dos serviços de ambiente de trabalho remoto


9. Verifique a política de AD seguinte para se certificar de que não a remover qualquer um dos seguintes as contas de acesso necessário:

   - Computador configuração Windows Settings\Local Policies\User Rights Assignment\Access este computação da rede

     Os seguintes grupos devem estar listados nesta política:

   - Administradores
   - Operadores de cópia de segurança
   - Todos
   - Utilizadores

10. Reinício da VM para se certificar de que o Windows está ainda em bom estado pode ser contatado, utilizando a ligação de RDP. Neste momento, pode querer criar uma VM no seu local Hyper-V para certificar-se de que a VM está a iniciar completamente e, em seguida, testar, mesmo que esteja acessível de RDP.

11. Remover quaisquer filtros de Interface de Driver de transporte Extras, como o software que analisa TCP pacotes ou firewalls extras. Poderá também ver isso num estágio posterior depois da VM é implementada no Azure, se necessário.

12. Desinstale qualquer software de terceiros e controladores que está relacionado com componentes físicos ou qualquer outra tecnologia de virtualização.

### <a name="install-windows-updates"></a>Instalar atualizações do Windows
A configuração ideal é **ter o nível de patch da máquina em que a versão mais recente**. Se não for possível, certifique-se de que as seguintes atualizações são instaladas:

| Componente               | Binário         | Windows 7 SP1,Windows Server 2008 R2  SP1 | Windows 8,Windows Server 2012               | Windows 8.1,Windows Server 2012 R2 | Windows 10 versão 1607 Windows Server 2016 versão 1607 | Windows 10, Versão 1703    | Windows 10 1709 Windows Server 2016 versão 1709 | Windows 10 1803 Windows Server 2016 versão 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Armazenamento                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Rede                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Principal                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Serviços de Ambiente de Trabalho Remoto | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Segurança                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Quando utilizar o sysprep <a id="step23"></a>    

Sysprep é um processo que pode se deparar com uma instalação do windows que irá repor a instalação do sistema e fornecerá um "o box experience" ao remover todos os dados pessoais e repor vários componentes. Normalmente fá-lo se pretender criar um modelo a partir da qual pode implementar várias VMs que têm uma configuração específica. Isso é chamado de um **generalizada imagem**.

Se, em vez disso, quiser apenas criar uma VM a partir de um disco, não tem de utilizar o sysprep. Nesta situação, apenas pode criar a VM do que é conhecido como um **imagem especializada**.

Para obter mais informações sobre como criar uma VM a partir de um disco especializado, consulte:

- [Criar uma VM a partir de um disco especializado](create-vm-specialized.md)
- [Criar uma VM a partir de um disco VHD especializado](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Se quiser criar uma imagem generalizada, terá de executar o sysprep. Para obter mais informações sobre o Sysprep, consulte [como utilizar Sysprep: Uma introdução](https://technet.microsoft.com/library/bb457073.aspx). 

Nem todas as funções ou a aplicação que é instalada num computador baseado em Windows oferece suporte a essa generalização. Portanto, antes de executar este procedimento, consulte o artigo seguinte para se certificar de que a função do computador é suportada pelo sysprep. Para obter mais informações, [suporte de Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Passos para generalizar um VHD

>[!NOTE]
> Depois de executar sysprep.exe conforme especificado nas etapas a seguir, desativar a VM e não voltar a ativá-lo até que cria uma imagem no Azure.

1. Entrar para o VM do Windows.
2. Execute **linha de comandos** como administrador. 
3. Altere o diretório para: **%windir%\system32\sysprep**e, em seguida, execute **sysprep.exe**.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Experiência 1ª Execução (OOBE) do Sistema** e certifique-se de que a caixa de verificação **Generalizar** está selecionada.

    ![Ferramenta de preparação do sistema](media/prepare-for-upload-vhd-image/syspre.png)
4. Na **opções de encerramento**, selecione **encerramento**.
5. Clique em **OK**.
6. Quando Sysprep estiver concluída, encerre a VM. Não utilize **reiniciar** para encerrar a VM.
7. Agora o VHD está pronto para ser carregado. Para obter mais informações sobre como criar uma VM a partir de um disco generalizado, consulte [carregar um VHD generalizado e utilizá-lo para criar um novo VMs no Azure](sa-upload-generalized.md).


>[!NOTE]
> Não é suportado um Unattend. XML personalizado. Enquanto é proporcionado suporte para a propriedade additionalUnattendContent, que só oferece suporte limitado para adição [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) opções em Unattend. XML que utiliza o agente de aprovisionamento do Azure. Por exemplo,  eles podem usar [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) adicionar FirstLogonCommands e LogonCommands. Consulte também [additionalUnattendContent FirstLogonCommands exemplo](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-recommended-configurations"></a>Conclua as configurações de recomendada
As seguintes definições não afetam a carregar o VHD. No entanto, recomendamos vivamente que configurou-los.

* Instalar o [agente de VMs do Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Em seguida, pode ativar as extensões de VM. As extensões VM implementam a maioria das funcionalidades críticas que poderá utilizar com as suas VMs como a reposição de palavras-passe, a configuração de RDP e assim por diante. Para obter mais informações, consulte [descrição geral do agente de Máquina Virtual do Azure](../extensions/agent-windows.md).
* Depois da VM é criada no Azure, recomendamos que colocar o ficheiro de paginação no volume "Unidade Temporal" para melhorar o desempenho. Pode configurar isso da seguinte forma:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Se houver qualquer disco de dados que esteja ligado à VM, letra da unidade do volume de unidade Temporal é, normalmente, "D." Essa designação poderia ser diferente, dependendo do número de unidades disponíveis e as definições que fizer.

## <a name="next-steps"></a>Passos Seguintes
* [Carregar uma imagem de VM do Windows para o Azure para implementações do Resource Manager](upload-generalized-managed.md)
* [Resolução de problemas de ativação de máquina virtual do Windows Azure](troubleshoot-activation-problems.md)

