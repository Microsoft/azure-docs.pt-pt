---
title: Azure PowerShell - Ative a encriptação de ponta a ponta no seu anfitrião VM
description: Como ativar a encriptação de ponta a ponta para os seus VMs Azure utilizando encriptação no anfitrião.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6cb6235c5c1a34cb3f48d315adee565591bb72c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088466"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-powershell"></a>Ativar encriptação de ponta a ponta utilizando encriptação no anfitrião - Azure PowerShell

Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. Para obter informações conceptuais sobre encriptação no anfitrião, bem como outros tipos de encriptação de discos [geridos, consulte a Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Também pode encontrar os tamanhos VM programáticamente. Para aprender a recuperá-los programáticamente, consulte a secção [de tamanhos VM suportados por Finding.](#finding-supported-vm-sizes)

## <a name="prerequisites"></a>Pré-requisitos

Para poder utilizar a encriptação no anfitrião para os seus VMs ou conjuntos de escala de máquinas virtuais, tem de obter a funcionalidade ativada na sua subscrição. Envie um e-mail para encryptionAtHost@microsoft .com com os seus Ids de subscrição para obter a funcionalidade ativada para as suas subscrições.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Criar um cofre de chave azul e diskEncryptionSet

Uma vez ativada a funcionalidade, terá de configurar um Cofre de Teclas Azure e um DiskEncryptionSet, se ainda não o fez.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Ativar a encriptação no anfitrião para discos ligados a conjuntos de escala de VM e máquinas virtuais

Pode ativar a encriptação no anfitrião, definindo uma nova propriedade EncryptionAtHost sob segurançaProfile de VMs ou conjuntos de escala de máquina virtual usando a versão API **2020-06-01** e acima.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Scripts de exemplo

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Ativar a encriptação no anfitrião para discos ligados a um VM com teclas geridas pelo cliente

Crie um VM com discos geridos utilizando o recurso URI do DiskEncrypationSet criado anteriormente.

`<yourPassword>`Substitua, , , , , `<yourVMName>` `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` `<yourRegion>` e, em seguida, executar o script.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Ativar a encriptação no anfitrião para discos ligados a um VM com teclas geridas pela plataforma

`<yourPassword>`Substitua, `<yourVMName>` , `<yourVMSize>` `<yourResourceGroupName>` `<yourRegion>` e, em seguida, executar o script.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Encontrar tamanhos de VM suportados

Os tamanhos VM legacy não são suportados. Pode encontrar a lista de tamanhos VM suportados por:

Chamando a [API de Recursos Skus](/rest/api/compute/resourceskus/list) e verificando se a `EncryptionAtHostSupported` capacidade está definida para **True**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Ou, chamando o [cmdlet Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e configura estes recursos, pode usá-los para proteger os seus discos geridos. O link que se segue contém scripts de exemplo, cada um com um cenário respetivo, que pode utilizar para proteger os seus discos geridos.

[Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
