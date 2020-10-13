---
title: Criar ficheiro de parâmetros
description: Crie um ficheiro de parâmetros para passar em valores durante a implementação de um modelo de Gestor de Recursos Azure
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89276665"
---
# <a name="create-resource-manager-parameter-file"></a>Criar ficheiro de parâmetro do Gestor de Recursos

Em vez de passar parâmetros como valores inline no seu script, pode ser mais fácil usar um ficheiro JSON que contenha os valores dos parâmetros. Este artigo mostra como criar o ficheiro de parâmetros.

## <a name="parameter-file"></a>Arquivo de parâmetros

O ficheiro parâmetro tem o seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Note que os valores dos parâmetros são armazenados como texto simples no ficheiro do parâmetro. Esta abordagem funciona para valores que não são sensíveis, tais como especificar o SKU para um recurso. Não funciona por valores sensíveis, como palavras-passe. Se precisar de passar um valor sensível como parâmetro, guarde o valor num cofre chave e refira o cofre de chaves no seu ficheiro de parâmetros. O valor sensível é recuperado de forma segura durante a implantação.

O ficheiro de parâmetros a seguir inclui um valor de texto simples e um valor que é armazenado num cofre de chaves.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Para obter mais informações sobre a utilização de valores a partir de um cofre de chaves, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definir valores de parâmetros

Para descobrir como definir os valores dos parâmetros, abra o modelo que está a implementar. Veja a secção de parâmetros do modelo. O exemplo a seguir mostra os parâmetros de um modelo.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

O primeiro detalhe a notar é o nome de cada parâmetro. Os valores no seu ficheiro de parâmetro devem corresponder aos nomes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Note o tipo do parâmetro. Os valores no seu ficheiro de parâmetros devem ter os mesmos tipos. Para este modelo, pode fornecer ambos os parâmetros como cordas.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Em seguida, procure um valor padrão. Se um parâmetro tiver um valor padrão, pode fornecer um valor, mas não precisa.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Finalmente, olhe para os valores permitidos e quaisquer restrições como o comprimento máximo. Dizem-lhe o alcance dos valores que pode fornecer para o parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

O seu ficheiro de parâmetros só pode conter valores para parâmetros definidos no modelo. Se o seu ficheiro de parâmetros contiver parâmetros extras que não correspondem aos parâmetros do modelo, recebe um erro.

## <a name="parameter-type-formats"></a>Formatos de tipo de parâmetro

O exemplo a seguir mostra os formatos de diferentes tipos de parâmetros.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>Implementar modelo com arquivo de parâmetros

Para passar um arquivo de parâmetro local com Azure CLI, use @ e o nome do ficheiro parâmetro.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Para obter mais informações, consulte [implementar recursos com modelos ARM e Azure CLI](./deploy-cli.md#parameters).

Para passar um ficheiro de parâmetro local com Azure PowerShell, utilize o `TemplateParameterFile` parâmetro.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para obter mais informações, consulte [implementar recursos com modelos ARM e Azure PowerShell](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> Não é possível utilizar um ficheiro de parâmetro com a lâmina do modelo personalizada no portal.

## <a name="file-name"></a>Nome de ficheiro

A convenção geral para nomear o ficheiro de parâmetros é adicionar **.parâmetros** ao nome do modelo. Por exemplo, se o seu modelo for nomeado **azuredeploy.js,** o seu ficheiro de parâmetros é nomeado **azuredeploy.parameters.jsem**. Esta convenção de nomeação ajuda-o a ver a ligação entre o modelo e os parâmetros.

Para implementar em diferentes ambientes, crie mais de um ficheiro de parâmetros. Ao nomear o ficheiro de parâmetros, adicione uma forma de identificar a sua utilização. Por exemplo, use **azuredeploy.parameters-dev.jse** **azuredeploy.parameters-prod.jsem**

## <a name="parameter-precedence"></a>Precedência do parâmetro

Pode utilizar parâmetros inline e um ficheiro de parâmetro local na mesma operação de implantação. Por exemplo, pode especificar alguns valores no ficheiro de parâmetro local e adicionar outros valores inline durante a implementação. Se fornecer valores para um parâmetro tanto no arquivo de parâmetro local como em linha, o valor inline tem precedência.

É possível utilizar um ficheiro de parâmetro externo, fornecendo o URI ao ficheiro. Quando se utiliza um ficheiro de parâmetro externo, não é possível passar outros valores, quer em linha, quer a partir de um ficheiro local. Todos os parâmetros inline são ignorados. Forneça todos os valores dos parâmetros no ficheiro externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nomes de parâmetros

Se o seu modelo incluir um parâmetro com o mesmo nome que um dos parâmetros do comando PowerShell, o PowerShell apresenta o parâmetro do seu modelo com o pós-fixação **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** no seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) É-lhe solicitado que forneça um valor para **o ResourceGroupNameFromTemplate**. Pode evitar esta confusão utilizando nomes de parâmetros que não são utilizados para comandos de implantação.


## <a name="next-steps"></a>Passos seguintes

- Para entender como definir parâmetros no seu modelo, consulte [parâmetros nos modelos do Gestor de Recursos Azure](template-parameters.md).
- Para obter mais informações sobre a utilização de valores a partir de um cofre de chaves, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](key-vault-parameter.md).
- Para obter mais informações sobre os parâmetros, consulte [parâmetros nos modelos do Gestor de Recursos Azure](template-parameters.md).
