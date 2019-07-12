---
title: Utilizar o Azure Policy para restringir a instalação da extensão de VM | Documentos da Microsoft
description: Utilize o Azure Policy para restringir as implementações de extensão VM.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 5a885637aa2321e12953a72754eed42c91b5b201
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706732"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Utilizar a política do Azure para restringir a instalação de extensões em VMs do Linux

Se quiser evitar que a utilização ou a instalação de determinadas extensões nas suas VMs do Linux, pode criar uma política do Azure com a CLI para restringir as extensões para as VMs dentro de um grupo de recursos. 

Este tutorial utiliza a CLI do Azure Cloud Shell, que é constantemente atualizada para a versão mais recente. Se quiser executar a CLI do Azure localmente, tem de instalar a versão 2.0.26 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Crie um ficheiro de regras

Para restringir que extensões podem ser instalados, tem de ter uma [regra](../../governance/policy/concepts/definition-structure.md#policy-rule) para fornecer a lógica para identificar a extensão.

Este exemplo mostra como negar a instalação de extensões publicadas por "Microsoft.OSTCExtensions" através da criação de um arquivo de regras no Azure Cloud Shell, mas se estiver a trabalhar na CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) com o caminho para o ficheiro local no seu computador.

Num [bash Cloud Shell](https://shell.azure.com/bash), tipo:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Copie e cole o seguinte. de JSON para o ficheiro.

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

Quando tiver terminado, prima a **Esc** da chave e, em seguida, escreva **: wq** para guardar e fechar o ficheiro.


## <a name="create-a-parameters-file"></a>Crie um ficheiro de parâmetros

Também é necessário um [parâmetros](../../governance/policy/concepts/definition-structure.md#parameters) ficheiro que cria uma estrutura para que possa utilizar para passar numa lista das extensões para bloquear. 

Este exemplo mostra como criar um ficheiro de parâmetros para VMs do Linux no Cloud Shell, mas se estiver a trabalhar na CLI localmente, também pode criar um ficheiro local e substituir o caminho (~/clouddrive) com o caminho para o arquivo local no seu computador.

Na [bash Cloud Shell](https://shell.azure.com/bash), tipo:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Copie e cole o seguinte. de JSON para o ficheiro.

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

Quando tiver terminado, prima a **Esc** da chave e, em seguida, escreva **: wq** para guardar e fechar o ficheiro.

## <a name="create-the-policy"></a>Criar a política

Uma definição de política é um objeto usado para armazenar a configuração que pretende utilizar. A definição de política usa os arquivos de regras e parâmetros para definir a política. Crie a definição de política com [criação da definição de política de az](/cli/azure/role/assignment?view=azure-cli-latest).

Neste exemplo, as regras e parâmetros são os arquivos criados e armazenados como arquivos. JSON no cloud shell.

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

Este exemplo atribui a política para um grupo de recursos utilizando [criação da atribuição de política de az](/cli/azure/policy/assignment). Qualquer VM criada no **myResourceGroup** grupo de recursos não será possível instalar o acesso de VM do Linux ou as extensões de Script personalizado para Linux. O grupo de recursos tem de existir antes de poder atribuir a política.

Utilizar [lista de contas de az](/cli/azure/account?view=azure-cli-latest) para obter o seu ID de subscrição para utilizar no lugar no exemplo.


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

Teste a política ao criar uma nova VM e a tentar adicionar um novo utilizador.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Tente criar um novo utilizador com o nome **myNewUser** usando a extensão de acesso à VM.

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, veja [Azure Policy](../../governance/policy/overview.md).