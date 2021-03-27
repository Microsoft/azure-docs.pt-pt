---
title: Módulos Bicep
description: Descreve como definir e consumir um módulo e como usar os âmbitos do módulo.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611740"
---
# <a name="use-bicep-modules-preview"></a>Utilize módulos Bicep (Pré-visualização)

O Bicep permite-lhe decompor uma solução complexa em módulos. Um módulo Bicep é um conjunto de um ou mais recursos a serem implantados em conjunto. Módulos abstratos detalhes complexos da declaração de recursos brutos, que podem aumentar a legibilidade. Podes reutilizar estes módulos e partilhá-los com outras pessoas. Combinado com [as especificações do modelo,](./template-specs.md)cria uma forma de modularidade e reutilização de códigos. Para um tutorial, consulte [Tutorial: Adicione módulos Bicep](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definir módulos

Todos os ficheiros Bicep podem ser consumidos como um módulo. Um módulo expõe apenas parâmetros e saídas como contrato a outros ficheiros Bicep. Ambos os parâmetros e saídas são opcionais.

O seguinte ficheiro Bicep pode ser implantado diretamente para criar uma conta de armazenamento ou ser usado como um módulo.  A próxima secção mostra-lhe como consumir módulos:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

A saída é usada para passar valores para os ficheiros Bicep dos pais.

## <a name="consume-modules"></a>Consumir módulos

Utilize a palavra-chave do _módulo_ para consumir um módulo. O seguinte ficheiro Bicep implementa o recurso definido no ficheiro do módulo que está a ser referenciado:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **módulo**: Palavra-chave.
- **nome simbólico** (stgModule): Identificador para o módulo.
- **ficheiro do módulo**: O caminho para o módulo neste exemplo é especificado utilizando um caminho relativo (./storageAccount.bicep). Todos os caminhos em Bicep devem ser especificados utilizando o separador de diretório de corte dianteiro (/) para garantir uma plataforma transversal de compilação consistente. O carácter de backslash do Windows \\ () não é suportado.
- A propriedade **_do nome_** (storageDeploy) é necessária ao consumir um módulo. Quando o Bicep gera o modelo IL, este campo é usado como o nome do recurso de implantação aninhado, que é gerado para o módulo:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Para obter um valor de saída de um módulo, recupere o valor da propriedade com sintaxe como: `stgModule.outputs.storageEndpoint` onde `stgModule` está o identificador do módulo.

## <a name="configure-module-scopes"></a>Configurar os âmbitos dos módulos

Ao declarar um módulo, pode fornecer uma propriedade _de âmbito_ para definir o âmbito para implantar o módulo:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

A propriedade _de âmbito_ pode ser omitida quando o âmbito alvo do módulo e o âmbito alvo do progenitor são os mesmos. Quando a propriedade de âmbito não é fornecida, o módulo é implantado no âmbito alvo do progenitor.

O seguinte ficheiro Bicep mostra como criar um grupo de recursos e implementar um módulo para o grupo de recursos:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Passos seguintes

- Para passar por um tutorial, consulte [Tutorial: Adicione módulos Bicep](./bicep-tutorial-add-modules.md).
