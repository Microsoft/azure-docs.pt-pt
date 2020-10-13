---
title: Migrar VMware VMs para Azure (sem agente) - PowerShell
description: Aprenda a executar uma migração sem agente de VMware VMs com Azure Migrate através da PowerShell.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: ea2768c22aeaab3dabb546df5aced0f2a5b35e41
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002678"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrar VMware VMs para Azure (sem agente) - PowerShell

Neste artigo, você aprenderá a migrar VMware VMs descobertos com o método sem agente usando Azure PowerShell para [Azure Migrate: Migração de servidores](migrate-services-overview.md#azure-migrate-server-migration-tool). 

Saiba como:

> [!div class="checklist"]
> * A Retrieve descobriu VMware VMs num projeto Azure Migrate.
> * Comece a replicar VMs.
> * Atualizar propriedades para replicar VMs.
> * Monitor replicação.
> * Faça um teste de migração para garantir que tudo está funcionando como esperado.
> * Executar uma migração completa de VM.

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário para que possa configurar rapidamente uma prova de conceito. Os tutoriais utilizam opções predefinidas sempre que possível e não mostram todas as definições e caminhos possíveis.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de:

1. [Complete o tutorial de descoberta](tutorial-discover-vmware.md) para preparar Azure e VMware para migração.
2. Recomendamos que complete o segundo tutorial para [avaliar vMware VMs](tutorial-assess-vmware.md) antes de migrar para Azure.
3. Tem o módulo Azure `Az` PowerShell. Se precisar de instalar ou atualizar o Azure PowerShell, siga este [guia para instalar e configurar a Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Instalar módulo Azure Migrate PowerShell

O módulo Azure Migrate PowerShell está disponível em visualização pública. Terá de instalar o módulo PowerShell utilizando o seguinte comando. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Inscreva-se na subscrição do Microsoft Azure

Inscreva-se na sua assinatura Azure com o `Connect-AzAccount` cmdlet.

```azurepowershell
Connect-AzAccount
```

Selecione a sua subscrição do Azure. Use o `Get-AzSubscription` cmdlet para obter a lista de subscrições Azure a que tem acesso. Selecione a subscrição Azure que tem o seu projeto Azure Migrate para trabalhar com a utilização do `Set-AzContext` cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Recuperar o projeto Azure Migrate

Um projeto Azure Migrate é usado para armazenar metadados de descoberta, avaliação e migração recolhidos do ambiente que está a avaliar ou migrar.
Num projeto é possível rastrear ativos descobertos, orquestrar avaliações e realizar migrações.

Como parte dos pré-requisitos, já teria criado um projeto Azure Migrate. Utilize o `Get-AzMigrateProject` cmdlet para obter detalhes de um projeto Azure Migrate. Você precisará especificar o nome do projeto Azure Migrate ( `Name` ) e o nome do grupo de recursos do projeto Azure Migrate ( `ResourceGroupName` ).

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Recuperar VMs descobertos num projeto da Azure Migrate

A Azure Migrate utiliza um [aparelho Azure Migrate](migrate-appliance-architecture.md)leve . Como parte dos pré-requisitos, teria implantado o aparelho Azure Migrate como VMware VMware.

Para recuperar um VMware VM específico num projeto Azure Migrate, especifique o nome do projeto Azure Migrate `ProjectName` (), grupo de recursos do projeto Azure Migrate ( `ResourceGroupName` , e o nome VM `DisplayName` ). 

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
Vamos migrar este VM como parte deste tutorial.

Também pode recuperar todos os VMware VMs num projeto Azure Migrate utilizando os `ProjectName` parâmetros e `ResourceGroupName` parâmetros.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Se tiver vários aparelhos num projeto Azure Migrate, pode utilizar `ProjectName` `ResourceGroupName` , e `ApplianceName` parâmetros para recuperar todos os VMs descobertos utilizando um aparelho específico Azure Migrate. 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Inicializar a infraestrutura de replicação

[Azure Migrate: A migração do servidor](migrate-services-overview.md#azure-migrate-server-migration-tool) aproveita vários recursos Azure para a migração de VMs. A Migração de Servidores fornece os seguintes recursos, no mesmo grupo de recursos que o projeto.

- **Autocarro de**serviço : A Migração do Servidor utiliza o autocarro de serviço para enviar mensagens de orquestração de replicação para o aparelho.
- **Conta de armazenamento gateway**: A migração do servidor utiliza a conta de armazenamento de gateway para armazenar informações do Estado sobre os VMs que estão a ser replicados.
- **Conta de armazenamento de**registo : O aparelho Azure Migrate envia registos de replicação de VMs para uma conta de armazenamento de registo. Azure Migrate aplica a informação de replicação aos discos geridos por réplicas.
- **Cofre chave**: O aparelho Azure Migrate utiliza o cofre-chave para gerir as cordas de ligação do autocarro de serviço e as chaves de acesso para as contas de armazenamento utilizadas na replicação.

Antes de replicar o primeiro VM no projeto Azure Migrate, executar o seguinte script para providenciar a infraestrutura de replicação. Este script prevê e configura os recursos acima mencionados para que possa começar a migrar os seus VMware VMs.

> [!NOTE]
> Um projeto Azure Migrate apoia as migrações apenas para uma região de Azure. Uma vez executado este script, não pode alterar a região alvo para a qual deseja migrar os seus VMware VMs.
> Terá de executar o `Initialize-AzMigrateReplicationInfrastructure` guião se configurar um novo aparelho no seu projeto Azure Migrate. 

No artigo, rubricaremos a infraestrutura de replicação para que possamos migrar os nossos VMs para a `Central US` região. Pode [descarregar o ficheiro](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) a partir do repositório GitHub ou executá-lo utilizando o seguinte corte. 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>Replicar VMs

Depois de completar a descoberta e inicialização da infraestrutura de replicação, pode começar a replicação de VMware VMs para Azure. Pode executar até 300 replicações simultaneamente.

Pode especificar as propriedades de replicação da seguinte forma.

- **Grupo de subscrição e recursos alvo** - Especifique o grupo de subscrição e recursos para o qual o VM deve ser migrado fornecendo o ID do grupo de recursos utilizando o `TargetResourceGroupId` parâmetro. 
- **Rede virtual-alvo e sub-rede** - Especifique o ID da Rede Virtual Azure e o nome da sub-rede para a qual o VM deve ser migrado utilizando os `TargetNetworkId` parâmetros e `TargetSubnetName` parâmetros respectivamente. 
- **Nome VM-alvo** - Especifique o nome do VM Azure a ser criado utilizando o `TargetVMName` parâmetro.
- **Tamanho do VM do alvo** - Especifique o tamanho Azure VM a ser utilizado para a replicação de VM utilizando `TargetVMSize` o parâmetro. Por exemplo, para migrar um VM para D2_v2 VM em Azure, especifique o valor para `TargetVMSize` "Standard_D2_v2".  
- **Licença** - Para utilizar o Benefício Híbrido Azure para as suas máquinas do Windows Server que estejam cobertas com subscrições ativas de Software Assurance ou Windows Server, especifique o valor para `LicenseType` o parâmetro como "AHUB". Caso contrário, especifique o valor do `LicenseType` parâmetro como "NoLicenseType".
- **Disco OS** - Especifique o identificador único do disco que tem o bootloader e o instalador do sistema operativo. O ID do disco a ser usado é a propriedade de identificador único (UUID) para o disco recuperado usando o `Get-AzMigrateServer` cmdlet.
- **Tipo de disco** - Especifique o valor para o `DiskType` parâmetro da seguinte forma.
    - Para utilizar discos geridos por prémios, especifique "Premium_LRS" como valor para `DiskType` o parâmetro. 
    - Para utilizar discos SSD padrão, especifique "StandardSSD_LRS" como valor para `DiskType` o parâmetro. 
    - Para utilizar discos HDD padrão, especifique "Standard_LRS" como valor para `DiskType` o parâmetro. 
- **Redundância de infraestruturas** - Especifique a opção de redundância de infraestruturas da seguinte forma. 
    - Zona de Disponibilidade para fixar a máquina migrada a uma zona de disponibilidade específica na região. Utilize esta opção para distribuir servidores que formam um nível de aplicação de vários nós em zonas de disponibilidade. Esta opção só está disponível se a região-alvo selecionada para a migração suportar Zonas de Disponibilidade. Para utilizar zonas de disponibilidade, especifique o valor da zona de disponibilidade para `TargetAvailabilityZone` parâmetro.
    - Disponibilidade Definir para colocar a máquina migrada num Conjunto de Disponibilidade. O Grupo de Recursos-Alvo que foi selecionado deve ter um ou mais conjuntos de disponibilidade para utilizar esta opção. Para utilizar o conjunto de disponibilidade, especifique o ID definidor de disponibilidade para `TargetAvailabilitySet` parâmetro. 

Neste tutorial, vamos replicar todos os discos do VM descoberto e especificar um novo nome para o VM em Azure. Especificamos o primeiro disco do servidor descoberto como DISCO DESeso e migramos todos os discos como HDD padrão. O disco do SO é o disco que possui o carregador e o instalador do sistema operativo.

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

Também pode replicar seletivamente os discos do VM descoberto utilizando `New-AzMigrateDiskMapping` o cmdlet e fornecendo-o como uma entrada para o `DiskToInclude` parâmetro no `New-AzMigrateServerReplication` cmdlet. Também pode utilizar `New-AzMigrateDiskMapping` o cmdlet para especificar diferentes tipos de disco-alvo para cada disco individual a ser replicado. 

Especificar os valores para os seguintes parâmetros do `New-AzMigrateDiskMapping` cmdlet.

- **DiskId** - Especifique o identificador único para o disco ser migrado. O ID do disco a ser usado é a propriedade de identificador único (UUID) para o disco recuperado usando o `Get-AzMigrateServer` cmdlet.  
- **IsOSDisk** - Especifique "verdadeiro" se o disco a migrar é o disco de SO do VM, caso contrário "falso".
- **DiskType** - Especifique o tipo de disco a utilizar no Azure. 

No exemplo seguinte, vamos replicar apenas dois discos do VM descoberto. especificaremos o disco de SO e usaremos diferentes tipos de disco para cada disco ser replicado.

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Monitorizar a replicação 

A replicação ocorre da seguinte forma:

- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Durante a replicação inicial, é criado um instantâneo VM. Os dados do disco do instantâneo são replicados em discos geridos por réplicas em Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas nos discos de réplica em Azure.

Acompanhe o estado da replicação utilizando o `Get-AzMigrateServerReplication` cmdlet. 

> [!NOTE]
> O VM ID descoberto e a replicação do VM ID são dois identificadores únicos diferentes. Ambos os identificadores podem ser usados para recuperar detalhes de um servidor replicador.  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Monitor replicação usando identificador VM descoberto
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Monitor replicação usando o identificador VM replicando

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

Pode rastrear as propriedades "Estado de migração" e "Descrição do Estado de Migração" na produção. 
- Para a replicação inicial, os valores para as propriedades de descrição do estado de migração e do estado de migração serão "InitialSeedingInProgress" e "Initial replication" respectivamente. 
- Durante a replicação delta, os valores para propriedades de descrição do estado migratório e migração serão "Replicating" e "Ready to migrar" respectivamente.
- Após completar a migração, os valores para as propriedades de descrição do estado migratório e migração serão "Migração bem sucedida" e "Migrada" respectivamente.

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Para obter detalhes sobre o progresso da replicação, executar o seguinte cmdlet.

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
Pode acompanhar o progresso da replicação inicial utilizando as propriedades "Percentagens de progresso de sementeira iniciais" na saída.

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

A replicação ocorre da seguinte forma:

- Quando o trabalho de Replicação inicial termina com sucesso, as máquinas iniciam a sua replicação inicial ao Azure.
- Durante a replicação inicial, é criado um instantâneo VM. Os dados do disco do instantâneo são replicados em discos geridos por réplicas em Azure.
- Após o final da replicação inicial, começa a replicação delta. As alterações incrementais nos discos no local são periodicamente replicadas nos discos de réplica em Azure.

## <a name="update-properties-of-a-replicating-vm"></a>Atualizar propriedades de um VM replicador

[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) permite-lhe alterar propriedades-alvo, tais como nome, tamanho, grupo de recursos, configuração NIC e assim por diante, para uma replicação de VM. 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
As seguintes propriedades podem ser atualizadas para um VM.

- **Nome VM** - Especifique o nome do VM Azure a ser criado utilizando o `TargetVMName` parâmetro.
- **Tamanho VM** - Especifique o tamanho Azure VM a ser utilizado para a replicação de VM utilizando `TargetVMSize` o parâmetro. Por exemplo, para migrar um VM para D2_v2 VM em Azure, especifique o valor para `TargetVMSize` "Standard_D2_v2".  
- **Rede Virtual** - Especifique o ID da Rede Virtual Azure para o qual o VM deve ser migrado utilizando o `TargetNetworkId` parâmetro. 
- **Grupo de Recursos** - Especifique o ID do grupo de recursos para o qual o VM deve ser migrado fornecendo o ID do grupo de recursos utilizando o `TargetResourceGroupId` parâmetro.
- **Interface de rede** - A configuração de NIC pode ser especificada usando o `New-AzMigrateNicMapping` cmdlet. O objeto é então passado uma entrada para o `NicToUpdate` parâmetro no `Set-AzMigrateServerReplication` cmdlet. 

    - **Alterar a atribuição de IP** -Para especificar um IP estático para um NIC, forneça o endereço IPv4 para ser usado como IP estático para o VM utilizando o `TargetNicIP` parâmetro. Para atribuir dinamicamente um IP para um NIC, forneça "auto" como o valor do `TargetNicIP` parâmetro.
    - Utilize os valores "Primário", "Secundário" ou "DoNotCreate" para `TargetNicSelectionType` o parâmetro para especificar se o NIC deve ser primário, secundário ou não deve ser criado no VM migrado. Apenas um NIC pode ser especificado como o NIC primário para o VM. 
    - Para fazer um NIC primário, também terá de especificar os outros NICs que devem ser feitos secundários ou não devem ser criados no VM migrado.  
    - Para alterar a sub-rede para o NIC, especifique o nome da sub-rede utilizando o `TargetNicSubnet` parâmetro.

 - **Zona de Disponibilidade** - Para utilizar zonas de disponibilidade, especifique o valor da zona de disponibilidade para `TargetAvailabilityZone` parâmetro.
 - **Conjunto de disponibilidade** - Para utilizar o conjunto de disponibilidade, especifique o ID definidor de disponibilidade para `TargetAvailabilitySet` parâmetro.

No exemplo seguinte, atualizaremos a configuração do NIC fazendo o primeiro NIC como primário e atribuindo-lhe um IP estático. vamos descartar o segundo NIC para migração e atualizar o nome e tamanho VM alvo. 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping
```

Também pode listar todos os servidores replicadores num projeto Azure Migrate e, em seguida, usar o identificador VM replicante para atualizar as propriedades VM.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>Executar uma migração de teste

Quando a replicação delta começar, pode executar uma migração de teste para os VMs antes de executar uma migração completa para Azure. Recomendamos vivamente que teste a migração pelo menos uma vez para cada máquina antes de a migrar.

- A realização de um teste de migração verifica que a migração funcionará como esperado. A migração de testes não afeta a máquina no local, que continua operacional, e continua a replicar-se. 
- A migração de testes simula a migração criando um VM Azure usando dados replicados (normalmente migrando para um VNet não produtivo na sua assinatura Azure).
- Pode utilizar o teste replicado Azure VM para validar a migração, realizar testes de aplicações e resolver quaisquer problemas antes da migração completa.

Selecione a Rede Virtual Azure para ser utilizada para testes especificando o ID da rede virtual utilizando o `TestNetworkID` parâmetro.

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id
```

Após o teste estar concluído, limpe a migração do teste utilizando o `Start-AzMigrateTestMigrationCleanup` cmdlet.

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer
```

## <a name="migrate-vms"></a>Migrar VMs

Depois de verificar que a migração do teste funciona como esperado, pode migrar o servidor replicante usando o seguinte cmdlet.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 
```
Se não quiser desligar o servidor de origem, então não use `TurnOffSourceServer` o parâmetro.

## <a name="complete-the-migration"></a>Complete a migração

1. Após a migração, pare a replicação para a máquina no local e limpe as informações do estado de replicação para o VM utilizando o seguinte cmdlet.

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 
```

2. Instale o Azure VM [Windows](../virtual-machines/extensions/agent-windows.md) ou o agente [Linux](../virtual-machines/extensions/agent-linux.md) nas máquinas migratórias.
3. Execute otimizações de aplicação pós-migração, tais como atualizar cadeias de ligação de base de dados e configurações de servidor Web.
4. Execute testes de aplicação final e de aceitação da migração na aplicação migrada em execução no Azure.
5. Corte o tráfego para o exemplo de Azure VM migrado.
6. Remova as VMs no local do seu inventário de VMs locais.
7. Remova as VMs no local das cópias de segurança locais.
8. Atualize qualquer documentação interna para mostrar a nova localização e endereço IP das VMs do Azure. 

## <a name="post-migration-best-practices"></a>Boas práticas pós-migração

- Para uma maior resiliência:
    - Mantenha os dados seguros ao fazer uma cópia de segurança das VMs do Azure através do serviço Azure Backup. [Saiba mais](../backup/quick-backup-vm-portal.md).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar VMs do Azure para uma região secundária com o Site Recovery. [Saiba mais](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para uma maior segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com [o Azure Security Center - Mesmo a tempo da administração](../security-center/security-center-just-in-time.md).
    - Restrinja o tráfego de rede para os pontos finais de gestão com os [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
    - Implemente o [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) para ajudar a proteger discos e a manter os dados protegidos contra roubo e acesso não autorizado.
    - Leia mais sobre como [proteger recursos de IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) e aceda ao [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/).
- Para monitorização e gestão:
-  Considere implementar o Azure Cost Management para monitorizar a utilização e as despesas do recurso.



