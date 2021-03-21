---
title: Utilize a Política Azure para restringir a instalação de extensão VM (Linux)
description: Utilize a Política Azure para restringir as extensões de VM.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/23/2018
ms.openlocfilehash: 582f690c00ff8bdd06e4d2867724aa482bcacaf3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559975"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Utilize a Política Azure para restringir a instalação de extensões em VMs Linux

Se pretender impedir a utilização ou instalação de determinadas extensões nos seus VMs Linux, pode criar uma definição de Política Azure utilizando o CLI para restringir as extensões de VMs dentro de um grupo de recursos. 

Este tutorial utiliza o CLI dentro da Azure Cloud Shell, que é constantemente atualizado para a versão mais recente. Se pretender executar o Azure CLI localmente, tem de instalar a versão 2.0.26 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Criar um ficheiro de regras

Para restringir que extensões podem ser instaladas, é necessário ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) que forneça a lógica para identificar a extensão.

Este exemplo mostra-lhe como negar a instalação de extensões publicadas por 'Microsoft.OSTCExtensions' criando um ficheiro de regras em Azure Cloud Shell, mas se estiver a trabalhar no CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Em uma [casca de nuvem bash,](https://shell.azure.com/bash)tipo:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Copiar e colar o seguinte .json no ficheiro.

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

Quando terminar, bata na tecla **Esc** e, em seguida, **escreva :wq** para guardar e fechar o ficheiro.


## <a name="create-a-parameters-file"></a>Criar um ficheiro de parâmetros

Também precisa de um ficheiro [de parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que crie uma estrutura para que possa usar para passar numa lista das extensões a bloquear. 

Este exemplo mostra como criar um ficheiro de parâmetros para Os VMs Do Linux em Cloud Shell, mas se estiver a trabalhar no CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) pelo caminho para o ficheiro local na sua máquina.

Na [bash Cloud Shell,](https://shell.azure.com/bash)escreva:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Copiar e colar o seguinte .json no ficheiro.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

Quando terminar, bata na tecla **Esc** e, em seguida, **escreva :wq** para guardar e fechar o ficheiro.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que gostaria de usar. A definição de política utiliza as regras e os ficheiros de parâmetros para definir a política. Crie a definição de política utilizando [a definição de política az create](/cli/azure/role/assignment).

Neste exemplo, as regras e parâmetros são os ficheiros que criou e guardou como ficheiros .json na sua concha de nuvem.

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

Este exemplo atribui a política a um grupo de recursos que utiliza [a az policy assignment create](/cli/azure/policy/assignment). Qualquer VM criado no grupo de recursos **myResourceGroup** não será capaz de instalar o Acesso Linux VM ou as extensões de Script Personalizado para Linux. O grupo de recursos deve existir antes de poder atribuir a apólice.

Use [a lista de conta az](/cli/azure/account) para obter o seu ID de subscrição para usar no lugar do que está no exemplo.


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

Tente criar um novo utilizador chamado **myNewUser** usando a extensão VM Access.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Remova a atribuição

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Remover a política

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja [Azure Policy](../../governance/policy/overview.md).
