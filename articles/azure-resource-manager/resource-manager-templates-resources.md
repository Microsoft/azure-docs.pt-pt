---
title: Recursos de modelo do Azure Resource Manager | Documentos da Microsoft
description: Descreve a seção de recursos de modelos Azure Resource Manager usando sintaxe declarativa do JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2019
ms.author: tomfitz
ms.openlocfilehash: eb819d19dd7c1eb9cf0b95549adda91a102304a5
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593901"
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Secção de recursos de modelos Azure Resource Manager

A seção de recursos, vai definir os recursos que são implementados ou atualizados. Esta secção pode são complicada, uma vez que deve compreender os tipos que estiver a implementar para fornecer os valores de certos.

## <a name="available-properties"></a>Propriedades disponíveis

Define os recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| condition | Não | Valor booleano que indica se o recurso será provisionado durante esta implementação. Quando `true`, o recurso é criado durante a implementação. Quando `false`, o recurso é ignorado para esta implementação. |
| apiVersion |Sim |Versão da API REST para utilizar para criar o recurso. |
| tipo |Sim |Tipo de recurso. Este valor é uma combinação do espaço de nomes do fornecedor de recursos e o tipo de recurso (por exemplo, **Storage/storageaccounts**). Para obter mais informações, consulte [fornecedores de recursos do Azure e tipos de](resource-manager-supported-services.md). |
| nome |Sim |Nome do recurso. O nome tem de seguir restrições de componente URI definidas na RFC3986. Além disso, os serviços do Azure que expõem o nome do recurso fora partes validar o nome para garantir que ela não é uma tentativa para falsificar a identidade de outra. |
| localização |Varia |Georreplicação-localizações suportadas do recurso fornecido. Pode selecionar qualquer uma das localizações disponíveis, mas geralmente faz sentido escolher um que está perto dos seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos exigem uma localização, mas alguns tipos (por exemplo, uma atribuição de função) não precisam de uma localização. |
| etiquetas |Não |Etiquetas de associado ao recurso. Aplica etiquetas para organizar logicamente os recursos na sua subscrição. |
| comentários |Não |Suas anotações para documentar os recursos no seu modelo. Para obter mais informações, consulte [comentários em modelos](resource-group-authoring-templates.md#comments). |
| copiar |Não |Se for necessário mais de uma instância, o número de recursos para criar. O modo predefinido é paralelo. Especifique o modo serial quando não quiser que todos os ou os recursos necessários para implementar ao mesmo tempo. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implementados para que este recurso está implementado. Resource Manager avalia as dependências entre os recursos e implementa-as na ordem correta. Quando os recursos não são dependentes entre si, serem implementadas em paralelo. O valor pode ser uma lista separada por vírgulas de um recurso nomes ou identificadores exclusivos de recursos. Lista apenas os recursos que são implementados neste modelo. Recursos que não sejam definidos neste modelo tem de existir. Evite a adição de dependências desnecessárias como podem atrasar a implantação e criar dependências circulares. Para obter orientações sobre as dependências de definição, consulte [definir dependências nos modelos Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração de recursos específicos. Os valores para as propriedades são os mesmos que os valores que fornecer no corpo do pedido para a operação de REST API (método PUT) criar o recurso. Também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. |
| sku | Não | Alguns recursos permitem que os valores que definem o SKU para implementar. Por exemplo, pode especificar o tipo de redundância para uma conta de armazenamento. |
| tipo | Não | Alguns recursos permitem que um valor que define o tipo de recurso que implementa. Por exemplo, pode especificar o tipo do Cosmos DB para criar. |
| plano | Não | Alguns recursos permitem que os valores que definem o plano de implementar. Por exemplo, pode especificar a imagem do marketplace para uma máquina virtual. | 
| recursos |Não |Recursos de subordinados que dependem do recurso que está a ser definido. Fornece apenas os tipos de recursos que são permitidos pelo esquema do recurso principal. O tipo totalmente qualificado do recurso subordinado inclui o tipo de recurso principal, como **Microsoft.Web/sites/extensions**. Dependência do recurso principal não está implícita. Tem de definir explicitamente essa dependência. |

## <a name="condition"></a>Condição

Quando tem de decidir durante a implementação se deve ou não criar um recurso, utilize o `condition` elemento. O valor para este elemento é resolvido para true ou false. Quando o valor for VERDADEIRO, o recurso é criado. Quando o valor for FALSO, o recurso não é criado. O valor só pode ser aplicado a todo o recurso.

Normalmente, utiliza este valor quando pretender criar um novo recurso ou utilize um já existente. Por exemplo, para especificar se é implementada uma nova conta de armazenamento ou uma conta de armazenamento existente, utilize:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para um modelo de exemplo completo que utilize o `condition` elemento, consulte [VM com uma rede Virtual nova ou existente, o armazenamento e o IP público](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="resource-specific-values"></a>Valores de recursos específicos

O **apiVersion**, **tipo**, e **propriedades** elementos são diferentes para cada tipo de recurso. O **sku**, **tipo**, e **plano** elementos estão disponíveis para alguns tipos de recursos, mas não todos. Para determinar os valores para estas propriedades, veja [referência de modelo](/azure/templates/).

## <a name="resource-names"></a>Nomes de recursos

Em geral, trabalha com três tipos de nomes de recursos no Gestor de recursos:

* Nomes de recursos tem de ser exclusivos.
* Os nomes de recursos que não são necessárias para que seja exclusivo, mas optar por fornecer um nome que pode ajudá-lo a identificar o recurso.
* Nomes de recursos que podem ser genéricos.

### <a name="unique-resource-names"></a>Nomes de recurso exclusiva

Forneça um nome de recurso exclusiva para qualquer tipo de recurso que tem um ponto de final de acesso de dados. Alguns tipos de recursos comuns que necessitam de um nome exclusivo incluem:

* O armazenamento do Azure<sup>1</sup> 
* Funcionalidade de Aplicações Web no Serviço de Aplicações do Azure
* SQL Server
* Azure Key Vault
* Cache do Azure para Redis
* Azure Batch
* Traffic Manager do Azure
* Azure Search
* Azure HDInsight

<sup>1</sup> nomes de conta de armazenamento também tem de estar em minúsculas, 24 carateres ou menos, e não tem qualquer hífenes.

Ao definir o nome, pode manualmente criar um nome exclusivo ou utilizar o [uniqueString()](resource-group-template-functions-string.md#uniquestring) função para gerar um nome. Pode também querer adicionar um prefixo ou sufixo para a **uniqueString** resultado. Modificar o nome exclusivo pode ajudá-lo a mais fácil identificar o tipo de recurso do nome. Por exemplo, pode gerar um nome exclusivo para uma conta de armazenamento ao utilizar a seguinte variável:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Nomes de recursos para fins de identificação
Alguns tipos de recursos, que pode querer nome, mas seus nomes não tem de ser exclusivo. Para estes tipos de recursos, pode fornecer um nome que identifica o contexto de recursos e o tipo de recurso.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Nomes de recurso genérico
Para tipos de recursos que acessar principalmente por meio de um recurso diferente, pode utilizar um nome genérico que está hard-coded no modelo. Por exemplo, pode definir um nome padrão, genérico para regras de firewall num SQL server:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Localização
Quando implementar um modelo, tem de fornecer uma localização de cada recurso. Diferentes tipos de recursos são suportados em localizações diferentes. Para obter as localizações suportadas para um tipo de recurso, consulte [fornecedores de recursos do Azure e tipos de](resource-manager-supported-services.md).

Utilizar um parâmetro para especificar a localização para os recursos e defina o valor predefinido `resourceGroup().location`.

O exemplo seguinte mostra uma conta de armazenamento que é implementada para uma localização especificada como um parâmetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

Se precisar de codificar a localização no seu modelo, forneça o nome de uma das regiões suportadas. O exemplo seguinte mostra uma conta de armazenamento que sempre é implementada nos e.u.a. Centro-Norte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Etiquetas
[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

### <a name="add-tags-to-your-template"></a>Adicionar etiquetas ao seu modelo

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Recursos subordinados

Dentro de alguns tipos de recursos, também pode definir uma matriz de recursos subordinados. Recursos subordinados são recursos que existem apenas dentro do contexto de outro recurso. Por exemplo, uma base de dados SQL não pode existir sem um SQL server para a base de dados é um filho do servidor. Pode definir a base de dados dentro da definição do servidor.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Quando forem aninhadas, o tipo está definido como `databases` mas o tipo de recurso completo é `Microsoft.Sql/servers/databases`. Não fornecer `Microsoft.Sql/servers/` porque é suposto o pai do tipo de recurso. O nome do recurso subordinado está definido como `exampledatabase` , mas o nome completo inclui o nome do principal. Não fornecer `exampleserver` porque é suposto do recurso principal.

O formato do tipo de recurso subordinado é: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

O formato do nome do recurso subordinado é: `{parent-resource-name}/{child-resource-name}`

No entanto, não precisa de definir a base de dados no servidor. Pode definir os recursos filho no nível superior. Pode usar essa abordagem se o recurso de principal não está implementado no mesmo modelo, ou se pretende utilizar `copy` para criar mais do que um recurso de subordinados. Com esta abordagem, tem de fornecer o tipo de recurso completo e incluir o nome do recurso principal no nome do recurso subordinado.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Ao construir uma referência completamente qualificada para um recurso, a ordem para combinar os segmentos do tipo e o nome não é simplesmente uma concatenação das duas. Em vez disso, depois do espaço de nomes, utilize uma seqüência de *nome do tipo* pares de menos específicos para a mais específica:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Por exemplo:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` está correto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` não está correto



## <a name="next-steps"></a>Passos Seguintes
* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para obter recomendações sobre a criação de modelos, veja [práticas recomendadas do modelo do Azure Resource Manager](template-best-practices.md).
* Poderá ter de utilizar recursos que existem dentro de um grupo de recursos diferente. Este cenário é comum ao trabalhar com redes virtuais que são partilhadas entre vários grupos de recursos ou contas de armazenamento. Para obter mais informações, consulte a [resourceId função](resource-group-template-functions-resource.md#resourceid).
* Para obter informações sobre restrições de nome de recurso, consulte [convenções de nomenclatura para recursos do Azure recomendadas](../guidance/guidance-naming-conventions.md).
