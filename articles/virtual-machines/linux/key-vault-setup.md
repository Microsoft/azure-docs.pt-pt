---
title: Configurar Azure Key Vault para VMs Linux
description: Como configurar Key Vault para uso com uma máquina virtual Azure Resource Manager com o CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: 25ef1d43af9d37cebde4a28479010776cc148b6d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035940"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Como configurar Key Vault para máquinas virtuais com o CLI do Azure

Na pilha de Azure Resource Manager, os segredos/certificados são modelados como recursos fornecidos pelo Key Vault. Para saber mais sobre Azure Key Vault, confira [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md) Para que Key Vault seja usado com Azure Resource Manager VMs, a propriedade *EnabledForDeployment* em Key Vault deve ser definida como true. Este artigo mostra como configurar Key Vault para uso com VMs (máquinas virtuais) do Azure usando o CLI do Azure. 

Para executar essas etapas, você precisará do [CLI do Azure](/cli/azure/install-az-cli2) mais recente instalado e conectado a uma conta do Azure usando [AZ login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Criar um Key Vault
Crie um cofre de chaves e atribua a política de implantação com [AZ keyvault Create](/cli/azure/keyvault). O exemplo a seguir cria um cofre de chaves chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um Key Vault para uso com VMs
Defina a política de implantação em um cofre de chaves existente com [AZ keyvault Update](/cli/azure/keyvault). O seguinte atualiza o cofre de chaves chamado `myKeyVault` no grupo de recursos `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar Key Vault
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de Key Vault da seguinte maneira:

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
Para obter outras opções que você pode definir ao criar um Key Vault usando modelos, consulte [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
