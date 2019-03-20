---
title: Criar, editar ou expandir o JSON para a lógica de definições de aplicação - Azure Logic Apps | Documentos da Microsoft
description: Criar e expandir o JSON para a lógica de definições de aplicação no Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: daeb900abc3f24a408fc1b5f6e989e5181f2a463
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862748"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Criar, editar ou expandir o JSON para a lógica de definições de aplicação no Azure Logic Apps

Quando cria enterprise soluções de integração com fluxos de trabalho no automatizados [do Azure Logic Apps](../logic-apps/logic-apps-overview.md), as definições de aplicação lógica subjacente utilizam simples e declarativa JavaScript Object Notation (JSON) juntamente com o [ Esquema de linguagem de definição (WDL) de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md) para a respetiva descrição e a validação. Esses formatos tornam lógica de definições de aplicação mais fácil de ler e entender sem saber muito sobre o código. Quando deseja automatizar a criação e implementação de aplicações lógicas, pode incluir definições de aplicação lógica como [recursos do Azure](../azure-resource-manager/resource-group-overview.md) dentro [modelos Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Para criar, gerir e implementar aplicações lógicas, em seguida, pode utilizar [do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), ou o [APIs de REST do Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Para trabalhar com definições de aplicação lógica em JSON, abra o editor de vista de código ao trabalhar no portal do Azure ou no Visual Studio ou copie a definição em qualquer editor que desejar. Se estiver familiarizado com aplicações lógicas, reveja [como criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Alguns recursos do Azure Logic Apps, tais como a definição de parâmetros e múltiplos acionadores em definições de aplicação lógica, só estão disponíveis no JSON, não o estruturador de aplicações lógicas.
> Portanto, para essas tarefas, tem de trabalhar na vista de código ou outro editor.

## <a name="edit-json---azure-portal"></a>Editar JSON - portal do Azure

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. No menu à esquerda, escolha **todos os serviços**. Na caixa de pesquisa, encontre "logic apps" e, em seguida, nos resultados, selecione a aplicação lógica.

3. No menu da sua aplicação lógica, sob **ferramentas de desenvolvimento**, selecione **vista de código de aplicação lógica**.

   O editor de vista de código é aberto e mostra a definição da aplicação lógica no formato JSON.

## <a name="edit-json---visual-studio"></a>Editar JSON - Visual Studio

Antes de pode trabalhar em sua definição da aplicação lógica no Visual Studio, certifique-se de que [instalado as ferramentas necessárias](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Para criar uma aplicação lógica com o Visual Studio, reveja [início rápido: Automatizar tarefas e processos com o Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

No Visual Studio, é possível abrir as aplicações lógicas que foram criadas e implementados seja diretamente no portal do Azure ou que os projetos do Azure Resource Manager a partir do Visual Studio.

1. Abra a solução do Visual Studio, ou [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) projeto, que contém a aplicação lógica.

2. Localize e abra a definição da sua aplicação lógica, que, por predefinição, é apresentado numa [modelo do Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), denominada **Logicapp**. Pode utilizar e personalizar este modelo para implementação em diferentes ambientes.

3. Abra o menu de atalho para a sua definição da aplicação lógica e o modelo. Selecione **Abrir com o Estruturador da Aplicação Lógica**.

   ![Aplicação de lógica aberto numa solução do Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. Na parte inferior do designer, escolha **vista de código**. 

   O editor de vista de código é aberto e mostra a definição da aplicação lógica no formato JSON.

5. Para regressar à exibição de design, na parte inferior do editor de vista de código, escolha **Design**.

## <a name="parameters"></a>Parâmetros

Parâmetros permitem-lhe reutilizar os valores em toda a sua aplicação lógica e são ideais para substituir os valores que podem ser alteradas frequentemente. Por exemplo, se tiver um endereço de e-mail que pretende utilizar em vários locais, deve definir esse endereço de e-mail como um parâmetro.

Parâmetros também são úteis quando precisar de parâmetros de substituição em ambientes diferentes, saiba mais sobre [parâmetros para a implantação](#deployment-parameters) e o [API de REST para obter a documentação do Azure Logic Apps](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Parâmetros só estão disponíveis na vista de código.

Na [primeira aplicação de lógica de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md), criou um fluxo de trabalho que envia mensagens de e-mail quando aparecem mensagens novas no feed RSS de um Web site. URL do feed é codificado, para que este exemplo mostra como substituir o valor da consulta com um parâmetro para que pode alterar o URL do feed de mais facilmente.

1. Na vista de código, localize a `parameters : {}` de objeto e adicionar um `currentFeedUrl` objeto:

   ``` json
   "currentFeedUrl" : {
      "type" : "string",
      "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. No `When_a_feed-item_is_published` ação, localize o `queries` secção e substitua o valor de consulta com `"feedUrl": "#@{parameters('currentFeedUrl')}"`.

   **Antes de**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },
   ```

   **Depois de**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },
   ```

   Para associar as cadeias de caracteres de dois ou mais, também pode utilizar o `concat` função. 
   Por exemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona da mesma forma que o exemplo anterior.

3.  Quando tiver terminado, escolha **Save** (Guardar).

Agora pode alterar o RSS feed, passando um URL diferente por meio do Web site a `currentFeedURL` objeto.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parâmetros de implementação para ambientes diferentes

Normalmente, os ciclos de vida de implementação tem ambientes para desenvolvimento, teste e produção. Por exemplo, pode usar a mesma definição de aplicação lógica em todos estes ambientes, mas utilizar bases de dados diferentes. Da mesma forma, pode querer utilizar a mesma definição em diferentes regiões para elevada disponibilidade, mas quiser a cada instância da aplicação lógica para utilizar a base de dados nessa região.

> [!NOTE]
> Neste cenário é diferente de parâmetros de tirar *tempo de execução* onde deve usar o `trigger()` funcionar em vez disso.

Esta é uma definição básica:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
No real `PUT` pedido para o logic apps, pode fornecer o parâmetro `uri`. Em cada ambiente, pode fornecer um valor diferente para o `connection` parâmetro. Uma vez que já não existe um valor predefinido, o payload da aplicação lógica requer este parâmetro:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
```

Para obter mais informações, consulte a [API REST para a documentação do Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Cadeias de caracteres de processo com as funções

O Logic Apps tem várias funções para trabalhar com cadeias de caracteres. Por exemplo, suponha que quiser passar um nome de empresa a partir de um pedido para outro sistema. No entanto, não tiver a certeza sobre a manipulação adequada para a codificação de caracteres. Poderia realizar a codificação base64 nessa cadeia de caracteres, mas para evitar escapa no URL, pode substituir vários caracteres em vez disso. Além disso, só precisa uma subcadeia para o nome da empresa porque não são utilizados os primeiros cinco carateres.

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

Estes passos descrevem como neste exemplo processa essa cadeia de caracteres, trabalhar a partir de dentro para fora:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obter o [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) para o nome da empresa, por isso, obtém o número total de carateres.

2. Para obter uma cadeia de caracteres mais curta, subtrair `5`.

3. Obtenha agora um [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Iniciar no índice `5`e vá para o restante da cadeia de caracteres.

4. Converter este substring para um [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) cadeia de caracteres.

5. Agora [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) todos os `+` carateres com `-` carateres.

6. Por fim, [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) todos os `/` carateres com `_` carateres.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Mapear os itens de lista para valores de propriedade, em seguida, utilizar mapas como parâmetros

Para obter resultados diferentes com base em valor de uma propriedade, pode criar um mapa que corresponde a cada valor de propriedade a um resultado de, em seguida, utilize esse mapa como um parâmetro.

Por exemplo, este fluxo de trabalho define algumas categorias como parâmetros e um mapa que corresponde a essas categorias com uma URL específica. Em primeiro lugar, o fluxo de trabalho obtém uma lista dos artigos. Em seguida, o fluxo de trabalho utiliza o mapa para localizar o URL que correspondem a categoria para cada artigo.

*   O [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) função verifica se a categoria corresponde a uma categoria de definidos conhecida.

*   Depois de obter uma categoria correspondente, o exemplo obtém o item do mapa usando Parênteses Retos: `parameters[...]`

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

## <a name="get-data-with-date-functions"></a>Obter dados com as funções de data

Para obter dados de uma origem de dados que não suporta nativamente *acionadores*, pode utilizar o data para trabalhar com tempos de funções e as datas em vez disso. Por exemplo, esta expressão localiza quanto passos neste fluxo de trabalho estão a demorar, trabalhar a partir de dentro para fora:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Partir do `order` ação, extrair o `startTime`.
2. Obter a hora atual com `utcNow()`.
3. Subtrair um segundo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Pode usar outras unidades de tempo, como `minutes` ou `hours`.

3. Agora, pode comparar estes dois valores. 

   Se o primeiro valor é menor que o segundo valor, em seguida, mais de um segundo foi aprovada uma vez que a encomenda foi realizada pela primeira vez.

Para formatar datas, pode usar formatadores de cadeia de caracteres. Por exemplo, para obter o RFC1123, utilize [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Saiba mais sobre [formatação de datas](../logic-apps/logic-apps-workflow-definition-language.md).

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

* [Execute os passos com base numa condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Execute os passos com base nos valores diferentes (declarações do comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repita os passos (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou unir a passos paralelos (ramos)](../logic-apps/logic-apps-control-flow-branches.md)
* [Execute os passos com base no estado da ação agrupados (âmbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Saiba mais sobre o [esquema de linguagem de definição de fluxo de trabalho para o Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [ações de fluxo de trabalho e acionadores para o Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
