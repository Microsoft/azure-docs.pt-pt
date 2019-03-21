---
title: Converter os dados JSON com transformações de fluidos - Azure Logic Apps | Documentos da Microsoft
description: Criar transformações ou mapas para transformações de JSON avançadas com Logic Apps e o modelo de líquidos
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 3441350a07047676ac43de23262be6c54912162c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104170"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Execute transformações de JSON avançadas com modelos de fluidos no Azure Logic Apps

Pode executar transformações de JSON básicas nas suas aplicações lógicas com ações de operação de dados nativos, como **Compose** ou **Parse JSON**. Para efetuar transformações avançadas do JSON, pode criar modelos ou mapas com [líquidos](https://shopify.github.io/liquid/), que é uma linguagem de modelo de código-fonte aberto para aplicações web flexível. Modelos de fluidos permitem-lhe definir como transformar a saída JSON e suporta mais complexas transformações de JSON, por exemplo, as iterações, controlam fluxos, variáveis e assim por diante. 

Portanto, antes de poder executar uma transformação líquida na sua aplicação lógica, primeiro define o JSON para o mapeamento de JSON com um modelo de fluidos e uma loja que mapeiam na sua conta de integração. Este artigo mostra-lhe como criar e utilizar este modelo de fluidos ou o mapa. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, pode [começar com uma conta do Azure gratuita](https://azure.microsoft.com/free/). Ou, [Inscreva-se uma subscrição pay as you go](https://azure.microsoft.com/pricing/purchase-options/).

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Básica [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Conhecimento básico sobre [linguagem do modelo de fluidos.](https://shopify.github.io/liquid/)

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Criar modelo de fluidos ou mapa para a sua conta de integração

1. Neste exemplo, crie o modelo de fluidos de exemplo descrito neste passo. No seu modelo de fluidos, pode usar [Liquid filtra](https://shopify.github.io/liquid/basics/introduction/#filters), que utilizam [DotLiquid](https://dotliquidmarkup.org/) e C# convenções de nomenclatura. No entanto, certifique-se de que *iniciar o filtro de nomes com carateres maiúsculos*, não minúsculos. 

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

2. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, localize e selecione a sua conta de integração.

   ![Selecione a conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Sob **componentes**, selecione **Maps**.

    ![Selecione o maps](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Escolher **adicionar** e forneça estes detalhes para o seu mapa:

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Nome** | JsonToJsonTemplate | O nome para o seu mapa, o que é "JsonToJsonTemplate" neste exemplo | 
   | **Tipo de mapa** | **liquid** | O tipo para seu mapa. Para JSON e transformação de JSON, tem de selecionar **líquidos**. | 
   | **Mapa** | "SimpleJsonToJsonTemplate.liquid" | Um líquidos modelo ou do mapa de arquivo existente para utilizar para transformação, que é "SimpleJsonToJsonTemplate.liquid" neste exemplo. Para localizar este ficheiro, pode utilizar o Seletor de ficheiros. |
   ||| 

   ![Adicionar modelo de líquidos](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Adicione a ação de fluidos para transformação de JSON

1. No portal do Azure, siga estes passos para [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. No Estruturador da aplicação lógica, adicione a [acionador de pedido](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) à sua aplicação lógica.

3. No acionador, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "liquid" como o filtro e selecione a ação: **Transformar JSON para JSON - Liquid**

   ![Localize e selecione a ação de líquidos](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Clique no interior da **conteúdo** caixa para que é apresentada a lista de conteúdo dinâmica e selecione o **corpo** token.
  
   ![Selecionar o corpo](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Partir do **mapa** , selecione o modelo de fluidos, que é "JsonToJsonTemplate" neste exemplo.

   ![Selecione o mapa](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Se a lista de mapas está vazia, é muito provável que a aplicação lógica não está ligada à sua conta de integração. 
   Para ligar a sua aplicação lógica para a conta de integração com o modelo de fluidos ou do mapa, siga estes passos:

   1. No menu da aplicação lógica, selecione **definições de fluxo de trabalho**.

   2. Partir do **Selecione uma conta de integração** lista, selecione a sua conta de integração e escolha **guardar**.

      ![Aplicação de lógica de ligação para a conta de integração](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Teste a aplicação lógica

Lance a entrada JSON para a aplicação lógica a partir [Postman](https://www.getpostman.com/postman) ou uma ferramenta semelhante. A saída JSON transformada da sua aplicação lógica é semelhante a este exemplo:
  
![Exemplo de saída](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Mais exemplos de ação de líquidos
Liquid não está limitado a apenas as transformações de JSON. Aqui estão outras ações de transformação disponíveis que utilizam líquida.

* Transformar JSON em text
  
  Este é o modelo de fluidos utilizado para este exemplo:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Seguem-se entradas de exemplo e saídas:
  
   ![Exemplo de saída JSON em text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformar XML em JSON
  
  Este é o modelo de fluidos utilizado para este exemplo:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Seguem-se entradas de exemplo e saídas:

   ![Resultado de exemplo XML em JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformar XML em text
  
  Este é o modelo de fluidos utilizado para este exemplo:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Seguem-se entradas de exemplo e saídas:

   ![Resultado de exemplo XML em text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre o maps](../logic-apps/logic-apps-enterprise-integration-maps.md "Saiba mais sobre o maps de integração do enterprise")  

