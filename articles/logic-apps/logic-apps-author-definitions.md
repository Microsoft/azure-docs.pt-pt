---
title: Criar, editar ou estender definições de fluxo de trabalho JSON de aplicação lógica
description: Como escrever, editar e alargar as definições de fluxo de trabalho JSON da sua aplicação lógica em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87060664"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>Criar, editar ou expandir JSON para definições de fluxo de aplicações lógicas no Azure Logic Apps

Quando cria soluções de integração empresarial com fluxos de trabalho automatizados em [Azure Logic Apps,](../logic-apps/logic-apps-overview.md)as definições de aplicações lógicas subjacentes utilizam a simples e declarativa Notação de Objetos JavaScript (JSON) juntamente com o [esquema de Linguagem de Definição de Fluxo de Trabalho (WDL)](../logic-apps/logic-apps-workflow-definition-language.md) para a sua descrição e validação. Estes formatos tornam as definições de aplicações lógicas mais fáceis de ler e entender sem saber muito sobre código.
Quando pretende automatizar a criação e implementação de aplicações lógicas, pode incluir definições de aplicações lógicas como [recursos Azure](../azure-resource-manager/management/overview.md) dentro [dos modelos do Azure Resource Manager](../azure-resource-manager/templates/overview.md).
Para criar, gerir e implementar aplicações lógicas, pode então utilizar [a Azure PowerShell,](/powershell/module/az.logicapp) [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md)ou as [Azure Logic Apps REST APIs](/rest/api/logic/).

Para trabalhar com definições de aplicações lógicas em JSON, abra o editor code view ao trabalhar no portal Azure ou no Visual Studio, ou copie a definição em qualquer editor que queira.
Se é novo em aplicações lógicas, [reveja como criar a sua primeira aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

> [!NOTE]
> Algumas capacidades de Azure Logic Apps, como a definição de parâmetros e múltiplos gatilhos nas definições de aplicações lógicas, estão disponíveis apenas no JSON, e não no Logic Apps Designer.
> Portanto, para estas tarefas, deve trabalhar no Code View ou noutro editor.

## <a name="edit-json---azure-portal"></a>Editar JSON - Portal Azure

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. A partir do menu esquerdo, escolha **Todos os serviços.**
Na caixa de pesquisa, encontre "aplicações lógicas", e depois, a partir dos resultados, selecione a sua aplicação lógica.

3. No menu da sua aplicação lógica, em **Ferramentas**de Desenvolvimento, selecione **Logic App Code View**.

   O editor code view abre e mostra a definição de aplicação lógica no formato JSON.

## <a name="edit-json---visual-studio"></a>Editar JSON - Estúdio Visual

Antes de poder trabalhar a definição da sua aplicação lógica no Visual Studio, certifique-se de que [instalou as ferramentas necessárias.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)
Para criar uma aplicação lógica com o Visual Studio, reveja [o Quickstart: Automatize tarefas e processos com Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

No Visual Studio, pode abrir aplicações lógicas que foram criadas e implementadas diretamente a partir do portal Azure ou como projetos Azure Resource Manager do Visual Studio.

1. Abra a solução Visual Studio, ou projeto [Azure Resource Group,](../azure-resource-manager/management/overview.md) que contém a sua aplicação lógica.

2. Encontre e abra a definição da sua aplicação lógica, que por padrão, aparece num [modelo de Gestor de Recursos,](../azure-resource-manager/templates/overview.md)nomeadoLogicApp.js** em**.
Pode utilizar e personalizar este modelo para a implementação de diferentes ambientes.

3. Abra o menu de atalho para a definição e modelo da sua aplicação lógica.
Selecione **Abrir com o Estruturador da Aplicação Lógica**.

   ![Abrir aplicativo de lógica numa solução visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Se não tiver este comando no Visual Studio 2019, verifique se tem as últimas atualizações para o Visual Studio.

4. Na parte inferior do designer, escolha **Code View**.

   O editor code view abre e mostra a definição de aplicação lógica no formato JSON.

5. Para voltar à vista do designer, na parte inferior do editor Code View, escolha **Design**.

## <a name="parameters"></a>Parâmetros

O ciclo de vida de implantação geralmente tem diferentes ambientes para desenvolvimento, teste, encenação e produção. Quando tiver valores que pretende reutilizar ao longo da sua aplicação lógica sem hardcoding ou que variam com base nas suas necessidades de implementação, pode criar um [modelo de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) para a definição de fluxo de trabalho para que também possa automatizar a implementação de aplicações lógicas.

Siga estes passos gerais para *parametrizar,* ou definir e utilizar parâmetros para, em vez disso, esses valores. Em seguida, pode fornecer os valores num ficheiro de parâmetro separado que passa esses valores para o seu modelo. Desta forma, pode alterar esses valores mais facilmente sem ter de atualizar e redistribuir a sua aplicação lógica. Para obter todos os detalhes, consulte [a visão geral: Automatize a implementação de aplicações lógicas com modelos de Gestor de Recursos Azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md).

1. No seu modelo, defina os parâmetros do modelo e os parâmetros de definição do fluxo de trabalho para aceitar os valores a utilizar na implementação e tempo de funcionamento, respectivamente.

   Os parâmetros do modelo são definidos numa secção de parâmetros que está fora da definição de fluxo de trabalho, enquanto os parâmetros de definição de fluxo de trabalho são definidos numa secção de parâmetros que está dentro da definição de fluxo de trabalho.

1. Substitua os valores codificados por expressões que referenciam estes parâmetros. As expressões do modelo usam sintaxe que difere das expressões de definição de fluxo de trabalho.

   Evite complicar o seu código não utilizando expressões de modelo, que são avaliadas na implementação, expressões de definição de fluxo de trabalho internas, que são avaliadas em tempo de execução. Utilize apenas expressões de modelo fora da definição de fluxo de trabalho. Utilize apenas expressões de definição de fluxo de trabalho dentro da definição de fluxo de trabalho.

   Quando especificar os valores para os parâmetros de definição de fluxo de trabalho, pode referenciar os parâmetros utilizando a secção de parâmetros que está fora da definição de fluxo de trabalho, mas ainda dentro da definição de recurso para a sua aplicação lógica. Desta forma, pode passar os valores dos parâmetros do modelo nos parâmetros de definição do fluxo de trabalho.

1. Guarde os valores dos seus parâmetros num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md) separado e inclua esse ficheiro com a sua implantação.

## <a name="process-strings-with-functions"></a>Cadeias de processo com funções

Logic Apps tem várias funções para trabalhar com cordas.
Por exemplo, suponha que queira passar um nome de empresa de uma ordem para outro sistema.
No entanto, não tens a certeza sobre o manuseamento adequado para codificar o carácter.
Pode executar codificação base64 nesta cadeia, mas para evitar fugas no URL, pode substituir vários caracteres. Além disso, só precisa de um sub-fundamento para o nome da empresa porque os primeiros cinco caracteres não são usados.

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

Estes passos descrevem como este exemplo processa esta cadeia, trabalhando de dentro para fora:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obtenha o [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) nome da empresa, para obter o número total de caracteres.

2. Para obter uma corda mais curta, `5` subtraia.

3. Agora arranja [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) um.
Comece no `5` índice, e vá para o resto da cadeia.

4. Converta este sub-cordão numa [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) corda.

5. Agora [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos os `+` personagens com `-` personagens.

6. Finalmente, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos os `/` personagens com `_` personagens.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapear itens de lista para valores de propriedade, em seguida, usar mapas como parâmetros

Para obter resultados diferentes com base no valor de uma propriedade, você pode criar um mapa que corresponda a cada valor de propriedade a um resultado, em seguida, use esse mapa como um parâmetro.

Por exemplo, este fluxo de trabalho define algumas categorias como parâmetros e um mapa que corresponde a essas categorias com um URL específico.
Primeiro, o fluxo de trabalho recebe uma lista de artigos. Em seguida, o fluxo de trabalho usa o mapa para encontrar o URL correspondente à categoria para cada artigo.

*   A [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) função verifica se a categoria corresponde a uma categoria definida conhecida.

*   Depois de obter uma categoria de correspondência, o exemplo retira o item do mapa utilizando suportes quadrados: `parameters[...]`

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

## <a name="get-data-with-date-functions"></a>Obter dados com funções De Data

Para obter dados de uma fonte de dados que não suporta de forma nativa *os gatilhos,* pode utilizar funções De Data para trabalhar com horários e datas.
Por exemplo, esta expressão encontra quanto tempo os passos deste fluxo de trabalho estão a tomar, trabalhando de dentro para fora:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Da `order` ação, extraia `startTime` o.
2. Obtenha o tempo atual com `utcNow()` .
3. Subtrair um segundo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Pode utilizar outras unidades de tempo, como `minutes` `hours` ou.

3. Agora, pode comparar estes dois valores.

   Se o primeiro valor for inferior ao segundo valor, então mais de um segundo passou desde que a encomenda foi colocada pela primeira vez.

Para datas de formato, pode utilizar formatters de cordas. Por exemplo, para obter o RFC1123, use [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md) .
Saiba mais sobre [formatação de datas.](../logic-apps/logic-apps-workflow-definition-language.md)

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

## <a name="next-steps"></a>Passos seguintes

* [Passos de execução com base numa condição (declarações condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Passos de execução com base em diferentes valores (declarações de switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir passos (laços)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou fundir etapas paralelas (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Passos de execução baseados no estado de ação agrupado (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Saiba mais sobre o esquema de linguagem de definição de [fluxo de trabalho para apps Azure Logic](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [ações de fluxo de trabalho e gatilhos para Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
