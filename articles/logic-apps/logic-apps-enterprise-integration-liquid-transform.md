---
title: Converter JSON e XML com modelos líquidos
description: Transforme json e XML usando modelos líquidos como mapas em Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: b3919cbbe0ba7a796a21ae566afb8e2d9fa784db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88716678"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Transforme JSON e XML usando modelos líquidos como mapas em Azure Logic Apps

Quando pretende realizar transformações JSON básicas nas suas aplicações lógicas, pode utilizar [operações](../logic-apps/logic-apps-perform-data-operations.md) de **dados nativos como Compose** ou **Parse JSON.** Para transformações avançadas e complexas de JSON para JSON que tenham elementos como iterações, fluxos de controlo e variáveis, crie e use modelos que descrevam essas transformações utilizando a linguagem de modelo de código aberto [líquido.](https://shopify.github.io/liquid/) Também pode [realizar outras transformações](#other-transformations), por exemplo, JSON para texto, XML para JSON e XML para texto.

Antes de poder realizar uma transformação líquida na sua aplicação lógica, tem primeiro de criar um modelo Liquid que defina o mapeamento que deseja. Em seguida, [faça o upload do modelo como um mapa](../logic-apps/logic-apps-enterprise-integration-maps.md) na sua conta de [integração.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) Quando adicionar o **Transform JSON a JSON - Ação líquida** à sua aplicação lógica, pode selecionar o modelo Liquid como o mapa para a ação a utilizar.

Este artigo mostra-lhe como completar estas tarefas:

* Crie um modelo líquido.
* Adicione o modelo à sua conta de integração.
* Adicione a ação de transformação de Líquido à sua aplicação lógica.
* Selecione o modelo como o mapa que pretende utilizar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Conhecimento básico sobre [a linguagem do modelo líquido](https://shopify.github.io/liquid/)

  > [!NOTE]
  > A **ação Transform JSON to JSON - Liquid** segue a implementação do [DotLiquid para Líquido,](https://github.com/dotliquid/dotliquid)que difere em casos específicos da [implementação shopify para líquido.](https://shopify.github.io/liquid) Para obter mais informações, consulte [considerações do modelo liquido](#liquid-template-considerations).

## <a name="create-the-template"></a>Criar o modelo

1. Crie o modelo Liquid que utiliza como mapa para a transformação do JSON. Pode utilizar qualquer ferramenta de edição que queira.

   Para este exemplo, crie o modelo líquido de amostra, conforme descrito nesta secção:

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}

   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
         {%- endfor -%}
      ]
   }
   ```

1. Guarde o gabarito utilizando a `.liquid` extensão. Este exemplo `SimpleJsonToJsonTemplate.liquid` utiliza.

## <a name="upload-the-template"></a>Faça upload do modelo

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa do portal Azure, insira `integration accounts` e selecione **contas de Integração**.

   ![Encontrar "Contas de integração"](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Encontre e selecione a sua conta de integração.

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. No **painel de visão** geral, em **Componentes,** selecione **Maps**.

    ![Selecione azulejo "Maps"](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. No painel **Maps,** **selecione Adicionar** e fornecer estes detalhes para o seu mapa:

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | `JsonToJsonTemplate` | O nome para o seu mapa, que é "JsonToJsonTemplate" neste exemplo |
   | **Tipo de mapa** | **líquido** | O tipo para o seu mapa. Para a transformação JSON para JSON, deve selecionar **líquido**. |
   | **Mapa** | `SimpleJsonToJsonTemplate.liquid` | Um modelo líquido existente ou um ficheiro de mapa para usar para a transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para encontrar este ficheiro, pode utilizar o selecionador de ficheiros. Para limites de tamanho do mapa, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Adicionar modelo de líquido](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Adicione a ação de transformação líquida

1. No portal Azure, siga estes passos para [criar uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

1. No Logic App Designer, adicione o [gatilho Request](../connectors/connectors-native-reqres.md#add-request) à sua aplicação lógica.

1. Sob o gatilho, escolha **novo passo.** Na caixa de pesquisa, insira `liquid` como filtro e selecione esta ação: **Transform JSON para JSON - Liquid**

   ![Localizar e selecionar ação líquida](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Abra a lista **de mapas** e selecione o seu modelo Liquid, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecione mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, muito provavelmente a sua aplicação lógica não está ligada à sua conta de integração. 
   Para ligar a sua aplicação lógica à conta de integração que tem o modelo ou mapa do Líquido, siga estes passos:

   1. No menu de aplicativos logico, selecione **as definições de Workflow**.

   1. Na lista **de conta Desemisso,** selecione a sua conta de integração e selecione **Guardar**.

      ![Link app lógica para conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Adicione agora a propriedade **Content** a esta ação. Abra a nova lista **de parâmetros** e selecione **Conteúdo**.

   ![Adicionar propriedade "Content" à ação](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Para definir o valor da propriedade **do Conteúdo,** clique dentro da caixa **De Conteúdo** para que a lista de conteúdos dinâmicos apareça. Selecione o **token do corpo,** que representa a saída do conteúdo do corpo a partir do gatilho.

   ![Selecione "Body" token para o valor da propriedade "Content"](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Quando estiver pronto, a ação terá o aspeto deste exemplo:

   ![Terminou a ação "Transform JSON to JSON"](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Teste o seu aplicativo de lógica

Ao utilizar [o Carteiro](https://www.getpostman.com/postman) ou uma ferramenta semelhante, publique a entrada JSON na sua aplicação lógica. A saída JSON transformada da sua aplicação lógica parece este exemplo:

![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Considerações do modelo líquido

* Os modelos líquidos seguem os limites do tamanho do [ficheiro para mapas](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) em Azure Logic Apps.

* A **ação Transform JSON to JSON - Liquid** segue a [implementação do DotLiquid para Liquid.](https://github.com/dotliquid/dotliquid) Esta implementação é um porto para o Quadro .NET da [implementação do Shopify para Líquido](https://shopify.github.io/liquid/) e difere em [casos específicos](https://github.com/dotliquid/dotliquid/issues).

  Aqui estão as diferenças conhecidas:

  * O **Transform JSON para JSON - Ação líquida** produz de forma nativa uma cadeia, que pode incluir JSON, XML, HTML, e assim por diante. A ação liquida apenas indica que a saída de texto esperada do modelo Liquid é uma cadeia JSON. A ação instrui a sua aplicação lógica para analisar a entrada como um objeto JSON e aplica um invólucro para que o Liquid possa interpretar a estrutura JSON. Após a transformação, a ação instrui a sua aplicação lógica para analisar a saída de texto de Liquid de volta para JSON.

    DotLiquid não compreende de forma nativa json, por isso certifique-se de que escapa do personagem de backslash `\` () e de quaisquer outros caracteres JSON reservados.

  * Se o seu modelo utilizar [filtros Líquidos,](https://shopify.github.io/liquid/basics/introduction/#filters)certifique-se de que segue as [convenções de nomeação DotLiquid e C#](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing), que utilizam *o invólucro da frase*. Para todas as transformações líquidas, certifique-se de que os nomes dos filtros no seu modelo também usam o invólucro da frase. Caso contrário, os filtros não funcionam.

    Por exemplo, quando utilizar o `replace` filtro, `Replace` use, não `replace` . A mesma regra aplica-se se experimentar exemplos no [DotLiquid online](http://dotliquidmarkup.org/try-online). Para obter mais informações, consulte [os filtros Shopify Liquid](https://shopify.dev/docs/themes/liquid/reference/filters) e [os filtros DotLiquid Liquid](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). A especificação Shopify inclui exemplos para cada filtro, para comparação, pode experimentar estes exemplos no [DotLiquid - Tente online.](https://dotliquidmarkup.org/try-online)

  * O `json` filtro dos filtros de extensão Shopify não é [atualmente implementado no DotLiquid](https://github.com/dotliquid/dotliquid/issues/384). Normalmente, pode utilizar este filtro para preparar a saída de texto para a análise das cordas JSON, mas em vez disso, tem de utilizar o `Replace` filtro.

  * O filtro padrão `Replace` na implementação do [DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) utiliza [a correspondência de expressão regular (RegEx),](/dotnet/standard/base-types/regular-expression-language-quick-reference)enquanto a [implementação do Shopify](https://shopify.github.io/liquid/filters/replace/) utiliza [uma simples correspondência de cordas](https://github.com/Shopify/liquid/issues/202). Ambas as implementações parecem funcionar da mesma forma até que utilize um personagem reservado ao RegEx ou um personagem de fuga no parâmetro da correspondência.

    Por exemplo, para escapar ao backslash reservado regEx ( `\` ) escape character, use `| Replace: '\\', '\\'` , e não `| Replace: '\', '\\'` . Estes exemplos mostram como o `Replace` filtro se comporta de forma diferente quando se tenta escapar ao carácter de retrocesso. Enquanto esta versão funciona com sucesso:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Com este resultado:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Esta versão falha:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Com este erro:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Para obter mais informações, consulte [Substituir o filtro padrão utiliza padrão RegEx correspondente...](https://github.com/dotliquid/dotliquid/issues/385).

  * O `Sort` filtro na implementação do [DotLiquid](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) classifica itens numa matriz ou recolha por propriedade, mas com estas diferenças:<p>

    * Segue o [comportamento sort_natural do Shopify,](https://shopify.github.io/liquid/filters/sort_natural/)não [o comportamento do Shopify.](https://shopify.github.io/liquid/filters/sort/)

    * Classifica apenas em ordem alfanumérica de cordas. Para mais informações, consulte [o tipo numérico.](https://github.com/Shopify/liquid/issues/980)

    * Usa ordem *insensível a caso,* não ordem sensível a casos. Para obter mais informações, consulte [O filtro Sort não segue o comportamento do invólucro a partir da especificação do Shopify]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Outras transformações usando o Líquido

O líquido não se limita apenas às transformações da JSON. Também pode utilizar o Liquid para realizar outras transformações, por exemplo:

* [JSON para texto](#json-text)
* [XML para JSON](#xml-json)
* [XML para texto](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Transformar JSON em texto

Aqui está o modelo líquido que é usado para este exemplo:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Aqui estão as entradas e saídas da amostra:

![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Transformar XML em JSON

Aqui está o modelo líquido que é usado para este exemplo:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

O `JSONArrayFor` loop é um mecanismo de loop personalizado para a entrada XML para que possa criar cargas JSON que evitem uma vírgula. Além disso, a `where` condição para este mecanismo de looping personalizado usa o nome do elemento XML para comparação, em vez do valor do elemento como outros filtros Líquidos. Para obter mais informações, consulte [Deep Dive on set-body Policy - Collections of Things](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Aqui estão as entradas e saídas da amostra:

![Saída de exemplo XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Transformar XML em texto

Aqui está o modelo líquido que é usado para este exemplo:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Aqui estão as entradas e saídas da amostra:

![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passos seguintes

* [Shopify Linguagem líquida e exemplos](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid - Tente online](https://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [Questões do DotLiquid GitHub](https://github.com/dotliquid/dotliquid/issues/)
* Saiba mais sobre [mapas](../logic-apps/logic-apps-enterprise-integration-maps.md)
