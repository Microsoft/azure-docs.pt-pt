---
title: Estrutura do modelo e sintaxe
description: Descreve a estrutura e propriedades dos modelos do Gestor de Recursos Azure usando a sintaxe declarativa JSON.
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: ae2c5a5fe1440c3adbae475cd4c7652a3b01c285
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116544"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Understand the structure and syntax of ARM templates (Compreender a estrutura e a sintaxe dos modelos do Resource Manager)

Este artigo descreve a estrutura de um modelo Azure Resource Manager (ARM). Apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis nessas secções.

Este artigo destina-se a utilizadores que tenham alguma familiaridade com os modelos ARM. Fornece informações detalhadas sobre a estrutura do modelo. Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implemente o seu primeiro modelo de Gestor de Recursos Azure](template-tutorial-create-first-template.md).

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
| $schema |Sim |Localização do ficheiro de esquema JSON que descreve a versão do idioma do modelo. O número de versão que utiliza depende do âmbito da implementação e do seu editor de JSON.<br><br>Se estiver a utilizar [o Código VS com a extensão de ferramentas Azure Resource Manager,](quickstart-create-templates-use-visual-studio-code.md)utilize a versão mais recente para implementações de grupos de recursos:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Outros editores (incluindo o Visual Studio) podem não ser capazes de processar este esquema. Para os editores, utilize:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implementações de subscrição, utilize:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Para implementações de grupos de gestão, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Para implantações de inquilinos, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| parte de conteúdoVersão |Sim |Versão do modelo (tal como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao utilizar recursos utilizando o modelo, este valor pode ser usado para garantir que o modelo certo está a ser utilizado. |
| apiProfile |Não | Uma versão API que serve como uma coleção de versões API para tipos de recursos. Utilize este valor para evitar ter de especificar versões API para cada recurso no modelo. Quando especifica uma versão de perfil API e não especifica uma versão API para o tipo de recurso, o Gestor de Recursos utiliza a versão API para esse tipo de recurso que é definido no perfil.<br><br>A propriedade de perfil API é especialmente útil ao implementar um modelo para diferentes ambientes, como Azure Stack e Global Azure. Utilize a versão de perfil API para se certificar de que o seu modelo utiliza automaticamente versões suportadas em ambos os ambientes. Para obter uma lista das versões de perfil da API atuais e as versões API de recursos definidas no perfil, consulte o [Perfil da API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para obter mais informações, consulte [as versões Track utilizando perfis API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parâmetros](#parameters) |Não |Valores que são fornecidos quando a implementação é executada para personalizar a implementação de recursos. |
| [variáveis](#variables) |Não |Valores que são usados como fragmentos de JSON no modelo para simplificar expressões de linguagem do modelo. |
| [funções](#functions) |Não |Funções definidas pelo utilizador que estão disponíveis dentro do modelo. |
| [recursos](#resources) |Sim |Tipos de recursos que são implantados ou atualizados num grupo de recursos ou subscrição. |
| [saídas](#outputs) |Não |Valores que são devolvidos após a implantação. |

Cada elemento tem propriedades que pode definir. Este artigo descreve as secções do modelo com maior detalhe.

## <a name="parameters"></a>Parâmetros

Na secção de parâmetros do modelo, especifique os valores que pode introduzir ao implementar os recursos. Está limitado a 256 parâmetros num modelo. Pode reduzir o número de parâmetros utilizando objetos que contêm múltiplas propriedades.

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
| nome de parâmetro |Sim |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| tipo |Sim |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **securestring**, **int,** **bool,** **object,** **secureObject**, e **array**. Consulte [os tipos de dados.](#data-types) |
| padrãoValue |Não |Valor predefinido para o parâmetro, se não for fornecido qualquer valor para o parâmetro. |
| permitidosValues |Não |Matriz de valores permitidos para o parâmetro para garantir que o valor certo é fornecido. |
| minValue |Não |O valor mínimo para os parâmetros do tipo int, este valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros do tipo int, este valor é inclusivo. |
| minLength |Não |O comprimento mínimo para parâmetros de corda, corda de segurança e tipo de matriz, este valor é inclusivo. |
| maxLength |Não |O comprimento máximo para parâmetros de corda, corda segura e tipo de matriz, este valor é inclusivo. |
| descrição |Não |Descrição do parâmetro que é apresentado aos utilizadores através do portal. Para obter mais informações, consulte [comentários em modelos.](#comments) |

Para exemplos de como utilizar parâmetros, consulte [parâmetros nos modelos do Gestor de Recursos Azure](template-parameters.md).

### <a name="data-types"></a>Tipos de dados

Para os inteiros passados como parâmetros inline, a gama de valores pode ser limitada pela ferramenta SDK ou linha de comando que utiliza para implantação. Por exemplo, quando se utiliza o PowerShell para implantar um modelo, os tipos inteiros podem variar entre -2147483648 e 2147483647. Para evitar esta limitação, especifique grandes valores inteiros num [ficheiro de parâmetros](parameter-files.md). Os tipos de recursos aplicam os seus próprios limites para propriedades inteiros.

Ao especificar valores booleanos e inteiros no seu modelo, não rodeie o valor com aspas. Valores de cadeia de início e fim com marcas duplas de aspas.

Os objetos começam com uma cinta esquerda e terminam com uma cinta direita. As matrizes começam com um suporte esquerdo e terminam com um suporte direito.

Quando se define um parâmetro para uma cadeia segura ou objeto seguro, o valor do parâmetro não é guardado para o histórico de implementação e não está registado. No entanto, se definir esse valor seguro para uma propriedade que não está à espera de um valor seguro, o valor não está protegido. Por exemplo, se definir uma cadeia segura numa etiqueta, esse valor é armazenado como texto simples. Use cordas seguras para senhas e segredos.

Para amostras de tipos de dados de formatação, consulte [os formatos do tipo parâmetro](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variáveis

Na secção de variáveis, constrói valores que podem ser usados em todo o seu modelo. Não é preciso definir variáveis, mas muitas vezes simplificam o seu modelo reduzindo expressões complexas.

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

Por exemplo, como utilizar variáveis, consulte [Variáveis no modelo Azure Resource Manager](template-variables.md).

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
| espaço de nomes |Sim |Espaço de nome para as funções personalizadas. Utilize para evitar o nome de conflitos com funções de modelo. |
| nome da função |Sim |Nome da função personalizada. Ao ligar para a função, combine o nome da função com o espaço de nome. Por exemplo, para chamar uma função chamada nome único no namespace contoso, use `"[contoso.uniqueName()]"` . |
| nome de parâmetro |Não |Nome do parâmetro a utilizar na função personalizada. |
| parâmetro-valor |Não |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **securestring**, **int,** **bool,** **object,** **secureObject**, e **array**. |
| tipo de saída |Sim |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada da função. |
| valor de saída |Sim |Expressão da linguagem do modelo que é avaliada e devolvida da função. |

Para exemplos de como utilizar funções personalizadas, consulte [funções definidas pelo Utilizador no modelo Azure Resource Manager](template-user-defined-functions.md).

## <a name="resources"></a>Recursos

Na secção de recursos, define-se os recursos que são implantados ou atualizados.

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
| condição | Não | Valor booleano que indica se o recurso será aprovisionado durante esta implantação. Quando `true` , o recurso é criado durante a implantação. Quando `false` , o recurso é ignorado para esta implantação. Ver [condição.](conditional-resource-deployment.md) |
| tipo |Sim |Tipo de recurso. Este valor é uma combinação do espaço de nome do fornecedor de recursos e do tipo de recursos (tais como **Microsoft.Storage/storageAccounts).** Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). Para um recurso infantil, o formato do tipo depende se está aninhado no recurso principal ou definido fora do recurso principal. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |
| apiVersion |Sim |Versão da API REST para a criação do recurso. Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). |
| name |Sim |Nome do recurso. O nome deve seguir as restrições de componentes URI definidas no RFC3986. Os serviços da Azure que expõem o nome de recursos a partes externas validam o nome para garantir que não é uma tentativa de falsificação de outra identidade. Para um recurso infantil, o formato do nome depende se está aninhado no recurso principal ou definido fora do recurso principal. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |
| comentários |Não |As suas notas para documentar os recursos no seu modelo. Para obter mais informações, consulte [comentários em modelos.](template-syntax.md#comments) |
| localização |Varia |Geo-localizações suportadas do recurso fornecido. Pode selecionar qualquer um dos locais disponíveis, mas normalmente faz sentido escolher um que esteja próximo dos seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem uns com os outros na mesma região. A maioria dos tipos de recursos requer uma localização, mas alguns tipos (como uma atribuição de funções) não requerem uma localização. Ver [localização do recurso definido](resource-location.md). |
| dependsOn |Não |Recursos que devem ser utilizados antes deste recurso ser implantado. O Gestor de Recursos avalia as dependências entre recursos e implementa-os na ordem correta. Quando os recursos não dependem uns dos outros, são implantados em paralelo. O valor pode ser uma lista separada por vírgula de um nome de recurso ou identificadores únicos de recursos. Apenas enumerar recursos que são implantados neste modelo. Os recursos que não estão definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias pois podem retardar a sua implantação e criar dependências circulares. Para obter orientações sobre a definição de dependências [nos modelos do Gestor de Recursos Azure](define-resource-dependency.md). |
| etiquetas |Não |Etiquetas que estão associadas ao recurso. Aplique tags para organizar logicamente recursos em toda a sua subscrição. |
| sku | Não | Alguns recursos permitem que valores que definem o SKU implementem. Por exemplo, pode especificar o tipo de redundância para uma conta de armazenamento. |
| tipo | Não | Alguns recursos permitem um valor que define o tipo de recurso que implementa. Por exemplo, pode especificar o tipo de Cosmos DB para criar. |
| Cópia |Não |Se for necessário mais do que uma instância, o número de recursos a criar. O modo predefinido é paralelo. Especifique o modo de série quando não quiser que todos ou os recursos implementem ao mesmo tempo. Para obter mais informações, consulte [Criar várias instâncias de recursos no Azure Resource Manager.](copy-resources.md) |
| plano | Não | Alguns recursos permitem valores que definem o plano a implementar. Por exemplo, pode especificar a imagem do mercado para uma máquina virtual. |
| propriedades |Não |Definições de configuração específicas de recursos. Os valores para as propriedades são os mesmos que os valores que fornece no organismo de pedido para a operação REST API (método PUT) para criar o recurso. Também pode especificar um conjunto de cópias para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte [a referência do modelo](/azure/templates/). |
| resources |Não |Recursos infantis que dependem do recurso que está a ser definido. Apenas forneça tipos de recursos que sejam permitidos pelo esquema do recurso principal. A dependência do recurso dos pais não está implícita. Tens de definir explicitamente essa dependência. Consulte [o nome definido e o tipo para obter recursos para crianças.](child-resource-name-type.md) |

## <a name="outputs"></a>Saídas

Na secção Saídas, especifique os valores que são devolvidos da implementação. Normalmente, devolve-se valores dos recursos que foram implantados.

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
| nome de saída |Sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condição |Não | Valor booleano que indica se este valor de saída é devolvido. Quando `true` , o valor é incluído na saída para a implantação. Quando `false` , o valor de saída é ignorado para esta implantação. Quando não especificado, o valor predefinido é `true` . |
| tipo |Sim |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada do modelo. Se especificar **o tipo de** saída, o valor não é apresentado no histórico de implementação e não pode ser recuperado a partir de outro modelo. Para utilizar um valor secreto em mais de um modelo, guarde o segredo num Cofre de Chaves e refira o segredo no ficheiro do parâmetro. Para obter mais informações, consulte [use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](key-vault-parameter.md). |
| valor |Não |Expressão da linguagem do modelo que é avaliada e devolvida como valor de saída. Especificar o **valor** ou **a cópia.** |
| Cópia |Não | Costumava devolver mais do que um valor para uma saída. Especificar **valor** ou **cópia.** Para obter mais informações, consulte [a iteração de saída nos modelos do Gestor de Recursos Azure](copy-outputs.md). |

Para exemplos de como utilizar saídas, consulte [saídas no modelo Azure Resource Manager](template-outputs.md).

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

No Código do Estúdio Visual, a [extensão Azure Resource Manager Tools](quickstart-create-templates-use-visual-studio-code.md) pode detetar automaticamente o modelo do Gestor de Recursos e alterar o modo de idioma em conformidade. Se vir **o Modelo do Gestor de Recursos Azure** no canto inferior direito do Código VS, pode utilizar os comentários inline. Os comentários inline já não são marcados como inválidos.

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

Para **parâmetros,** adicione um `metadata` objeto com uma `description` propriedade.

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

Para **obter recursos,** adicione um `comments` elemento ou um objeto de metadados. O exemplo a seguir mostra tanto um elemento de comentários como um objeto de metadados.

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

Para **as saídas,** adicione um objeto de metadados ao valor de saída.

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

Não é possível adicionar um objeto de metadados às funções definidas pelo utilizador.

## <a name="multi-line-strings"></a>Cordas multi-linha

Pode partir uma corda em várias linhas. Por exemplo, consulte a propriedade de localização e um dos comentários no exemplo JSON a seguir.

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

Para implementar modelos com cordas multi-linhas utilizando o CLI Azure com a versão 2.3.0 ou mais antiga, deve utilizar o `--handle-extended-json-format` interruptor.

## <a name="next-steps"></a>Passos seguintes

* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para obter detalhes sobre as funções que pode utilizar dentro de um modelo, consulte as [funções do modelo do Gestor de Recursos Azure](template-functions.md).
* Para combinar vários modelos durante a implementação, consulte [utilizando modelos ligados com O Gestor de Recursos Azure](linked-templates.md).
* Para recomendações sobre a criação de modelos, consulte [as melhores práticas do modelo do Azure Resource Manager](template-best-practices.md).
* Para obter respostas a perguntas comuns, consulte [perguntas frequentes sobre modelos ARM](frequently-asked-questions.md).
