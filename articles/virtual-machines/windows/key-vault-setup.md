---
title: Configurar Key Vault para VMs do Windows no Azure Resource Manager
description: Como configurar Key Vault para uso com uma máquina virtual Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038214"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar Key Vault para máquinas virtuais no Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

No Azure Resource Manager Stack, os segredos/certificados são modelados como recursos fornecidos pelo provedor de recursos do Key Vault. Para saber mais sobre Key Vault, confira [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Para que Key Vault seja usado com Azure Resource Manager máquinas virtuais, a propriedade **EnabledForDeployment** em Key Vault deve ser definida como true. Você pode fazer isso em vários clientes.
> 2. O Key Vault precisa ser criado na mesma assinatura e no mesmo local que a máquina virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Usar o PowerShell para configurar o Key Vault
Para criar um cofre de chaves usando o PowerShell, consulte [definir e recuperar um segredo de Azure Key Vault usando o PowerShell](../../key-vault/quick-create-powershell.md).

Para novos cofres de chaves, você pode usar este cmdlet do PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Para os cofres de chaves existentes, você pode usar este cmdlet do PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Usar a CLI para configurar Key Vault
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [gerenciar Key Vault usando a CLI](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Para a CLI, você precisa criar o cofre de chaves antes de atribuir a política de implantação. Pode fazê-lo utilizando o comando seguinte:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Em seguida, para habilitar Key Vault para uso com a implantação de modelo, execute o seguinte comando:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar Key Vault
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de Key Vault.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Para obter outras opções que você pode configurar ao criar um cofre de chaves usando modelos, consulte [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
