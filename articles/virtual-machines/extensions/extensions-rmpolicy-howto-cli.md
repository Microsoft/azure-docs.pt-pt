---
title: Utilize a Política Azure para restringir a instalação de extensão VM
description: Utilize a Política Azure para restringir as extensões vm.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 3c660f7e05af43c2aad6f7283e32cfc1d85571ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066835"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Use a Política Azure para restringir a instalação de extensões em VMs Linux

Se pretender evitar a utilização ou instalação de determinadas extensões nos seus VMs Linux, pode criar uma política Azure utilizando o CLI para restringir as extensões para VMs dentro de um grupo de recursos. 

Este tutorial utiliza o CLI dentro da Cloud Shell Azure, que é constantemente atualizada para a versão mais recente. Se quiser executar o Azure CLI localmente, tem de instalar a versão 2.0.26 ou mais tarde. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Criar um ficheiro de regras

Para restringir as extensões que podem ser instaladas, é necessário ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) que forneça a lógica para identificar a extensão.

Este exemplo mostra como negar a instalação de extensões publicadas pela 'Microsoft.OSTCExtensions' através da criação de um ficheiro de regras em Azure Cloud Shell, mas se estiver a trabalhar no CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Em uma [bash Cloud Shell,](https://shell.azure.com/bash)tipo:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Copie e cole o seguinte .json no ficheiro.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Quando terminar, acerte na tecla **Esc** e, em seguida, digite **:wq** para guardar e fechar o ficheiro.


## <a name="create-a-parameters-file"></a>Criar um ficheiro de parâmetros

Também precisa de um ficheiro de [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que crie uma estrutura para que possa utilizar para passar uma lista das extensões a bloquear. 

Este exemplo mostra-lhe como criar um ficheiro de parâmetros para VMs Linux em Cloud Shell, mas se estiver a trabalhar no CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Na [bash Cloud Shell,](https://shell.azure.com/bash)tipo:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Copie e cole o seguinte .json no ficheiro.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Quando terminar, acerte na tecla **Esc** e, em seguida, digite **:wq** para guardar e fechar o ficheiro.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que gostaria de usar. A definição de política utiliza as regras e os ficheiros de parâmetros para definir a política. Criar a definição de política utilizando a definição de [política az criar](/cli/azure/role/assignment?view=azure-cli-latest).

Neste exemplo, as regras e parâmetros são os ficheiros que criou e armazenou como ficheiros .json na sua casca de nuvem.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Atribuir a política

Este exemplo atribui a política a um grupo de recursos que utiliza a criação de [uma política az](/cli/azure/policy/assignment). Qualquer VM criado no grupo de recursos **myResourceGroup** não será capaz de instalar o Acesso VM Linux ou as extensões de Script personalizado para linux. O grupo de recursos deve existir antes de poder atribuir a apólice.

Utilize a [lista de conta az](/cli/azure/account?view=azure-cli-latest) para obter o seu ID de subscrição para usar no lugar do exemplo.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testar a política

Teste a política criando um novo VM e tentando adicionar um novo utilizador.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Tente criar um novo utilizador chamado **myNewUser** utilizando a extensão VM Access.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Remover a atribuição

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Remover a política

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja [Azure Policy](../../governance/policy/overview.md).