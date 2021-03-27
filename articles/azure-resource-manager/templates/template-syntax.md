---
title: Estrutura do modelo e sintaxe
description: Descreve a estrutura e propriedades dos modelos Azure Resource Manager (modelos ARM) usando a sintaxe declarativa JSON.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 42b893e25155bb3ebe66e0deac180698446a2c9b
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612182"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Understand the structure and syntax of ARM templates (Compreender a estrutura e a sintaxe dos modelos do Resource Manager)

Este artigo descreve a estrutura de um modelo de Gestor de Recursos Azure (modelo ARM). Apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis nessas secções.

Este artigo destina-se a utilizadores que tenham alguma familiaridade com os modelos ARM. Fornece informações detalhadas sobre a estrutura do modelo. Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implemente o seu primeiro modelo ARM](template-tutorial-create-first-template.md). Para conhecer os modelos ARM através de um conjunto guiado de módulos no Microsoft Learn, consulte [implementar e gerir recursos em Azure utilizando modelos ARM](/learn/paths/deploy-manage-resource-manager-templates/).

## <a name="template-format"></a>Formato de modelo

Na sua estrutura mais simples, um modelo tem os seguintes elementos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| $schema |Yes |Localização do ficheiro de esquema de notação de objetos JavaScript (JSON) que descreve a versão do idioma do modelo. O número de versão que utiliza depende do âmbito da implementação e do seu editor de JSON.<br><br>Se estiver a utilizar [o Código do Estúdio Visual com a extensão de ferramentas Azure Resource Manager,](quickstart-create-templates-use-visual-studio-code.md)utilize a versão mais recente para implementações de grupos de recursos:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Outros editores (incluindo o Visual Studio) podem não ser capazes de processar este esquema. Para os editores, utilize:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implementações de subscrição, utilize:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Para implementações de grupos de gestão, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Para implantações de inquilinos, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| parte de conteúdoVersão |Yes |Versão do modelo (tal como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao utilizar recursos utilizando o modelo, este valor pode ser usado para garantir que o modelo certo está a ser utilizado. |
| apiProfile |No | Uma versão API que serve como uma coleção de versões API para tipos de recursos. Utilize este valor para evitar ter de especificar versões API para cada recurso no modelo. Quando especifica uma versão de perfil API e não especifica uma versão API para o tipo de recurso, o Gestor de Recursos utiliza a versão API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil API é especialmente útil ao implementar um modelo para diferentes ambientes, como Azure Stack e Global Azure. Utilize a versão de perfil API para se certificar de que o seu modelo utiliza automaticamente versões suportadas em ambos os ambientes. Para obter uma lista das versões de perfil da API atuais e as versões API de recursos definidas no perfil, consulte o [Perfil da API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [as versões Track utilizando perfis API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parâmetros](#parameters) |No |Valores que são fornecidos quando a implementação é executada para personalizar a implementação de recursos. |
| [variáveis](#variables) |No |Valores que são usados como fragmentos de JSON no modelo para simplificar expressões de linguagem do modelo. |
| [funções](#functions) |No |Funções definidas pelo utilizador que estão disponíveis dentro do modelo. |
| [recursos](#resources) |Yes |Tipos de recursos que são implantados ou atualizados num grupo de recursos ou subscrição. |
| [saídas](#outputs) |No |Valores que são devolvidos após a implantação. |

Cada elemento tem propriedades que pode definir. Este artigo descreve as secções do modelo com maior detalhe.

## <a name="parameters"></a>Parâmetros

Na `parameters` secção do modelo, especifique quais os valores que pode inserir ao utilizar os recursos. Está limitado a 256 parâmetros num modelo. Pode reduzir o número de parâmetros utilizando objetos que contêm múltiplas propriedades.

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

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| nome de parâmetro |Yes |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| tipo |Yes |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **securestring**, **int,** **bool,** **object,** **secureObject**, e **array**. Consulte [os tipos de dados nos modelos ARM](data-types.md). |
| padrãoValue |No |Valor predefinido para o parâmetro, se não for fornecido qualquer valor para o parâmetro. |
| permitidosValues |No |Matriz de valores permitidos para o parâmetro para garantir que o valor certo é fornecido. |
| minValue |No |O valor mínimo para os parâmetros do tipo int, este valor é inclusivo. |
| maxValue |No |O valor máximo para parâmetros do tipo int, este valor é inclusivo. |
| minLength |No |O comprimento mínimo para parâmetros de corda, corda de segurança e tipo de matriz, este valor é inclusivo. |
| maxLength |No |O comprimento máximo para parâmetros de corda, corda segura e tipo de matriz, este valor é inclusivo. |
| descrição |No |Descrição do parâmetro que é apresentado aos utilizadores através do portal. Para obter mais informações, consulte [comentários em modelos.](#comments) |

Por exemplo, como utilizar parâmetros, consulte [parâmetros nos modelos ARM](template-parameters.md).

## <a name="variables"></a>Variáveis

Na `variables` secção, constrói valores que podem ser utilizados em todo o seu modelo. Não é preciso definir variáveis, mas muitas vezes simplificam o seu modelo reduzindo expressões complexas. O formato de cada variável corresponde a um dos tipos de [dados.](data-types.md)

O exemplo a seguir mostra as opções disponíveis para a definição de uma variável:

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

Para obter informações sobre `copy` a utilização para criar vários valores para uma variável, consulte [iteração variável.](copy-variables.md)

Por exemplo, como utilizar variáveis, consulte [Variáveis no modelo ARM](template-variables.md).

## <a name="functions"></a>Funções

Dentro do seu modelo, pode criar as suas próprias funções. Estas funções estão disponíveis para utilização no seu modelo. Tipicamente, define expressões complicadas que não quer repetir ao longo do seu modelo. Cria as funções definidas pelo utilizador a partir de expressões e [funções](template-functions.md) que são suportadas em modelos.

Ao definir uma função de utilizador, existem algumas restrições:

* A função não pode aceder a variáveis.
* A função só pode utilizar parâmetros definidos na função. Quando utiliza a função de [parâmetros](template-functions-deployment.md#parameters) dentro de uma função definida pelo utilizador, está restrito aos parâmetros para essa função.
* A função não pode chamar outras funções definidas pelo utilizador.
* A função não pode utilizar a [função de referência](template-functions-resource.md#reference).
* Os parâmetros para a função não podem ter valores predefinidos.

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

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| espaço de nomes |Yes |Espaço de nome para as funções personalizadas. Utilize para evitar o nome de conflitos com funções de modelo. |
| nome da função |Yes |Nome da função personalizada. Ao ligar para a função, combine o nome da função com o espaço de nome. Por exemplo, para chamar uma função nomeada `uniqueName` no namespace contoso, use `"[contoso.uniqueName()]"` . |
| nome de parâmetro |No |Nome do parâmetro a utilizar na função personalizada. |
| parâmetro-valor |No |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **securestring**, **int,** **bool,** **object,** **secureObject**, e **array**. |
| tipo de saída |Yes |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada da função. |
| valor de saída |Yes |Expressão da linguagem do modelo que é avaliada e devolvida da função. |

Por exemplo, como utilizar funções personalizadas, consulte [funções definidas pelo Utilizador no modelo ARM](template-user-defined-functions.md).

## <a name="resources"></a>Recursos

Na `resources` secção, define-se os recursos que são implementados ou atualizados.

Define recursos com a seguinte estrutura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "scope": "<target-scope-for-extension-resources>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| condição | No | Valor booleano que indica se o recurso será aprovisionado durante esta implantação. Quando `true` , o recurso é criado durante a implantação. Quando `false` , o recurso é ignorado para esta implantação. Ver [condição.](conditional-resource-deployment.md) |
| tipo |Yes |Tipo de recurso. Este valor é uma combinação do espaço de nome do fornecedor de recursos e do tipo de recurso (tal `Microsoft.Storage/storageAccounts` como). Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). Para um recurso infantil, o formato do tipo depende se está aninhado no recurso principal ou definido fora do recurso principal. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |
| apiVersion |Yes |Versão da API REST para a criação do recurso. Ao criar um novo modelo, desloque este valor para a versão mais recente do recurso que está a implementar. Enquanto o modelo funcionar conforme necessário, continue a utilizar a mesma versão API. Ao continuar a utilizar a mesma versão API, minimiza o risco de uma nova versão API alterar o funcionamento do seu modelo. Considere atualizar a versão API apenas quando pretender utilizar uma nova funcionalidade que é introduzida numa versão posterior. Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). |
| name |Yes |Nome do recurso. O nome deve seguir as restrições de componentes URI definidas no RFC3986. Os serviços da Azure que expõem o nome de recursos a partes externas validam o nome para garantir que não é uma tentativa de falsificação de outra identidade. Para um recurso infantil, o formato do nome depende se está aninhado no recurso principal ou definido fora do recurso principal. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |
| comentários |No |As suas notas para documentar os recursos no seu modelo. Para obter mais informações, consulte [comentários em modelos.](template-syntax.md#comments) |
| localização |Varia |Geo-localizações suportadas do recurso fornecido. Pode selecionar qualquer um dos locais disponíveis, mas normalmente faz sentido escolher um que esteja próximo dos seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem uns com os outros na mesma região. A maioria dos tipos de recursos requer uma localização, mas alguns tipos (como uma atribuição de funções) não requerem uma localização. Ver [localização do recurso definido](resource-location.md). |
| dependsOn |No |Recursos que devem ser utilizados antes deste recurso ser implantado. O Gestor de Recursos avalia as dependências entre recursos e implementa-os na ordem correta. Quando os recursos não dependem uns dos outros, são implantados em paralelo. O valor pode ser uma lista separada por vírgula de um nome de recurso ou identificadores únicos de recursos. Apenas enumerar recursos que são implantados neste modelo. Os recursos que não estão definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias pois podem retardar a sua implantação e criar dependências circulares. Para obter orientações sobre a definição das dependências, consulte [definir a ordem para a implantação de recursos nos modelos ARM](define-resource-dependency.md). |
| etiquetas |No |Etiquetas que estão associadas ao recurso. Aplique tags para organizar logicamente recursos em toda a sua subscrição. |
| sku | No | Alguns recursos permitem que valores que definem o SKU implementem. Por exemplo, pode especificar o tipo de redundância para uma conta de armazenamento. |
| tipo | No | Alguns recursos permitem um valor que define o tipo de recurso que implementa. Por exemplo, pode especificar o tipo de Cosmos DB para criar. |
| scope | No | A propriedade de âmbito só está disponível para [tipos de recursos de extensão.](../management/extension-resource-types.md) Utilize-o ao especificar um âmbito diferente do âmbito de implantação. Consulte [a definição de possibilidade de extensão de recursos nos modelos ARM](scope-extension-resources.md). |
| Cópia |No |Se for necessário mais do que uma instância, o número de recursos a criar. O modo predefinido é paralelo. Especifique o modo de série quando não quiser que todos ou os recursos implementem ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager.](copy-resources.md) |
| plano | No | Alguns recursos permitem valores que definem o plano a implementar. Por exemplo, pode especificar a imagem do mercado para uma máquina virtual. |
| propriedades |No |Definições de configuração específicas de recursos. Os valores para as propriedades são os mesmos que os valores que fornece no organismo de pedido para a operação REST API (método PUT) para criar o recurso. Também pode especificar um conjunto de cópias para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). |
| resources |No |Recursos infantis que dependem do recurso que está a ser definido. Apenas forneça tipos de recursos que sejam permitidos pelo esquema do recurso principal. A dependência do recurso dos pais não está implícita. Tens de definir explicitamente essa dependência. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |

## <a name="outputs"></a>Saídas

Na `outputs` secção, especifique os valores que são devolvidos da implantação. Normalmente, devolve-se valores dos recursos que foram implantados.

O exemplo a seguir mostra a estrutura de uma definição de saída:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Nome do elemento | Obrigatório | Descrição |
|:--- |:--- |:--- |
| nome de saída |Yes |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condição |No | Valor booleano que indica se este valor de saída é devolvido. Quando `true` , o valor é incluído na saída para a implantação. Quando `false` , o valor de saída é ignorado para esta implantação. Quando não especificado, o valor predefinido é `true` . |
| tipo |Yes |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada do modelo. Se especificar **o tipo de** saída, o valor não é apresentado no histórico de implementação e não pode ser recuperado a partir de outro modelo. Para utilizar um valor secreto em mais de um modelo, guarde o segredo num Cofre de Chaves e refira o segredo no ficheiro do parâmetro. Para obter mais informações, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](key-vault-parameter.md). |
| valor |No |Expressão da linguagem do modelo que é avaliada e devolvida como valor de saída. Especificar o **valor** ou **a cópia.** |
| Cópia |No | Costumava devolver mais do que um valor para uma saída. Especificar **valor** ou **cópia.** Para obter mais informações, consulte [a iteração de saída nos modelos ARM](copy-outputs.md). |

Por exemplo, como utilizar saídas, consulte [saídas no modelo ARM](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Comentários e metadados

Tem algumas opções para adicionar comentários e metadados ao seu modelo.

### <a name="comments"></a>Comentários

Para comentários inline, você pode usar ou `//` `/* ... */` mas esta sintaxe não funciona com todas as ferramentas. Se adicionar este tipo de comentário, certifique-se de que as ferramentas que utiliza suportam comentários JSON.

> [!NOTE]
> Para implementar modelos com comentários utilizando o Azure CLI com a versão 2.3.0 ou mais antiga, tem de utilizar o `--handle-extended-json-format` interruptor.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

No Código do Estúdio Visual, a [extensão Azure Resource Manager Tools](quickstart-create-templates-use-visual-studio-code.md) pode detetar automaticamente um modelo ARM e alterar o modo de idioma. Se vir **o Modelo do Gestor de Recursos Azure** no canto inferior direito do Código do Estúdio Visual, pode utilizar os comentários inline. Os comentários inline já não são marcados como inválidos.

![Modo modelo de gestor de recursos do código de estúdio visual Azure](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadados

Pode adicionar um `metadata` objeto em qualquer lugar do seu modelo. O Gestor de Recursos ignora o objeto, mas o seu editor JSON pode avisá-lo que a propriedade não é válida. No objeto, defina as propriedades de que necessita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para `parameters` , adicionar um objeto com uma `metadata` `description` propriedade.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Ao implementar o modelo através do portal, o texto que fornece na descrição é automaticamente utilizado como uma dica para esse parâmetro.

![Mostrar ponta de parâmetro](./media/template-syntax/show-parameter-tip.png)

Para `resources` , adicionar um elemento ou um `comments` `metadata` objeto. O exemplo a seguir mostra tanto um elemento como `comments` um `metadata` objeto.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

Para `outputs` , adicionar um objeto ao valor de `metadata` saída.

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

Não é possível adicionar um `metadata` objeto a funções definidas pelo utilizador.

## <a name="multi-line-strings"></a>Cordas multi-linha

Pode partir uma corda em várias linhas. Por exemplo, consulte a `location` propriedade e um dos comentários no exemplo JSON a seguir.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

> [!NOTE]
> Para implementar modelos com cordas multi-linhas utilizando o CLI Azure com a versão 2.3.0 ou mais antiga, deve utilizar o `--handle-extended-json-format` interruptor.

## <a name="next-steps"></a>Passos seguintes

* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar dentro de um modelo, consulte [as funções do modelo ARM](template-functions.md).
* Para combinar vários modelos durante a implementação, consulte [utilizando modelos ligados e aninhados ao implementar recursos Azure](linked-templates.md).
* Para recomendações sobre a criação de modelos, consulte [as melhores práticas do modelo ARM](template-best-practices.md).
* Para obter respostas a perguntas comuns, consulte [perguntas frequentes sobre modelos ARM](frequently-asked-questions.md).
