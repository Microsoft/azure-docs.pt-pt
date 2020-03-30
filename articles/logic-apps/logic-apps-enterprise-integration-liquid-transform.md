---
title: Converter dados da JSON com transformações líquidas
description: Crie transformações ou mapas para transformações avançadas da JSON usando aplicações lógicas e modelo líquido
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: fb9f9cfdba07ebe0bc5800def6d93950869e9727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456639"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Realizar transformações avançadas da JSON com modelos líquidos em aplicações lógicas azure

Pode realizar transformações básicas da JSON nas suas aplicações lógicas com ações de operação de dados nativos, como **Compor** ou **Parse JSON**. Para realizar transformações avançadas de JSON, pode criar modelos ou mapas com [Líquido,](https://shopify.github.io/liquid/)que é um idioma de modelo de código aberto para aplicações web flexíveis. Um modelo líquido define como transformar a saída jSON e suporta transformações JSON mais complexas, tais como iterações, fluxos de controlo, variáveis, e assim por diante. 

Antes de poder realizar uma transformação líquida na sua aplicação lógica, tem primeiro de definir o JSON para o mapeamento JSON com um modelo de Liquid e armazenar esse mapa na sua conta de integração. Este artigo mostra-lhe como criar e usar este modelo ou mapa líquido. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [inscreva-se para uma subscrição pay-As-You-Go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica

* Conhecimento básico sobre linguagem do [modelo líquido](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Crie modelo ou mapa líquido para a sua conta de integração

1. Para este exemplo, crie o modelo líquido da amostra descrito neste passo. No seu modelo Liquid, pode utilizar [filtros líquidos,](https://shopify.github.io/liquid/basics/introduction/#filters)que utilizam convenções de nomeação [DotLiquid](https://dotliquidmarkup.org/) e C# . 

   > [!NOTE]
   > Certifique-se de que os nomes dos filtros utilizam *o invólucro* da frase no seu modelo. Caso contrário, os filtros não funcionarão. Além disso, os mapas têm [limites](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits)de tamanho de ficheiros.

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

2. Inicie sessão no [Portal do Azure](https://portal.azure.com). No menu Azure principal, selecione **Todos os recursos.** Na caixa de pesquisa, encontre e selecione a sua conta de integração.

   ![Selecione conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Em **Componentes,** selecione **Maps**.

    ![Selecione mapas](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Escolha **Adicionar** e forneça estes detalhes para o seu mapa:

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | Modelo JsonToJson | O nome para o seu mapa, que é "JsonToJsonTemplate" neste exemplo | 
   | **Tipo de mapa** | **líquido** | O tipo para o seu mapa. Para a transformação json para JSON, você deve selecionar **líquido**. | 
   | **Mapa** | "SimpleJsonToJsonTemplate.liquid" | Um modelo líquido ou ficheiro de mapa existente para usar para transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para encontrar este ficheiro, pode utilizar o apanhador de ficheiros. Para obter limites de tamanho do mapa, consulte [limites e configuração](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   ||| 

   ![Adicionar modelo líquido](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação líquida para a transformação da JSON

1. No portal Azure, siga estes passos para [criar uma aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

2. No Logic App Designer, adicione o [gatilho do Pedido](../connectors/connectors-native-reqres.md#add-request) à sua aplicação lógica.

3. Sob o gatilho, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "líquido" como filtro e selecione esta ação: **Transforme jSON para JSON - Líquido**

   ![Localizar e selecionar ação líquida](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Clique dentro da caixa **de conteúdo** para que a lista de conteúdos dinâmicos apareça e selecione o símbolo **do Corpo.**
  
   ![Selecione o corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Na lista do **Mapa,** selecione o seu modelo de liquidez, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecione mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, muito provavelmente a sua aplicação lógica não está ligada à sua conta de integração. 
   Para ligar a sua aplicação lógica à conta de integração que tem o modelo ou mapa líquido, siga estes passos:

   1. No menu de aplicações lógicas, selecione **definições de Workflow**.

   2. A partir da lista de **conta Integração,** selecione a sua conta de integração e escolha **Guardar**.

      ![Link aplicação lógica para conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Teste a sua aplicação lógica

Publique a entrada jSON na sua aplicação lógica do [Carteiro](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída jSON transformada da sua aplicação lógica parece este exemplo:
  
![Saída de exemplo](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação líquida
O líquido não se limita apenas às transformações da JSON. Aqui estão outras ações de transformação disponíveis que usam líquido.

* Transforme jSON em texto
  
  Aqui está o modelo líquido usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão as inputs e saídas da amostra:
  
   ![Exemplo de saída JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transforme xML em JSON
  
  Aqui está o modelo líquido usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão as inputs e saídas da amostra:

   ![Saída de exemplo XML para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transforme o XML em texto
  
  Aqui está o modelo líquido usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão as inputs e saídas da amostra:

   ![Exemplo de saída XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Pacote de Integração Empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Pacote de Integração Empresarial")  
* [Saiba mais sobre mapas](../logic-apps/logic-apps-enterprise-integration-maps.md "Conheça mapas de integração empresarial")  

