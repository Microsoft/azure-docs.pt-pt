---
title: Converter dados JSON com transformações líquidas
description: Crie transformações ou mapas para transformações avançadas de JSON usando Apps lógicas e modelo líquido
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/01/2020
ms.openlocfilehash: d2598dfe9d7972dcb764abf4a1239613a1e8417a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879178"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Realizar transformações JSON avançadas com modelos Liquid no Azure Logic Apps

Pode realizar transformações JSON básicas nas suas aplicações lógicas com ações de operação de **dados nativos como Compose** ou **Parse JSON.** Para realizar transformações avançadas de JSON, você pode criar modelos ou mapas com [Liquid](https://shopify.github.io/liquid/), que é uma linguagem de modelo de código aberto para aplicações web flexíveis. Um modelo Liquid define como transformar a saída JSON e suporta transformações JSON mais complexas, tais como iterações, fluxos de controlo, variáveis e assim por diante.

Antes de realizar uma transformação líquida na sua aplicação lógica, tem primeiro de definir o mapeamento JSON para JSON com um modelo Liquid e armazenar esse mapa na sua conta de integração. Este artigo mostra-lhe como criar e usar este modelo ou mapa líquido.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica

* Conhecimento básico sobre [a linguagem do modelo líquido](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Crie o modelo líquido ou o mapa para a sua conta de integração

1. Para este exemplo, crie o modelo líquido de amostra descrito neste passo. No seu modelo Liquid, pode utilizar [filtros Líquidos,](https://shopify.github.io/liquid/basics/introduction/#filters)que utilizam convenções de nomeação [DotLiquid](https://github.com/dotliquid/dotliquid) e C#.

   > [!NOTE]
   > Certifique-se de que os nomes do filtro usam *o invólucro da frase* no seu modelo. Caso contrário, os filtros não funcionam. Além disso, os mapas têm [limites de tamanho de ficheiro.](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)

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

1. No [portal Azure,](https://portal.azure.com)a partir da caixa de pesquisa Azure, insira `integration accounts` e selecione **contas de Integração**.

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
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação líquida para a transformação JSON

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

Publique a entrada JSON na sua aplicação lógica do [Carteiro](https://www.getpostman.com/postman) ou numa ferramenta semelhante. A saída JSON transformada da sua aplicação lógica parece este exemplo:
  
![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação líquida
O líquido não se limita apenas às transformações JSON. Aqui estão outras ações de transformação disponíveis que usam o Liquid.

* Transformar JSON em texto
  
  Aqui está o modelo líquido usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão as entradas e saídas da amostra:
  
   ![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Aqui está o modelo líquido usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão as entradas e saídas da amostra:

   ![Saída de exemplo XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em texto
  
  Aqui está o modelo líquido usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão as entradas e saídas da amostra:

   ![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Próximos passos

* [Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Conheça os mapas de integração empresarial")  

