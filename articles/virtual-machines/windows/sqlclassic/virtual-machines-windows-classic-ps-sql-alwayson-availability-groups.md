---
title: Configure o grupo Always On availability num Azure VM utilizando o PowerShell Microsoft Docs
description: Este tutorial utiliza recursos que foram criados com o modelo clássico de implantação. Utiliza o PowerShell para criar um grupo de disponibilidade Always On em Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 380abff03ad4ee4befb645f7f992ab037d213b33
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086709"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configure o grupo de disponibilidade Always On num Azure VM com PowerShell
> [!div class="op_single_selector"]
> * [Clássico: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere que agora pode completar esta tarefa no modelo de gestor de recursos Azure. Recomendamos o modelo de gestor de recursos Azure para novas implementações. Consulte [o SQL Server Sempre Em grupos de disponibilidade em máquinas virtuais Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md).

> [!IMPORTANT]
> Recomendamos que a maioria das novas implementações utilizem o modelo Gestor de Recursos. O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo cobre a utilização do modelo de implementação clássica.

As máquinas virtuais Azure (VMs) podem ajudar os administradores de bases de dados a reduzir o custo de um sistema de servidor SQL de alta disponibilidade. Este tutorial mostra-lhe como implementar um grupo de disponibilidade utilizando o SQL Server Always On End dentro de um ambiente Azure. No final do tutorial, a sua solução SQL Server Always On em Azure será composta pelos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo uma extremidade frontal e uma sub-rede traseira.
* Um controlador de domínio com um domínio ative directory.
* Dois VMs do Servidor SQL que são implantados na sub-rede de back-end e unidos ao domínio do Ative Directory.
* Um cluster de falha de três nós do Windows com o modelo de quórum no nó Majority.
* Um grupo de disponibilidade com duas réplicas sincronizadas de uma base de dados de disponibilidade.

Este cenário é uma boa escolha para a sua simplicidade no Azure, não pela sua custo-eficácia ou outros fatores. Por exemplo, pode minimizar o número de VMs para um grupo de disponibilidade de duas réplicas para economizar em horas de computação em Azure, utilizando o controlador de domínio como testemunha de partilha de ficheiros quórum num cluster de falhade dois nós. Este método reduz a contagem de VM por uma da configuração acima.

Este tutorial destina-se a mostrar-lhe os passos necessários para configurar a solução descrita acima, sem especificar os detalhes de cada passo. Portanto, em vez de fornecer os passos de configuração DO GUI, ele usa a scripting PowerShell para levá-lo rapidamente através de cada passo. Este tutorial pressupõe o seguinte:

* Já tem uma conta Azure com a subscrição da máquina virtual.
* Instalou os [cmdlets Azure PowerShell.](/powershell/azure/overview)
* Já tem uma compreensão sólida de grupos de disponibilidade always on para soluções no local. Para obter mais informações, consulte [sempre os grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conecte-se à sua subscrição Azure e crie a rede virtual
1. Numa janela PowerShell no seu computador local, importe o módulo Azure, descarregue o ficheiro de definições de publicação para a sua máquina e ligue a sua sessão PowerShell à sua assinatura Azure importando as definições de publicação descarregadas.

    ```powershell
    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
    Get-AzurePublishSettingsFile
    Import-AzurePublishSettingsFile <publishsettingsfilepath>
    ```

    O comando **Get-AzurePublishSettingsFile** gera automaticamente um certificado de gestão com o Azure e transfere-o para a sua máquina. Um navegador é aberto automaticamente e é solicitado a introduzir as credenciais de conta da Microsoft para a sua subscrição Azure. O ficheiro **.publishsettings** descarregado contém todas as informações necessárias para gerir a sua subscrição Azure. Depois de guardar este ficheiro para um diretório local, importe-o utilizando o comando **Import-AzurePublishSettingsFile.**

   > [!NOTE]
   > O ficheiro .publishsettings contém as suas credenciais (não codificadas) que são usadas para administrar as suas subscrições e serviços Azure. A melhor prática de segurança para este ficheiro é armazená-lo temporariamente fora dos seus diretórios de origem (por exemplo, na pasta Bibliotecas\Documentos) e depois eliminá-lo após o fim da importação. Um utilizador malicioso que obtenha acesso ao ficheiro .publishsettings pode editar, criar e eliminar os seus serviços Azure.

2. Defina uma série de variáveis que utilizará para criar a sua infraestrutura de TI em nuvem.

    ```powershell
    $location = "West US"
    $affinityGroupName = "ContosoAG"
    $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
    $affinityGroupLabel = "IaaS BI Affinity Group"
    $networkConfigPath = "C:\scripts\Network.netcfg"
    $virtualNetworkName = "ContosoNET"
    $storageAccountName = "<uniquestorageaccountname>"
    $storageAccountLabel = "Contoso SQL HADR Storage Account"
    $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
    $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
    $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
    $dcServerName = "ContosoDC"
    $dcServiceName = "<uniqueservicename>"
    $availabilitySetName = "SQLHADR"
    $vmAdminUser = "AzureAdmin"
    $vmAdminPassword = "Contoso!000"
    $workingDir = "c:\scripts\"
    ```

    Preste atenção ao seguinte para garantir que os seus comandos serão bem sucedidos mais tarde:

   * As variáveis **$storageAccountName** e **$dcServiceName** devem ser únicas porque são usadas para identificar a sua conta de armazenamento em nuvem e servidor de nuvem, respectivamente, na Internet.
   * Os nomes que especifica para variáveis **$affinityGroupName** e **$virtualNetworkName** estão configurados no documento de configuração da rede virtual que utilizará mais tarde.
   * **$sqlImageName** especifica o nome atualizado da imagem VM que contém SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para simplificar, **Contoso!000** é a mesma senha que é usada em todo o tutorial.

3. Criar um grupo de afinidades.

    ```powershell
    New-AzureAffinityGroup `
        -Name $affinityGroupName `
        -Location $location `
        -Description $affinityGroupDescription `
        -Label $affinityGroupLabel
    ```

4. Criar uma rede virtual importando um ficheiro de configuração.

    ```powershell
    Set-AzureVNetConfig `
        -ConfigurationPath $networkConfigPath
    ```

    O ficheiro de configuração contém o seguinte documento XML. Em resumo, especifica uma rede virtual chamada **ContosoNET** no grupo de afinidade chamado **ContosoAG**. Tem o espaço de endereço **10.10.0.0/16** e tem duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24,** que são a sub-rede dianteira e a sub-rede traseira, respectivamente. A sub-rede frontal é onde pode colocar aplicações de clientes como o Microsoft SharePoint. A sub-rede traseira é onde vai colocar os VMs do SQL Server. Se alterar as **variáveis $affinityGroupName** e **$virtualNetworkName** anteriormente, também deve alterar os nomes correspondentes abaixo.

    ```xml
    <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns />
        <VirtualNetworkSites>
          <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
            <AddressSpace>
              <AddressPrefix>10.10.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="Front">
                <AddressPrefix>10.10.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="Back">
                <AddressPrefix>10.10.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
    ```xml

5. Create a storage account that's associated with the affinity group that you created, and set it as the current storage account in your subscription.

    ```powershell
    New-AzureStorageAccount `
        -StorageAccountName $storageAccountName `
        -Label $storageAccountLabel `
        -AffinityGroup $affinityGroupName
    Set-AzureSubscription `
        -SubscriptionName (Get-AzureSubscription).SubscriptionName `
        -CurrentStorageAccount $storageAccountName
    ```

6. Crie o servidor do controlador de domínio no novo serviço de nuvem e conjunto de disponibilidade.

    ```powershell
    New-AzureVMConfig `
        -Name $dcServerName `
        -InstanceSize Medium `
        -ImageName $winImageName `
        -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
        -DiskLabel "OS" |
        Add-AzureProvisioningConfig `
            -Windows `
            -DisableAutomaticUpdates `
            -AdminUserName $vmAdminUser `
            -Password $vmAdminPassword |
            New-AzureVM `
                -ServiceName $dcServiceName `
                –AffinityGroup $affinityGroupName `
                -VNetName $virtualNetworkName
    ```

    Estes comandos canalizados fazem as seguintes coisas:

   * **New-AzureVMConfig** cria uma configuração VM.
   * **Add-AzureProvisioningConfig** fornece os parâmetros de configuração de um servidor Windows autónomo.
   * **O Add-AzureDataDisk** adiciona o disco de dados que utilizará para armazenar dados do Ative Directory, com a opção de cache definida para Nenhum.
   * **O New-AzureVM** cria um novo serviço de nuvem e cria o novo Azure VM no novo serviço de nuvem.

7. Aguarde que o novo VM seja totalmente a provisionado e baixe o ficheiro de ambiente de trabalho remoto para o seu diretório de trabalho. Como o novo Azure VM demora muito tempo a providenciar, o `while` loop continua a sondar o novo VM até estar pronto para ser usado.

    ```powershell
    $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

    While ($VMStatus.InstanceStatus -ne "ReadyRole")
    {
        write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
        Start-Sleep -Seconds 15
        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
    }

    Get-AzureRemoteDesktopFile `
        -ServiceName $dcServiceName `
        -Name $dcServerName `
        -LocalPath "$workingDir$dcServerName.rdp"
    ```

O servidor do controlador de domínio está agora a ser a provisionado com sucesso. Em seguida, irá configurar o domínio ative directory neste servidor de controlador de domínio. Deixe a janela PowerShell aberta no computador local. Voltará a usá-lo mais tarde para criar os dois VMs do SQL Server.

## <a name="configure-the-domain-controller"></a>Configure o controlador de domínio
1. Ligue-se ao servidor do controlador de domínio lançando o ficheiro de ambiente de trabalho remoto. Utilize o nome de utilizador do administrador da máquina AzureAdmin e palavra-passe **Contoso!000,** que especificou quando criou o novo VM.
2. Abra uma janela PowerShell no modo de administrador.
3. Executar o seguinte **comandoDCPROMO.EXE** para configurar o domínio **corp.contoso.com,** com os diretórios de dados na unidade M.

    ```powershell
    dcpromo.exe `
        /unattend `
        /ReplicaOrNewDomain:Domain `
        /NewDomain:Forest `
        /NewDomainDNSName:corp.contoso.com `
        /ForestLevel:4 `
        /DomainNetbiosName:CORP `
        /DomainLevel:4 `
        /InstallDNS:Yes `
        /ConfirmGc:Yes `
        /CreateDNSDelegation:No `
        /DatabasePath:"C:\Windows\NTDS" `
        /LogPath:"C:\Windows\NTDS" `
        /SYSVOLPath:"C:\Windows\SYSVOL" `
        /SafeModeAdminPassword:"Contoso!000"
    ```

    Após o fim do comando, o VM reinicia automaticamente.

4. Ligue-se novamente ao servidor do controlador de domínio, lançando o ficheiro de ambiente de trabalho remoto. Desta vez, inscreva-se como **CORP\Administrator**.
5. Abra uma janela PowerShell no modo de administrador e importe o módulo PowerShell do Ative Directory utilizando o seguinte comando:

    ```powershell
    Import-Module ActiveDirectory
    ```

6. Executar os seguintes comandos para adicionar três utilizadores ao domínio.

    ```powershell
    $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
    New-ADUser `
        -Name 'Install' `
        -AccountPassword  $pwd `
        -PasswordNeverExpires $true `
        -ChangePasswordAtLogon $false `
        -Enabled $true
    New-ADUser `
        -Name 'SQLSvc1' `
        -AccountPassword  $pwd `
        -PasswordNeverExpires $true `
        -ChangePasswordAtLogon $false `
        -Enabled $true
    New-ADUser `
        -Name 'SQLSvc2' `
        -AccountPassword  $pwd `
        -PasswordNeverExpires $true `
        -ChangePasswordAtLogon $false `
        -Enabled $true
    ```

    **A INSTALAÇÃO CORP\É** usada para configurar tudo relacionado com as instâncias de serviço do SQL Server, o cluster failover e o grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são usados como contas de serviço SQL Server para os dois VMs do SqL Server.
7. Em seguida, executar os seguintes comandos para dar **CORP\Instale** as permissões para criar objetos de computador no domínio.

    ```powershell
    Cd ad:
    $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
    $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
    $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
    $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
    $acl = Get-Acl $corp
    $acl.AddAccessRule($ace1)
    Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl
    ```

    O GUID acima especificado é o GUID para o tipo de objeto de computador. A conta **CORP\Install** precisa da permissão **de Ler Todas as Propriedades** e Criar **Objetos de Computador** para criar os objetos Ative Direct para o cluster de failover. A permissão **Read All Properties** já foi dada à CORP\Install por padrão, pelo que não precisa de a conceder explicitamente. Para obter mais informações sobre permissões necessárias para criar o cluster failover, consulte o [Guia passo a passo do Cluster Failover: Configurar contas no Diretório Ativo](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que terminou de configurar o Ative Directory e os objetos do utilizador, criará dois SQL Server VMs e juntar-se-á a este domínio.

## <a name="create-the-sql-server-vms"></a>Criar os VMs do Servidor SQL
1. Continue a utilizar a janela PowerShell que está aberta no seu computador local. Definir as seguintes variáveis adicionais:

    ```powershell
    $domainName= "corp"
    $FQDN = "corp.contoso.com"
    $subnetName = "Back"
    $sqlServiceName = "<uniqueservicename>"
    $quorumServerName = "ContosoQuorum"
    $sql1ServerName = "ContosoSQL1"
    $sql2ServerName = "ContosoSQL2"
    $availabilitySetName = "SQLHADR"
    $dataDiskSize = 100
    $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"
    ```

    O endereço IP **10.10.0.4** é normalmente atribuído ao primeiro VM que cria na sub-rede **10.10.0.0/16** da sua rede virtual Azure. Deverá verificar se este é o endereço do seu servidor de controlador de domínio executando **IPCONFIG**.
2. Executar os seguintes comandos canalizados para criar o primeiro VM no cluster failover, chamado **ContosoQuorum**:

    ```powershell
    New-AzureVMConfig `
        -Name $quorumServerName `
        -InstanceSize Medium `
        -ImageName $winImageName `
        -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
        -AvailabilitySetName $availabilitySetName `
        -DiskLabel "OS" |
        Add-AzureProvisioningConfig `
            -WindowsDomain `
            -AdminUserName $vmAdminUser `
            -Password $vmAdminPassword `
            -DisableAutomaticUpdates `
            -Domain $domainName `
            -JoinDomain $FQDN `
            -DomainUserName $vmAdminUser `
            -DomainPassword $vmAdminPassword |
            Set-AzureSubnet `
                -SubnetNames $subnetName |
                New-AzureVM `
                    -ServiceName $sqlServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName `
                    -DnsSettings $dnsSettings
    ```

    Note o seguinte relativamente ao comando acima referido:

   * **New-AzureVMConfig** cria uma configuração VM com o nome de conjunto de disponibilidade pretendido. Os VMs subsequentes serão criados com o mesmo nome definido de disponibilidade para que se juntem ao mesmo conjunto de disponibilidade.
   * **Add-AzureProvisioningConfig** une o VM ao domínio do Diretório Ativo que criou.
   * **Set-AzureSubnet** coloca o VM na sub-rede traseira.
   * **O New-AzureVM** cria um novo serviço de nuvem e cria o novo Azure VM no novo serviço de nuvem. O parâmetro **DnsSettings** especifica que o servidor DNS para os servidores do novo serviço de nuvem tem o endereço IP **10.10.0.4**. Este é o endereço IP do servidor do controlador de domínio. Este parâmetro é necessário para permitir que os novos VMs no serviço de nuvem se juntem ao domínio ative directory com sucesso. Sem este parâmetro, deve definir manualmente as definições de IPv4 no seu VM para utilizar o servidor do controlador de domínio como o servidor dNS primário após o fornecimento do VM e, em seguida, juntar o VM ao domínio ative Directory.
3. Executar os seguintes comandos canalizados para criar os VMs do SQL Server, denominados **ContosoSQL1** e **ContosoSQL2**.

    ```powershell
    # Create ContosoSQL1...
    New-AzureVMConfig `
        -Name $sql1ServerName `
        -InstanceSize Large `
        -ImageName $sqlImageName `
        -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
        -AvailabilitySetName $availabilitySetName `
        -HostCaching "ReadOnly" `
        -DiskLabel "OS" |
        Add-AzureProvisioningConfig `
            -WindowsDomain `
            -AdminUserName $vmAdminUser `
            -Password $vmAdminPassword `
            -DisableAutomaticUpdates `
            -Domain $domainName `
            -JoinDomain $FQDN `
            -DomainUserName $vmAdminUser `
            -DomainPassword $vmAdminPassword |
            Set-AzureSubnet `
                -SubnetNames $subnetName |
                Add-AzureEndpoint `
                    -Name "SQL" `
                    -Protocol "tcp" `
                    -PublicPort 1 `
                    -LocalPort 1433 |
                    New-AzureVM `
                        -ServiceName $sqlServiceName

    # Create ContosoSQL2...
    New-AzureVMConfig `
        -Name $sql2ServerName `
        -InstanceSize Large `
        -ImageName $sqlImageName `
        -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
        -AvailabilitySetName $availabilitySetName `
        -HostCaching "ReadOnly" `
        -DiskLabel "OS" |
        Add-AzureProvisioningConfig `
            -WindowsDomain `
            -AdminUserName $vmAdminUser `
            -Password $vmAdminPassword `
            -DisableAutomaticUpdates `
            -Domain $domainName `
            -JoinDomain $FQDN `
            -DomainUserName $vmAdminUser `
            -DomainPassword $vmAdminPassword |
            Set-AzureSubnet `
                -SubnetNames $subnetName |
                Add-AzureEndpoint `
                    -Name "SQL" `
                    -Protocol "tcp" `
                    -PublicPort 2 `
                    -LocalPort 1433 |
                    New-AzureVM `
                        -ServiceName $sqlServiceName
    ```

    Note o seguinte relativamente aos comandos acima referidos:

   * **New-AzureVMConfig** usa o mesmo nome de conjunto de disponibilidade que o servidor do controlador de domínio, e utiliza a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na galeria de máquinas virtuais. Também define o disco do sistema operativo apenas para ler-cacheing (sem caching de escrita). Recomendamos que emigre os ficheiros da base de dados para um disco de dados separado que anexe ao VM e o configuure sem leitura ou gravação. No entanto, a próxima coisa melhor é remover o cache de escrita no disco do sistema operativo porque não é possível remover a leitura do disco do sistema operativo.
   * **Add-AzureProvisioningConfig** une o VM ao domínio do Diretório Ativo que criou.
   * **Set-AzureSubnet** coloca o VM na sub-rede traseira.
   * **O Add-AzureEndpoint** adiciona pontos finais de acesso para que as aplicações do cliente possam aceder a estas instâncias de serviços do SQL Server na Internet. Portas diferentes são dadas a ContosoSQL1 e ContosoSQL2.
   * **A New-AzureVM** cria o novo SQL Server VM no mesmo serviço de cloud que o ContosoQuorum. Deve colocar os VMs no mesmo serviço de nuvem se quiser que estejam no mesmo conjunto de disponibilidade.
4. Aguarde que cada VM seja totalmente a provisionado e que cada VM descarregue o seu ficheiro de secretária remoto para o seu diretório de trabalho. O `for` loop passa pelos três novos VMs e executa os comandos dentro dos suportes encaracolados de nível superior para cada um deles.

    ```powershell
    Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
    {
        write-host "Waiting for " $VM.Name "..."

        # Loop until the VM status is "ReadyRole"
        While ($VM.InstanceStatus -ne "ReadyRole")
        {
            write-host "  Current Status = " $VM.InstanceStatus
            Start-Sleep -Seconds 15
            $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
        }

        write-host "  Current Status = " $VM.InstanceStatus

        # Download remote desktop file
        Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
    }
    ```

    Os VMs do SQL Server estão agora a provisionados e em execução, mas estão instalados com o SQL Server com opções predefinidas.

## <a name="initialize-the-failover-cluster-vms"></a>Inicialize os VMs de cluster de failover
Nesta secção, é necessário modificar os três servidores que utilizará no cluster de failover e na instalação do SQL Server. Mais concretamente:

* Todos os servidores: É necessário instalar a **função de Clustering Failover.**
* Todos os servidores: É necessário adicionar **CORP\Instale-se** como **administrador**da máquina .
* ContosoSQL1 e ContosoSQL2 apenas: É necessário adicionar **CORP\Install** como função **sysadmin** na base de dados predefinido.
* ContosoSQL1 e ContosoSQL2 apenas: É necessário adicionar **NT AUTHORITY\System** como um sinal com as seguintes permissões:

  * Alterar qualquer grupo de disponibilidade
  * Ligar SQL
  * Ver estado do servidor
* ContosoSQL1 e ContosoSQL2 apenas: O protocolo **TCP** já está ativado no SQL Server VM. No entanto, ainda precisa de abrir a firewall para acesso remoto ao SQL Server.

Agora, está pronto para começar. Começando por **ContosoQuorum,** siga os passos abaixo:

1. Ligue-se ao **ContosoQuorum** lançando os ficheiros de ambiente de trabalho remotos. Utilize o nome de utilizador do administrador da máquina **AzureAdmin** e palavra-passe **Contoso!000,** que especificou quando criou os VMs.
2. Verifique se os computadores foram unidos com sucesso a **corp.contoso.com**.
3. Aguarde que a instalação do SQL Server termine de executar as tarefas de inicialização automatizada antes de prosseguir.
4. Abra uma janela PowerShell no modo de administrador.
5. Instale a função de clustering de falha do Windows.

    ```powershell
    Import-Module ServerManager
    Add-WindowsFeature Failover-Clustering
    ```

6. Adicione **CORP\Instale-se** como administrador local.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    ```

7. Assine fora de ContosoQuorum. Já acabou com este servidor.

    ```powershell
    logoff.exe
    ```

Em seguida, inicialize **ContosoSQL1** e **ContosoSQL2**. Siga os passos abaixo, que são idênticos para ambos os VMs do SqL Server.

1. Ligue-se aos dois VMs do servidor SQL lançando os ficheiros de ambiente de trabalho remoto. Utilize o nome de utilizador do administrador da máquina **AzureAdmin** e palavra-passe **Contoso!000,** que especificou quando criou os VMs.
2. Verifique se os computadores foram unidos com sucesso a **corp.contoso.com**.
3. Aguarde que a instalação do SQL Server termine de executar as tarefas de inicialização automatizada antes de prosseguir.
4. Abra uma janela PowerShell no modo de administrador.
5. Instale a função de clustering de falha do Windows.

    ```powershell
    Import-Module ServerManager
    Add-WindowsFeature Failover-Clustering
    ```

6. Adicione **CORP\Instale-se** como administrador local.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    ```

7. Importe o Fornecedor de Energia do Servidor SQL.

    ```powershell
    Set-ExecutionPolicy -Execution RemoteSigned -Force
    Import-Module -Name "sqlps" -DisableNameChecking
    ```

8. Adicione **CORP\Instale** como função sysadmin para a instância padrão do Servidor SQL.

    ```powershell
    net localgroup administrators "CORP\Install" /Add
    Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
    ```

9. Adicione **NT AUTHORITY\System** como um sinal com as três permissões acima descritas.

    ```powershell
    Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
    ```

10. Abra a firewall para acesso remoto ao SQL Server.

    ```powershell
     netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
    ```

11. Assine fora de ambos os VMs.

    ```powershell
     logoff.exe
    ```

Finalmente, está pronto para configurar o grupo de disponibilidade. Utilizará o Fornecedor de Energia do Servidor SQL para realizar todo o trabalho no **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade
1. Ligue-se novamente ao **ContosoSQL1** lançando os ficheiros de ambiente de trabalho remoto. Em vez de iniciar sessão utilizando a conta da máquina, inscreva-se utilizando **a CORP\Install**.
2. Abra uma janela PowerShell no modo de administrador.
3. Definir as seguintes variáveis:

    ```powershell
    $server1 = "ContosoSQL1"
    $server2 = "ContosoSQL2"
    $serverQuorum = "ContosoQuorum"
    $acct1 = "CORP\SQLSvc1"
    $acct2 = "CORP\SQLSvc2"
    $password = "Contoso!000"
    $clusterName = "Cluster1"
    $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
    $db = "MyDB1"
    $backupShare = "\\$server1\backup"
    $quorumShare = "\\$server1\quorum"
    $ag = "AG1"
    ```

4. Importe o Fornecedor de Energia do Servidor SQL.

    ```powershell
    Set-ExecutionPolicy RemoteSigned -Force
    Import-Module "sqlps" -DisableNameChecking
    ```

5. Altere a conta de serviço SQL Server para ContosoSQL1 para CORP\SQLSvc1.

    ```powershell
    $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
    $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
    $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
    $svc1.Stop()
    $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
    $svc1.Start();
    $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
    ```

6. Altere a conta de serviço SQL Server para ContosoSQL2 para CORP\SQLSvc2.

    ```powershell
    $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
    $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
    $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
    $svc2.Stop()
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
    $svc2.Start();
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
    ```

7. Faça **o downloadCreateAzureFailoverCluster.ps1** do Create [Failover Cluster para Grupos De Disponibilidade Sempre Em Disponibilidade em VM](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Você usará este script para ajudá-lo a criar um cluster funcional de failover. Para obter informações importantes sobre como o Clustering de Falha do Windows interage com a rede Azure, consulte [alta disponibilidade e recuperação de desastres para o SQL Server em Máquinas Virtuais Azure](../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Mude para o seu diretório de trabalho e crie o cluster failover com o script descarregado.

    ```powershell
    Set-ExecutionPolicy Unrestricted -Force
    .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
    ```

9. Ativar sempre em grupos de disponibilidade para as instâncias padrão do SQL Server em **ContosoSQL1** e **ContosoSQL2**.

    ```powershell
    Enable-SqlAlwaysOn `
        -Path SQLSERVER:\SQL\$server1\Default `
        -Force
    Enable-SqlAlwaysOn `
        -Path SQLSERVER:\SQL\$server2\Default `
        -NoServiceRestart
    $svc2.Stop()
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
    $svc2.Start();
    $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
    ```

10. Crie um diretório de backup e conceda permissões para as contas de serviço sql Server. Você usará este diretório para preparar a base de dados de disponibilidade na réplica secundária.

    ```powershell
    $backup = "C:\backup"
    New-Item $backup -ItemType directory
    net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
    icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
    ```

11. Crie uma base de dados em **ContosoSQL1** chamada **MyDB1,** pegue uma cópia de segurança completa e uma cópia de segurança de registo, e restaure-as em **ContosoSQL2** com a opção **SEMRECOVERY.**

    ```powershell
    Invoke-SqlCmd -Query "CREATE database $db"
    Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
    Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
    Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
    Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
    ```

12. Crie os pontos finais do grupo de disponibilidade nos VMs do SERVIDOR SQL e desajuste as permissões adequadas nos pontos finais.

    ```powershell
    $endpoint =
      New-SqlHadrEndpoint MyMirroringEndpoint `
        -Port 5022 `
        -Path "SQLSERVER:\SQL\$server1\Default"
      Set-SqlHadrEndpoint `
        -InputObject $endpoint `
        -State "Started"
    $endpoint =
      New-SqlHadrEndpoint MyMirroringEndpoint `
        -Port 5022 `
        -Path "SQLSERVER:\SQL\$server2\Default"
    Set-SqlHadrEndpoint `
         -InputObject $endpoint `
         -State "Started"

    Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
    Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
    Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
    Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
    ```

13. Crie as réplicas de disponibilidade.

    ```powershell
    $primaryReplica =
       New-SqlAvailabilityReplica `
         -Name $server1 `
         -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
         -AvailabilityMode "SynchronousCommit" `
         -FailoverMode "Automatic" `
         -Version 11 `
         -AsTemplate
    $secondaryReplica =
       New-SqlAvailabilityReplica `
         -Name $server2 `
         -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
         -AvailabilityMode "SynchronousCommit" `
         -FailoverMode "Automatic" `
         -Version 11 `
         -AsTemplate
    ```

14. Finalmente, crie o grupo de disponibilidade e junte-se à réplica secundária ao grupo de disponibilidade.

    ```powershell
    New-SqlAvailabilityGroup `
        -Name $ag `
        -Path "SQLSERVER:\SQL\$server1\Default" `
        -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
        -Database $db
    Join-SqlAvailabilityGroup `
        -Path "SQLSERVER:\SQL\$server2\Default" `
        -Name $ag
    Add-SqlAvailabilityDatabase `
        -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
        -Database $db
    ```

## <a name="next-steps"></a>Próximos passos
Implementou com sucesso o SQL Server Always On criando um grupo de disponibilidade em Azure. Para configurar um ouvinte para este grupo de disponibilidade, consulte [configurar um ouvinte do ILB para sempre em grupos de disponibilidade em Azure](../classic/ps-sql-int-listener.md).

Para obter outras informações sobre a utilização do SQL Server em Azure, consulte [o SQL Server em máquinas virtuais Azure](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).
