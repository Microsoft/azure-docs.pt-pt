---
title: Configurar o grupo de disponibilidade Always On na VM do Azure com o PowerShell | Documentos da Microsoft
description: Este tutorial utiliza recursos que foram criados com o modelo de implementação clássica. Utilizar o PowerShell para criar um grupo de disponibilidade Always On no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a6d8326afa3bcf13234ab072a2cd2909a864738b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002860"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configurar o grupo de disponibilidade Always On na VM do Azure com o PowerShell
> [!div class="op_single_selector"]
> * [Clássico: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere que agora pode concluir esta tarefa no modelo de Gestor de recursos do Azure. Recomendamos que o modelo de Gestor de recursos do Azure para novas implementações. Ver [SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Recomendamos que as implementações mais novas utilizem o modelo de Gestor de recursos. O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo cobre a utilização do modelo de implementação clássica.

Máquinas virtuais do Azure (VMs) pode ajudar os administradores de banco de dados para reduzir o custo de um sistema do SQL Server de elevada disponibilidade. Este tutorial mostra-lhe como implementar um grupo de disponibilidade com o SQL Server Always On-a-ponto dentro de um ambiente do Azure. No final do tutorial, sua solução do SQL Server Always On no Azure consiste dos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo um front-end e uma sub-rede de back-end.
* Um controlador de domínio com um domínio do Active Directory.
* Duas VMs do SQL Server que são implementadas na sub-rede de back-end e associado ao domínio do Active Directory.
* Cluster de ativação pós-falha de Windows três nós com o modelo de quórum maioria de nós.
* Um grupo de disponibilidade com duas réplicas com consolidação síncrona de uma base de dados de disponibilidade.

Este cenário é uma boa opção para sua simplicidade no Azure, não para a respetiva relação custo-eficácia ou outros fatores. Por exemplo, pode minimizar o número de VMs para um grupo de disponibilidade da réplica de dois a economizar em horas de computação no Azure, utilizando o controlador de domínio como a testemunha de compartilhamento de arquivo de quórum no cluster de ativação pós-falha de dois nós. Esse método reduz o número de VMS por um da configuração acima.

Este tutorial destina-se para mostrar a os passos necessários para configurar a solução descrita acima, sem elaborando nos detalhes de cada passo. Por conseguinte, em vez de fornecer os passos de configuração de GUI, ele usa scripts para levá-lo rapidamente em cada etapa do PowerShell. Este tutorial pressupõe que o seguinte:

* Já tem uma conta do Azure com a subscrição de máquina virtual.
* Instalar o [cmdlets do Azure PowerShell](/powershell/azure/overview).
* Já tem uma compreensão sólida do sempre em grupos de disponibilidade para soluções no local. Para obter mais informações, consulte [Always On grupos de disponibilidade (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Ligar à sua subscrição do Azure e criar a rede virtual
1. Numa janela do PowerShell no seu computador local, importe o módulo do Azure, transferir o ficheiro de definições de publicação para o computador e ligar a sua sessão do PowerShell para a sua subscrição do Azure ao importar as definições de publicação transferidas.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    O **Get-AzurePublishSettingsFile** comando automaticamente gera um certificado de gestão com o Azure e transfere-o para o seu computador. Um navegador é aberto automaticamente e lhe for pedido para introduzir as credenciais da conta Microsoft para a sua subscrição do Azure. O transferido **. publishsettings** arquivo contém todas as informações de que precisa de gerir a sua subscrição do Azure. Depois de guardar este ficheiro para um diretório local, importá-lo utilizando o **Import-AzurePublishSettingsFile** comando.

   > [!NOTE]
   > O ficheiro. publishsettings contém as suas credenciais (não codificadas) que são utilizadas para administrar as subscrições do Azure e serviços. A prática recomendada de segurança para este ficheiro é armazená-lo temporariamente fora os diretórios de origem (por exemplo, na pasta Libraries\Documents) e, em seguida, elimine-o após a importação foi concluída. Um utilizador mal intencionado obtiver acesso ao ficheiro. publishsettings pode editar, criar e eliminar os seus serviços do Azure.

2. Defina uma série de variáveis que irá utilizar para criar a sua infraestrutura de TI de cloud.

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

    Preste atenção ao seguinte para se certificar de que os seus comandos terá êxito mais tarde:

   * Variáveis **$storageAccountName** e **$dcServiceName** deve ser exclusivo, porque eles são usados para identificar o seu servidor na nuvem armazenamento conta e na cloud, respectivamente, na Internet.
   * Os nomes que especificar para variáveis **$affinityGroupName** e **$virtualNetworkName** estão configurados no documento de configuração de rede virtual que irá utilizar mais tarde.
   * **$sqlImageName** Especifica o nome atualizado do que a imagem de VM que contém o SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para simplificar, **Contoso! 000** é a mesma palavra-passe é usado em todo o tutorial completo.

3. Crie um grupo de afinidades.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Crie uma rede virtual através da importação de um ficheiro de configuração.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    O ficheiro de configuração contém o seguinte documento XML. Em resumo, especifica uma rede virtual denominada **ContosoNET** no grupo de afinidade denominado **ContosoAG**. Ele tem o espaço de endereços **10.10.0.0/16** e tem duas sub-redes **10.10.1.0/24** e **10.10.2.0/24**, que são a sub-rede de front- e a sub-rede de back-, respectivamente. A sub-rede de front-é onde pode colocar aplicações de cliente como o Microsoft SharePoint. A sub-rede de back-é onde vou colocar as VMs do SQL Server. Se alterar o **$affinityGroupName** e **$virtualNetworkName** variáveis anteriormente, também tem de alterar os nomes correspondentes abaixo.

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

5. Crie uma conta de armazenamento associada ao grupo de afinidade que criou e defina-o como a conta de armazenamento atual na sua subscrição.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Crie o servidor de controlador de domínio no novo conjunto de disponibilidade e o serviço de nuvem.

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

    Estes comandos pipe efetue os seguintes procedimentos:

   * **Novo AzureVMConfig** cria uma configuração de VM.
   * **Adicionar-AzureProvisioningConfig** fornece os parâmetros de configuração de um servidor do Windows autônoma.
   * **Adicionar-AzureDataDisk** adiciona o disco de dados que pretende utilizar para armazenar dados do Active Directory, com a opção de colocação em cache definida como None.
   * **Novo-AzureVM** cria um novo serviço cloud e cria a nova VM do Azure no novo serviço cloud.

7. Aguarde que a nova VM a ser totalmente aprovisionado e transfira o ficheiro de ambiente de trabalho remoto para o seu diretório de trabalho. Uma vez que a nova VM do Azure demora muito tempo a aprovisionar, o `while` ciclo continua consultar a nova VM, até que ele esteja pronto para ser utilizado.

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

O servidor de controlador de domínio agora é aprovisionado com êxito. Em seguida, irá configurar o domínio do Active Directory neste servidor de controlador de domínio. Deixe a janela do PowerShell aberta no seu computador local. Usará-lo novamente mais tarde para criar as duas VMs do SQL Server.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
1. Ligar ao servidor de controlador de domínio, iniciando o ficheiro de ambiente de trabalho remoto. Utilize o administrador de máquinas AzureAdmin de nome de utilizador e palavra-passe **Contoso! 000**, que especificou quando criou a nova VM.
2. Abra uma janela do PowerShell no modo de administrador.
3. Execute o seguinte **DCPROMO. EXE** comando para configurar o **corp.contoso.com** domínio, com os diretórios de dados na unidade M.

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

    Quando o comando estiver concluído, a VM é reiniciada automaticamente.

4. Ligar ao servidor de controlador de domínio novamente, iniciando o ficheiro de ambiente de trabalho remoto. Desta vez, inicie sessão como **CORP\Administrator**.
5. Abra uma janela do PowerShell no modo de administrador e importe o módulo do PowerShell do Active Directory, utilizando o seguinte comando:

        Import-Module ActiveDirectory

6. Execute os seguintes comandos para adicionar três utilizadores ao domínio.

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

    **CORP\Install** é utilizado para configurar todas as informações relacionadas a instâncias de serviço do SQL Server, o cluster de ativação pós-falha e o grupo de disponibilidade. **CORP\SQLSvc1** e **CORP\SQLSvc2** são utilizados como as contas de serviço do SQL Server para as duas VMs do SQL Server.
7. Em seguida, execute os seguintes comandos para dar **CORP\Install** as permissões para criar objetos de computador no domínio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    O GUID especificado acima é o GUID para o tipo de objeto de computador. O **CORP\Install** necessidades de conta a **ler todas as propriedades** e **criar objetos de computador** permissão para criar os objetos Active Directory direto para o cluster de ativação pós-falha. O **ler todas as propriedades** permissão já é concedida a CORP\Install por predefinição, pelo que não precisa conceder explicitamente. Para obter mais informações sobre as permissões que são necessários para criar o cluster de ativação pós-falha, consulte [guia de passo a passo do Cluster de ativação pós-falha: Configurar contas no Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Agora que tiver terminado de configurar o Active Directory e os objetos de utilizador, irá criar duas VMs do SQL Server e associá-las a este domínio.

## <a name="create-the-sql-server-vms"></a>Criar as VMs do SQL Server
1. Continue a utilizar a janela do PowerShell que está aberta no seu computador local. Defina as seguintes variáveis adicionais:

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

    O endereço IP **10.10.0.4** normalmente é atribuído à primeira VM que criar o **10.10.0.0/16** sub-rede da rede virtual do Azure. Deve verificar que este é o endereço do seu servidor de controlador de domínio, executando **IPCONFIG**.
2. Execute os seguintes comandos para criar a primeira VM no cluster de ativação pós-falha, com o nome de enviado por pipe **ContosoQuorum**:

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

    Tenha em atenção o seguinte sobre o comando acima:

   * **Novo AzureVMConfig** cria uma configuração de VM com o nome do conjunto de disponibilidade pretendidos. As VMs subsequentes serão criadas com o mesmo nome de conjunto de disponibilidade, de modo a que são associadas ao mesmo conjunto de disponibilidade.
   * **Adicionar-AzureProvisioningConfig** associa a VM ao domínio do Active Directory que criou.
   * **Conjunto AzureSubnet** coloca a VM na sub-rede de back.
   * **Novo-AzureVM** cria um novo serviço cloud e cria a nova VM do Azure no novo serviço cloud. O **DnsSettings** parâmetro especifica o servidor DNS para os servidores no novo serviço em nuvem tem o endereço IP **10.10.0.4**. Este é o endereço IP do servidor de controlador de domínio. Este parâmetro é necessário para ativar as novas VMs no serviço cloud associar ao domínio do Active Directory com êxito. Sem este parâmetro, tem de definir manualmente as definições de IPv4 na sua VM para utilizar o servidor de controlador de domínio como o servidor DNS principal depois da VM está aprovisionada e, em seguida, associar a VM ao domínio do Active Directory.
3. Execute os seguintes comandos para criar as VMs do SQL Server, com o nome de enviado por pipe **ContosoSQL1** e **ContosoSQL2**.

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

    Tenha em atenção o seguinte sobre os comandos acima:

   * **Novo AzureVMConfig** utiliza o mesmo nome de conjunto de disponibilidade do servidor de controlador de domínio e utiliza a imagem do SQL Server 2012 Service Pack 1 Enterprise Edition na galeria da máquina virtual. Também define o disco do sistema operativo para leitura-colocação em cache apenas (não escrita colocação em cache). Recomendamos que migre os ficheiros de base de dados para um disco de dados separado que anexar à VM e configurá-lo a sem leitura ou gravação em cache. No entanto, a segunda melhor coisa é remover a gravação em cache no disco do sistema operativo porque não é possível remover a cache de leitura no disco do sistema operativo.
   * **Adicionar-AzureProvisioningConfig** associa a VM ao domínio do Active Directory que criou.
   * **Conjunto AzureSubnet** coloca a VM na sub-rede de back.
   * **Adicionar-AzureEndpoint** Adiciona pontos finais de acesso para que as aplicações de cliente podem aceder a estas instâncias de serviços do SQL Server na Internet. Portas diferentes são fornecidas para ContosoSQL1 e ContosoSQL2.
   * **Novo-AzureVM** cria a nova VM do SQL Server no mesmo serviço cloud como ContosoQuorum. Tem de colocar as VMs no mesmo serviço cloud, se deseja que eles estejam no mesmo conjunto de disponibilidade.
4. Aguarde para cada VM ser totalmente aprovisionados e para cada VM transferir o seu ficheiro de ambiente de trabalho remoto para o diretório de trabalho. O `for` loop percorre as três VMs novas e executa os comandos dentro de chavetas de nível superior para cada uma delas.

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

    As VMs do SQL Server agora são aprovisionadas e em execução, mas eles estiverem instalado com o SQL Server com opções padrão.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializar as VMs do cluster de ativação pós-falha
Nesta secção, terá de modificar os três servidores que pretende utilizar no cluster de ativação pós-falha e a instalação do SQL Server. Especificamente:

* Todos os servidores: Tem de instalar o **Clustering de ativação pós-falha** funcionalidade.
* Todos os servidores: Precisa adicionar **CORP\Install** que a máquina **administrador**.
* ContosoSQL1 e ContosoSQL2 apenas: Precisa adicionar **CORP\Install** como um **sysadmin** função no banco de dados padrão.
* ContosoSQL1 e ContosoSQL2 apenas: Precisa adicionar **NT AUTHORITY\System** como um início de sessão com as seguintes permissões:

  * Alterar qualquer grupo de disponibilidade
  * Connect SQL
  * Exibir estado do servidor
* ContosoSQL1 e ContosoSQL2 apenas: O **TCP** protocolo já está ativado na VM do SQL Server. No entanto, ainda tem de abrir a firewall para acesso remoto do SQL Server.

Agora, está pronto para começar. A partir **ContosoQuorum**, siga os passos abaixo:

1. Ligar à **ContosoQuorum** , iniciando os arquivos do desktop remotos. Utilize o nome de utilizador do administrador de máquina **AzureAdmin** e a palavra-passe **Contoso! 000**, que especificou quando criou as VMs.
2. Certifique-se de que os computadores foram associados com êxito ao **corp.contoso.com**.
3. Aguarde pela instalação do SQL Server concluir as tarefas de inicialização automática antes de continuar a executar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale a funcionalidade de Clustering de ativação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Terminar sessão ContosoQuorum. Já está com este servidor agora.

        logoff.exe

Em seguida, inicialize **ContosoSQL1** e **ContosoSQL2**. Siga os passos abaixo, que são idêntico para ambas as VMs do SQL Server.

1. Ligue-se para as duas VMs do SQL Server, iniciando os arquivos do desktop remotos. Utilize o nome de utilizador do administrador de máquina **AzureAdmin** e a palavra-passe **Contoso! 000**, que especificou quando criou as VMs.
2. Certifique-se de que os computadores foram associados com êxito ao **corp.contoso.com**.
3. Aguarde pela instalação do SQL Server concluir as tarefas de inicialização automática antes de continuar a executar.
4. Abra uma janela do PowerShell no modo de administrador.
5. Instale a funcionalidade de Clustering de ativação pós-falha do Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adicione **CORP\Install** como um administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Importe o fornecedor de PowerShell do SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adicione **CORP\Install** que a função de administrador do sistema para a instância do SQL Server predefinida.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adicione **NT AUTHORITY\System** como um início de sessão com as permissões de três descrito acima.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Abra a firewall para acesso remoto do SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Terminar sessão em ambas as VMs.

         logoff.exe

Por fim, está pronto para configurar o grupo de disponibilidade. Usará o provedor de PowerShell do SQL Server para realizar todo o trabalho em **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configurar o grupo de disponibilidade
1. Ligar à **ContosoSQL1** novamente, iniciando os arquivos do desktop remotos. Em vez de iniciar sessão com a conta de computador, inicie sessão utilizando **CORP\Install**.
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
4. Importe o fornecedor de PowerShell do SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Altere a conta de serviço do SQL Server para ContosoSQL1 para CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Altere a conta de serviço do SQL Server para ContosoSQL2 para CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Baixe **CreateAzureFailoverCluster.ps1** partir [criar o Cluster de ativação pós-falha para grupos de Disponibilidade AlwaysOn na VM do Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) para o diretório de trabalho local. Irá utilizar este script para ajudar a criar um cluster de ativação pós-falha funcional. Para obter informações importantes sobre como o Clustering de ativação pós-falha do Windows interage com a rede do Azure, veja [elevada disponibilidade e recuperação após desastre para o SQL Server em máquinas de virtuais do Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Altere o diretório de trabalho e criar o cluster de ativação pós-falha com o script transferido.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Ativar Always On grupos de disponibilidade para as instâncias do SQL Server padrão em **ContosoSQL1** e **ContosoSQL2**.

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
10. Criar um diretório de cópia de segurança e conceder permissões para as contas de serviço do SQL Server. Irá utilizar este diretório para preparar a base de dados de disponibilidade na réplica secundária.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Criar uma base de dados **ContosoSQL1** chamado **MyDB1**, faça uma cópia de segurança completa e uma cópia de segurança do registo e restaurá-las no **ContosoSQL2** com o **WITH NORECOVERY**  opção.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Criar a disponibilidade de pontos finais de grupo em VMs do SQL Server e configurar as permissões adequadas nos pontos finais.

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
14. Por fim, crie o grupo de disponibilidade e Junte-se a réplica secundária ao grupo de disponibilidade.

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

## <a name="next-steps"></a>Passos Seguintes
Agora com êxito implementou Always On do SQL Server através da criação de um grupo de disponibilidade no Azure. Para configurar um serviço de escuta para este grupo de disponibilidade, consulte [configurar um serviço de escuta ILB para grupos de disponibilidade Always On no Azure](../classic/ps-sql-int-listener.md).

Para outras informações sobre como utilizar o SQL Server no Azure, consulte [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
