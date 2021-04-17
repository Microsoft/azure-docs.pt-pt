---
title: Criar ficheiro de parâmetros
description: Crie um ficheiro de parâmetros para passar em valores durante a implementação de um modelo de Gestor de Recursos Azure
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531216"
---
# <a name="create-resource-manager-parameter-file"></a>Criar ficheiro de parâmetro do Gestor de Recursos

Em vez de passar parâmetros como valores inline no seu script, pode utilizar um ficheiro JSON que contenha os valores dos parâmetros. Este artigo mostra como criar um ficheiro de parâmetro que utiliza com um modelo JSON ou ficheiro Bicep.

## <a name="parameter-file"></a>Arquivo de parâmetros

Um ficheiro de parâmetro utiliza o seguinte formato:

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

Note que o ficheiro de parâmetro armazena os valores dos parâmetros como texto simples. Esta abordagem funciona para valores que não são sensíveis, como um recurso SKU. Texto simples não funciona para valores sensíveis, como palavras-passe. Se precisar de passar um parâmetro que contenha um valor sensível, guarde o valor num cofre de chaves. Em seguida, faça referência ao cofre chave no seu ficheiro de parâmetros. O valor sensível é recuperado de forma segura durante a implantação.

O seguinte ficheiro de parâmetros inclui um valor de texto simples e um valor sensível que é armazenado num cofre de chaves.

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

Para determinar como definir os nomes e valores dos parâmetros, abra o seu modelo JSON ou Bicep. Veja a secção de parâmetros do modelo. Os exemplos a seguir mostram os parâmetros dos modelos JSON e Bicep.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

No ficheiro do parâmetro, o primeiro detalhe a notar é o nome de cada parâmetro. Os nomes dos parâmetros no seu ficheiro de parâmetro devem corresponder aos nomes dos parâmetros do seu modelo.

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

Note o tipo de parâmetro. Os tipos de parâmetros no seu ficheiro de parâmetro devem utilizar os mesmos tipos que o seu modelo. Neste exemplo, ambos os tipos de parâmetros são cordas.

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

Verifique se o modelo tem um valor padrão. Se um parâmetro tiver um valor padrão, pode fornecer um valor no ficheiro de parâmetros, mas não é necessário. O valor do ficheiro do parâmetro sobrepõe-se ao valor predefinido do modelo.

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

Verifique os valores permitidos do modelo e quaisquer restrições tais como o comprimento máximo. Estes valores especificam a gama de valores que pode prever para um parâmetro. Neste exemplo, `storagePrefix` pode ter um máximo de 11 caracteres e deve `storageAccountType` especificar um valor permitido.

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

> [!NOTE]
> O seu ficheiro de parâmetros só pode conter valores para parâmetros definidos no modelo. Se o seu ficheiro de parâmetros contiver parâmetros extras que não correspondem aos parâmetros do modelo, recebe um erro.

## <a name="parameter-type-formats"></a>Formatos de tipo de parâmetro

O exemplo a seguir mostra os formatos de diferentes tipos de parâmetros: string, inteiro, boolean, array e object.

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

A partir do CLI Azure, passa-se um ficheiro de parâmetro local utilizando `@` e o nome do ficheiro do parâmetro. Por exemplo, `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Para obter mais informações, consulte [implementar recursos com modelos ARM e Azure CLI](./deploy-cli.md#parameters). Para implementar ficheiros _.bicep,_ precisa da versão 2.20 ou superior do Azure CLI.

A partir de Azure PowerShell, passa-se um ficheiro de parâmetro local utilizando o `TemplateParameterFile` parâmetro.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Para obter mais informações, consulte [implementar recursos com modelos ARM e Azure PowerShell](./deploy-powershell.md#pass-parameter-values). Para implementar ficheiros _.bicep,_ precisa da versão 5.6.0 ou superior do Azure PowerShell.

> [!NOTE]
> Não é possível utilizar um ficheiro de parâmetro com a lâmina do modelo personalizada no portal.

> [!TIP]
> Se estiver a utilizar o [projeto Azure Resource Group no Visual Studio,](create-visual-studio-deployment-project.md)certifique-se de que o ficheiro parâmetro tem o seu conjunto **de Build Action** para **conteúdo**.

## <a name="file-name"></a>Nome de ficheiro

A convenção geral de nomeação para o ficheiro de parâmetros deve incluir _parâmetros_ no nome do modelo. Por exemplo, se o seu modelo for nomeado _azuredeploy.js,_ o seu ficheiro de parâmetros é nomeado _azuredeploy.parameters.jsem_. Esta convenção de nomeação ajuda-o a ver a ligação entre o modelo e os parâmetros.

Para implantar em diferentes ambientes, cria mais de um ficheiro de parâmetros. Quando nomear os ficheiros de parâmetros, identifique a sua utilização, como desenvolvimento e produção. Por exemplo, utilizar _azuredeploy.parameters-dev.js_ e _azuredeploy.parameters-prod.js_ para mobilizar recursos.

## <a name="parameter-precedence"></a>Precedência do parâmetro

Pode utilizar parâmetros inline e um ficheiro de parâmetro local na mesma operação de implantação. Por exemplo, pode especificar alguns valores no ficheiro de parâmetro local e adicionar outros valores inline durante a implementação. Se fornecer valores para um parâmetro tanto no arquivo de parâmetro local como em linha, o valor inline tem precedência.

É possível utilizar um ficheiro de parâmetro externo, fornecendo o URI ao ficheiro. Quando se utiliza um ficheiro de parâmetro externo, não é possível passar outros valores, quer em linha, quer a partir de um ficheiro local. Todos os parâmetros inline são ignorados. Forneça todos os valores dos parâmetros no ficheiro externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nomes de parâmetros

Se o seu modelo incluir um parâmetro com o mesmo nome que um dos parâmetros do comando PowerShell, o PowerShell apresenta o parâmetro do seu modelo com o pós-estafixo `FromTemplate` . Por exemplo, um parâmetro nomeado `ResourceGroupName` no seu modelo entra em conflito com o parâmetro no `ResourceGroupName` cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) É-lhe pedido que forneça um valor `ResourceGroupNameFromTemplate` para. Para evitar esta confusão, utilize nomes de parâmetros que não sejam utilizados para comandos de implantação.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre como definir parâmetros num modelo, consulte [Parâmetros nos modelos ARM](template-parameters.md).
- Para obter mais informações sobre a utilização de valores a partir de um cofre de chaves, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](key-vault-parameter.md).
