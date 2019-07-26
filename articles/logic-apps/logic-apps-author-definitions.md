---
title: Criar, editar ou estender JSON para definições de aplicativo lógico – aplicativos lógicos do Azure | Microsoft Docs
description: Criar e estender o JSON para definições de aplicativo lógico em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 89a77c25c75617be0e1ef92b73eec28263f53f82
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385582"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Criar, editar ou estender JSON para definições de aplicativo lógico em aplicativos lógicos do Azure

Quando você cria soluções de integração corporativa com fluxos de trabalho automatizados em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), as definições de aplicativo lógico subjacente usam JSON (JavaScript Object Notation simples e declarativo) junto com a [linguagem de definição de fluxo de trabalho (WDL) esquema](../logic-apps/logic-apps-workflow-definition-language.md) para sua descrição e validação. Esses formatos facilitam a leitura e a compreensão das definições do aplicativo lógico, sem saber muito sobre o código. Quando desejar automatizar a criação e implantação de aplicativos lógicos, você pode incluir definições de aplicativo lógico como [recursos do Azure](../azure-resource-manager/resource-group-overview.md) dentro de [modelos de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Para criar, gerenciar e implantar aplicativos lógicos, você pode usar as APIs REST [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)ou [aplicativos lógicos do Azure](https://docs.microsoft.com/rest/api/logic/).

Para trabalhar com definições de aplicativo lógico em JSON, abra o editor de exibição de código ao trabalhar no portal do Azure ou no Visual Studio ou copie a definição em qualquer editor desejado. Se você for novo em aplicativos lógicos, examine [como criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Alguns recursos de aplicativos lógicos do Azure, como a definição de parâmetros e vários gatilhos em definições de aplicativos lógicos, estão disponíveis apenas em JSON, não no designer de aplicativos lógicos.
> Portanto, para essas tarefas, você deve trabalhar no modo de exibição de código ou em outro editor.

## <a name="edit-json---azure-portal"></a>Editar JSON-portal do Azure

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. No menu à esquerda, escolha **todos os serviços**. Na caixa de pesquisa, localize "aplicativos lógicos" e, em seguida, nos resultados, selecione seu aplicativo lógico.

3. No menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione **modo de exibição de código do aplicativo lógico**.

   O editor de exibição de código é aberto e mostra a definição do aplicativo lógico no formato JSON.

## <a name="edit-json---visual-studio"></a>Editar JSON-Visual Studio

Antes de poder trabalhar em sua definição de aplicativo lógico no Visual Studio, verifique se você [instalou as ferramentas necessárias](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Para criar um aplicativo lógico com o Visual Studio, [examine o início rápido: Automatizar tarefas e processos com aplicativos lógicos do Azure-](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)Visual Studio.

No Visual Studio, você pode abrir aplicativos lógicos que foram criados e implantados diretamente do portal do Azure ou como Azure Resource Manager projetos do Visual Studio.

1. Abra a solução do Visual Studio ou o projeto [do grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) que contém seu aplicativo lógico.

2. Localize e abra a definição do aplicativo lógico, que por padrão aparece em um [modelo do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), chamado **LogicApp. JSON**. Você pode usar e personalizar este modelo para implantação em ambientes diferentes.

3. Abra o menu de atalho para a definição e o modelo do aplicativo lógico. Selecione **Abrir com o Estruturador da Aplicação Lógica**.

   ![Abrir o aplicativo lógico em uma solução do Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Se você não tiver esse comando no Visual Studio 2019, verifique se você tem as atualizações mais recentes para o Visual Studio.

4. Na parte inferior do designer, escolha **exibição de código**. 

   O editor de exibição de código é aberto e mostra a definição do aplicativo lógico no formato JSON.

5. Para retornar ao modo de exibição de designer, na parte inferior do editor de exibição de código, escolha **design**.

## <a name="parameters"></a>Parâmetros

O ciclo de vida da implantação geralmente tem ambientes diferentes para desenvolvimento, teste, preparo e produção. Quando você tem valores que deseja reutilizar em seu aplicativo lógico sem codificar ou que variam de acordo com suas necessidades de implantação, você pode criar um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para sua definição de fluxo de trabalho para que você também possa automatizar o aplicativo lógico planta. 

Siga estas etapas gerais para *parametrizar*ou definir e usar parâmetros para, esses valores em vez disso. Em seguida, você pode fornecer os valores em um arquivo de parâmetro separado que passa esses valores para o modelo. Dessa forma, você pode alterar esses valores com mais facilidade, sem precisar atualizar e reimplantar seu aplicativo lógico. Para obter detalhes completos, [consulte Visão geral: Automatize a implantação de aplicativos lógicos com modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Azure Resource Manager.

1. Em seu modelo, defina parâmetros de modelo e parâmetros de definição de fluxo de trabalho para aceitar os valores a serem usados em implantação e tempo de execução, respectivamente.

   Parâmetros de modelo são definidos em uma seção de parâmetros que está fora de sua definição de fluxo de trabalho, enquanto parâmetros de definição de fluxo de trabalho são definidos em uma seção de parâmetros que está dentro de sua definição de fluxo de trabalho

1. Substitua os valores codificados por expressões que fazem referência a esses parâmetros. Expressões de modelo usam sintaxe que difere das expressões de definição de fluxo de trabalho.

   Evite complicar seu código não usando expressões de modelo, que são avaliadas na implantação, dentro de expressões de definição de fluxo de trabalho, que são avaliadas no tempo de execução. Use apenas expressões de modelo fora de sua definição de fluxo de trabalho. Use somente expressões de definição de fluxo de trabalho dentro de sua definição de fluxo de trabalho.

   Ao especificar os valores para seus parâmetros de definição de fluxo de trabalho, você pode referenciar parâmetros de modelo usando a seção de parâmetros que está fora de sua definição de fluxo de trabalho, mas ainda dentro da definição de recurso para seu aplicativo lógico. Dessa forma, você pode passar valores de parâmetro de modelo para seus parâmetros de definição de fluxo de trabalho.

1. Armazene os valores para seus parâmetros em um [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) separado e inclua esse arquivo com a sua implantação.

## <a name="process-strings-with-functions"></a>Processar cadeias de caracteres com funções

Os aplicativos lógicos têm várias funções para trabalhar com cadeias de caracteres. Por exemplo, suponha que você deseja passar um nome de empresa de um pedido para outro. No entanto, você não tem certeza sobre o tratamento adequado para codificação de caracteres. Você pode executar a codificação Base64 nessa cadeia de caracteres, mas para evitar escapes na URL, você pode substituir vários caracteres em vez disso. Além disso, você só precisa de uma subcadeia de caracteres para o nome da empresa porque os cinco primeiros caracteres não são usados.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Estas etapas descrevem como este exemplo processa essa cadeia de caracteres, trabalhando de dentro para fora:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obtenha o [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) para o nome da empresa, para que você obtenha o número total de caracteres.

2. Para obter uma cadeia de caracteres mais curta `5`, subtraia.

3. Agora obtenha um [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Inicie no índice `5`e vá para o restante da cadeia de caracteres.

4. Converta essa Subcadeia em [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) uma cadeia de caracteres.

5. Agora [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) , todos `+` os caracteres `-` com caracteres.

6. Por fim [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) , todos `/` os caracteres `_` com caracteres.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapear itens de lista para valores de propriedade e, em seguida, usar mapas como parâmetros

Para obter resultados diferentes com base no valor de uma propriedade, você pode criar um mapa que corresponda a cada valor de propriedade a um resultado e, em seguida, usar esse mapa como um parâmetro.

Por exemplo, esse fluxo de trabalho define algumas categorias como parâmetros e um mapa que corresponde a essas categorias com uma URL específica. Primeiro, o fluxo de trabalho obtém uma lista de artigos. Em seguida, o fluxo de trabalho usa o mapa para localizar a URL correspondente à categoria de cada artigo.

*   A [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) função verifica se a categoria corresponde a uma categoria definida conhecida.

*   Depois de obter uma categoria correspondente, o exemplo efetua pull do item do mapa usando colchetes:`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Obter dados com funções de data

Para obter dados de uma fonte de dados que não dá suporte nativo a gatilhos, você pode usar funções de data para trabalhar com horários e datas. Por exemplo, essa expressão localiza quanto tempo as etapas do fluxo de trabalho estão dando, trabalhando de dentro para fora:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Na ação, extraia o `startTime`. `order`
2. Obtenha a hora atual com `utcNow()`.
3. Subtrair um segundo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Você pode usar outras unidades de tempo, como `minutes` ou `hours`.

3. Agora, você pode comparar esses dois valores. 

   Se o primeiro valor for menor que o segundo valor, mais de um segundo passará desde que o pedido foi colocado pela primeira vez.

Para formatar datas, você pode usar formatadores de cadeia de caracteres. Por exemplo, para obter o RFC1123, use [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). Saiba mais sobre a [formatação de data](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>Passos Seguintes

* [Executar etapas com base em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas com base em valores diferentes (instruções switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir etapas (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas com base no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Saiba mais sobre o [esquema de linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [ações de fluxo de trabalho e gatilhos para aplicativos lógicos do Azure](../logic-apps/logic-apps-workflow-actions-triggers.md)
