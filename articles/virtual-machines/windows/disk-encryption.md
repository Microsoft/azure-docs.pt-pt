---
title: Encriptação do lado do servidor dos discos geridos Azure - PowerShell
description: O Azure Storage protege os seus dados encriptando-os em repouso antes de os persistir nos clusters de armazenamento. Pode confiar nas teclas geridas pela Microsoft para a encriptação dos seus discos geridos, ou pode utilizar chaves geridas pelo cliente para gerir a encriptação com as suas próprias chaves.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: a66af3f74dbb88818f2490bdfbcbc12284652551
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392544"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Encriptação do lado do servidor dos discos geridos pelo Azure

Os discos geridos pelo Azure encriptam automaticamente os seus dados por padrão ao persistirem na nuvem. A encriptação do lado do servidor (SSE) protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

Os dados em discos geridos azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

A encriptação não afeta o desempenho dos discos geridos e não existe um custo adicional para a encriptação. 

> [!NOTE]
> Os discos temporários não são discos geridos e não são encriptados pela SSE; para obter mais informações sobre discos temporários, consulte [a visão geral dos discos geridos: funções de disco](managed-disks-overview.md#disk-roles).

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

As secções seguintes descrevem cada uma das opções para a gestão das chaves com maior detalhe.

## <a name="platform-managed-keys"></a>Chaves geridas pela plataforma

Por predefinição, os discos geridos utilizam chaves de encriptação geridas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos geridos, instantâneos, imagens e novos dados escritos para discos geridos existentes são automaticamente encriptados em repouso com chaves geridas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

Pode optar por gerir a encriptação ao nível de cada disco gerido, com as suas próprias chaves. A encriptação do lado do servidor para discos geridos com chaves geridas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Pode importar [as chaves RSA](../../key-vault/keys/hsm-protected-keys.md) para o cofre de chaves ou gerar novas chaves RSA no Cofre da Chave Azure. 

Os discos geridos pela Azure tratam a encriptação e a desencriptação de forma totalmente transparente utilizando [a encriptação do envelope.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Encripta dados utilizando uma chave de encriptação de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que está, por sua vez, protegida usando as suas chaves. O serviço de armazenamento gera chaves de encriptação de dados e encripta-as com chaves geridas pelo cliente utilizando encriptação RSA. A encriptação do envelope permite-lhe rodar (alterar) as suas chaves periodicamente de acordo com as suas políticas de conformidade sem afetar os seus VMs. Quando gira as suas chaves, o serviço de Armazenamento reencripta as chaves de encriptação de dados com as novas teclas geridas pelo cliente. 

Tem de conceder acesso a discos geridos no cofre para usar as suas chaves para encriptar e desencriptar o DEK. Isto permite-lhe o controlo total dos seus dados e chaves. Pode desativar as suas chaves ou revogar o acesso a discos geridos a qualquer momento. Também pode auditar a utilização da chave de encriptação com a monitorização do Azure Key Vault para garantir que apenas discos geridos ou outros serviços de Azure confiáveis estão a aceder às suas chaves.

Para SSDs premium, SSDs standard e HDDs padrão: Quando desativar ou apagar a sua chave, quaisquer VMs com discos que utilizem essa tecla serão automaticamente desligados. Depois disso, os VM não serão utilizáveis a menos que a chave esteja novamente ativada ou atribua uma nova chave.

Para discos ultra, quando desativa ou apaga uma tecla, quaisquer VMs com discos ultra que utilizem a tecla não desligam automaticamente. Assim que fizer negócios e reiniciar os VMs, os discos deixarão de usar a chave e os VM não voltarão a funcionar. Para repor os VMs, tem de atribuir uma nova chave ou ativar a chave existente.

O seguinte diagrama mostra como os discos geridos utilizam o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Fluxo de trabalho de chaves gerido pelo disco gerido pelo cliente e gerido pelo cliente. Um administrador cria um Cofre de Chave Azure, em seguida, cria um conjunto de encriptação de disco e configura o conjunto de encriptação do disco. O Conjunto está associado a um VM, que permite ao disco utilizar a AD Azure para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A seguinte lista explica o diagrama com mais detalhes:

1. Um administrador do Azure Key Vault cria recursos de cofre chave.
1. O administrador do cofre-chave importa as chaves RSA para o Key Vault ou gera novas chaves RSA no Key Vault.
1. Este administrador cria uma instância de recurso de Conjunto de Encriptação de Disco, especificando um ID do cofre da chave Azure e um URL chave. O Conjunto de Encriptação de Disco é um novo recurso introduzido para simplificar a gestão chave para discos geridos. 
1. Quando um conjunto de encriptação de disco é criado, uma [identidade gerida atribuída ao sistema](../../active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Ative Directory (AD) e associada ao conjunto de encriptação do disco. 
1. O administrador do cofre-chave Azure concede então a permissão de identidade gerida para realizar operações no cofre principal.
1. Um utilizador VM cria discos associando-os ao conjunto de encriptação do disco. O utilizador VM também pode ativar a encriptação do lado do servidor com as chaves geridas pelo cliente para os recursos existentes, associando-os ao conjunto de encriptação do disco. 
1. Os discos geridos utilizam a identidade gerida para enviar pedidos para o Cofre da Chave Azure.
1. Para ler ou escrever dados, os discos geridos enviam pedidos ao Azure Key Vault para encriptar (embrulhar) e desencriptar (desembrulhar) a chave de encriptação de dados de forma a realizar encriptação e desencriptação dos dados. 

Para revogar o acesso às chaves geridas pelo cliente, consulte [o Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [o Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados na conta de armazenamento, uma vez que a chave de encriptação é inacessível pela Azure Storage.

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o seu disco, não poderá desativá-la.
    Se precisar de contornar isto, tem de [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerido totalmente diferente que não esteja a utilizar chaves geridas pelo cliente.
- Apenas [o software e as teclas HSM RSA](../../key-vault/keys/about-keys.md) do tamanho 2080 são suportadas, sem outras chaves ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptados usando as mesmas chaves geridas pelo cliente e devem estar na mesma subscrição.
- As imagens criadas a partir de discos encriptados com encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptadas com as mesmas teclas geridas pelo cliente.
- Todos os recursos relacionados com as chaves geridas pelo cliente (Cofres chave Azure, conjuntos de encriptação de discos, VMs, discos e instantâneos) devem estar na mesma subscrição e região.
- Discos, instantâneos e imagens encriptadas com chaves geridas pelo cliente não podem passar para outra subscrição.
- Os discos geridos encriptados utilizando encriptação do lado do servidor com chaves geridas pelo cliente também não podem ser encriptados com encriptação do disco Azure e vice-versa
- Para obter informações sobre a utilização de chaves geridas pelo cliente com galerias de imagens partilhadas, consulte [Preview: Utilize chaves geridas pelo cliente para encriptar imagens](../image-version-encryption.md).

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurar o cofre da chave Azure e o DiskEncryptionSet

1. Certifique-se de que instalou a versão mais recente [do Azure PowerShell,](/powershell/azure/install-az-ps)e está a fazer sôs o seu contrato com a Connect-AzAccount

1. Crie uma instância de Azure Key Vault e chave de encriptação.

    Ao criar a instância Key Vault, deve ativar a proteção de eliminação e purga suave. A eliminação suave garante que o Cofre de Chaves detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Crie um exemplo de um DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Conceda ao diskEncrypationSet acesso ao cofre de chaves.

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do seu DiskEncryptionSet no seu Diretório Ativo Azure. Se tiver um erro como "Não é possível encontrar o objeto Ative Directory" ao executar o seguinte comando, aguarde alguns minutos e tente novamente.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Criar um VM utilizando uma imagem do Marketplace, encriptando o SISTEMA e os discos de dados com chaves geridas pelo cliente

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio encriptado utilizando encriptação do lado do servidor com chaves geridas pelo cliente e anexe-o a um VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>Criptografar discos geridos existentes 

Os discos existentes não devem ser anexados a um VM em execução para que os criptografe utilizando o seguinte script:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual utilizando uma imagem do Marketplace, encriptando o SISTEMA e os discos de dados com teclas geridas pelo cliente

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Altere a tecla de um DiskEncrypationSet para rodar a chave para todos os recursos que referenciam o DiskEncrypationSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Encontre o estado da encriptação do lado do servidor de um disco

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente dependem de identidades geridas para recursos Azure, uma característica do Azure Ative Directory (Azure AD). Ao configurar as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o disco gerido de um diretório AD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte [a transferência de uma subscrição entre os diretórios AD da Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Encriptação do lado do servidor contra encriptação do disco Azure

[A Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita a funcionalidade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows para encriptar discos geridos com chaves geridas pelo cliente dentro do VM do hóspede.  A encriptação do lado do servidor com as teclas geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de OS para os seus VMs encriptando dados no serviço de Armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o cofre de chave do Azure?](../../key-vault/general/overview.md)
- [Replicar máquinas com chaves geridas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de VMware VMs para Azure com a PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Confiem da recuperação de desastres para a Azure para VMs hiper-V usando PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
