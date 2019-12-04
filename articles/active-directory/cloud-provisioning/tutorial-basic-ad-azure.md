---
title: Tutorial-Active Directory básica do ambiente local e do Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356a05d4d92f17ceb66ff0208153ec3eac736757
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793901"
---
# <a name="tutorial-basic-active-directory-environment"></a>Tutorial: ambiente de Active Directory básica

Este tutorial orienta você pela criação de um ambiente de Active Directory básico. 

![Create](media/tutorial-single-forest/diagram1.png)

Você pode usar o ambiente criado no tutorial para testar vários aspectos de cenários de identidade híbrida e será um pré-requisito para alguns dos tutoriais.  Se você já tiver um ambiente de Active Directory existente, poderá usá-lo como um substituto.  Essas informações são fornecidas para pessoas que não estão iniciando nada.

Este tutorial consiste em
## <a name="prerequisites"></a>Pré-requisitos
Veja a seguir os pré-requisitos necessários para concluir este tutorial
- Um computador com o [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  É recomendável fazer isso em um computador com Windows [10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os) ou [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) .
- Um [adaptador de rede externo](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual se comunique com a Internet.
- Uma [subscrição do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Este tutorial usa scripts do PowerShell para que você possa criar o ambiente do tutorial no período de tempo mais rápido.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Você pode e deve alterar as variáveis para refletir seu ambiente.
>
>Os scripts usados criam um ambiente de Active Directory geral antes de instalar o agente de provisionamento de nuvem Azure AD Connect.  Eles são relevantes para todos os tutoriais.
>
> Cópias dos scripts do PowerShell que são usados neste tutorial estão disponíveis no GitHub [aqui](https://github.com/billmath/tutorial-phs).

## <a name="create-a-virtual-machine"></a>Crie uma máquina virtual
A primeira coisa que você precisa fazer para colocar o ambiente de identidade híbrida em funcionamento é criar uma máquina virtual que será usada como nosso servidor de Active Directory local.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

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

## <a name="complete-the-operating-system-deployment"></a>Concluir a implantação do sistema operacional
Para concluir a criação da máquina virtual, você precisa concluir a instalação do sistema operacional.

1. Gerenciador do Hyper-V, clique duas vezes na máquina virtual
2. Clique no botão Iniciar.
3. Você será solicitado a "pressionar qualquer tecla para inicializar do CD ou DVD". Vá em frente e faça isso.
4. Na tela inicial do Windows Server, selecione seu idioma e clique em **Avançar**.
5. Clique em **instalar agora**.
6. Insira sua chave de licença e clique em **Avançar**.
7. Marque * * aceito os termos de licença e clique em **Avançar**.
8. Selecione **personalizado: instalar somente Windows (avançado)**
9. Clique em **Seguinte**
10. Depois que a instalação for concluída, reinicie a máquina virtual, entre e execute as atualizações do Windows para garantir que a VM seja a mais atualizada.  Instale as atualizações mais recentes.

## <a name="install-active-directory-prerequisites"></a>Instalar Active Directory pré-requisitos
Agora que você tem uma máquina virtual ativa, precisa fazer algumas coisas antes de instalar o Active Directory.  Ou seja, você precisa renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de administração de servidor remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

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

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente do AD do Windows Server
Agora que a VM foi criada e foi renomeada e tem um endereço IP estático, você pode instalar e configurar o Active Directory Domain Services.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

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

## <a name="create-a-windows-server-ad-user"></a>Criar um usuário do AD do Windows Server
Agora que você tem nosso ambiente de Active Directory, precisa de uma conta de teste.  Essa conta será criada em nosso ambiente do AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

    ```powershell 
    # Filename:    4_CreateUser.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
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
Agora você precisa criar um locatário do Azure AD para que possa sincronizar nossos usuários com a nuvem.  Para criar um novo locatário do Azure AD, faça o seguinte.

1. Navegue até o [portal do Azure](https://portal.azure.com) e entre com uma conta que tenha uma assinatura do Azure.
2. Selecione o **ícone de adição (+)** e procure **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados da pesquisa.
4. Selecione **Criar**.</br>
![Criar](media/tutorial-single-forest/create1.png)</br>
5. Forneça um **nome para a organização** junto com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Isso criará seu diretório.
6. Depois que isso for concluído, clique no link **aqui** para gerenciar o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global no Azure AD
Agora que você tem um locatário do Azure AD, você criará uma conta de administrador global.  Para criar a conta de administrador global, faça o seguinte.

1.  Em **Gerir**, selecione **Utilizadores**.</br>
![Criar](media/tutorial-single-forest/administrator1.png)</br>
2.  Selecione **todos os usuários** e, em seguida, selecione **+ novo usuário**.
3.  Forneça um nome e um nome de usuário para este usuário. Esse será o administrador global do locatário. Você também vai querer alterar a **função de diretório** para **administrador global.** Você também pode mostrar a senha temporária. Quando terminar, selecione **criar**.</br>
![Criar](media/tutorial-single-forest/administrator2.png)</br>
4. Depois que isso for concluído, abra um novo navegador da Web e entre no myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a senha do administrador global para algo que você irá lembrar.

## <a name="optional--additional-server-and-forest"></a>Opcional: servidor e floresta adicionais
A seguir está uma seção opcional que fornece etapas para criar um servidor e ou floresta adicional.  Isso pode ser usado em alguns dos tutoriais mais avançados, como o [piloto, para Azure ad Connect para o provisionamento em nuvem](tutorial-pilot-aadc-aadccp.md).

Se você precisar apenas de um servidor adicional, poderá parar após a etapa- **criar a máquina virtual** e ingressar o servidor no domínio existente que foi criado acima.  

### <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

    ```powershell
    # Filename:    1_CreateVM_CP.ps1
    # Description: Creates a VM to be used in the tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. #This script is made available to you without any express, implied or statutory warranty, not even the implied warranty of merchantability or fitness for a particular purpose, or the warranty of title or non-infringement. The entire risk of the use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $VMName = 'CP1'
    $Switch = 'External'
    $InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
    $Path = 'D:\VM'
    $VHDPath = 'D:\VM\CP1\CP1.vhdx'
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

### <a name="complete-the-operating-system-deployment"></a>Concluir a implantação do sistema operacional
Para concluir a criação da máquina virtual, você precisa concluir a instalação do sistema operacional.

1. Gerenciador do Hyper-V, clique duas vezes na máquina virtual
2. Clique no botão Iniciar.
3. Você será solicitado a "pressionar qualquer tecla para inicializar do CD ou DVD". Vá em frente e faça isso.
4. Na tela inicial do Windows Server, selecione seu idioma e clique em **Avançar**.
5. Clique em **instalar agora**.
6. Insira sua chave de licença e clique em **Avançar**.
7. Marque * * aceito os termos de licença e clique em **Avançar**.
8. Selecione **personalizado: instalar somente Windows (avançado)**
9. Clique em **Seguinte**
10. Depois que a instalação for concluída, reinicie a máquina virtual, entre e execute as atualizações do Windows para garantir que a VM seja a mais atualizada.  Instale as atualizações mais recentes.

### <a name="install-active-directory-prerequisites"></a>Instalar Active Directory pré-requisitos
Agora que você tem uma máquina virtual ativa, precisa fazer algumas coisas antes de instalar o Active Directory.  Ou seja, você precisa renomear a máquina virtual, definir um endereço IP estático e informações de DNS e instalar as ferramentas de administração de servidor remoto.   Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

    ```powershell
    # Filename:    2_ADPrep_CP.ps1
    # Description: Prepares your environment for Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $ipaddress = "10.0.1.118" 
    $ipprefix = "24" 
    $ipgw = "10.0.1.1" 
    $ipdns = "10.0.1.118"
    $ipdns2 = "8.8.8.8" 
    $ipif = (Get-NetAdapter).ifIndex 
    $featureLogPath = "c:\poshlog\featurelog.txt" 
    $newname = "CP1"
    $addsTools = "RSAT-AD-Tools" 

    #Set static IP address
    New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

    #Set the DNS servers
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
### <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente do AD do Windows Server
Agora que a VM foi criada e foi renomeada e tem um endereço IP estático, você pode instalar e configurar o Active Directory Domain Services.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

    ```powershell
    # Filename:    3_InstallAD_CP.ps1
    # Description: Creates an on-premises AD envrionment.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $DatabasePath = "c:\windows\NTDS"
    $DomainMode = "WinThreshold"
    $DomainName = "fabrikam.com"
    $DomaninNetBIOSName = "FABRIKAM"
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

### <a name="create-a-windows-server-ad-user"></a>Criar um usuário do AD do Windows Server
Agora que você tem nosso ambiente de Active Directory, precisa de uma conta de teste.  Essa conta será criada em nosso ambiente do AD local e, em seguida, sincronizada com o Azure AD.  Faça o seguinte:

1. Abra o ISE do PowerShell como administrador.
2. Execute o script a seguir.

    ```powershell 
    # Filename:    4_CreateUser_CP.ps1
    # Description: Creates a user in Active Directory.  This is part of
    #              the Azure AD Connect password hash sync tutorial.
    #
    # DISCLAIMER:
    # Copyright (c) Microsoft Corporation. All rights reserved. This 
    # script is made available to you without any express, implied or 
    # statutory warranty, not even the implied warranty of 
    # merchantability or fitness for a particular purpose, or the 
    # warranty of title or non-infringement. The entire risk of the 
    # use or the results from the use of this script remains with you.
    #
    #
    #
    #
    #Declare variables
    $Givenname = "Anna"
    $Surname = "Ringdal"
    $Displayname = "Anna Ringdal"
    $Name = "aringdal"
    $Password = "Pass1w0rd"
    $Identity = "CN=aringdal,CN=Users,DC=fabrikam,DC=com"
    $SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


    #Create the user
    New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

    #Set the password to never expire
    Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
    ```

## <a name="conclusion"></a>Conclusão
Agora você tem um ambiente que pode ser usado para tutoriais existentes e para testar recursos adicionais fornecidos pelo provisionamento de nuvem.

## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)
