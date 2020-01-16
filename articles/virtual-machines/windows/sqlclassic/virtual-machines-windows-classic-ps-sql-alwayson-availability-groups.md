---
title: Configurar o grupo de disponibilidade Always On em uma VM do Azure usando o PowerShell | Microsoft Docs
description: Este tutorial usa recursos que foram criados com o modelo de implantação clássico. Use o PowerShell para criar um grupo de disponibilidade Always On no Azure.
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
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978148"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurar o grupo de disponibilidade Always On em uma VM do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Clássico: interface do usuário](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere que agora você pode concluir essa tarefa no modelo do Azure Resource Manager. Recomendamos o modelo do Azure Resource Manager para novas implantações. Confira [SQL Server grupos de disponibilidade Always on em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Recomendamos que a maioria das implantações novas usem o modelo do Resource Manager. O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../../../azure-resource-manager/management/deployment-models.md). Este artigo cobre a utilização do modelo de implementação clássica.

As VMs (máquinas virtuais) do Azure podem ajudar os administradores de banco de dados a reduzir o custo de um sistema de SQL Server de alta disponibilidade. Este tutorial mostra como implementar um grupo de disponibilidade usando SQL Server Always On de ponta a ponta dentro de um ambiente do Azure. No final do tutorial, sua solução de Always On SQL Server no Azure consistirá nos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end.
* Um controlador de domínio com um domínio Active Directory.
* Duas VMs SQL Server implantadas na sub-rede de back-end e ingressadas no domínio Active Directory.
* Um cluster de failover do Windows de três nós com o modelo de quorum maioria dos nós.
* Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade.

Esse cenário é uma boa opção para sua simplicidade no Azure, não por sua economia de custo ou outros fatores. Por exemplo, você pode minimizar o número de VMs para que um grupo de disponibilidade de duas réplicas Economize em horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivos de quorum em um cluster de failover de dois nós. Esse método reduz a contagem de VM em uma da configuração acima.

Este tutorial destina-se a mostrar as etapas necessárias para configurar a solução descrita acima, sem elaborando sobre os detalhes de cada etapa. Portanto, em vez de fornecer as etapas de configuração da GUI, ele usa o script do PowerShell para levá-lo rapidamente a cada etapa. Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure com a assinatura da máquina virtual.
* Você instalou os [cmdlets Azure PowerShell](/powershell/azure/overview).
* Você já tem uma compreensão sólida de grupos de disponibilidade Always On para soluções locais. Para obter mais informações, consulte [grupos de disponibilidade Always on (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conectar-se à sua assinatura do Azure e criar a rede virtual
1. Em uma janela do PowerShell no computador local, importe o módulo do Azure, baixe o arquivo de configurações de publicação em seu computador e conecte sua sessão do PowerShell à sua assinatura do Azure importando as configurações de publicação baixadas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O comando **Get-AzurePublishSettingsFile** gera automaticamente um certificado de gerenciamento com o Azure e o baixa em seu computador. Um navegador é aberto automaticamente e você é solicitado a inserir as credenciais de conta Microsoft para sua assinatura do Azure. O arquivo **. publishsettings** baixado contém todas as informações de que você precisa para gerenciar sua assinatura do Azure. Depois de salvar esse arquivo em um diretório local, importe-o usando o comando **Import-AzurePublishSettingsFile** .

   > [!NOTE]
   > O arquivo. publishsettings contém suas credenciais (sem codificação) que são usadas para administrar suas assinaturas e serviços do Azure. A prática recomendada de segurança para esse arquivo é armazená-lo temporariamente fora dos diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, excluí-lo após a conclusão da importação. Um usuário mal-intencionado que obtém acesso ao arquivo. publishsettings pode editar, criar e excluir seus serviços do Azure.

2. Defina uma série de variáveis que você usará para criar sua infraestrutura de ti na nuvem.

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

    Preste atenção ao seguinte para garantir que seus comandos tenham sucesso mais tarde:

   * As variáveis **$storageAccountName** e **$dcServiceName** devem ser exclusivas porque são usadas para identificar sua conta de armazenamento de nuvem e o servidor de nuvem, respectivamente, na Internet.
   * Os nomes que você especificar para as variáveis **$affinityGroupName** e **$virtualNetworkName** são configurados no documento de configuração de rede virtual que você usará mais tarde.
   * **$sqlImageName** especifica o nome atualizado da imagem de VM que contém SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para simplificar, **contoso! 000** é a mesma senha usada em todo o tutorial.

3. Crie um grupo de afinidade.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Crie uma rede virtual importando um arquivo de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O arquivo de configuração contém o seguinte documento XML. Em suma, ele especifica uma rede virtual chamada **ContosoNET** no grupo de afinidade chamado **ContosoAG**. Ele tem o espaço de endereço **10.10.0.0/16** e tem duas sub-redes, **10.10.1.0/24** e **10.10.2.0/24**, que são a sub-rede frontal e a sub-rede traseira, respectivamente. A sub-rede frontal é onde você pode posicionar aplicativos cliente, como o Microsoft SharePoint. A sub-rede traseira é onde você colocará o SQL Server VMs. Se você alterar o **$affinityGroupName** e **$virtualNetworkName** variáveis anteriormente, também deverá alterar os nomes correspondentes abaixo.

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

5. Crie uma conta de armazenamento associada ao grupo de afinidade que você criou e defina-a como a conta de armazenamento atual em sua assinatura.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Crie o servidor do controlador de domínio no novo serviço de nuvem e conjunto de disponibilidade.

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

    Esses comandos canalizados fazem o seguinte:

   * **New-AzureVMConfig** cria uma configuração de VM.
   * **Add-AzureProvisioningConfig** fornece os parâmetros de configuração de um Windows Server autônomo.
   * **Add-AzureDataDisk** adiciona o disco de dados que você usará para armazenar Active Directory dados, com a opção de cache definida como None.
   * **New-AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço de nuvem.

7. Aguarde até que a nova VM seja totalmente provisionada e baixe o arquivo da área de trabalho remota em seu diretório de trabalho. Como a nova VM do Azure leva muito tempo para ser provisionada, o loop de `while` continua a sondar a nova VM até que ela esteja pronta para uso.

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

O servidor do controlador de domínio agora foi provisionado com êxito. Em seguida, você configurará o domínio Active Directory nesse servidor do controlador de domínio. Deixe a janela do PowerShell aberta no computador local. Você o usará novamente mais tarde para criar as duas VMs SQL Server.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
1. Conecte-se ao servidor do controlador de domínio iniciando o arquivo da área de trabalho remota. Use o nome de usuário AzureAdmin e a senha **contoso! 000**do administrador do computador que você especificou quando criou a nova VM.
2. Abra uma janela do PowerShell no modo de administrador.
3. Execute o Dcpromo a seguir **. EXE** para configurar o domínio **Corp.contoso.com** , com os diretórios de dados na unidade M.

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

    Após a conclusão do comando, a VM é reiniciada automaticamente.

4. Conecte-se ao servidor do controlador de domínio novamente iniciando o arquivo da área de trabalho remota. Desta vez, entre como **CORP\Administrator**.
5. Abra uma janela do PowerShell no modo de administrador e importe o Active Directory módulo do PowerShell usando o seguinte comando:

        Import-Module ActiveDirectory

6. Execute os comandos a seguir para adicionar três usuários ao domínio.

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

    **CORP\Install** é usado para configurar tudo relacionado às instâncias de serviço SQL Server, ao cluster de failover e ao grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são usados como as contas de serviço SQL Server para as duas VMs SQL Server.
7. Em seguida, execute os comandos a seguir para conceder ao **CORP\Install** as permissões para criar objetos de computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    O GUID especificado acima é o GUID para o tipo de objeto de computador. A conta **CORP\Install** precisa da permissão **ler todas as propriedades** e **criar objetos de computador** para criar os objetos do Active Direct para o cluster de failover. A permissão **ler todas as propriedades** já foi atribuída ao CORP\Install por padrão, portanto, você não precisa concedê-la explicitamente. Para obter mais informações sobre as permissões necessárias para criar o cluster de failover, consulte [guia passo a passo do cluster de failover: Configurando contas no Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que você concluiu a configuração de Active Directory e os objetos de usuário, criará duas VMs SQL Server e as unirá a esse domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs SQL Server
1. Continue a usar a janela do PowerShell que está aberta no computador local. Defina as seguintes variáveis adicionais:

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

    O endereço IP **10.10.0.4** normalmente é atribuído à primeira VM que você cria na sub-rede **10.10.0.0/16** da sua rede virtual do Azure. Você deve verificar se esse é o endereço do seu servidor do controlador de domínio executando **ipconfig**.
2. Execute os seguintes comandos canalizados para criar a primeira VM no cluster de failover, chamada **ContosoQuorum**:

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

    Observe o seguinte em relação ao comando acima:

   * **New-AzureVMConfig** cria uma configuração de VM com o nome do conjunto de disponibilidade desejado. As VMs subsequentes serão criadas com o mesmo nome do conjunto de disponibilidade para que elas sejam unidas ao mesmo conjunto de disponibilidade.
   * **Add-AzureProvisioningConfig** une a VM ao domínio Active Directory que você criou.
   * **Set-AzureSubnet** coloca a VM na sub-rede posterior.
   * **New-AzureVM** cria um novo serviço de nuvem e cria a nova VM do Azure no novo serviço de nuvem. O parâmetro **DnsSettings** especifica que o servidor DNS para os servidores no novo serviço de nuvem tem o endereço IP **10.10.0.4**. Esse é o endereço IP do servidor do controlador de domínio. Esse parâmetro é necessário para permitir que as novas VMs no serviço de nuvem ingressem no domínio de Active Directory com êxito. Sem esse parâmetro, você deve definir manualmente as configurações de IPv4 em sua VM para usar o servidor do controlador de domínio como o servidor DNS primário depois que a VM é provisionada e, em seguida, ingressar a VM no domínio Active Directory.
3. Execute os seguintes comandos canalizados para criar as VMs SQL Server, chamadas **ContosoSQL1** e **ContosoSQL2**.

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

    Observe o seguinte em relação aos comandos acima:

   * **New-AzureVMConfig** usa o mesmo nome do conjunto de disponibilidade que o servidor do controlador de domínio e usa a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na Galeria de máquinas virtuais. Ele também define o disco do sistema operacional para somente cache de leitura (sem cache de gravação). Recomendamos que você migre os arquivos de banco de dados para um disco de armazenamento separado que você anexa à VM e configure-o sem cache de leitura ou gravação. No entanto, a melhor opção é remover o cache de gravação no disco do sistema operacional, pois não é possível remover o cache de leitura no disco do sistema operacional.
   * **Add-AzureProvisioningConfig** une a VM ao domínio Active Directory que você criou.
   * **Set-AzureSubnet** coloca a VM na sub-rede posterior.
   * **Add-AzureEndpoint** adiciona pontos de extremidade de acesso para que os aplicativos cliente possam acessar essas instâncias de serviços de SQL Server na Internet. Portas diferentes são dadas a ContosoSQL1 e ContosoSQL2.
   * **New-AzureVM** cria a nova VM SQL Server no mesmo serviço de nuvem que ContosoQuorum. Você deve posicionar as VMs no mesmo serviço de nuvem se quiser que elas estejam no mesmo conjunto de disponibilidade.
4. Aguarde até que cada VM seja totalmente provisionada e para cada VM baixar seu arquivo de área de trabalho remota para seu diretório de trabalho. O loop de `for` percorre as três novas VMs e executa os comandos dentro das chaves de nível superior para cada uma delas.

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

    As VMs SQL Server agora estão provisionadas e em execução, mas são instaladas com SQL Server com opções padrão.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializar as VMs do cluster de failover
Nesta seção, você precisa modificar os três servidores que serão usados no cluster de failover e a instalação do SQL Server. Especificamente:

* Todos os servidores: você precisa instalar o recurso de **clustering de failover** .
* Todos os servidores: você precisa adicionar **CORP\Install** como o **administrador**do computador.
* Somente ContosoSQL1 e ContosoSQL2: você precisa adicionar **CORP\Install** como uma função **sysadmin** no banco de dados padrão.
* Somente ContosoSQL1 e ContosoSQL2: você precisa adicionar **NT AUTHORITY\SYSTEM** como uma entrada com as seguintes permissões:

  * Alterar qualquer grupo de disponibilidade
  * Conectar SQL
  * Exibir estado do servidor
* Somente ContosoSQL1 e ContosoSQL2: o protocolo **TCP** já está habilitado na VM SQL Server. No entanto, você ainda precisa abrir o firewall para acesso remoto de SQL Server.

Agora, você está pronto para começar. A partir do **ContosoQuorum**, siga as etapas abaixo:

1. Conecte-se ao **ContosoQuorum** iniciando os arquivos da área de trabalho remota. Use o nome de usuário **AzureAdmin** e a senha **contoso! 000**do administrador do computador que você especificou quando criou as VMs.
2. Verifique se os computadores ingressaram com êxito no **Corp.contoso.com**.
3. Aguarde até que a instalação do SQL Server conclua a execução das tarefas de inicialização automatizada antes de continuar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale o recurso Windows failover clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Saia do ContosoQuorum. Você concluiu este servidor agora.

        logoff.exe

Em seguida, inicialize **ContosoSQL1** e **ContosoSQL2**. Siga as etapas abaixo, que são idênticas para as duas VMs SQL Server.

1. Conecte-se às duas VMs SQL Server iniciando os arquivos da área de trabalho remota. Use o nome de usuário **AzureAdmin** e a senha **contoso! 000**do administrador do computador que você especificou quando criou as VMs.
2. Verifique se os computadores ingressaram com êxito no **Corp.contoso.com**.
3. Aguarde até que a instalação do SQL Server conclua a execução das tarefas de inicialização automatizada antes de continuar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale o recurso Windows failover clustering.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Importe o provedor de SQL Server PowerShell.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adicione **CORP\Install** como a função sysadmin para a instância de SQL Server padrão.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adicione **NT AUTHORITY\SYSTEM** como uma entrada com as três permissões descritas acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Abra o firewall para acesso remoto de SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Saia de ambas as VMs.

         logoff.exe

Por fim, você está pronto para configurar o grupo de disponibilidade. Você usará o provedor de SQL Server PowerShell para executar todo o trabalho em **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade
1. Conecte-se ao **ContosoSQL1** novamente iniciando os arquivos da área de trabalho remota. Em vez de entrar usando a conta da máquina, entre usando **CORP\Install**.
2. Abra uma janela do PowerShell no modo de administrador.
3. Defina as seguintes variáveis:

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
4. Importe o provedor de SQL Server PowerShell.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Altere a conta de serviço SQL Server para ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Altere a conta de serviço SQL Server para ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Baixe **CreateAzureFailoverCluster. ps1** de [Create Failover cluster for Always on grupos de disponibilidade na VM do Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Você usará esse script para ajudá-lo a criar um cluster de failover funcional. Para obter informações importantes sobre como o clustering de failover do Windows interage com a rede do Azure, consulte [alta disponibilidade e recuperação de desastres para SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Altere para seu diretório de trabalho e crie o cluster de failover com o script baixado.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Habilite grupos de disponibilidade Always On para as instâncias de SQL Server padrão em **ContosoSQL1** e **ContosoSQL2**.

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
10. Crie um diretório de backup e conceda permissões para as contas de serviço SQL Server. Você usará esse diretório para preparar o banco de dados de disponibilidade na réplica secundária.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Crie um banco de dados em **ContosoSQL1** chamado **MyDB1**, faça um backup completo e um backup de log e restaure-os no **CONTOSOSQL2** com a opção **WITH NORECOVERY** .

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Crie os pontos de extremidade do grupo de disponibilidade nas VMs SQL Server e defina as permissões apropriadas nos pontos de extremidade.

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
13. Crie as réplicas de disponibilidade.

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
14. Por fim, crie o grupo de disponibilidade e ingresse a réplica secundária no grupo de disponibilidade.

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

## <a name="next-steps"></a>Passos seguintes
Agora você implementou com êxito SQL Server Always On criando um grupo de disponibilidade no Azure. Para configurar um ouvinte para esse grupo de disponibilidade, consulte [configurar um ouvinte ILB para grupos de disponibilidade Always on no Azure](../classic/ps-sql-int-listener.md).

Para obter outras informações sobre como usar SQL Server no Azure, consulte [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
