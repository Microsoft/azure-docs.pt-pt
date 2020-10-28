---
title: Ativar a dupla encriptação em repouso - Azure CLI - discos geridos
description: Ativar a dupla encriptação em repouso para os dados do disco gerido utilizando o Azure CLI.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1b2773b1d860126748e0dd05134d778073478d9f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741674"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Utilize o CLI Azure para permitir a dupla encriptação em repouso para discos geridos

O Azure Disk Storage suporta a dupla encriptação em repouso para discos geridos. Para obter informações conceptuais sobre a dupla encriptação em repouso, bem como outros tipos de encriptação de disco gerido, consulte a [encriptação dupla na](disk-encryption.md#double-encryption-at-rest) secção de repouso do nosso artigo de encriptação de disco.

## <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

Instale o mais recente [Azure CLI](/cli/azure/install-az-cli2) e faça login numa conta Azure com [login az](/cli/azure/reference-index).

## <a name="getting-started"></a>Introdução

1. Crie uma instância de Azure Key Vault e chave de encriptação.

    Ao criar a instância Key Vault, deve ativar a proteção de eliminação e purga suave. A eliminação suave garante que o Cofre de Chaves detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre-Chave para encriptar discos geridos.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Criar um DiskEncryptionSet com encriptação Otype definido como EncryptionAtRestWithPlatformAndCustomerKeys. Utilize a versão API **2020-05-01** no modelo Azure Resource Manager (ARM). 
    
        ```azurecli
        az group deployment create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Conceda ao diskEncrypationSet acesso ao cofre de chaves. 

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do seu DiskEncryptionSet no seu Diretório Ativo Azure. Se tiver um erro como "Não é possível encontrar o objeto Ative Directory" ao executar o seguinte comando, aguarde alguns minutos e tente novamente.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e configura estes recursos, pode usá-los para proteger os seus discos geridos. Os seguintes links contêm scripts de exemplo, cada um com um cenário respetivo, que pode usar para proteger os seus discos geridos.

- [Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Ativar as chaves geridas pelo cliente com encriptação do lado do servidor - Exemplos](disks-enable-customer-managed-keys-cli.md#examples)
