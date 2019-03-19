---
title: 'Tutorial:  Integrar uma única floresta do AD para o Azure com sincronização de hash de palavra-passe (PHS) | Documentos da Microsoft'
description: Demonstra como configurar um ambiente de identidade híbrida com sincronização de hash de palavra-passe.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45379f8f955c50e2598ebcebd34e971c29b2c81c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103235"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-password-hash-sync-phs"></a>Tutorial:  Integrar uma única floresta do AD com a sincronização de hash de palavra-passe (PHS)

![Criar](media/tutorial-password-hash-sync/diagram.png)

O tutorial seguinte irá guiá-lo através da criação de um ambiente de identidade híbrida com sincronização de hash de palavra-passe.  Neste ambiente, em seguida, pode ser utilizado para fins de teste ou para obter mais familiarizado com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para a conclusão deste tutorial
- Um computador com [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  Recomenda-se para fazê-lo em qualquer uma um [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou uma [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) computador.
- Uma [adaptador de rede externo](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual para comunicar com a internet.
- Uma [subscrição do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016

> [!NOTE]
> Este tutorial utiliza scripts do PowerShell para que possa criar o ambiente de tutorial no período de tempo mais rápido.  Cada um dos scripts usa variáveis declaradas no início dos scripts.  Pode e deve alterar as variáveis para refletir o seu ambiente.
>
>Os scripts utilizados criam um ambiente do Active Directory geral antes de instalar o Azure AD Connect.  Eles são relevantes para todos os tutoriais.
>
> Cópias dos scripts do PowerShell que são utilizados neste tutorial estão disponíveis no GitHub [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que precisamos para o fazer, para que o nosso ambiente de identidade híbrida a cópia de segurança e em execução é criar uma máquina virtual que será utilizada como nosso servidor do Active Directory no local.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Concluir a implementação do sistema operativo
Para concluir a criação da máquina virtual, é necessário concluir a instalação do sistema operativo.

1. Gestor de Hyper-V, faça duplo clique na máquina virtual
2. Clique no botão Iniciar.
3. Será solicitado a 'Pressione qualquer tecla para inicializar a partir de CD ou DVD'. Vá em frente e fazê-lo.
4. No ecrã de arranque do Windows Server selecione seu idioma e clique em **seguinte**.
5. Clique em **instalar agora**.
6. Introduza a chave de licença e clique em **seguinte**.
7. Verificar * * posso aceitar os termos de licenciamento e clique em **seguinte**.
8. Selecione **personalizado:  Instalar apenas o Windows (avançado)**
9. Clique em **Seguinte**
10. Depois de concluída a instalação, reinicie a máquina virtual, do início de sessão e executar atualizações do Windows para garantir que a VM está mais atualizadas.  Instale as atualizações mais recentes.

## <a name="install-active-directory-prerequisites"></a>Instalar a pré-requisitos do Active Directory
Agora que temos uma máquina virtual de cópia de segurança, é necessário fazer algumas coisas antes de instalar o Active Directory.  Ou seja, é necessário mudar o nome da máquina virtual, defina um endereço IP estático e informações de DNS e instalar as ferramentas de administração remota do servidor.   Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente do Windows Server AD
Agora que temos a VM criada e ele o nome foi mudado e tem um endereço IP estático, podemos ir em frente e instalar e configurar serviços de domínio do Active Directory.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Criar um utilizador do Windows Server AD
Agora que temos nosso ambiente do Active Directory, é necessário uma conta de teste.  Esta conta será criada no nosso locais ambiente AD e, em seguida, sincronizadas com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Criar um inquilino do Azure AD
Agora, precisamos criar um inquilino do Azure AD para que o pode sincronizar os nossos utilizadores para a cloud.  Para criar um novo do Azure AD de inquilino, faça o seguinte.

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. Selecione o **(+) do ícone de adição** e procure **Azure Active Directory**.
3. Selecione **do Azure Active Directory** nos resultados da pesquisa.
4. Selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/create1.png)</br>
5. Fornecer um **nome de organização** juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Esta ação irá criar o seu diretório.
6. Assim que terminar, clique nas **aqui** ligação, para gerir o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que temos um inquilino do Azure AD, iremos criar uma conta de administrador global.  Esta conta é utilizada para criar a conta de conector do Azure AD durante a instalação do Azure AD Connect.  A conta de conector do Azure AD é utilizada para gravar informações para o Azure AD.   Para criar o administrador global conta efetue o seguinte.

1.  Em **Gerir**, selecione **Utilizadores**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **todos os utilizadores** e, em seguida, selecione **+ novo utilizador**.
3.  Forneça um nome e o nome de utilizador para este utilizador. Este será o Administrador Global do inquilino. Também queira fazer a **função de diretório** para **Administrador Global.** Também pode mostrar a palavra-passe temporária. Quando tiver terminado, selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Assim que terminar, abra um novo navegador da web e início de sessão para myapps.microsoft.com usando a nova conta de administrador global e a palavra-passe temporária.
5. Altere a palavra-passe de administrador global para algo que se lembrará.

## <a name="download-and-install-azure-ad-connect"></a>Transferir e instalar o Azure AD Connect
Agora é hora de transferir e instalar o Azure AD Connect.  Assim que tiver sido instalado, será executado por meio de instalação rápida.  Faça o seguinte:

1. Transferir [do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi** e faça duplo clique.
3. No ecrã de boas-vindas, selecione a caixa em que aceita os termos de licenciamento e clique em **Continuar**.  
4. No ecrã de definições rápidas, clique em **Utilizar definições rápidas**.</br>  
![Criar](media/tutorial-password-hash-sync/express1.png)</br>
5. No ecrã ligar ao Azure AD, introduza o nome de utilizador e palavra-passe de administrador global para o Azure AD. Clique em **Seguinte**.  
6. No ecrã Ligar ao AD DS, introduza o nome de utilizador e palavra-passe para uma conta de administrador da empresa. Clique em **Seguinte**.  
7. No ecrã Preparado para configurar, clique em **Instalar**.
8. Quando concluir a instalação, clique em **Sair**.
9. Depois de concluída a instalação, termine e inicie sessão novamente antes de utilizar o Synchronization Service Manager ou Editor de regras de sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Certifique-se de que os utilizadores são criados e a sincronização está a ocorrer
Agora iremos confirmar que os utilizadores que tínhamos no nosso diretório no local foram sincronizados e agora existem no inquilino do Azure AD.  Lembre-se de que esta operação pode demorar algumas horas a concluir.  Para verificar se os utilizadores forem sincronizados efetue o seguinte procedimento.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. No lado esquerdo, selecione **do Azure Active Directory**
3. Em **Gerir**, selecione **Utilizadores**.
4. Certifique-se de que consegue ver os novos usuários no nosso inquilino</br>
![Sincronização](media/tutorial-password-hash-sync/synch1.png)</br>

## <a name="test-signing-in-with-one-of-our-users"></a>Testar início de sessão com um dos nossos usuários

1. Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inicie sessão com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de início de sessão com o seguinte formato: (user@domain.onmicrosoft.com). Utilizar a mesma palavra-passe que o utilizador utiliza para iniciar sessão no local.</br>
   ![Certifique-se](media/tutorial-password-hash-sync/verify1.png)</br>

Agora tem com êxito um ambiente de identidade híbrida que pode utilizar para testar e familiarizar-se com o Azure tem para oferecer a configuração.

## <a name="next-steps"></a>Próximos Passos


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições rápidas](how-to-connect-install-express.md)
- [Sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)|
