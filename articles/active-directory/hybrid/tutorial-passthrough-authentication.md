---
title: 'Tutorial: Integre uma única floresta de AD para Azure usando aPT'
description: Demonstra como configurar um ambiente de identidade híbrida utilizando a autenticação pass-through.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f02ec2220827fbec8c981ab3a1859d633675a6f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313268"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-pass-through-authentication-pta"></a>Tutorial: Integrar uma única floresta AD utilizando a autenticação pass-through (PTA)

![Criar](media/tutorial-passthrough-authentication/diagram.png)

O seguinte tutorial irá acompanhá-lo através da criação de um ambiente de identidade híbrida usando a autenticação pass-through.  Este ambiente pode então ser usado para testar ou para se familiarizar mais com o funcionamento de uma identidade híbrida.

## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para completar este tutorial
- Um computador com [Hiper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  Sugere-se que o faça num [computador Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) ou Windows [Server 2016.](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)
- Uma [subscrição do Azure](https://azure.microsoft.com/free)
- - Um [adaptador de rede externo](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual comunique com a internet.
- Uma cópia do Windows Server 2016
- Um [domínio personalizado](../../active-directory/fundamentals/add-custom-domain.md) que pode ser verificado

> [!NOTE]
> Este tutorial utiliza scripts PowerShell para que possa criar o ambiente tutorial no mais rápido tempo.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Pode e deve alterar as variáveis para refletir o seu ambiente.
>
>Os scripts utilizados criam um ambiente de Diretório Ativo geral antes de instalar o Azure AD Connect.  São relevantes para todos os tutoriais.
>
> Cópias dos scripts PowerShell que são usados neste tutorial estão disponíveis no GitHub [aqui.](https://github.com/billmath/tutorial-phs)

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que temos de fazer, para pôr o nosso ambiente de identidade híbrida a funcionar é criar uma máquina virtual que será usada como o nosso servidor Ative Directory no local.  

>[!NOTE]
>Se nunca executou um script no PowerShell na sua máquina de anfitrião, terá de executar `Set-ExecutionPolicy remotesigned` e dizer sim no PowerShell, antes de executar scripts.

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
3. Será solicitado que "Pressione qualquer tecla para arrancar a partir de CD ou DVD". Vá em frente e fazê-lo.
4. No ecrã de arranque do Windows Server selecione o seu idioma e clique em **Seguinte**.
5. Clique **em Instalar agora**.
6. Introduza a chave da licença e clique em **Seguinte**.
7. Verifique **Aceito os termos da licença e clique em **Seguinte**.
8. Selecione **Personalizado: Instalar apenas o Windows (Avançado)**
9. Clique **em seguida**
10. Uma vez concluída a instalação, reinicie a máquina virtual, inscreva-se e execute atualizações do Windows para garantir que o VM é o mais atualizado.  Instale as últimas atualizações.

## <a name="install-active-directory-prerequisites"></a>Instalar pré-requisitos do Diretório Ativo
Agora que temos uma máquina virtual, temos de fazer algumas coisas antes de instalar o Ative Directory.  Ou seja, precisamos de renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de Administração do Servidor Remoto.   Faça o seguinte:

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

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente AD do Servidor do Windows
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

## <a name="create-a-windows-server-ad-user"></a>Criar um utilizador AD do Servidor do Windows
Agora que temos o nosso ambiente de Diretório Ativo, precisamos de uma conta de teste.  Esta conta será criada no nosso ambiente de AD no local e depois sincronizada para Azure AD.  Faça o seguinte:

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

## <a name="create-an-azure-ad-tenant"></a>Criar um inquilino do Azure AD
Agora precisamos criar um inquilino AZure AD para que possamos sincronizar os nossos utilizadores com a nuvem.  Para criar um novo inquilino do Azure AD, faça o seguinte.

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. Selecione o **ícone de adição (+)** e pesquise **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados de pesquisa.
4. Selecione **Criar**.</br>
![Screenshot que mostra como criar um inquilino AZure AD.](media/tutorial-password-hash-sync/create1.png)</br>
5. Forneça um **nome de organização**, juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Esta ação irá criar o seu diretório.
6. Uma vez concluído, clique no link **aqui,** para gerir o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global em Azure AD
Agora que temos um inquilino da AD Azure, vamos criar uma conta de administrador global.  Esta conta é utilizada para criar a conta Azure AD Connector durante a instalação Azure AD Connect.  A conta Azure AD Connector é usada para escrever informações para a Azure AD.   Para criar a conta de administrador global faça o seguinte.

1.  Em **Gerir**, selecione **Utilizadores**.</br>
![Screenshot que mostra a opção Utilizador selecionada na secção Gerir onde cria um administrador global em Azure AD.](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Selecione **Todos os utilizadores** e, em seguida, selecione **+ Novo utilizador**.
3.  Indique um nome e nome de utilizador para o mesmo. Este será o Administrador Global do inquilino. Também vai querer alterar o **papel de Diretório** para **administrador global.** Pode também mostrar a palavra-passe temporária. Quando concluir, selecione **Criar**.</br>
![Screenshot que mostra o botão Criar que seleciona quando cria um administrador global em Azure AD.](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Uma vez concluído, abra um novo navegador web e inscreva-se para myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a palavra-passe para o administrador global para algo que irá lembrar.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Adicione o nome de domínio personalizado ao seu diretório
Agora que temos um inquilino e um administrador global, precisamos adicionar o nosso domínio personalizado para que a Azure possa comprová-lo.  Faça o seguinte:

1. De volta ao [portal Azure](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) certifique-se de fechar a lâmina **de Todos os Utilizadores.**
2. No lado esquerdo, selecione **Nomes de domínio personalizado**.
3. **Selecione Adicionar domínio personalizado**.</br>
![Screenshot que mostra o botão de domínio personalizado Adicionar realçado.](media/tutorial-federation/custom1.png)</br>
4. Em **nomes de domínio personalizados,** insira o nome do seu domínio personalizado na caixa e clique em **Adicionar Domínio**.
5. No ecrã de nome de domínio personalizado, será fornecido com informações TXT ou MX.  Estas informações devem ser adicionadas à informação do DNS do registo de domínio sob o seu domínio.  Por isso, tem de ir ao seu registo de domínio, introduzir as informações TXT ou MX nas definições de DNS para o seu domínio.  Isto permitirá ao Azure verificar o seu domínio.  Isto pode levar até 24 horas para a Azure verificar.  Para obter mais informações, consulte a adição de uma documentação [de domínio personalizada.](../../active-directory/fundamentals/add-custom-domain.md)</br>
![Screenshot que mostra onde adiciona as informações TXT ou MX.](media/tutorial-federation/custom2.png)</br>
6. Para garantir a verificação, clique no botão Verificar.</br>
![Screenshot que mostra uma mensagem de verificação bem sucedida depois de selecionar Verificar.](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Descarregue e instale O Azure AD Connect
Agora é hora de descarregar e instalar o Azure AD Connect.  Uma vez instalado, passaremos pela instalação expressa.  Faça o seguinte:

1. Baixar [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2. Navegue até **AzureADConnect.msi** e faça duplo clique.
3. No ecrã de boas-vindas, selecione a caixa em que aceita os termos de licenciamento e clique em **Continuar**.  
4. No ecrã de definições express, clique **em Personalizar**.  
5. No ecrã de componentes necessários para a instalação. Clique em **Install** (Instalar).  
6. No ecrã de inscrição do utilizador, selecione **a autenticação Pass-through** e **ative o sign-in único** e clique em **Seguinte**.</br>
![PTA](media/tutorial-passthrough-authentication/pta1.png)</b>
7. No ecrã AD Do Ligar ao Azure, insira o nome de utilizador e a palavra-passe do administrador global que criamos acima e clique em **Next**.
2. No ecrã 'Ligar os directórios', clique em **Adicionar Diretório**.  Em seguida, **selecione Criar uma nova conta AD** e introduzir o nome de utilizador e palavra-passe do administrador contoso\Administrador e clique em **OK**.
3. Clique em **Seguinte**.
4. No ecrã de configuração de inscrição AD Azure, selecione **Continue sem combinar todos os sufixos UPN para domínios verificados** e clique em **Seguinte.**
5. No ecrã de filtragem Domain e OU, clique em **Seguinte**.
6. No ecrã de identificação única dos seus utilizadores, clique em **Seguinte**.
7. No ecrã de utilizadores e dispositivos do Filtro, clique em **Seguinte**.
8. No ecrã de funcionalidades opcionais, clique em **Seguinte**.
9. Na página de credenciais de sinal-n único, insira o nome de utilizador e palavra-passe do administrador contoso\Administrador e clique em **Seguinte.**
10. No ecrã Preparado para configurar, clique em **Instalar**.
11. Quando concluir a instalação, clique em **Sair**.
12. Depois de concluída a instalação, inscreva-se e inscreva-se novamente antes de utilizar o Gestor de Serviço de Sincronização ou o Editor de Regras de Sincronização.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifique se os utilizadores são criados e a sincronização está a ocorrer
Vamos agora verificar se os utilizadores que tínhamos no nosso diretório no local foram sincronizados e agora existem no inquilino da AD Azure.  Esteja ciente de que isto pode levar algumas horas para ser concluído.  Para verificar se os utilizadores estão sincronizados, faça o seguinte.


1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. À esquerda, selecione **Azure Ative Directory**
3. Em **Gerir**, selecione **Utilizadores**.
4. Verifique se vê os novos utilizadores no nosso inquilino ![ Synch](media/tutorial-password-hash-sync/synch1.png)

## <a name="test-signing-in-with-one-of-our-users"></a>Iniciar sessão de teste com um dos nossos utilizadores

1. Navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Iniciar sôms com uma conta de utilizador que foi criada no nosso novo inquilino.  Terá de iniciar s-in utilizando o seguinte formato: user@domain.onmicrosoft.com (). Utilize a mesma palavra-passe que o utilizador utiliza para iniciar sômposições no local.
   ![Verificação](media/tutorial-password-hash-sync/verify1.png)

Você agora criou com sucesso um ambiente de identidade híbrida que você pode usar para testar e familiarizar-se com o que Azure tem para oferecer.

## <a name="next-steps"></a>Passos Seguintes


- [Hardware e pré-requisitos](how-to-connect-install-prerequisites.md) 
- [Definições personalizadas](how-to-connect-install-custom.md)
- [Autenticação pass-through](how-to-connect-pta.md)