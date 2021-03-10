---
title: Azure PowerShell - Ative a dupla encriptação em repouso - discos geridos
description: Ativar a dupla encriptação em repouso para os dados do disco geridos utilizando o Azure PowerShell.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cfef3925152c0b94a59c662443cf202724dadc0a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549554"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>Utilize o módulo Azure PowerShell para permitir a dupla encriptação em repouso para discos geridos

O Azure Disk Storage suporta a dupla encriptação em repouso para discos geridos. Para obter informações conceptuais sobre a dupla encriptação em repouso, bem como outros tipos de encriptação de disco gerido, consulte a [encriptação dupla na](../disk-encryption.md#double-encryption-at-rest) secção de repouso do nosso artigo de encriptação de disco.

## <a name="prerequisites"></a>Pré-requisitos

Instale a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)e inscreva-se numa conta Azure utilizando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

## <a name="getting-started"></a>Introdução

1. Crie uma instância de Azure Key Vault e chave de encriptação.

    Ao criar a instância Key Vault, deve ativar a proteção de eliminação e purga suave. A eliminação suave garante que o Cofre de Chaves detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Criar um DiskEncryptionSet com encriptação Otype definido como EncryptionAtRestWithPlatformAndCustomerKeys. Utilize a versão API **2020-05-01** no modelo Azure Resource Manager (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. Conceda ao diskEncrypationSet acesso ao cofre de chaves.

    > [!NOTE]
    > Pode levar alguns minutos para o Azure criar a identidade do seu DiskEncryptionSet no seu Diretório Ativo Azure. Se tiver um erro como "Não é possível encontrar o objeto Ative Directory" ao executar o seguinte comando, aguarde alguns minutos e tente novamente.

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e configura estes recursos, pode usá-los para proteger os seus discos geridos. Os seguintes links contêm scripts de exemplo, cada um com um cenário respetivo, que pode usar para proteger os seus discos geridos.

- [Azure PowerShell - Ativar as chaves geridas pelo cliente com encriptação do lado do servidor - discos geridos](disks-enable-customer-managed-keys-powershell.md)
- [Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
