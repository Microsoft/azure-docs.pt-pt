---
title: Estrutura e sintaxe do modelo de Azure Resource Manager | Microsoft Docs
description: Descreve a estrutura e as propriedades de modelos de Azure Resource Manager usando a sintaxe JSON declarativa.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: tomfitz
ms.openlocfilehash: 8fe665ed9a9c580f5ce7d7bf43e71b9672a2bc5b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075037"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Entender a estrutura e a sintaxe de modelos de Azure Resource Manager

Este artigo descreve a estrutura de um modelo de Azure Resource Manager. Ele apresenta as diferentes seções de um modelo e as propriedades que estão disponíveis nessas seções.

Este artigo destina-se a usuários que têm alguma familiaridade com modelos do Resource Manager. Ele fornece informações detalhadas sobre a estrutura do modelo. Para obter um tutorial passo a passo que orienta você durante o processo de criação de um modelo, consulte [tutorial: criar e implantar seu primeiro modelo de Azure Resource Manager](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Formato do modelo

Em sua estrutura mais simples, um modelo tem os seguintes elementos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| $schema |Sim |Local do arquivo de esquema JSON que descreve a versão do idioma do modelo.<br><br> Para implantações de grupo de recursos, use: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implantações de assinatura, use: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |Sim |Versão do modelo (como 1.0.0.0). Você pode fornecer qualquer valor para este elemento. Use esse valor para documentar alterações significativas em seu modelo. Ao implantar recursos usando o modelo, esse valor pode ser usado para garantir que o modelo correto esteja sendo usado. |
| apiProfile |Não | Uma versão de API que serve como uma coleção de versões de API para tipos de recursos. Use esse valor para evitar a especificação de versões de API para cada recurso no modelo. Quando você especifica uma versão de perfil de API e não especifica uma versão de API para o tipo de recurso, o Resource Manager usa a versão de API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil de API é especialmente útil ao implantar um modelo em ambientes diferentes, como Azure Stack e o Azure global. Use a versão do perfil de API para certificar-se de que o modelo usa automaticamente as versões com suporte em ambos os ambientes. Para obter uma lista das versões de perfil de API atuais e as versões de API de recursos definidas no perfil, consulte [perfil de API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [rastrear versões usando perfis de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Não |Valores que são fornecidos quando a implantação é executada para personalizar a implantação de recursos. |
| [variables](#variables) |Não |Valores que são usados como fragmentos JSON no modelo para simplificar as expressões de linguagem do modelo. |
| [funções](#functions) |Não |Funções definidas pelo usuário que estão disponíveis no modelo. |
| [os](#resources) |Sim |Tipos de recursos que são implantados ou atualizados em um grupo de recursos ou assinatura. |
| [produz](#outputs) |Não |Valores retornados após a implantação. |

Cada elemento tem propriedades que você pode definir. Este artigo descreve as seções do modelo com mais detalhes.

## <a name="parameters"></a>Parâmetros

Na seção de parâmetros do modelo, você especifica quais valores você pode inserir ao implantar os recursos. Você está limitado a 256 parâmetros em um modelo. Você pode reduzir o número de parâmetros usando objetos que contêm várias propriedades.

As propriedades disponíveis para um parâmetro são:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| nome do parâmetro |Sim |Nome do parâmetro. Tem de ser um identificador de JavaScript válido. |
| tipo |Sim |Tipo do valor do parâmetro. Os tipos e valores permitidos são **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**e **array**. Consulte [tipos de dados](#data-types). |
| defaultValue |Não |Valor padrão para o parâmetro, se nenhum valor for fornecido para o parâmetro. |
| allowedValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor correto seja fornecido. |
| minValue |Não |O valor mínimo para parâmetros de tipo int, esse valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros de tipo int, esse valor é inclusivo. |
| minLength |Não |O comprimento mínimo para parâmetros de cadeia de caracteres, Cadeia de caracteres segura e tipo de matriz, esse valor é inclusivo. |
| maxLength |Não |O comprimento máximo para parâmetros de cadeia de caracteres, Cadeia de caracteres segura e tipo de matriz, esse valor é inclusivo. |
| descrição |Não |Descrição do parâmetro que é exibido aos usuários por meio do Portal. Para obter mais informações, consulte [comentários em modelos](#comments). |

Para obter exemplos de como usar parâmetros, consulte [parâmetros em modelos de Azure Resource Manager](template-parameters.md).

### <a name="data-types"></a>Tipos de dados

Para números inteiros passados como parâmetros embutidos, o intervalo de valores pode ser limitado pelo SDK ou pela ferramenta de linha de comando usada para implantação. Por exemplo, ao usar o PowerShell para implantar um modelo, os tipos de inteiros podem variar de-2147483648 a 2147483647. Para evitar essa limitação, especifique valores inteiros grandes em um [arquivo de parâmetro](resource-manager-parameter-files.md). Os tipos de recurso aplicam seus próprios limites para propriedades de inteiros.

Ao especificar valores Boolianos e inteiros em seu modelo, não coloque o valor entre aspas. Comece e termine valores de cadeia de caracteres com aspas duplas.

Os objetos começam com uma chave esquerda e terminam com uma chave direita. As matrizes começam com um colchete esquerdo e terminam com um colchete direito.

Cadeias de caracteres seguras e objetos seguros não podem ser lidos após a implantação de recursos.

Para obter exemplos de formatação de tipos de dados, consulte [formatos de tipo de parâmetro](resource-manager-parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variáveis

Na seção variáveis, você constrói valores que podem ser usados em todo o modelo. Você não precisa definir variáveis, mas elas geralmente simplificam seu modelo, reduzindo expressões complexas.

O exemplo a seguir mostra as opções disponíveis para definir uma variável:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Para obter informações sobre como usar `copy` para criar vários valores para uma variável, consulte [Iteration Variable](resource-group-create-multiple.md#variable-iteration).

Para obter exemplos de como usar variáveis, consulte [variáveis no modelo Azure Resource Manager](template-variables.md).

## <a name="functions"></a>Functions

Em seu modelo, você pode criar suas próprias funções. Essas funções estão disponíveis para uso em seu modelo. Normalmente, você define expressões complicadas que não deseja repetir em todo o modelo. Você cria as funções definidas pelo usuário a partir de expressões e [funções](resource-group-template-functions.md) com suporte em modelos.

Ao definir uma função de usuário, há algumas restrições:

* A função não pode acessar variáveis.
* A função só pode usar parâmetros que são definidos na função. Quando você usa a [função Parameters](resource-group-template-functions-deployment.md#parameters) em uma função definida pelo usuário, você está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo usuário.
* A função não pode usar a [função de referência](resource-group-template-functions-resource.md#reference).
* Os parâmetros da função não podem ter valores padrão.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| espaço de nomes |Sim |Namespace para as funções personalizadas. Use para evitar conflitos de nomenclatura com funções de modelo. |
| nome da função |Sim |Nome da função personalizada. Ao chamar a função, combine o nome da função com o namespace. Por exemplo, para chamar uma função chamada uniqueName no namespace contoso, use `"[contoso.uniqueName()]"`. |
| nome do parâmetro |Não |Nome do parâmetro a ser usado na função personalizada. |
| valor do parâmetro |Não |Tipo do valor do parâmetro. Os tipos e valores permitidos são **String**, **SecureString**, **int**, **bool**, **Object**, **secureobject**e **array**. |
| tipo de saída |Sim |Tipo do valor de saída. Os valores de saída dão suporte aos mesmos tipos de parâmetros de entrada de função. |
| saída-valor |Sim |Expressão de linguagem do modelo que é avaliada e retornada da função. |

Para obter exemplos de como usar funções personalizadas, consulte [funções definidas pelo usuário no modelo Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Recursos

Na seção de recursos, você define os recursos que são implantados ou atualizados.

Você define recursos com a seguinte estrutura:

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
| condition | Não | Valor booliano que indica se o recurso será provisionado durante essa implantação. Quando `true`, o recurso é criado durante a implantação. Quando `false`, o recurso é ignorado para essa implantação. Consulte a [condição](conditional-resource-deployment.md). |
| apiVersion |Sim |Versão da API REST a ser usada para criar o recurso. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| tipo |Sim |Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso (como **Microsoft. Storage/storageAccounts**). Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). Para um recurso filho, o formato do tipo depende de se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |
| nome |Sim |Nome do recurso. O nome deve seguir as restrições de componente de URI definidas em RFC3986. Os serviços do Azure que expõem o nome do recurso a partes externas validam o nome para verificar se não há uma tentativa de falsificar outra identidade. Para um recurso filho, o formato do nome depende se ele está aninhado dentro do recurso pai ou definido fora do recurso pai. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |
| localização |Varia |Locais geográficos com suporte do recurso fornecido. Você pode selecionar qualquer um dos locais disponíveis, mas, normalmente, faz sentido escolher um que esteja próximo de seus usuários. Normalmente, também faz sentido posicionar recursos que interagem entre si na mesma região. A maioria dos tipos de recursos requer um local, mas alguns tipos (como uma atribuição de função) não exigem um local. Consulte [definir local do recurso](resource-location.md). |
| etiquetas |Não |Marcas associadas ao recurso. Aplique marcas para organizar os recursos de forma lógica em sua assinatura. |
| feitos |Não |Suas notas para documentar os recursos em seu modelo. Para obter mais informações, consulte [comentários em modelos](resource-group-authoring-templates.md#comments). |
| CopiarObjeto |Não |Se mais de uma instância for necessária, o número de recursos a serem criados. O modo padrão é Parallel. Especifique o modo Serial quando não quiser que todos ou os recursos sejam implantados ao mesmo tempo. Para obter mais informações, consulte [criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Não |Recursos que devem ser implantados antes do recurso ser implantado. O Gerenciador de recursos avalia as dependências entre os recursos e os implanta na ordem correta. Quando os recursos não dependem uns dos outros, eles são implantados em paralelo. O valor pode ser uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos. Lista apenas os recursos que são implantados neste modelo. Os recursos que não estão definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois elas podem retardar a implantação e criar dependências circulares. Para obter orientação sobre como definir dependências, consulte [definindo dependências em modelos de Azure Resource Manager](resource-group-define-dependencies.md). |
| propriedades |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são os mesmos que os valores fornecidos no corpo da solicitação para a operação da API REST (método PUT) para criar o recurso. Você também pode especificar uma matriz de cópia para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [referência de modelo](/azure/templates/). |
| SKU | Não | Alguns recursos permitem valores que definem a SKU a ser implantada. Por exemplo, você pode especificar o tipo de redundância para uma conta de armazenamento. |
| type | Não | Alguns recursos permitem um valor que define o tipo de recurso que você implanta. Por exemplo, você pode especificar o tipo de Cosmos DB a ser criado. |
| intenção | Não | Alguns recursos permitem valores que definem o plano a ser implantado. Por exemplo, você pode especificar a imagem do Marketplace para uma máquina virtual. |
| os |Não |Recursos filho que dependem do recurso que está sendo definido. Forneça apenas os tipos de recursos que são permitidos pelo esquema do recurso pai. A dependência do recurso pai não está implícita. Você deve definir explicitamente essa dependência. Consulte [definir nome e tipo para recursos filho](child-resource-name-type.md). |

## <a name="outputs"></a>Saídas

Na secção de saídas, especifique os valores que são devolvidos da implementação. Normalmente, você retorna valores de recursos que foram implantados.

O exemplo seguinte mostra a estrutura de uma definição de saída:

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| nome de saída |Sim |Nome do valor de saída. Tem de ser um identificador de JavaScript válido. |
| condition |Não | Valor booliano que indica se esse valor de saída é retornado. Quando `true`, o valor é incluído na saída para a implantação. Quando `false`, o valor de saída é ignorado para essa implantação. Quando não especificado, o valor padrão é `true`. |
| tipo |Sim |Tipo do valor de saída. Valores de saída suportam os mesmos tipos de parâmetros de entrada de modelo. Se você especificar **SecureString** para o tipo de saída, o valor não será exibido no histórico de implantação e não poderá ser recuperado de outro modelo. Para usar um valor secreto em mais de um modelo, armazene o segredo em um Key Vault e referencie o segredo no arquivo de parâmetro. Para obter mais informações, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](resource-manager-keyvault-parameter.md). |
| valor |Sim |Expressão de linguagem de modelo que é avaliada e devolvida como valor de saída. |

Para obter exemplos de como usar saídas, consulte [saídas no modelo Azure Resource Manager](template-outputs.md).

<a id="comments" />

## <a name="comments-and-metadata"></a>Comentários e metadados

Você tem algumas opções para adicionar comentários e metadados ao seu modelo.

### <a name="comments"></a>Comentários

Para comentários embutidos, você pode usar o `//` ou `/* ... */`, mas essa sintaxe não funciona com todas as ferramentas. Você não pode usar o editor de modelos de portal para trabalhar em modelos com comentários embutidos. Se você adicionar esse estilo de comentário, certifique-se de que as ferramentas usadas suportam comentários JSON embutidos.

> [!NOTE]
> Para implantar modelos com comentários usando CLI do Azure, você deve usar a opção `--handle-extended-json-format`.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

No Visual Studio Code, a [extensão de ferramentas de Azure Resource Manager](./resource-manager-tools-vs-code.md#install-resource-manager-tools-extension) pode detectar automaticamente o modelo do Resource Manager e alterar o modo de linguagem adequadamente. Se você vir **Azure Resource Manager modelo** no canto inferior direito de vs Code, poderá usar os comentários embutidos. Os comentários embutidos não são mais marcados como inválidos.

![Modo de modelo de Azure Resource Manager Visual Studio Code](./media/resource-group-authoring-templates/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadados

Você pode adicionar um objeto `metadata` quase em qualquer lugar em seu modelo. O Resource Manager ignora o objeto, mas seu editor de JSON pode avisá-lo que a propriedade não é válida. No objeto, defina as propriedades necessárias.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para **parâmetros**, adicione um objeto `metadata` com uma propriedade `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Ao implantar o modelo por meio do portal, o texto que você fornece na descrição é usado automaticamente como uma dica para esse parâmetro.

![Mostrar dica de parâmetro](./media/resource-group-authoring-templates/show-parameter-tip.png)

Para **recursos**, adicione um elemento `comments` ou um objeto de metadados. O exemplo a seguir mostra um elemento Comments e um objeto Metadata.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Para **saídas**, adicione um objeto de metadados ao valor de saída.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Você não pode adicionar um objeto de metadados a funções definidas pelo usuário.

## <a name="multi-line-strings"></a>Cadeias de caracteres de várias linhas

Você pode dividir uma cadeia de caracteres em várias linhas. Por exemplo, a propriedade Location e um dos comentários no exemplo JSON a seguir.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Para implantar modelos com cadeias de caracteres de várias linhas usando CLI do Azure, você deve usar a opção `--handle-extended-json-format`.

## <a name="next-steps"></a>Passos seguintes

* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar a partir de dentro de um modelo, consulte [funções de modelo do Azure Resource Manager](resource-group-template-functions.md).
* Para combinar vários modelos durante a implantação, consulte [usando modelos vinculados com Azure Resource Manager](resource-group-linked-templates.md).
* Para obter recomendações sobre como criar modelos, consulte [Azure Resource Manager modelo de práticas recomendadas](template-best-practices.md).
* Para obter recomendações sobre como criar modelos do Resource Manager que você pode usar em todos os ambientes do Azure e Azure Stack, consulte [desenvolver modelos de Azure Resource Manager para consistência de nuvem](templates-cloud-consistency.md).
