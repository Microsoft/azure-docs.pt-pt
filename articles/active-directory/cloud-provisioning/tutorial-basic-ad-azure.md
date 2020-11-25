---
title: Tutorial - Diretório Ativo Básico no local e ambiente AD Azure.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceffc928a0cf8313723ff6193d9a6a63f8465f90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994779"
---
# <a name="tutorial-basic-active-directory-environment"></a>Tutorial: Ambiente básico de diretório ativo

Este tutorial acompanha-o através da criação de um ambiente básico de Diretório Ativo. 

![Diagrama que mostra um ambiente Azure A D básico.](media/tutorial-single-forest/diagram1.png)

Você pode usar o ambiente que você cria no tutorial para testar vários aspetos de cenários de identidade híbrida e será um pré-requisito para alguns dos tutoriais.  Se já tiver um ambiente de Diretório Ativo existente, pode usá-lo como substituto.  Esta informação é fornecida para indivíduos que o meu começo do nada.

Este tutorial consiste em
## <a name="prerequisites"></a>Pré-requisitos
Seguem-se os pré-requisitos necessários para completar este tutorial
- Um computador com [Hiper-V](/windows-server/virtualization/hyper-v/hyper-v-technology-overview) instalado.  Sugere-se que o faça num [computador Windows 10](/virtualization/hyper-v-on-windows/about/supported-guest-os) ou Windows [Server 2016.](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)
- Um [adaptador de rede externo](/virtualization/hyper-v-on-windows/quick-start/connect-to-network) para permitir que a máquina virtual comunique com a internet.
- Uma [subscrição do Azure](https://azure.microsoft.com/free)
- Uma cópia do Windows Server 2016
- [Quadro Microsoft .NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115)

> [!NOTE]
> Este tutorial utiliza scripts PowerShell para que possa criar o ambiente tutorial no mais rápido tempo.  Cada um dos scripts usa variáveis que são declaradas no início dos scripts.  Pode e deve alterar as variáveis para refletir o seu ambiente.
>
>Os scripts utilizados criam um ambiente de Diretório Ativo geral antes de instalar o agente de provisionamento de nuvem Azure AD Connect.  São relevantes para todos os tutoriais.
>
> Cópias dos scripts PowerShell que são usados neste tutorial estão disponíveis no GitHub [aqui.](https://github.com/billmath/tutorial-phs)

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
A primeira coisa que precisa de fazer, para pôr o nosso ambiente de identidade híbrida a funcionar é criar uma máquina virtual que será usada como o nosso servidor Ative Directory no local.  Faça o seguinte:

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
Agora que tem uma máquina virtual, precisa fazer algumas coisas antes de instalar o Ative Directory.  Ou seja, é necessário mudar o nome da máquina virtual, definir um endereço IP estático e informações sobre DNS e instalar as ferramentas de Administração do Servidor Remoto.   Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
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

## <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente AD do Servidor do Windows
Agora que tem o VM criado e foi renomeado e tem um endereço IP estático, pode ir em frente e instalar e configurar serviços de domínio de diretório ativo.  Faça o seguinte:

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
Agora que tens o nosso ambiente de Diretório Ativo, precisas de uma conta de teste.  Esta conta será criada no nosso ambiente de AD no local e depois sincronizada para Azure AD.  Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

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
Agora você precisa criar um inquilino AZure AD para que você possa sincronizar nossos utilizadores para a nuvem.  Para criar um novo inquilino do Azure AD, faça o seguinte.

1. Navegue para o [portal do Azure](https://portal.azure.com) e inicie sessão com uma conta que tenha uma subscrição do Azure.
2. Selecione o **ícone de adição (+)** e pesquise **Azure Active Directory**.
3. Selecione **Azure Active Directory** nos resultados de pesquisa.
4. Selecione **Criar**.</br>
![Screenshot que mostra a página do Diretório Ativo Azure no portal Azure.](media/tutorial-single-forest/create1.png)</br>
5. Forneça um **nome de organização**, juntamente com o **nome de domínio inicial**. Em seguida, selecione **Criar**. Esta ação irá criar o seu diretório.
6. Uma vez concluído, clique no link **aqui,** para gerir o diretório.

## <a name="create-a-global-administrator-in-azure-ad"></a>Criar um administrador global em Azure AD
Agora que tem um inquilino da AD Azure, vai criar uma conta de administrador global.  Para criar a conta de administrador global faça o seguinte.

1.  Em **Gerir**, selecione **Utilizadores**.</br>
![Screenshot que mostra o menu "Visão Geral" com "Utilizadores" selecionados.](media/tutorial-single-forest/administrator1.png)</br>
2.  Selecione **Todos os utilizadores** e, em seguida, selecione **+ Novo utilizador**.
3.  Indique um nome e nome de utilizador para o mesmo. Este será o Administrador Global do inquilino. Também vai querer alterar o **papel de Diretório** para **administrador global.** Pode também mostrar a palavra-passe temporária. Quando concluir, selecione **Criar**.</br>
![Criar](media/tutorial-single-forest/administrator2.png)</br>
4. Uma vez concluído, abra um novo navegador web e inscreva-se para myapps.microsoft.com usando a nova conta de administrador global e a senha temporária.
5. Altere a palavra-passe para o administrador global para algo que irá lembrar.

## <a name="optional--additional-server-and-forest"></a>Opcional: Servidor adicional e floresta
Segue-se uma secção opcional que fornece passos para a criação de um servidor adicional e ou floresta.  Isto pode ser usado em alguns dos tutoriais mais avançados, tais como [Pilot for Azure AD Connect para o provisionamento em nuvem](tutorial-pilot-aadc-aadccp.md).

Se necessitar apenas de um servidor adicional, pode parar após o - Criar o passo **da máquina virtual** e juntar o servidor ao domínio existente que foi criado acima.  

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

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

### <a name="complete-the-operating-system-deployment"></a>Complete a implementação do sistema operativo
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

### <a name="install-active-directory-prerequisites"></a>Instalar pré-requisitos do Diretório Ativo
Agora que tem uma máquina virtual, precisa fazer algumas coisas antes de instalar o Ative Directory.  Ou seja, é necessário mudar o nome da máquina virtual, definir um endereço IP estático e informações sobre DNS e instalar as ferramentas de Administração do Servidor Remoto.   Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

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
### <a name="create-a-windows-server-ad-environment"></a>Criar um ambiente AD do Servidor do Windows
Agora que tem o VM criado e foi renomeado e tem um endereço IP estático, pode ir em frente e instalar e configurar serviços de domínio de diretório ativo.  Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

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

### <a name="create-a-windows-server-ad-user"></a>Criar um utilizador AD do Servidor do Windows
Agora que tens o nosso ambiente de Diretório Ativo, precisas de uma conta de teste.  Esta conta será criada no nosso ambiente de AD no local e depois sincronizada para Azure AD.  Faça o seguinte:

1. Abra o PowerShell ISE como Administrador.
2. Execute o seguinte script.

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
Agora você tem um ambiente que pode ser usado para tutoriais existentes e para testar funcionalidades adicionais que o fornecimento de nuvens fornece.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)