---
title: Usar Azure Policy para restringir a instalação da extensão de VM | Microsoft Docs
description: Use Azure Policy para restringir implantações de extensão de VM.
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
ms.openlocfilehash: 20099bb32a1984be0bfbbaaa4e7bc6cd4481a806
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174026"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Usar Azure Policy para restringir a instalação de extensões em VMs Linux

Se você quiser impedir o uso ou a instalação de determinadas extensões em suas VMs do Linux, poderá criar uma política do Azure usando a CLI para restringir as extensões para VMs em um grupo de recursos. 

Este tutorial usa a CLI dentro do Azure Cloud Shell, que é constantemente atualizado para a versão mais recente. Se você quiser executar o CLI do Azure localmente, precisará instalar a versão 2.0.26 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Criar um arquivo de regras

Para restringir quais extensões podem ser instaladas, você precisa ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) para fornecer a lógica para identificar a extensão.

Este exemplo mostra como negar a instalação de extensões publicadas por ' Microsoft. OSTCExtensions ' criando um arquivo de regras no Azure Cloud Shell, mas se você estiver trabalhando localmente na CLI, também poderá criar um arquivo local e substituir o caminho (~/CloudDrive) pelo caminho para o arquivo local em seu computador.

Em um [Cloud shell bash](https://shell.azure.com/bash), digite:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Copie e cole o. JSON a seguir no arquivo.

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

Quando terminar, pressione a tecla **ESC** e digite **: Wq** para salvar e fechar o arquivo.


## <a name="create-a-parameters-file"></a>Criar um arquivo de parâmetros

Você também precisa de um arquivo de [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) que cria uma estrutura a ser usada para passar uma lista das extensões a serem bloqueadas. 

Este exemplo mostra como criar um arquivo de parâmetros para VMs do Linux no Cloud Shell, mas se você estiver trabalhando localmente na CLI, também poderá criar um arquivo local e substituir o caminho (~/CloudDrive) pelo caminho para o arquivo local em seu computador.

No [Cloud shell bash](https://shell.azure.com/bash), digite:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Copie e cole o. JSON a seguir no arquivo.

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

Quando terminar, pressione a tecla **ESC** e digite **: Wq** para salvar e fechar o arquivo.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que você deseja usar. A definição de política usa os arquivos de regras e parâmetros para definir a política. Crie a definição de política usando [AZ Policy Definition Create](/cli/azure/role/assignment?view=azure-cli-latest).

Neste exemplo, as regras e os parâmetros são os arquivos que você criou e armazenou como arquivos. JSON em seu Cloud Shell.

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

Este exemplo atribui a política a um grupo de recursos usando a [atribuição de política AZ Create](/cli/azure/policy/assignment). Qualquer VM criada no grupo de recursos MyResource Group não será capaz de instalar o acesso de VM do Linux ou as extensões de script personalizado para Linux. O grupo de recursos deve existir antes que você possa atribuir a política.

Use a [lista de contas AZ](/cli/azure/account?view=azure-cli-latest) para obter sua ID de assinatura a ser usada no lugar do exemplo.


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

Teste a política criando uma nova VM e tentando adicionar um novo usuário.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Tente criar um novo usuário chamado **myNewUser** usando a extensão de acesso da VM.

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