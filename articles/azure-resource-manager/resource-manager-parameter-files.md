---
title: Criar ficheiro de parâmetros
description: Criar arquivo de parâmetro para passar valores durante a implantação de um modelo de Azure Resource Manager
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 7333e33af90ff7883b53a24bacdc63b42bf107ee
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149449"
---
# <a name="create-resource-manager-parameter-file"></a>Criar arquivo de parâmetro do Resource Manager

Em vez de passar parâmetros como valores embutidos em seu script, você pode achar mais fácil usar um arquivo JSON que contenha os valores de parâmetro. Este artigo mostra como criar o arquivo de parâmetro.

## <a name="parameter-file"></a>Ficheiro de parâmetros

O arquivo de parâmetro tem o seguinte formato:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Observe que os valores de parâmetro são armazenados como texto sem formatação no arquivo de parâmetro. Essa abordagem funciona para valores que não são confidenciais, como especificar a SKU para um recurso. Ele não funciona para valores confidenciais, como senhas. Se você precisar passar um valor confidencial como um parâmetro, armazene o valor em um cofre de chaves e faça referência ao cofre de chaves em seu arquivo de parâmetro. O valor confidencial é recuperado com segurança durante a implantação.

O arquivo de parâmetro a seguir inclui um valor de texto sem formatação e um valor que é armazenado em um cofre de chaves.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Para obter mais informações sobre como usar valores de um cofre de chaves, consulte [usar Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](resource-manager-keyvault-parameter.md).

## <a name="define-parameter-values"></a>Definir valores de parâmetro

Para descobrir como definir os valores de parâmetro, abra o modelo que você está implantando. Examine a seção de parâmetros do modelo. O exemplo a seguir mostra os parâmetros de um modelo.

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

O primeiro detalhe a ser observado é o nome de cada parâmetro. Os valores em seu arquivo de parâmetro devem corresponder aos nomes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
        },
        "storageAccountType": {
        }
    }
}
```

Observe o tipo do parâmetro. Os valores em seu arquivo de parâmetro devem ter os mesmos tipos. Para este modelo, você pode fornecer ambos os parâmetros como cadeias de caracteres.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Em seguida, procure um valor padrão. Se um parâmetro tiver um valor padrão, você poderá fornecer um valor, mas não precisará.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Por fim, examine os valores permitidos e as restrições como comprimento máximo. Eles informam o intervalo de valores que você pode fornecer para o parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

## <a name="parameter-type-formats"></a>Formatos de tipo de parâmetro

O exemplo a seguir mostra os formatos de tipos de parâmetros diferentes.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

A Convenção geral para nomear o arquivo de parâmetro é adicionar **. Parameters** ao nome do modelo. Por exemplo, se seu modelo for denominado **azuredeploy. JSON**, o arquivo de parâmetro será chamado **azuredeploy. Parameters. JSON**. Essa convenção de nomenclatura ajuda a ver a conexão entre o modelo e os parâmetros.

Para implantar em ambientes diferentes, crie mais de um arquivo de parâmetro. Ao nomear o arquivo de parâmetro, adicione uma maneira de identificar seu uso. Por exemplo, use **azuredeploy. Parameters-dev. JSON** e **azuredeploy. Parameters-prod. JSON**


## <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetro local e embutido, o valor embutido terá precedência.

No entanto, quando você usa um arquivo de parâmetro externo, não pode passar outros valores embutidos ou de um arquivo local. Todos os parâmetros embutidos são ignorados. Forneça todos os valores de parâmetro no arquivo externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nome de parâmetro

Se o modelo incluir um parâmetro com o mesmo nome de um dos parâmetros no comando do PowerShell, o PowerShell apresentará o parâmetro do seu modelo com o sufixo **detemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo está em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Você será solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Você pode evitar essa confusão usando nomes de parâmetros que não são usados para comandos de implantação.

## <a name="next-steps"></a>Passos seguintes

- Para entender como definir parâmetros em seu modelo, consulte [parâmetros em modelos de Azure Resource Manager](template-parameters.md).
- Para obter mais informações sobre como usar valores de um cofre de chaves, consulte [usar Azure Key Vault para passar um valor de parâmetro seguro durante a implantação](resource-manager-keyvault-parameter.md).
- Para obter mais informações sobre parâmetros, consulte [parâmetros em modelos de Azure Resource Manager](template-parameters.md).
