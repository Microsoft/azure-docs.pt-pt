---
title: Configurar o Cofre de Chaves usando o PowerShell
description: Como configurar o Key Vault para utilização com uma máquina virtual utilizando o PowerShell.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557493"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Configurar o Cofre-Chave para máquinas virtuais usando a Azure PowerShell

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
