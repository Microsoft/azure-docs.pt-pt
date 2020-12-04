---
title: Teste de caixas para kit de ferramentas de teste
description: Descreve os testes que são executados pelo kit de ferramentas de teste do modelo ARM.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: ff9ad659e15a88725e4c3905ab6c623fda7610fd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600909"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Casos de teste predefinidos para o kit de ferramentas de teste do modelo ARM

Este artigo descreve os testes predefinidos que são executados com o [kit de ferramentas de teste do modelo](test-toolkit.md). Fornece exemplos que passam ou falham no teste. Inclui o nome de cada teste.

## <a name="use-correct-schema"></a>Use o esquema correto

Nome do teste: **ImplantaçãoSteplate Schema está correto**

No seu modelo, deve especificar um valor de esquema válido.

O exemplo que **se segue passa** neste teste.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

A propriedade do esquema no modelo deve ser definida como um dos seguintes esquemas:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Os parâmetros devem existir

Nome do teste: **Parâmetros Propriedade deve existir**

O seu modelo deve ter um elemento de parâmetros. Os parâmetros são essenciais para tornar os seus modelos reutilizáveis em diferentes ambientes. Adicione parâmetros ao seu modelo para valores que mudam ao implementar em diferentes ambientes.

O seguinte exemplo **passa** neste teste:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Os parâmetros declarados devem ser utilizados

Nome do teste: **Os parâmetros devem ser referenciados**

Para reduzir a confusão no seu modelo, elimine quaisquer parâmetros definidos mas não utilizados. Este teste encontra quaisquer parâmetros que não sejam usados em qualquer lugar do modelo. Eliminar parâmetros não utilizados também facilita a implementação do seu modelo porque não tem de fornecer valores desnecessários.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Parâmetros seguros não podem ter padrão codificado

Nome do teste: **Parâmetros de cadeia seguros não podem ter predefinição**

Não forneça um valor predefinido codificado para um parâmetro seguro no seu modelo. Uma corda vazia é boa para o valor padrão.

Utiliza os tipos **SecureString** ou **SecureObject** em parâmetros que contêm valores sensíveis, como palavras-passe. Quando um parâmetro utiliza um tipo seguro, o valor do parâmetro não é registado ou armazenado no histórico de implantação. Esta ação impede que um utilizador malicioso descubra o valor sensível.

No entanto, quando fornece um valor padrão, esse valor é detetável por qualquer pessoa que possa aceder ao modelo ou ao histórico de implementação.

O exemplo a seguir **falha** neste teste:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

O próximo exemplo **passa** neste teste:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>UrLs ambientais não podem ser codificados

Nome do teste: **DeploymentTemplate Must Not Contain Hardcoded Uri**

Não faça o hardcode ambiente URLs no seu modelo. Em vez disso, utilize a [função ambiente](template-functions-deployment.md#environment) para obter dinamicamente estes URLs durante a implantação. Para obter uma lista dos anfitriões url que estão bloqueados, consulte o [caso de teste](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

O exemplo a seguir **falha** neste teste porque o URL está codificado.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

O teste também **falha** quando utilizado com [concat](template-functions-string.md#concat) ou [uri](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

O exemplo que **se segue passa** neste teste.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Localização usa parâmetro

Nome do teste: **Localização não deve ser codificada**

Os seus modelos devem ter um parâmetro chamado localização. Utilize este parâmetro para definir a localização dos recursos no seu modelo. No modelo principal (nomeado azuredeploy.jsem ou mainTemplate.js), este parâmetro pode predefinir a localização do grupo de recursos. Em modelos ligados ou aninhados, o parâmetro de localização não deve ter uma localização padrão.

Os utilizadores do seu modelo podem ter regiões limitadas à sua disposição. Quando codificar a localização do recurso, os utilizadores podem estar impedidos de criar um recurso nessa região. Os utilizadores podem ser bloqueados mesmo que desemalte a localização do recurso para `"[resourceGroup().location]"` . O grupo de recursos pode ter sido criado numa região a que outros utilizadores não podem aceder. Esses utilizadores estão impedidos de usar o modelo.

Ao fornecer um parâmetro de localização que predefini a localização do grupo de recursos, os utilizadores podem usar o valor padrão quando conveniente, mas também especificar uma localização diferente.

O exemplo a seguir **falha** neste teste porque a localização do recurso está definida para `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

O próximo exemplo usa um parâmetro de localização, mas **falha** neste teste porque o parâmetro de localização é padrão para um local codificado.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Em vez disso, crie um parâmetro que predefini a localização do grupo de recursos, mas permite que os utilizadores forneçam um valor diferente. O exemplo a seguir **passa** neste teste quando o modelo é usado como o modelo principal.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

No entanto, se o exemplo anterior for utilizado como modelo ligado, o teste **falha**. Quando usado como um modelo ligado, remova o valor predefinido.

## <a name="resources-should-have-location"></a>Os recursos devem ter localização

Nome do teste: **Recursos devem ter localização**

A localização de um recurso deve ser definida como uma [expressão de modelo](template-expressions.md) ou `global` . A expressão do modelo normalmente usaria o parâmetro de localização descrito no teste anterior.

O exemplo a seguir **falha** neste teste porque a localização não é uma expressão ou `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

O exemplo que **se segue passa** neste teste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

O próximo exemplo também **passa** neste teste.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>O tamanho do VM usa o parâmetro

Nome do teste: **Tamanho VM deve ser um parâmetro**

Não descodifique o tamanho da máquina virtual. Forneça um parâmetro para que os utilizadores do seu modelo possam modificar o tamanho da máquina virtual implantada.

O exemplo a seguir **falha** neste teste.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Em vez disso, forneça um parâmetro.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Em seguida, ajuste o tamanho VM para este parâmetro.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Valores min e máximo são números

Nome do teste: **Valor Min e Max São Números**

Se definir valores min e max para um parâmetro, especifique-os como números.

O exemplo a seguir **falha** neste teste:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Em vez disso, forneça os valores como números. O seguinte exemplo **passa** neste teste:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Você também recebe este aviso se fornecer um valor min ou máximo, mas não o outro.

## <a name="artifacts-parameter-defined-correctly"></a>Parâmetro de artefactos definido corretamente

Nome do teste: **parâmetro de artefactos**

Quando incluir parâmetros para `_artifactsLocation` `_artifactsLocationSasToken` e, utilize os predefinidos e os tipos corretos. Devem ser reunidas as seguintes condições para passar neste ensaio:

* se fornecer um parâmetro, deve fornecer o outro
* `_artifactsLocation` deve ser uma **corda**
* `_artifactsLocation` deve ter um valor padrão no modelo principal
* `_artifactsLocation` não pode ter um valor padrão em um modelo aninhado 
* `_artifactsLocation` deve ter um `"[deployment().properties.templateLink.uri]"` ou o URL de repo cru para o seu valor padrão
* `_artifactsLocationSasToken` deve ser um **secureString**
* `_artifactsLocationSasToken` só pode ter uma corda vazia para o seu valor padrão
* `_artifactsLocationSasToken` não pode ter um valor padrão em um modelo aninhado 

## <a name="declared-variables-must-be-used"></a>Variáveis declaradas devem ser utilizadas

Nome do teste: **Variáveis devem ser referenciadas**

Para reduzir a confusão no seu modelo, elimine quaisquer variáveis que sejam definidas mas não utilizadas. Este teste encontra quaisquer variáveis que não são usadas em qualquer lugar do modelo.

## <a name="dynamic-variable-should-not-use-concat"></a>Variável dinâmica não deve usar concat

Nome do teste: **Referências variáveis dinâmicas não devem usar Concat**

Às vezes é preciso construir dinamicamente uma variável com base no valor de outra variável ou parâmetro. Não utilize a função [concat](template-functions-string.md#concat) ao definir o valor. Em vez disso, utilize um objeto que inclua as opções disponíveis e obtenha dinamicamente uma das propriedades do objeto durante a implementação.

O exemplo que **se segue passa** neste teste. A **variável ActualImage** é definida dinamicamente durante a implementação.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Use a versão API recente

Nome do teste: **apiVersions should be Recent**

A versão API para cada recurso deve utilizar uma versão recente. O teste avalia a versão que utiliza contra as versões disponíveis para esse tipo de recurso.

## <a name="use-hardcoded-api-version"></a>Use a versão API codificada

Nome do teste: **Não são permitidas apiversões de fornecedores**

A versão API para um tipo de recurso determina quais as propriedades disponíveis. Forneça uma versão API codificada duramente no seu modelo. Não recupere uma versão API que seja determinada durante a implementação. Não saberá quais as propriedades disponíveis.

O exemplo a seguir **falha** neste teste.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

O exemplo que **se segue passa** neste teste.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>As propriedades não podem estar vazias

Nome do teste: **Modelo não deve conter espaços em branco**

Não descodifique propriedades a um valor vazio. Os valores vazios incluem cordas, objetos ou matrizes nulos e vazios. Se você definiu uma propriedade para um valor vazio, remova essa propriedade do seu modelo. No entanto, não há problema em definir uma propriedade para um valor vazio durante a implementação, como através de um parâmetro.

## <a name="use-resource-id-functions"></a>Use funções de ID de recursos

Nome do teste: **IDs devem ser derivados de recursos**

Ao especificar um ID de recurso, utilize uma das funções de ID de recursos. As funções permitidas são:

* [recursosId](template-functions-resource.md#resourceid)
* [subscriçãoResourceId](template-functions-resource.md#subscriptionresourceid)
* [inquilinoResourceId](template-functions-resource.md#tenantresourceid)
* [extensãoResourceId](template-functions-resource.md#extensionresourceid)

Não utilize a função concat para criar um ID de recurso. O exemplo a seguir **falha** neste teste.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

O próximo exemplo **passa** neste teste.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Função ResourceId tem parâmetros corretos

Nome do teste: **Os ResourceIds não devem conter**

Ao gerar IDs de recursos, não utilize funções desnecessárias para parâmetros opcionais. Por predefinição, a função [resourceId](template-functions-resource.md#resourceid) utiliza o grupo de subscrição e recursos atuais. Não precisas de fornecer esses valores.  

O exemplo a seguir **falha** neste teste, porque não precisa de fornecer o ID de subscrição atual e o nome do grupo de recursos.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

O próximo exemplo **passa** neste teste.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Este teste aplica-se a:

* [recursosId](template-functions-resource.md#resourceid)
* [subscriçãoResourceId](template-functions-resource.md#subscriptionresourceid)
* [inquilinoResourceId](template-functions-resource.md#tenantresourceid)
* [extensãoResourceId](template-functions-resource.md#extensionresourceid)
* [referência](template-functions-resource.md#reference)
* [lista*](template-functions-resource.md#list)

Para `reference` `list*` e, o teste **falha** quando se utiliza `concat` para construir o ID do recurso.

## <a name="dependson-best-practices"></a>depende Das melhores práticas

Nome do teste: **Depende das melhores práticas**

Ao definir as dependências de implantação, não utilize a função [se](template-functions-logical.md#if) funcionar para testar uma condição. Se um recurso depender de um recurso que esteja [implementado condicionalmente,](conditional-resource-deployment.md)desloque a dependência como faria com qualquer recurso. Quando um recurso condicional não é implantado, o Azure Resource Manager remove-o automaticamente das dependências necessárias.

O exemplo a seguir **falha** neste teste.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

O próximo exemplo **passa** neste teste.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Implementações aninhadas ou ligadas não podem usar depuração

Nome do teste: **Recursos de implantação não devem ser depuros**

Quando definir um [modelo aninhado ou ligado](linked-templates.md) com o tipo de recurso **Microsoft.Resources/Deployments,** pode ativar a depuração para esse modelo. Depurar é bom quando você precisa testar esse modelo, mas deve ser virado quando você está pronto para usar o modelo em produção.

## <a name="admin-user-names-cant-be-literal-value"></a>Nomes de utilizadores de administração não podem ser valor literal

Nome do teste: **adminUsername Não deve ser um literal**

Ao definir um nome de utilizador administrativo, não utilize um valor literal.

O exemplo a seguir **falha** neste teste:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Em vez disso, use um parâmetro. O seguinte exemplo **passa** neste teste:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Use a imagem VM mais recente

Nome do teste: **Imagens VM devem usar a versão mais recente**

Se o seu modelo incluir uma máquina virtual com uma imagem, certifique-se de que está a usar a versão mais recente da imagem.

## <a name="use-stable-vm-images"></a>Use imagens VM estáveis

Nome do teste: **Máquinas virtuais não devem ser pré-visualização**

As máquinas virtuais não devem utilizar imagens de pré-visualização.

O exemplo a seguir **falha** neste teste.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

O exemplo que **se segue passa** neste teste.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Não utilize a extensão ManagedIdentity

Nome do teste: **ManagedIdentityExtension não deve ser utilizada**

Não aplique a extensão ManagedIdentity a uma máquina virtual. Para obter mais informações, consulte [Como parar de usar a extensão de identidades geridas pela máquina virtual e comece a utilizar o Serviço de Metadados de Instância Azure](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>As saídas não podem incluir segredos

Nome do teste: **Saídas não devem conter segredos**

Não inclua quaisquer valores na secção de saídas que possam expor segredos. A saída de um modelo é armazenada no histórico de implementação, para que um utilizador malicioso possa encontrar essa informação.

O exemplo a seguir **falha** no teste porque inclui um parâmetro seguro num valor de saída.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

O exemplo a seguir **falha** porque utiliza uma função [de lista*](template-functions-resource.md#list) nas saídas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

Para aprender a executar o kit de ferramentas de teste, consulte [o kit de ferramentas de teste do modelo ARM](test-toolkit.md).