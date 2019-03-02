---
title: Como criar o modelo Azure Resource Manager
description: Descreve o processo de criação de um modelo Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: a802b12f34c5fe16202f55416a698823e55c01bb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245903"
---
# <a name="create-azure-resource-manager-template"></a>Criar o modelo Azure Resource Manager

Este artigo descreve o processo e as decisões que tomar durante a criação de um modelo Azure Resource Manager. Ele fornece uma visão geral dos exemplos e recursos que poderão ajudá-lo durante a criação de seu modelo. O artigo parte do princípio de que está a implementar recursos para um grupo de recursos. Se precisar de implementar recursos na sua subscrição do Azure, como a criação de políticas do Azure ou atribuições de controlo de acesso baseado em funções, consulte [criar grupos de recursos e recursos para uma subscrição do Azure](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Selecione o editor de JSON

O modelo do Resource Manager é um ficheiro JSON. Tem uma boa ferramenta de criação para trabalhar no ficheiro JSON. Tem muitas opções, mas se ainda não tiver um editor que preferir, instale [Visual Studio Code (código de VS)](https://code.visualstudio.com/). 

Depois de instalar o VS Code, adicione a [extensão Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Esta extensão adiciona muitas funcionalidades que simplificam a criação de modelo.

![Modelo Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

A captura de ecrã mostra um modelo do Resource Manager aberto no Visual Studio Code. 

Para obter um tutorial de instalar a extensão de ferramentas do Resource Manager e como utilizar o VS Code, consulte [início rápido: Criar modelos Azure Resource Manager com o Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Compreender a estrutura do modelo

Vamos examinar as partes do modelo para compreender como funciona o modelo. O modelo poderá não ter cada secção. As secções a que se pretender focar-se no são:

* O [parâmetros](resource-manager-templates-parameters.md) seção, que mostra os valores que pode especificar durante a implementação para personalizar a infraestrutura que é implementada. 

* O [variáveis](resource-group-authoring-templates.md#variables) seção, que mostra os valores que são utilizados em todo o modelo.

* O [funções](resource-group-authoring-templates.md#functions) seção, que mostra expressões de modelo que são utilizadas no seu modelo de personalizado.

* O [recursos](resource-manager-templates-resources.md) seção, que mostra os recursos do Azure que são implementados na sua subscrição.

* O [produz](resource-group-authoring-templates.md#outputs) seção, que mostra os valores que são devolvidos após a implementação estiver concluída.

## <a name="look-for-similar-templates"></a>Procure os modelos semelhantes

Muitas vezes, pode encontrar um modelo existente, que implementa uma solução que é semelhante ao que precisa. O [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) possui centenas de modelos a partir dos contribuintes da Comunidade.

![Repositório de início rápido do modelo](./media/how-to-create-template/template-quickstart-repo.png)

Procure esse repositório para um modelo que é semelhante ao que precisa. Há problema que se o modelo não faz exatamente o que precisa, pode personalizá-lo.

Depois de encontrar um modelo, selecione **procurar no Github**e, em seguida, copie a **azuredeploy. JSON** ficheiro a partir do repositório. No VS Code, crie um novo ficheiro designado **azuredeploy. JSON** e adicione o conteúdo do ficheiro de modelo que copiou a partir do repositório de início rápido.

## <a name="add-resources"></a>Adicionar recursos

Provavelmente desejará personalizar o modelo para se certificar de que ele faz exatamente o que deseja. Em primeiro lugar, reveja os recursos que são implementados. Se pretender adicionar, remover ou alterar recursos no modelo. Para obter descrições e sintaxe dos recursos, consulte [referência de modelo do Azure Resource Manager](/azure/templates/).

![Referência de modelo](./media/how-to-create-template/template-reference.png)

Depois de rever essas propriedades, efetue as alterações necessárias. Para obter recomendações sobre como definir os recursos, veja [recursos - práticas recomendados](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Adicionar ou remover parâmetros

Também poderá ter de ajustar os parâmetros para o modelo. Pode adicionar ou remover parâmetros com base na quantidade de personalização que pretende ativar durante a implementação. Para obter recomendações sobre como utilizar parâmetros, veja [parâmetros - práticas recomendados](template-best-practices.md#parameters).

## <a name="add-tags"></a>Adicionar etiquetas

Pode adicionar etiquetas aos seus recursos para as organizar por categorias e dividir os custos de faturação. Adicionar etiquetas é fácil, aplicá-las no JSON para o recurso. Por exemplo, a seguinte conta de armazenamento tem duas etiquetas:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
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

Também pode aplicar etiquetas dinamicamente de parâmetros. Para obter mais informações, consulte [etiquetas num modelo](resource-manager-templates-resources.md#tags).

## <a name="review-template-functions"></a>Reveja as funções de modelo

Pode observar expressões no seu modelo, que são circundadas por entre colchetes, como `"[some-expression]"`. Essas expressões usar funções de modelo para construir dinamicamente valores durante a implementação.

Por exemplo, muitas vezes, verá uma expressão, como:

```json
"name": "[parameters('siteName')]"
```

Essa expressão obtém o valor de um parâmetro. O valor é atribuído à propriedade name.

Em alternativa, poderá ver uma expressão mais complexa que utiliza várias funções, como:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Essa expressão obtém um objeto com propriedades de uma conta de armazenamento.

Para compreender o que as funções, reveja os [referência de função de modelo](resource-group-template-functions.md) documentação.

## <a name="create-more-than-one-instance"></a>Criar mais de uma instância

Às vezes deseja criar mais de uma instância de um recurso. Por exemplo, poderá ter várias contas de armazenamento. Em vez de repetir o recurso por meio de seu modelo, pode utilizar o `copy` sintaxe para especificar mais de uma instância.

O exemplo seguinte cria três contas de armazenamento:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Também pode especificar o número de instâncias dinamicamente a partir de um parâmetro. Para obter mais informações, consulte [implementar mais de uma instância de um recurso ou uma propriedade nos modelos do Azure Resource Manager](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Implementar condicionalmente um recurso

Às vezes precisa especificar durante a implementação, se um recurso no modelo é implementado. Por exemplo, poderá pretender a flexibilidade para implementar um novo recurso ou utilize um recurso existente. O `condition` elemento dá-lhe a capacidade de ativar ou desativar a implementação de um recurso. Quando a expressão no elemento condição for verdadeira, o recurso está implementado. Quando falso, o recurso será ignorado durante a implementação.

O exemplo seguinte implementa condicionalmente uma conta de armazenamento:

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

Para obter mais informações, consulte a [elemento condition](resource-manager-templates-resources.md#condition).

## <a name="review-dependencies"></a>Dependências de revisão

Alguns recursos no seu modelo tem de ser implementado antes de outros recursos. Por exemplo, o SQL server tem de existir antes da criação de base de dados SQL. Gestor de recursos implicitamente determina a ordem de implementação para os recursos quando os [fazer referência a função](resource-group-template-functions-resource.md#reference) é utilizado. No entanto, em alguns casos, terá de definir explicitamente as dependências com o `dependsOn` elemento. Reveja o seu modelo para ver se quaisquer dependências têm de ser adicionado. Tenha cuidado para não adicionar dependências desnecessárias, à medida que eles podem retardar a implementação ou criar referências circulares.

A imagem seguinte mostra a ordem da dependência para vários recursos de serviço de aplicações:

![Dependências de aplicações Web](./media/how-to-create-template/web-dependencies.png)

O exemplo seguinte mostra a parte de um modelo que define as dependências.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Para obter mais informações, veja [Definir a ordem para implementar recursos nos Modelos do Azure Resource Manager](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Rever as práticas recomendadas

Antes de implementar o modelo, consulte [práticas recomendadas do modelo do Azure Resource Manager](template-best-practices.md) ver se existem algumas recomendado abordagens que queira implementar no seu modelo.

Se precisar de utilizar o seu modelo em ambientes de cloud do Azure diferente, consulte [modelos de desenvolver o Azure Resource Manager para manter a consistência na cloud](templates-cloud-consistency.md).

## <a name="next-steps"></a>Passos Seguintes

* Para implementar um modelo, veja [implantar com a CLI do Azure](resource-group-template-deploy-cli.md) ou [implementar com o PowerShell](resource-group-template-deploy.md).
* Para um guia de introdução passo a passo sobre como criar um modelo, consulte [modelos de criar o Azure Resource Manager com o Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Para obter uma lista das funções disponíveis num modelo, consulte [funções de modelo](resource-group-template-functions.md).
