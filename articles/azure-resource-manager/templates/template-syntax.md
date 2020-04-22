---
title: Estrutura do modelo e sintaxe
description: Descreve a estrutura e as propriedades dos modelos do Gestor de Recursos Azure usando a sintaxe declarativa da JSON.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 60d800eb5251fb3454ba60a67bd109261c6ff9d4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687874"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Compreender a estrutura e a sintaxe dos modelos ARM

Este artigo descreve a estrutura de um modelo de Gestor de Recursos Azure (ARM). Apresenta as diferentes secções de um modelo e as propriedades que estão disponíveis nessas secções.

Este artigo destina-se a utilizadores que tenham alguma familiaridade com os modelos ARM. Fornece informações detalhadas sobre a estrutura do modelo. Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte [Tutorial: Crie e implemente o seu primeiro modelo](template-tutorial-create-first-template.md)de Gestor de Recursos Azure .

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

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| $schema |Sim |Localização do ficheiro de esquemaJSON que descreve a versão do idioma do modelo. O número da versão que utiliza depende do âmbito da implementação e do seu editor JSON.<br><br>Se estiver a utilizar [o Código VS com a extensão das ferramentas do Gestor de Recursos Do Azure,](use-vs-code-to-create-template.md)utilize a versão mais recente para implementações de grupos de recursos:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Outros editores (incluindo o Estúdio Visual) podem não ser capazes de processar este esquema. Para os editores, use:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Para implementações de subscrição, utilize:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Para implementações de grupos de gestão, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Para a implantação de inquilinos, utilize:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| conteúdoVersão |Sim |Versão do modelo (como 1.0.0.0). Pode fornecer qualquer valor para este elemento. Utilize este valor para documentar alterações significativas no seu modelo. Ao utilizar recursos utilizando o modelo, este valor pode ser utilizado para se certificar de que o modelo certo está a ser utilizado. |
| apiPerfil |Não | Uma versão API que serve como uma coleção de versões API para tipos de recursos. Utilize este valor para evitar ter de especificar versões API para cada recurso no modelo. Quando especifica uma versão de perfil API e não especifica uma versão API para o tipo de recurso, o Gestor de Recursos utiliza a versão API para esse tipo de recursos que é definido no perfil.<br><br>A propriedade de perfil API é especialmente útil ao implementar um modelo para diferentes ambientes, como O Azure Stack e o Global Azure. Utilize a versão de perfil API para se certificar de que o seu modelo utiliza automaticamente versões que são suportadas em ambos os ambientes. Para obter uma lista das versões atuais do perfil DaPI e dos recursos das versões API definidas no perfil, consulte o [Perfil API](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Para mais informações, consulte [versões Track utilizando perfis API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parâmetros](#parameters) |Não |Valores que são fornecidos quando a implementação é executada para personalizar a implementação de recursos. |
| [variáveis](#variables) |Não |Valores que são usados como fragmentos JSON no modelo para simplificar expressões de linguagem de modelo. |
| [funções](#functions) |Não |Funções definidas pelo utilizador que estão disponíveis dentro do modelo. |
| [recursos](#resources) |Sim |Tipos de recursos que são implantados ou atualizados num grupo de recursos ou subscrição. |
| [saídas](#outputs) |Não |Valores que são devolvidos após a implantação. |

Cada elemento tem propriedades que pode definir. Este artigo descreve as secções do modelo com mais detalhes.

## <a name="parameters"></a>Parâmetros

Na secção de parâmetros do modelo, especifica quais os valores que pode inserir ao utilizar os recursos. Está limitado a 256 parâmetros num modelo. Pode reduzir o número de parâmetros utilizando objetos que contenham múltiplas propriedades.

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
| nome do parâmetro |Sim |Nome do parâmetro. Deve ser um identificador JavaScript válido. |
| tipo |Sim |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **cordões de segurança,** **int,** **bool,** **object,** **secureObject**e **array**. Ver [tipos de Dados](#data-types). |
| defaultValor |Não |Valor predefinido para o parâmetro, se não for previsto qualquer valor para o parâmetro. |
| permitidoValores |Não |Conjunto de valores permitidos para o parâmetro para se certificar de que o valor certo é fornecido. |
| minValue |Não |O valor mínimo para parâmetros do tipo int, este valor é inclusivo. |
| maxValue |Não |O valor máximo para parâmetros do tipo int, este valor é inclusivo. |
| minLength |Não |O comprimento mínimo para os parâmetros de corda, corda segura e tipo de matriz, este valor é inclusivo. |
| comprimento máximo |Não |O comprimento máximo para os parâmetros de corda, corda segura e tipo de matriz, este valor é inclusivo. |
| descrição |Não |Descrição do parâmetro que é apresentado aos utilizadores através do portal. Para mais informações, consulte [Comentários em modelos](#comments). |

Por exemplo, como utilizar parâmetros, consulte [parâmetros nos modelos do Gestor](template-parameters.md)de Recursos Azure .

### <a name="data-types"></a>Tipos de dados

Para os inteiros passados como parâmetros inline, a gama de valores pode ser limitada pelo SDK ou pela ferramenta de linha de comando que utiliza para a implantação. Por exemplo, ao utilizar o PowerShell para implementar um modelo, os tipos inteiros podem variar entre -2147483648 e 2147483647. Para evitar esta limitação, especifique grandes valores inteiros num [ficheiro de parâmetro](parameter-files.md). Os tipos de recursos aplicam os seus próprios limites para propriedades inteiros.

Ao especificar valores booleanos e inteiros no seu modelo, não rode o valor com aspas. Iniciar e terminar valores de corda com duas aspas.

Os objetos começam com uma cinta esquerda e terminam com uma cinta direita. As matrizes começam com um suporte esquerdo e terminam com um suporte direito.

As cordas seguras e os objetos seguros não podem ser lidos após a implantação do recurso.

Para amostras de tipos de dados de formatação, consulte [formatos do tipo Parâmetro](parameter-files.md#parameter-type-formats).

## <a name="variables"></a>Variáveis

Na secção de variáveis, constrói valores que podem ser usados em todo o seu modelo. Não é preciso definir variáveis, mas muitas vezes simplificam o seu modelo reduzindo expressões complexas.

O exemplo que se segue mostra as opções disponíveis para definir uma variável:

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

Para obter `copy` informações sobre o uso para criar vários valores para uma variável, consulte [a iteração variável.](copy-variables.md)

Por exemplo, como usar variáveis, consulte [variáveis no modelo de Gestor](template-variables.md)de Recursos Azure .

## <a name="functions"></a>Funções

Dentro do seu modelo, pode criar as suas próprias funções. Estas funções estão disponíveis para utilização no seu modelo. Normalmente, define expressões complicadas que não quer repetir ao longo do seu modelo. Cria as funções definidas pelo utilizador a partir de expressões e [funções](template-functions.md) que são suportadas em modelos.

Ao definir uma função de utilizador, existem algumas restrições:

* A função não pode aceder a variáveis.
* A função só pode utilizar parâmetros definidos na função. Quando utiliza a função de [parâmetros](template-functions-deployment.md#parameters) dentro de uma função definida pelo utilizador, limita-se aos parâmetros dessa função.
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

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| espaço de nomes |Sim |Espaço de nome para as funções personalizadas. Utilize para evitar nomear conflitos com funções de modelo. |
| nome da função |Sim |Nome da função personalizada. Ao ligar para a função, combine o nome da função com o espaço de nome. Por exemplo, para chamar uma função denominada nome `"[contoso.uniqueName()]"`único Nome no espaço de nome contoso, use . |
| nome do parâmetro |Não |Nome do parâmetro a utilizar dentro da função personalizada. |
| valor dos parâmetros |Não |Tipo do valor do parâmetro. Os tipos e valores permitidos são **cordas,** **cordões de segurança,** **int,** **bool,** **object,** **secureObject**e **array**. |
| tipo de saída |Sim |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada da função. |
| valor de saída |Sim |Expressão de linguagem do modelo que é avaliada e devolvida da função. |

Por exemplo, como usar funções personalizadas, consulte [funções definidas pelo utilizador no modelo do Gestor](template-user-defined-functions.md)de Recursos Azure .

## <a name="resources"></a>Recursos

Na secção de recursos, define os recursos que são implantados ou atualizados.

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

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| condição | Não | Valor booleano que indica se o recurso será provisionado durante esta implantação. Quando, `true`o recurso é criado durante a implantação. Quando, `false`o recurso é ignorado para esta implantação. Ver [condição](conditional-resource-deployment.md). |
| tipo |Sim |Tipo de recurso. Este valor é uma combinação do espaço de nome do fornecedor de recursos e do tipo de recursos (como **Microsoft.Storage/storageAccounts**). Para determinar os valores disponíveis, consulte a [referência do modelo](/azure/templates/). Para um recurso infantil, o formato do tipo depende se está aninhado dentro do recurso-mãe ou definido fora do recurso-mãe. Consulte [o nome e o tipo de conjunto sinuosos para os recursos infantis](child-resource-name-type.md). |
| apiVersion |Sim |Versão da API REST para usar para criar o recurso. Para determinar os valores disponíveis, consulte a [referência do modelo](/azure/templates/). |
| nome |Sim |Nome do recurso. O nome deve seguir as restrições dos componentes URI definidas no RFC3986. Os serviços azure que expõem o nome do recurso a partes externas validam o nome para garantir que não é uma tentativa de falsificar outra identidade. Para um recurso infantil, o formato do nome depende se está aninhado dentro do recurso-mãe ou definido fora do recurso-mãe. Consulte [o nome e o tipo de conjunto sinuosos para os recursos infantis](child-resource-name-type.md). |
| comentários |Não |Suas notas para documentar os recursos no seu modelo. Para mais informações, consulte [Comentários em modelos](template-syntax.md#comments). |
| localização |Varia |Geo-localizações suportadas do recurso fornecido. Pode selecionar qualquer um dos locais disponíveis, mas normalmente faz sentido escolher um que esteja próximo dos seus utilizadores. Normalmente, também faz sentido colocar recursos que interagem uns com os outros na mesma região. A maioria dos tipos de recursos requer uma localização, mas alguns tipos (como uma atribuição de funções) não requerem uma localização. Ver [Definir localização de recursos](resource-location.md). |
| dependsOn |Não |Recursos que devem ser implantados antes de este recurso ser implantado. O Gestor de Recursos avalia as dependências entre recursos e implanta-os na ordem correta. Quando os recursos não dependem uns dos outros, são implantados em paralelo. O valor pode ser uma lista separada da vírposta de nomes de recursos ou identificadores únicos de recursos. Apenas lista risa os recursos que são implantados neste modelo. Os recursos que não estão definidos neste modelo já devem existir. Evite adicionar dependências desnecessárias, pois podem atrasar a sua implantação e criar dependências circulares. Para obter orientações sobre a definição de dependências, consulte [A Definição de dependências nos modelos do Gestor](define-resource-dependency.md)de Recursos Azure . |
| etiquetas |Não |Etiquetas que estão associadas ao recurso. Aplique etiquetas para organizar logicamente recursos em toda a sua subscrição. |
| sku | Não | Alguns recursos permitem valores que definem o SKU para implantar. Por exemplo, pode especificar o tipo de redundância para uma conta de armazenamento. |
| tipo | Não | Alguns recursos permitem um valor que define o tipo de recurso que implementa. Por exemplo, pode especificar o tipo de Cosmos DB para criar. |
| cópia |Não |Se for necessário mais do que um caso, o número de recursos para criar. O modo predefinido é paralelo. Especifique o modo de série quando não quiser que todos ou recursos sejam implantados ao mesmo tempo. Para mais informações, consulte [Criar vários casos de recursos no Gestor de Recursos Azure.](copy-resources.md) |
| plano | Não | Alguns recursos permitem valores que definem o plano de implantação. Por exemplo, pode especificar a imagem de mercado para uma máquina virtual. |
| propriedades |Não |Definições de configuração específicas do recurso. Os valores para as propriedades são os mesmos que os valores que fornece no organismo de pedido para a operação REST API (método PUT) para criar o recurso. Também pode especificar um conjunto de cópias para criar várias instâncias de uma propriedade. Para determinar os valores disponíveis, consulte a [referência do modelo](/azure/templates/). |
| resources |Não |Recursos infantis que dependem do recurso que está a ser definido. Apenas forneça tipos de recursos que sejam permitidos pelo esquema do recurso-mãe. A dependência do recurso dos pais não está implícita. Tens de definir explicitamente essa dependência. Consulte [o nome e o tipo de conjunto sinuosos para os recursos infantis](child-resource-name-type.md). |

## <a name="outputs"></a>Saídas

Na secção Saídas, especifice valores que são devolvidos da implantação. Normalmente, devolve-se valores dos recursos que foram implantados.

O exemplo que se segue mostra a estrutura de uma definição de saída:

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

| Nome do elemento | Necessário | Descrição |
|:--- |:--- |:--- |
| nome de saída |Sim |Nome do valor de saída. Deve ser um identificador JavaScript válido. |
| condição |Não | Valor booleano que indica se este valor de saída é devolvido. Quando, `true`o valor é incluído na saída para a implantação. Quando, `false`o valor de saída é ignorado para esta implantação. Quando não especificado, o `true`valor predefinido é . |
| tipo |Sim |Tipo do valor de saída. Os valores de saída suportam os mesmos tipos que os parâmetros de entrada do modelo. Se especificar o **securestring** para o tipo de saída, o valor não é apresentado no histórico de implementação e não pode ser recuperado de outro modelo. Para usar um valor secreto em mais de um modelo, guarde o segredo num Cofre chave e faça referência ao segredo no ficheiro do parâmetro. Para mais informações, consulte [o Cofre chave Azure para passar](key-vault-parameter.md)o valor do parâmetro seguro durante a implementação . |
| valor |Não |Expressão de linguagem de modelo que é avaliada e devolvida como valor de saída. Especifique o **valor** ou **a cópia**. |
| cópia |Não | Usado para devolver mais do que um valor para uma saída. Especificar **valor** ou **cópia**. Para mais informações, consulte a [iteração de saída nos modelos do Gestor](copy-outputs.md)de Recursos Azure . |

Por exemplo, como utilizar saídas, consulte [saídas no modelo do Gestor](template-outputs.md)de Recursos Azure .

<a id="comments" />

## <a name="comments-and-metadata"></a>Comentários e metadados

Tem algumas opções para adicionar comentários e metadados ao seu modelo.

### <a name="comments"></a>Comentários

Para comentários inline, você `//` pode `/* ... */` usar ou, mas esta sintaxe não funciona com todas as ferramentas. Não pode usar o editor do modelo do portal para trabalhar em modelos com comentários inline. Se adicionar este estilo de comentário, certifique-se de que as ferramentas que utiliza suportam comentários da JSON.

> [!NOTE]
> Para implementar modelos com comentários utilizando o Azure CLI com a versão `--handle-extended-json-format` 2.3.0 ou mais antiga, deve utilizar o interruptor.

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

No Código do Estúdio Visual, a extensão de Ferramentas de Gestor de [Recursos Azure](use-vs-code-to-create-template.md#install-resource-manager-tools-extension) pode detetar automaticamente o modelo de Gestor de Recursos e alterar o modo de idioma em conformidade. Se vir o Modelo de Gestor de **Recursos Azure** no canto inferior direito do Código VS, pode utilizar os comentários inline. Os comentários inline já não são marcados como inválidos.

![Modo de modelo de gestor de recursos do código do estúdio visual Azure](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadados

Pode adicionar `metadata` um objeto em qualquer lugar do seu modelo. O Gestor de Recursos ignora o objeto, mas o seu editor da JSON pode avisá-lo de que a propriedade não é válida. No objeto, defina as propriedades de que necessita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para **parâmetros,** `metadata` adicione um `description` objeto com uma propriedade.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Ao implantar o modelo através do portal, o texto que fornece na descrição é automaticamente utilizado como uma dica para esse parâmetro.

![Mostrar ponta do parâmetro](./media/template-syntax/show-parameter-tip.png)

Para **recursos,** `comments` adicione um elemento ou um objeto de metadados. O exemplo que se segue mostra tanto um elemento de comentários como um objeto de metadados.

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

Para **saídas,** adicione um objeto de metadados ao valor de saída.

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

Não é possível adicionar um objeto de metadados a funções definidas pelo utilizador.

## <a name="multi-line-strings"></a>Cordas multi-linhas

Pode quebrar uma corda em várias linhas. Por exemplo, consulte a propriedade de localização e um dos comentários no exemplo json seguinte.

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

Para implantar modelos com cordas multi-linhas utilizando o Azure CLI com a versão `--handle-extended-json-format` 2.3.0 ou mais antiga, tem de utilizar o interruptor.

## <a name="next-steps"></a>Passos seguintes

* Para ver modelos completos para vários tipos de soluções, veja os [Modelos de Início Rápido do Azure](https://azure.microsoft.com/documentation/templates/).
* Para mais detalhes sobre as funções que pode utilizar dentro de um modelo, consulte funções de modelo de [gestor de recursos azure](template-functions.md).
* Para combinar vários modelos durante a implementação, consulte [Utilizar modelos ligados com o Gestor](linked-templates.md)de Recursos Azure .
* Para recomendações sobre a criação de modelos, consulte [as melhores práticas do modelo do Gestor de Recursos do Azure.](template-best-practices.md)
* Para recomendações sobre a criação de modelos de Gestor de Recursos que você pode usar em todos os ambientes Azure e Azure Stack, consulte [modelos de Gestor de Recursos Azure para](templates-cloud-consistency.md)consistência na nuvem .
