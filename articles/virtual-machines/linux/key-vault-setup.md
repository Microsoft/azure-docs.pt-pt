---
title: Configurar o Cofre da Chave Azure para os VMs Linux
description: Como configurar o Key Vault para utilização com uma máquina virtual Azure Resource Manager com o Azure CLI.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 9ae486ee522982b116af58cfb7cbfbca66a7ef4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81458748"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Como configurar o Key Vault para máquinas virtuais com o Azure CLI

Na pilha Azure Resource Manager, os segredos/certificados são modelados como recursos fornecidos pela Key Vault. Para saber mais sobre o Azure Key Vault, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md) Para que o Cofre de Chaves seja utilizado com VMs do Gestor de Recursos Azure, a propriedade *EnabledForDeployment* no Key Vault deve ser definida como verdadeira. Este artigo mostra-lhe como configurar o Key Vault para utilização com máquinas virtuais Azure (VMs) utilizando o Azure CLI. 

Para realizar estes passos, precisa do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado numa conta Azure utilizando [o login az](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Criar um Key Vault
Crie um cofre-chave e atribua a política de implantação com [a criação de keyvault az](/cli/azure/keyvault). O exemplo a seguir cria um cofre-chave nomeado `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualize um cofre chave para uso com VMs
Desa estabaça a política de implantação de um cofre de chaves existente com [atualização de keyvault az](/cli/azure/keyvault). As seguintes atualizações do cofre-chave nomeado `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Use modelos para configurar o Cofre de Chaves
Quando utilizar um modelo, tem de definir a `enabledForDeployment` propriedade para o recurso Key Vault da seguinte `true` forma:

```json
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

## <a name="next-steps"></a>Passos seguintes
Para outras opções que pode configurar quando criar um Cofre de Chaves utilizando modelos, consulte [Criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
