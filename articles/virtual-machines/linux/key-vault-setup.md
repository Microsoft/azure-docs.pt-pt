---
title: Configurar o Azure Key Vault para VMs do Linux | Documentos da Microsoft
description: Como configurar o Cofre de chaves para utilização com uma máquina virtual do Azure Resource Manager com a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 04f47c0a4f6647ff0d45cc5dac40a677cc45563e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970265"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Como configurar o Cofre de chaves para máquinas virtuais com a CLI do Azure

Na pilha do Azure Resource Manager, os segredos/certificados são modelados como recursos que são fornecidos pelo Key Vault. Para saber mais sobre o Azure Key Vault, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-whatis.md) Para que o Cofre de chaves para serem utilizadas com VMs do Azure Resource Manager, o *EnabledForDeployment* propriedade no Cofre de chaves tem de ser definida como true. Este artigo mostra-lhe como configurar o Cofre de chaves para utilização com máquinas virtuais do Azure (VMs) com a CLI do Azure. 

Para executar estes passos, terá da versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e registado à utilização conta do Azure [início de sessão az](/cli/azure/reference-index#az_login).

## <a name="create-a-key-vault"></a>Criar um Key Vault
Criar um cofre de chaves e atribuir a política de implementação com [az keyvault criar](/cli/azure/keyvault#az_keyvault_create). O exemplo seguinte cria um cofre de chaves com o nome `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Atualizar um cofre de chaves para uso com VMs
Conjunto de cofre com a política de implementação numa chave existente [atualização do Cofre de chaves de az](/cli/azure/keyvault#az_keyvault_update). O seguinte atualiza o Cofre de chaves com o nome `myKeyVault` no `myResourceGroup` grupo de recursos:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Utilize modelos para configurar o Cofre de chaves
Quando utiliza um modelo, tem de definir o `enabledForDeployment` propriedade `true` para a chave do cofre dos recursos da seguinte forma:

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

## <a name="next-steps"></a>Passos Seguintes
Para outras opções que pode configurar quando criar um Key Vault com os modelos, veja [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
