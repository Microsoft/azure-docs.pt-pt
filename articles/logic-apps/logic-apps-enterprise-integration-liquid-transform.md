---
title: Converter dados JSON com transformações Liquid-aplicativos lógicos do Azure | Microsoft Docs
description: Criar transformações ou mapas para transformações avançadas de JSON usando aplicativos lógicos e o modelo líquido
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 203c57a2755a3287566a774e2878a87b847337b9
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900665"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Executar transformações JSON avançadas com modelos líquidos nos aplicativos lógicos do Azure

Você pode executar transformações JSON básicas em seus aplicativos lógicos com ações de operação de dados nativas, como **Compose** ou **Parse JSON**. Para executar transformações avançadas de JSON, você pode criar modelos ou mapas com o [Liquid](https://shopify.github.io/liquid/), que é uma linguagem de modelo de software livre para aplicativos Web flexíveis. Um modelo Liquid define como transformar a saída JSON e dá suporte a transformações JSON mais complexas, como iterações, fluxos de controle, variáveis e assim por diante. 

Antes de executar uma transformação Liquid em seu aplicativo lógico, você deve primeiro definir o mapeamento JSON para JSON com um modelo líquido e armazenar o mapa em sua conta de integração. Este artigo mostra como criar e usar este modelo ou mapa Liquid. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou então, [Inscreva-se para uma assinatura paga conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica

* Conhecimento básico sobre a [linguagem de modelo Liquid.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Criar um modelo ou mapa Liquid para sua conta de integração

1. Para este exemplo, crie o modelo Liquid de exemplo descrito nesta etapa. No seu modelo Liquid, você pode usar [filtros Liquid](https://shopify.github.io/liquid/basics/introduction/#filters), que usam [DotLiquid](https://dotliquidmarkup.org/) e C# convenções de nomenclatura. 

   > [!NOTE]
   > Verifique se os nomes de filtro usam *maiúsculas e minúsculas* no seu modelo. Caso contrário, os filtros não funcionarão.

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

2. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, localize e selecione sua conta de integração.

   ![Selecionar conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Em **componentes**, selecione **mapas**.

    ![Selecionar mapas](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Escolha **Adicionar** e forneça estes detalhes para o mapa:

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Name** | JsonToJsonTemplate | O nome do mapa, que é "JsonToJsonTemplate" neste exemplo | 
   | **Tipo de mapa** | **liquid** | O tipo do mapa. Para transformação JSON para JSON, você deve selecionar **Liquid**. | 
   | **Mapeada** | "SimpleJsonToJsonTemplate.liquid" | Um modelo Liquid ou um arquivo de mapa existente a ser usado para transformação, que é "SimpleJsonToJsonTemplate. Liquid" neste exemplo. Para localizar esse arquivo, você pode usar o seletor de arquivos. |
   ||| 

   ![Adicionar modelo Liquid](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicionar a ação Liquid para transformação JSON

1. No portal do Azure, siga estas etapas para [criar um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. No designer de aplicativo lógico, adicione o [gatilho de solicitação](../connectors/connectors-native-reqres.md#add-request) ao seu aplicativo lógico.

3. No gatilho, escolha **nova etapa**. 
   Na caixa de pesquisa, digite "Liquid" como filtro e selecione esta ação: **Transformar JSON em JSON-Liquid**

   ![Localizar e Selecionar ação líquida](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Clique dentro da caixa de **conteúdo** para que a lista de conteúdo dinâmico seja exibida e selecione o token de **corpo** .
  
   ![Selecionar corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Na lista **mapa** , selecione seu modelo Liquid, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecionar mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas estiver vazia, provavelmente seu aplicativo lógico não está vinculado à sua conta de integração. 
   Para vincular seu aplicativo lógico à conta de integração que tem o modelo ou mapa Liquid, siga estas etapas:

   1. No menu do aplicativo lógico, selecione **configurações de fluxo de trabalho**.

   2. Na lista **selecionar uma conta de integração** , selecione sua conta de integração e escolha **salvar**.

      ![Vincular o aplicativo lógico à conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testar seu aplicativo lógico

Poste a entrada JSON para seu aplicativo lógico do [postmaster](https://www.getpostman.com/postman) ou de uma ferramenta semelhante. A saída JSON transformada do seu aplicativo lógico é semelhante a este exemplo:
  
![Exemplo de saída](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação Liquid
Liquid não está limitado a apenas transformações JSON. Aqui estão outras ações de transformação disponíveis que usam Liquid.

* Transformar JSON em texto
  
  Este é o modelo Liquid usado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Aqui estão exemplos de entradas e saídas:
  
   ![Exemplo de saída de JSON para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Este é o modelo Liquid usado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Aqui estão exemplos de entradas e saídas:

   ![Exemplo de XML de saída para JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em texto
  
  Este é o modelo Liquid usado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Aqui estão exemplos de entradas e saídas:

   ![Exemplo de saída de XML para texto](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Enterprise Integration Pack] (../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre mapas] (../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre mapas de integração corporativa")  

