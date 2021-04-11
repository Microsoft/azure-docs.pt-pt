---
title: Estrutura de arquivo bicep e sintaxe
description: Descreve a estrutura e as propriedades de um ficheiro Bicep usando sintaxe declarativa.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168693"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Compreender a estrutura e sintaxe dos ficheiros Bicep

Este artigo descreve a estrutura de um ficheiro Bicep. Apresenta as diferentes secções do ficheiro e as propriedades que estão disponíveis nessas secções.

Este artigo destina-se a utilizadores que tenham alguma familiaridade com ficheiros Bicep. Fornece informações detalhadas sobre a estrutura do modelo. Para um tutorial passo a passo que o guia através do processo de criação de um ficheiro Bicep, consulte [Tutorial: Criar e implementar primeiro ficheiro Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Formato de modelo

Um ficheiro Bicep tem os seguintes elementos. Os elementos podem aparecer em qualquer ordem.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

O exemplo que se segue mostra uma implementação destes elementos.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Âmbito-alvo

Por predefinição, o âmbito de destino é definido para `resourceGroup` . Se estiver a implementar ao nível do grupo de recursos, não precisa de definir o âmbito de destino no seu ficheiro Bicep.

Os valores permitidos são:

* **resourceGroup** - valor predefinido, utilizado para [implementações de grupos de recursos](deploy-to-resource-group.md).
* **subscrição** - utilizada para [implementações de subscrição](deploy-to-subscription.md).
* **managementGroup** - utilizado para [implantações de grupos de gestão](deploy-to-management-group.md).
* **inquilino** - usado para [implantações de inquilinos](deploy-to-tenant.md).

## <a name="parameters"></a>Parâmetros

Utilize parâmetros para valores que precisam variar para diferentes implementações. Pode definir um valor predefinido para o parâmetro que é utilizado se não for fornecido qualquer valor durante a implementação.

Por exemplo, pode adicionar um parâmetro SKU para especificar diferentes tamanhos para um recurso. Pode utilizar funções de modelo para criar o valor predefinido, como obter a localização do grupo de recursos.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Para os tipos de dados disponíveis, consulte [os tipos de dados nos modelos.](data-types.md)

Para obter mais informações, consulte [Parâmetros em modelos.](template-parameters.md)

## <a name="parameter-decorators"></a>Decoradores de parâmetros

Pode adicionar um ou mais decoradores para cada parâmetro. Estes decoradores definem os valores que são permitidos para o parâmetro. O exemplo a seguir especifica os SKUs que podem ser implantados através do ficheiro Bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

A tabela a seguir descreve os decoradores disponíveis e como usá-los.

| Decorador | Aplicar a | Argumento | Description |
| --------- | ---- | ----------- | ------- |
| permitido | all | matriz | Valores permitidos para o parâmetro. Utilize este decorador para se certificar de que o utilizador fornece valores corretos. |
| descrição | all | string | Texto que explica como usar o parâmetro. A descrição é apresentada aos utilizadores através do portal. |
| maxLength | matriz, corda | int | O comprimento máximo para parâmetros de corda e matriz. O valor é inclusivo. |
| maxValue | int | int | O valor máximo para o parâmetro inteiro. Este valor é inclusivo. |
| do IdP | all | objeto | Propriedades personalizadas para aplicar ao parâmetro. Pode incluir uma propriedade de descrição que é equivalente ao decorador de descrição. |
| minLength | matriz, corda | int | O comprimento mínimo para parâmetros de corda e matriz. O valor é inclusivo. |
| minValue | int | int | O valor mínimo para o parâmetro inteiro. Este valor é inclusivo. |
| seguro | corda, objeto | nenhum | Marca o parâmetro como seguro. O valor para um parâmetro seguro não é guardado para o histórico de implantação e não está registado. Para obter mais informações, consulte [secure strings e objetos](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Variáveis

Use variáveis para expressões complexas que são repetidas num ficheiro Bicep. Por exemplo, pode adicionar uma variável para um nome de recurso que é construído através da concatenação de vários valores em conjunto.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Não se especifica um tipo de [dado para](data-types.md) uma variável. Em vez disso, o tipo de dados é deduzido do valor.

Para obter mais informações, consulte [Variáveis em modelos.](template-variables.md)

## <a name="resource"></a>Recurso

Utilize a `resource` palavra-chave para definir um recurso para implementar. A sua declaração de recursos inclui um nome simbólico para o recurso. Você usará este nome simbólico em outras partes do ficheiro Bicep se precisar de obter um valor do recurso.

A declaração de recursos também inclui o tipo de recurso e a versão API.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

Na sua declaração de recursos, inclui propriedades para o tipo de recurso. Estas propriedades são únicas para cada tipo de recurso.

Para obter mais informações, consulte [a declaração de recursos nos modelos.](resource-declaration.md)

Para [implementar um recurso condicionalmente,](conditional-resource-deployment.md)adicione uma `if` expressão.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Para [implementar mais de um tipo](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) de recurso, adicione uma `for` expressão. A expressão pode iterar sobre membros de uma matriz.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Módulos

Utilize módulos para ligar a outros ficheiros Bicep que contenham código que pretende reutilizar. O módulo contém um ou mais recursos para implementar. Esses recursos são mobilizados juntamente com quaisquer outros recursos no seu ficheiro Bicep.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

O nome simbólico permite-lhe fazer referência ao módulo a partir de outro lugar do ficheiro. Por exemplo, pode obter um valor de saída a partir de um módulo usando o nome simbólico e o nome do valor de saída.

Tal como os recursos, pode implementar um módulo condicional ou iterativamente. A sintaxe é a mesma para os módulos que os recursos.

Para obter mais informações, consulte [os módulos Bicep.](bicep-modules.md)

## <a name="resource-and-module-decorators"></a>Decoradores de recursos e módulos

Pode adicionar um decorador a uma definição de recurso ou módulo. O único decorador apoiado `batchSize(int)` é. Só é possível aplicá-lo a um recurso ou definição de módulo que utilize uma `for` expressão.

Por defeito, os recursos são implantados em paralelo. Não sabe a ordem pela qual terminam. Quando se adiciona o `batchSize` decorador, implementa-se as ocorrências em série. Utilize o argumento inteiro para especificar o número de casos a implementar em paralelo.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Para mais informações, consulte [Serial ou Parallel.](copy-resources.md#serial-or-parallel)

## <a name="outputs"></a>Saídas

Utilize saídas para devolver valor da implementação. Normalmente, devolve-se um valor a partir de um recurso implantado quando é necessário reutilizar esse valor para outra operação.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Especifique um [tipo de dados](data-types.md) para o valor de saída.

Para obter mais informações, consulte [outputs em modelos.](template-outputs.md)

## <a name="comments"></a>Comentários

Utilização `//` para comentários de linha única ou para comentários `/* ... */` multi-linhas

O exemplo a seguir mostra um comentário de uma única linha.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

O exemplo a seguir mostra um comentário multi-line.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Cordas multi-linha

Pode partir uma corda em várias linhas. Utilize três caracteres de citação única `'''` para iniciar e terminar a cadeia multi-linha. 

Os caracteres dentro da corda multi-linha são manuseados como-é. Os personagens de fuga são desnecessários. Não pode incluir `'''` na corda multi-linha. A interpolação de cordas não é suportada atualmente.

Pode iniciar a corda logo após a abertura `'''` ou incluir uma nova linha. Em qualquer dos casos, a cadeia resultante não inclui uma nova linha. Dependendo das terminações de linha no seu ficheiro Bicep, novas linhas são interpretadas como `\r\n` ou `\n` .

O exemplo a seguir mostra uma corda multi-linha.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

O exemplo anterior é equivalente ao seguinte JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Passos seguintes

Para uma introdução a Bicep, veja [o que é Bicep?](bicep-overview.md)
