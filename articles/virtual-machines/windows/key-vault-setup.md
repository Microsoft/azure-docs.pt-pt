---
title: Configurar o Cofre de Chaves
description: Como configurar o Key Vault para utilização com uma máquina virtual.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: d5e4bb1d3f62685c90e95eebf5f8f07720a23b82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086998"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurar o Cofre-Chave para máquinas virtuais no Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha Azure Resource Manager, os segredos/certificados são modelados como recursos fornecidos pelo fornecedor de recursos do Key Vault. Para saber mais sobre o Key Vault, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Para que o Cofre de Chaves seja utilizado com máquinas virtuais do Azure Resource Manager, a propriedade **EnabledForDeployment** no Key Vault deve ser definida como verdadeira. Pode fazer isso em vários clientes.
> 2. O Cofre-Chave precisa de ser criado na mesma subscrição e localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Use powerShell para configurar o Cofre da Chave
Para criar um cofre de chaves utilizando o PowerShell, consulte [o set e recupere um segredo do Azure Key Vault utilizando o PowerShell](../../key-vault/secrets/quick-create-powershell.md).

Para novos cofres chave, pode utilizar este cmdlet PowerShell:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Para cofres-chave existentes, pode utilizar este cmdlet PowerShell:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>Use o CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves utilizando a interface de linha de comando (CLI), consulte [Gerir o Cofre de Chaves utilizando o CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

Para o CLI, tens de criar o cofre de chaves antes de atribuires a política de implantação. Pode fazê-lo utilizando o comando seguinte:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Em seguida, para ativar o Cofre da Chave para utilização com a implementação do modelo, executar o seguinte comando:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Use modelos para configurar o Cofre de Chaves
Enquanto usa um modelo, precisa de definir a `enabledForDeployment` propriedade para o recurso Key `true` Vault.

```config
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
```

Para outras opções que pode configurar quando criar um cofre de chaves utilizando modelos, consulte [Criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
