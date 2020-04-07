---
title: 'Tutorial: Federar um único ambiente florestal aD para Azure Microsoft Docs'
description: Demonstra como configurar um ambiente de identidade híbrida usando a federação.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3a17eb7fdde6840ce04fb0cbce13ec3f1a121e0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673708"
---
# <a name="tutorial-federate-a-single-ad-forest-environment-to-the-cloud"></a>Tutorial: Federar um único ambiente florestal ad para a nuvem

![Criar](media/tutorial-federation/diagram.png)

O seguinte tutorial irá acompanhá-lo através da criação de um ambiente de identidade híbrida usando a federação.  Este ambiente pode então ser utilizado para testes ou para se familiarizar mais com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para completar este tutorial
- Um computador com [Hiper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  Sugere-se que o faça num computador [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou windows [Server 2016.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)
- Uma [subscrição azure](https://azure.microsoft.com/free)
- - Um [adaptador de rede externo](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual se comunique com a internet.
- Uma cópia do Windows Server 2016
- Um [domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md) que pode ser verificado

> [!NOTE]
> Este tutorial utiliza scripts PowerShell para que possa criar o ambiente tutorial no mais rápido tempo.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Pode e deve alterar as variáveis para refletir o seu ambiente.
>
>Os scripts utilizados criam um ambiente geral de Diretório Ativo antes de instalar o Azure AD Connect.  São relevantes para todos os tutoriais.
>
> Cópias dos scripts PowerShell que são usados neste tutorial estão disponíveis no GitHub [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que temos de fazer, para que o nosso ambiente de identidade híbrida esteja a funcionar é criar uma máquina virtual que será usada como nosso servidor de Diretório Ativo no local.  

>[!NOTE]
>Se nunca executou um script no PowerShell na sua `Set-ExecutionPolicy remotesigned` máquina de anfitriões, terá de executar e dizer sim no PowerShell, antes de executar scripts.

Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
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

## <a name="complete-the-operating-system-deployment"></a>Complete a implementação do sistema operativo
Para terminar a construção da máquina virtual, é necessário terminar a instalação do sistema operativo.

1. Hyper-V Manager, clique duas vezes na máquina virtual
2. Clique no botão Iniciar.
3. Será solicitado a "carregar em qualquer tecla para arrancar a partir de CD ou DVD". Vá em frente e faça-o.
4. No ecrã de arranque do Windows Server, selecione o seu idioma e clique em **Next**.
5. Clique em **Instalar agora**.
6. Insira a chave da sua licença e clique **em Next**.
7. Verifique **Aceito os termos da licença e clico **em Seguinte**.
8. Selecione **Personalizado: Instale apenas o Windows (Avançado)**
9. Clique **em Seguinte**
10. Uma vez concluída a instalação, reinicie a máquina virtual, inicie o início e execute as atualizações do Windows para garantir que o VM é o mais atualizado.  Instale as últimas atualizações.

## <a name="install-active-directory-pre-requisites"></a>Instalar pré-requisitos de Diretório Ativo
Agora que temos uma máquina virtual, precisamos fazer algumas coisas antes de instalar o Ative Directory.  Ou seja, precisamos de mudar o nome da máquina virtual, definir um endereço IP estático e informações DNS, e instalar as ferramentas de Administração do Servidor Remoto.   Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Corra `Set-ExecutionPolicy remotesigned` e diga sim a todos [A].  Prima Enter.
3. Execute o seguinte script.

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

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente aD do Windows Server
Agora que temos o VM criado e foi renomeado e tem um endereço IP estático, podemos ir em frente e instalar e configurar serviços de domínio de diretório ativo.  Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
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
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $Password -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Criar um utilizador de Anúncio do Servidor windows
Agora que temos o nosso ambiente de Diretório Ativo, precisamos de uma conta de teste.  Esta conta será criada no nosso ambiente ad-in-local e, em seguida, sincronizada com a Azure AD.  Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
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

## <a name="create-a-certificate-for-ad-fs"></a>Criar um certificado para AD FS
Agora vamos criar um certificado TLS/SSL que será usado pela AD FS.  Este será um certificado auto-assinado e é apenas para fins de teste.  A Microsoft não recomenda a utilização de um certificado auto-assinado num ambiente de produção. Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

```powershell 
#Declare variables
$DNSname = "adfs.contoso.com"
$Location = "cert:\LocalMachine\My"

#Create certificate
New-SelfSignedCertificate -DnsName $DNSname -CertStoreLocation $Location
```

## <a name="create-an-azure-ad-tenant"></a>Criar um inquilino do Azure AD
Agora precisamos criar um inquilino Azure AD para que possamos sincronizar os nossos utilizadores na nuvem.  Para criar um novo inquilino do Azure AD, faça o seguinte.

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. Selecione o **ícone de adição (+)** e pesquise **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados de pesquisa.
4. Selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/create1.png)</br>
5. Forneça um **nome de organização**, juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Esta ação irá criar o seu diretório.
6. Uma vez concluída, clique no link **aqui,** para gerir o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global em Azure AD
Agora que temos um inquilino da AD Azure, vamos criar uma conta de administrador global.  Esta conta é utilizada para criar a conta de Conector Azure AD durante a instalação Azure AD Connect.  A conta Azure AD Connector é utilizada para escrever informações para a AD Azure.   Para criar a conta de administrador global faça o seguinte.

1.  Em **Gerir**, selecione **Utilizadores**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **Todos os utilizadores** e, em seguida, selecione **+ Novo utilizador**.
3.  Indique um nome e nome de utilizador para o mesmo. Este será o Administrador Global do inquilino. Também vai querer mudar o **papel de Diretório** para **administrador global.** Pode também mostrar a palavra-passe temporária. Quando concluir, selecione **Criar**.</br>
![Criar](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Uma vez concluído, abra um novo navegador web e inscreva-se para myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Mude a palavra-passe para o administrador global para algo de que se lembrará.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Adicione o nome de domínio personalizado ao seu diretório
Agora que temos um inquilino e um administrador global, precisamos adicionar o nosso domínio personalizado para que o Azure possa comprová-lo.  Faça o seguinte:

1. De volta ao [portal Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) certifique-se de fechar a lâmina **de Todos os Utilizadores.**
2. No lado esquerdo, selecione **Nomes de domínio personalizado**.
3. **Selecione Adicionar domínio personalizado**.</br>
![de Federação](media/tutorial-federation/custom1.png)</br>
4. Em nomes de **domínio personalizados,** introduza o nome do seu domínio personalizado na caixa e clique em **Adicionar Domínio**.
5. No ecrã de nome de domínio personalizado, será fornecido com informações TXT ou MX.  Estas informações devem ser adicionadas às informações do DNS do registo de domínio sob o seu domínio.  Por isso, tem de ir ao seu registo de domínio, introduzir as informações TXT ou MX nas definições de DNS para o seu domínio.  Isto permitirá ao Azure verificar o seu domínio.  Isto pode levar até 24 horas para o Azure verificar.  Para mais informações, consulte a adição de uma documentação de [domínio personalizado.](../../active-directory/fundamentals/add-custom-domain.md)</br>
![de Federação](media/tutorial-federation/custom2.png)</br>
6. Para garantir a verificação, clique no botão Verificar.</br>
![de Federação](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Descarregue e instale o Azure AD Connect
Agora é hora de descarregar e instalar o Azure AD Connect.  Uma vez instalado, vamos passar pela instalação expressa.  Faça o seguinte:

1. Baixar [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi** e faça duplo clique.
3. No ecrã de boas-vindas, selecione a caixa em que aceita os termos de licenciamento e clique em **Continuar**.  
4. No ecrã de definições express, clique em **Personalizar**.  
5. No ecrã de componentes de instalação necessário. Clique em **Instalar**.  
6. No ecrã de iniciar sessão do utilizador, selecione **Federação com AD FS** e clique **em Seguinte**.
![de Federação](media/tutorial-federation/fed1.png)

1. No ecrã Connect to Azure AD, introduza o nome de utilizador e a palavra-passe do administrador global que criámos acima e clique em **Next**.
2. No ecrã Connect your directdirecties, clique em **Adicionar Diretório**.  Em seguida, selecione **Criar nova conta AD** e introduza o nome de utilizador e senha do administrador de contos e clique em **OK**.
3. Clique em **Seguinte**.
4. No ecrã de configuração de entrada de AD Azure, selecione **Continue sem combinar todos os sufixos UPN com domínios verificados** e clique **em Seguinte.**
5. No ecrã de filtragem Domain e U, clique em **Next**.
6. No ecrã de identificação exclusivamente dos seus utilizadores, clique em **Next**.
7. No ecrã de utilizadores e dispositivos do Filtro, clique em **Seguinte**.
8. No ecrã de funcionalidades Opcionais, clique em **Next**.
9. Na página de credenciais do Administrador de Domínio, introduza o nome de utilizador e senha do administrador de contos e clique em **Seguinte.**
10. No ecrã da quinta AD FS, certifique-se de **que configurar uma nova quinta AD FS.**
11. Selecione **Utilize um certificado instalado nos servidores da federação** e clique em **Navegar**.
12. Introduza DC1 na caixa de pesquisa e selecione-o quando for encontrado.  Clique em **OK**.
13. A partir da entrega do **Ficheiro de Certificado,** selecione **adfs.contoso.com** o certificado que criámos acima.  Clique em **Seguinte**.
![de Federação](media/tutorial-federation/fed2.png)

1. No ecrã do servidor AD FS, clique em **Navegar** e introduza DC1 na caixa de pesquisa e selecione-o quando for encontrado.  Clique em **OK**.  Clique em **Seguinte**.
![de Federação](media/tutorial-federation/fed3.png)

1. No ecrã dos servidores proxy da aplicação Web, clique em **Seguinte**.
2. No ecrã da conta de serviço AD FS, introduza o nome de utilizador e senha do administrador de contose clique em **Seguinte.**
3. No ecrã de Domínio AD Azure, selecione o seu domínio personalizado verificado a partir do drop-down e clique **em Next**.
4. No ecrã Preparado para configurar, clique em **Instalar**.
5. Quando concluir a instalação, clique em **Sair**.
6. Depois de concluída a instalação, assine e inscreva-se novamente antes de utilizar o Gestor de Serviços de Sincronização ou editor de regras de sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifique se os utilizadores são criados e a sincronização está a ocorrer
Vamos agora verificar se os utilizadores que tínhamos no nosso diretório no local foram sincronizados e existem agora no inquilino da AD Azure.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão sincronizados, faça o seguinte.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Ative Diretório**
3. Em **Gerir**, selecione **Utilizadores**.
4. Verifique se vê os novos ![utilizadores no nosso inquilino Synch](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Teste de sessão com um dos nossos utilizadores

1. Navegue para[https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Inscreva-se com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar sessão utilizando ouser@domain.onmicrosoft.comseguinte formato: ( ). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sessão no local.
   ![Verificar](media/tutorial-password-hash-sync/verify1.png)

Agora criou com sucesso um ambiente de identidade híbrida que pode usar para testar e familiarizar-se com o que o Azure tem para oferecer.

## <a name="next-steps"></a>Passos Seguintes

- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)

