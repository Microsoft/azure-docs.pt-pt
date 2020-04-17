---
title: Configurar o Cofre chave Azure para VMs Linux
description: Como configurar o Cofre chave para utilização com uma máquina virtual Do Gestor de Recursos Azure com o CLI Azure.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 9ae486ee522982b116af58cfb7cbfbca66a7ef4a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458748"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Como configurar o Cofre chave para máquinas virtuais com o AZURE CLI

Na pilha de Gestor de Recursos Azure, os segredos/certificados são modelados como recursos fornecidos pela Key Vault. Para saber mais sobre o Cofre chave Azure, veja [o que é o Cofre chave Azure?](../../key-vault/general/overview.md) Para que o Cofre chave seja utilizado com VMs do Gestor de Recursos Azure, a propriedade *EnabledForDeployment* no Key Vault deve ser definida como verdadeira. Este artigo mostra-lhe como configurar o Cofre chave para utilização com máquinas virtuais Azure (VMs) utilizando o Azure CLI. 

Para realizar estes passos, necessita do mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e registado numa conta Azure utilizando [login az](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Criar um Key Vault
Crie um cofre chave e atribua a política de implantação com [a criação de keyvault az](/cli/azure/keyvault). O exemplo seguinte cria `myKeyVault` um `myResourceGroup` cofre chave nomeado no grupo de recursos:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um cofre de chaves para uso com VMs
Desestabeleça a política de implementação num cofre de chaves existente com [atualização do cofre az](/cli/azure/keyvault). As seguintes atualizações `myKeyVault` do `myResourceGroup` cofre-chave nomeado no grupo de recursos:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Use modelos para configurar o cofre chave
Quando você usa um modelo, `enabledForDeployment` você `true` precisa definir a propriedade para o recurso Key Vault da seguinte forma:

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
Para outras opções que pode configurar quando criar um Cofre chave utilizando modelos, consulte [Criar um cofre chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
