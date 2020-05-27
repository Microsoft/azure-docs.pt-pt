---
title: Criar ficheiro de parâmetros
description: Crie ficheiro de parâmetro para passar em valores durante a implementação de um modelo de Gestor de Recursos Azure
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a9845bbb9e14288a01fb7836db260a2baf484395
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873069"
---
# <a name="create-resource-manager-parameter-file"></a>Criar ficheiro de parâmetro sinuoso do Gestor de Recursos

Em vez de passar parâmetros como valores inline no seu script, poderá ser mais fácil utilizar um ficheiro JSON que contenha os valores do parâmetro. Este artigo mostra como criar o ficheiro de parâmetros.

## <a name="parameter-file"></a>Arquivo parâmetro

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

Note que os valores do parâmetro são armazenados como texto simples no ficheiro do parâmetro. Esta abordagem funciona para valores que não são sensíveis, como especificar o SKU para um recurso. Não funciona para valores sensíveis, como senhas. Se precisar passar um valor sensível como parâmetro, guarde o valor num cofre chave e faça referência ao cofre chave no seu ficheiro de parâmetros. O valor sensível é recuperado de forma segura durante a implantação.

O ficheiro de parâmetro si só inclui um valor de texto simples e um valor que é armazenado num cofre chave.

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

Para obter mais informações sobre a utilização de valores a partir de um cofre chave, consulte [use o Cofre chave Azure para passar o valor do parâmetro seguro durante](key-vault-parameter.md)a implementação .

## <a name="define-parameter-values"></a>Definir valores de parâmetros

Para descobrir como definir os valores do parâmetro, abra o modelo que está a implementar. Olhe para a secção de parâmetros do modelo. O exemplo que se segue mostra os parâmetros de um modelo.

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

O primeiro detalhe a notar é o nome de cada parâmetro. Os valores do seu ficheiro de parâmetros devem coincidir com os nomes.

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

Reparem no tipo de parâmetro. Os valores do seu ficheiro de parâmetros devem ter os mesmos tipos. Para este modelo, pode fornecer ambos os parâmetros como cordas.

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

Em seguida, procure um valor padrão. Se um parâmetro tiver um valor predefinido, pode fornecer um valor, mas não tem de o fazer.

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

Finalmente, olhe para os valores permitidos e quaisquer restrições como o comprimento máximo. Dizem-lhe o leque de valores que pode fornecer para o parâmetro.

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

## <a name="parameter-type-formats"></a>Formatos do tipo parâmetro

O exemplo que se segue mostra os formatos de diferentes tipos de parâmetros.

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

## <a name="file-name"></a>Nome de ficheiro

A convenção geral para nomear o ficheiro parâmetro é adicionar **.parâmetros** ao nome do modelo. Por exemplo, se o seu modelo for nomeado **azuredeploy.json,** o seu ficheiro de parâmetros é nomeado **azuredeploy.parameters.json**. Esta convenção de nomeação ajuda-o a ver a ligação entre o modelo e os parâmetros.

Para implantar em diferentes ambientes, crie mais de um ficheiro de parâmetros. Ao nomear o ficheiro parâmetro, adicione uma forma de identificar a sua utilização. Por exemplo, utilize **azuredeploy.parameters-dev.json** e **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Precedência do parâmetro

Pode utilizar parâmetros inline e um ficheiro de parâmetro local na mesma operação de implantação. Por exemplo, pode especificar alguns valores no ficheiro parâmetro local e adicionar outros valores inline durante a implementação. Se fornecer valores para um parâmetro tanto no ficheiro de parâmetro local como na linha de dentro, o valor inline tem precedência.

É possível usar um ficheiro de parâmetro externo, fornecendo o URI ao ficheiro. Quando faz isto, não pode passar outros valores, seja inline ou de um ficheiro local. Todos os parâmetros inline são ignorados. Forneça todos os valores dos parâmetros no ficheiro externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nome de parâmetros

Se o seu modelo incluir um parâmetro com o mesmo nome que um dos parâmetros do comando PowerShell, o PowerShell apresenta o parâmetro do seu modelo com o modelo de pós-fixação **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** no seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) É-lhe pedido que forneça um valor para **o ResourceGroupNameFromTemplate**. Pode evitar esta confusão utilizando nomes de parâmetros que não são utilizados para comandos de implantação.

## <a name="next-steps"></a>Passos seguintes

- Para entender como definir parâmetros no seu modelo, consulte [parâmetros nos modelos do Gestor](template-parameters.md)de Recursos Azure .
- Para obter mais informações sobre a utilização de valores a partir de um cofre chave, consulte [use o Cofre chave Azure para passar o valor do parâmetro seguro durante](key-vault-parameter.md)a implementação .
- Para obter mais informações sobre parâmetros, consulte [parâmetros nos modelos do Gestor de Recursos Azure](template-parameters.md).
