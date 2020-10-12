---
title: Migrar as máquinas virtuais VMware para Azure com encriptação do lado do servidor (SSE) e chaves geridas pelo cliente (CMK) utilizando a migração do servidor Azure Migrate Server
description: Saiba como migrar VMware VMs para Azure com encriptação do lado do servidor (SSE) e teclas geridas pelo cliente (CMK) utilizando a migração do servidor Azure Migrate Server
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: 01f30305529e7f142be0ca6ddffa0f5a12a235bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260011"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrar VMware VMs para VMs Azure habilitados com encriptação do lado do servidor e chaves geridas pelo cliente

Este artigo descreve como migrar VMware VMs para máquinas virtuais Azure com discos encriptados usando encriptação do lado do servidor (SSE) com chaves geridas pelo cliente (CMK), utilizando a migração do servidor migratório Azure (replicação sem agente).

A experiência do portal de migração do servidor Azure Migrate permite [migrar VMware VMs para Azure com replicação sem agentes.](tutorial-migrate-vmware.md) A experiência do portal atualmente não oferece a capacidade de ligar SSE com CMK para os seus discos replicados em Azure. A capacidade de ligar a SSE com CMK para os discos replicados só está disponível através da REST API. Neste artigo, você verá como criar e implementar um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para replicar um VMware VM e configurar os discos replicados em Azure para usar SSE com CMK.

Os exemplos deste artigo utilizam [o Azure PowerShell](/powershell/azure/new-azureps-module-az) para executar as tarefas necessárias para criar e implementar o modelo de Gestor de Recursos.

[Saiba mais](../virtual-machines/windows/disk-encryption.md) sobre encriptação do lado do servidor (SSE) com teclas geridas pelo cliente (CMK) para discos geridos.

## <a name="prerequisites"></a>Pré-requisitos

- [Reveja o tutorial](tutorial-migrate-vmware.md) sobre a migração de VMware VMs para Azure com replicação sem agente para entender os requisitos da ferramenta.
- [Siga estas instruções](how-to-add-tool-first-time.md) para criar um projeto Azure Migrate e adicione a ferramenta de migração do **Azure Migrate: Server Migration** ao projeto.
- [Siga estas instruções](how-to-set-up-appliance-vmware.md) para configurar o aparelho Azure Migrate para VMware no seu ambiente no local e descoberta completa.

## <a name="prepare-for-replication"></a>Preparar para a replicação

Uma vez concluída a descoberta do VM, a linha De Servidores Descobertos no azulejo de migração do servidor mostrará uma contagem de VMware VMs descobertos pelo aparelho.

Antes de começar a replicar VMs, a infraestrutura de replicação precisa de ser preparada.

1. Crie uma instância de ônibus de serviço na região alvo. O Service Bus é utilizado pelo aparelho Azure Migrate no local para comunicar com o serviço de migração do servidor para coordenar a replicação e migração.
2. Crie uma conta de armazenamento para a transferência de registos de operação a partir da replicação.
3. Crie uma conta de armazenamento para a que o aparelho Azure Migrate envia dados de replicação.
4. Crie um Cofre-Chave e configuure o Cofre-Chave para gerir fichas de assinatura de acesso partilhado para acesso blob nas contas de armazenamento criadas nos passos 3 e 4.
5. Gere um símbolo de assinatura de acesso partilhado para o autocarro de serviço criado no passo 1 e crie um segredo para o símbolo no Cofre chave criado no passo anterior.
6. Crie uma política de acesso ao Cofre de Chaves para dar acesso ao cofre Azure Migrate (utilizando a aplicação AAD do aparelho) e ao Serviço de Migração do Servidor ao Cofre de Chaves.
7. Crie uma política de replicação e configuure o serviço de migração do servidor com detalhes da infraestrutura de replicação criada no passo anterior.

A infraestrutura de replicação deve ser criada na região de Azure alvo para a migração e na assinatura Azure-alvo para a a que os VM estão a ser migrados.

A experiência do portal de migração do servidor simplifica a preparação da infraestrutura de replicação fazendo-o automaticamente para si quando replica um VM pela primeira vez num projeto. Neste artigo, assumimos que já replicou um ou mais VMs usando a experiência do portal e que a infraestrutura de replicação já está criada. Vamos ver como descobrir detalhes da infraestrutura de replicação existente e como usar estes detalhes como entradas para o modelo de Gestor de Recursos que será usado para configurar a replicação com CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identificar componentes de infraestrutura de replicação

1. No portal Azure, vá à página dos grupos de recursos e selecione o grupo de recursos no qual foi criado o projeto Azure Migrate.
2. Selecione **Implementações** do menu esquerdo e procure um nome de implementação que comece com a cadeia *"Microsoft.MigrateV2.VMwareV2EnableMigrate"*. Você verá uma lista de modelos de Gestor de Recursos criados pela experiência do portal para configurar a replicação de VMs neste projeto. Vamos descarregar um tal modelo e usá-lo como base para preparar o modelo para replicação com CMK.
3. Para descarregar o modelo, selecione qualquer implementação correspondente ao padrão de corda no passo anterior > **selecionar Modelo** a partir do menu esquerdo > clique **em baixar** a partir do menu superior. Guarde as template.jsno arquivo local. Irá editar este ficheiro de modelo no último passo.

## <a name="create-a-disk-encryption-set"></a>Criar um conjunto de encriptação de disco

Um conjunto de conjunto de encriptação de disco mapeia Discos Geridos para um Cofre de Chaves que contém o CMK para usar para SSE. Para replicar VMs com CMK, irá criar um conjunto de encriptação de disco e passá-lo como uma entrada para a operação de replicação.

Siga o exemplo [aqui](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) para criar um conjunto de encriptação de disco usando Azure PowerShell. Certifique-se de que o conjunto de encriptação do disco é criado na subscrição-alvo para a qual os VMs estão a ser migrados e na região alvo de Azure para a migração.

O conjunto de encriptação do disco pode ser configurado para encriptar discos geridos com uma chave gerida pelo cliente, ou para uma dupla encriptação com uma chave gerida pelo cliente e uma chave de plataforma. Para utilizar a dupla encriptação na opção de repouso, configuure o conjunto de encriptação do disco, tal como descrito [aqui](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

No exemplo mostrado abaixo, o conjunto de encriptação do disco está configurado para utilizar uma chave gerida pelo cliente.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Obtenha detalhes do VMware VM para migrar

Neste passo, você usará Azure PowerShell para obter os detalhes do VM que precisa ser migrado. Estes detalhes serão usados para construir o modelo de Gestor de Recursos para replicação. Especificamente, as duas propriedades de interesse são:

- A identificação de recursos da máquina para os VMs descobertos.
- A lista de discos para o VM e os seus identificadores de disco.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Copie o valor da cadeia SiteId correspondente ao aparelho Azure Migrate através do qual o VM é descoberto. No exemplo acima mostrado, o SiteId é *"/subscrições/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Copie os valores de utoide, nome e disco para a máquina ser migrada.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Criar modelo de gestor de recursos para replicação

- Abra o ficheiro de modelo do Gestor de Recursos que descarregou nos **componentes da infraestrutura de replicação identificando** um editor à sua escolha.
- Remover todas as definições de recursos do modelo, com exceção dos recursos que são do tipo *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Se existirem múltiplas definições de recursos do tipo acima, remova todas, menos uma. Remova **quaisquer definições de** propriedade dependentes da definição de recurso.
- No final deste passo, deverá ter um ficheiro que se pareça com o exemplo abaixo e que tenha o mesmo conjunto de propriedades.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Editar a propriedade **do nome** na definição de recurso. Substitua a cadeia que segue o último "/" na propriedade do nome pelo valor de *$machine. Nome*(do passo anterior).
- Alterar o valor da **propriedade.providerSpecificDetails.vmwareMachineId** propriedade com valor de *$machine. ResourceId*(do passo anterior).
- Decreva os valores para **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** para o ID do grupo de recursos-alvo, iD de recurso de rede virtual alvo e nome de sub-rede alvo, respectivamente.
- Desajuste o valor da **licençaType** a "WindowsServer" para aplicar o Benefício Híbrido Azure para este VM. Se este VM não for elegível para benefício híbrido Azure, desajuste o valor da **licençaType** para NoLicenseType.
- Altere o valor da propriedade **targetVmName** para o nome de máquina virtual Azure desejado para o VM migrado.
- Opcionalmente adicione uma propriedade chamada **targetVmSize** abaixo da propriedade **targetVmName.** Desajuste o valor da propriedade **targetVmSize** para o tamanho da máquina virtual Azure desejada para o VM migrado.
- A propriedade **disksToInclude** é uma lista de entradas de disco para replicação com cada item de lista representando um disco no local. Crie tantos itens de lista como o número de discos no VM no local. Substitua a propriedade **diskId** no item da lista para o uuid dos discos identificados no passo anterior. Desajuste o valor **isOSDisk** para "verdadeiro" para o disco DE do VM e "falso" para todos os outros discos. Deixe inalterados os **registosStorageAccountId** e as propriedades do **logStorageAccountSasSecretName inalteradas.** Desa estarir o valor do **discoType** ao tipo disco gerido Azure *(Standard_LRS, Premium_LRS, StandardSSD_LRS*) para utilizar para o disco. Para os discos que precisam de ser encriptados com CMK, adicione uma propriedade chamada **diskEncryptionSetId** e defina o valor para o ID de recursos do conjunto de encriptação do disco**criado( $des. Id**) no passo de configuração de *encriptação de disco*
- Guarde o ficheiro de modelo editado. Para o exemplo acima, o ficheiro de modelo editado parece o seguinte:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Configurar a replicação

Agora pode implementar o modelo de Gestor de Recursos editado para o grupo de recursos do projeto para configurar a replicação para o VM. Saiba como [implementar recursos com modelos de Gestor de Recursos Azure e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Passos seguintes

[Monitorize](tutorial-migrate-vmware.md#track-and-monitor) o estado de replicação através da experiência do portal e realize migrações de teste e migração.
