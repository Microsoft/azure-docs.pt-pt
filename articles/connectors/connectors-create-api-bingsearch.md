---
title: Ligar a pesquisa do Bing - Azure Logic Apps
description: Encontre notícias com APIs de REST de pesquisa do Bing e o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313554"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Encontre notícias com pesquisa do Bing e o Azure Logic Apps

Este artigo mostra como encontrará notícias, vídeos e outros itens por meio da pesquisa do Bing de dentro de uma aplicação lógica com o conector de pesquisa do Bing. Dessa forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para processamento de resultados da pesquisa e tornam esses itens disponíveis para outras ações. 

Por exemplo, pode encontrar itens de notícias com base nos critérios de pesquisa e ter o Twitter, publicar esses itens como feed de tweets no seu Twitter.

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [início rápido: Criar a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">referência do conector de pesquisa do Bing</a>.

## <a name="prerequisites"></a>Pré-requisitos

* A [conta dos serviços cognitivos](../cognitive-services/cognitive-services-apis-create-account.md)

* R [chave de API de pesquisa Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), que fornece acesso da sua aplicação lógica para as APIs de pesquisa do Bing

* A aplicação de lógica onde pretende aceder ao seu Hub de eventos. Para começar a sua aplicação lógica com um acionador de pesquisa do Bing, tem um [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicionar um acionador de pesquisa do Bing

No Azure Logic Apps, todas as aplicações lógicas têm de começar com uma [acionador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico acontece ou quando for cumprida uma condição específica. Sempre que o acionador é acionado, o motor do Logic Apps cria uma instância da aplicação lógica e começa a ser executado o fluxo de trabalho da sua aplicação.

1. O portal do Azure ou o Visual Studio, crie uma aplicação lógica em branco, que abre o Estruturador da aplicação lógica. Este exemplo utiliza o portal do Azure.

2. Na caixa de pesquisa, introduza "Pesquisa do Bing" como o filtro. Na lista de disparadores, selecione o acionador que pretende.

   Este exemplo utiliza este acionador: **Pesquisa do Bing - no novo artigo de notícias**

   ![Localizar acionador de pesquisa do Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de pesquisa do Bing agora](#create-connection).
Em alternativa, se a ligação já existir, fornecer as informações necessárias para o acionador.

   Neste exemplo, fornece critérios para retornar os artigos de notícias correspondente de pesquisa do Bing.

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de Pesquisa | Sim | <*search-words*> | Introduza as palavras-chave de pesquisa que pretende utilizar. |
   | Comercializar | Sim | <*locale*> | A localidade de pesquisa. A predefinição é "en-US", mas pode selecionar outro valor. |
   | Pesquisa segura | Sim | <*search-level*> | O nível de filtro para excluir o conteúdo para adultos. A predefinição é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*results-count*> | Devolva o número especificado de resultados. A predefinição é 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*skip-value*> | O número de resultados a ignorar antes de retornar resultados |
   |||||

   Por exemplo:

   ![Configurar o acionador](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecione o intervalo e a frequência para a frequência com que pretende que o acionador para verificar a existência de resultados.

5. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

6. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados de Acionador.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Adicionar uma ação de pesquisa do Bing

No Azure Logic Apps, um [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no fluxo de trabalho que se segue um acionador ou outra ação. Neste exemplo, a aplicação lógica começa com um acionador de pesquisa do Bing que retorna artigos de notícias que correspondam aos critérios especificados.

1. No portal do Azure ou Visual Studio, abra a aplicação lógica no Estruturador da aplicação lógica. Este exemplo utiliza o portal do Azure.

2. Sob o acionador ou ação, escolha **novo passo** > **adicionar uma ação**.

   Este exemplo utiliza este acionador:

   **Pesquisa do Bing - no novo artigo de notícias**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre os passos existentes, mova o rato por cima da seta de ligação. 
   Selecione o sinal de adição (**+**) que é apresentada e, em seguida, escolha **adicionar uma ação**.

3. Na caixa de pesquisa, introduza "Pesquisa do Bing" como o filtro.
Na lista de ações, selecione a ação que pretende.

   Este exemplo utiliza esta ação:

   **Pesquisa do Bing - notícias de lista por consulta**

   ![Encontrar a ação de pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se lhe for pedido para obter detalhes de ligação, [criar a ligação de pesquisa do Bing agora](#create-connection). Em alternativa, se a ligação já existir, fornecer as informações necessárias para a ação.

   Neste exemplo, fornece os critérios para retornando um subconjunto dos resultados do acionador.

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de Pesquisa | Sim | <*search-expression*> | Introduza uma expressão de consulta de resultados de Acionador. Pode selecionar a partir de campos na lista de conteúdo dinâmico ou crie uma expressão com o construtor de expressões. |
   | Comercializar | Sim | <*locale*> | A localidade de pesquisa. A predefinição é "en-US", mas pode selecionar outro valor. |
   | Pesquisa segura | Sim | <*search-level*> | O nível de filtro para excluir o conteúdo para adultos. A predefinição é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*results-count*> | Devolva o número especificado de resultados. A predefinição é 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*skip-value*> | O número de resultados a ignorar antes de retornar resultados |
   |||||

   Por exemplo, suponha que pretende que esses resultados cujo nome de categoria inclui a palavra "técnico".

   1. Clique nas **consulta de pesquisa** caixa para que a lista de conteúdo dinâmico apareça. 
   Nessa lista, escolha **expressão** para que o construtor de expressões apareça. 

      ![Acionador de pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora pode começar a criar a sua expressão.

   2. Na lista de funções, selecione o **contains()** função, que, em seguida, é apresentado na caixa de expressão. Clique em **conteúdo dinâmico** para que a lista de campos reaparece, mas certifique-se de que o cursor permanece dentro dos parênteses.

      ![Selecione uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Na lista de campos, selecione **categoria**, que converte a um parâmetro. 
   Adicione uma vírgula após o primeiro parâmetro e, após a vírgula, adicione esta palavra: `'tech'` 

      ![Selecione um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando tiver terminado, escolha **OK**.

      A expressão agora é apresentado na **consulta de pesquisa** caixa no seguinte formato:

      ![Expressão concluído](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na vista de código, esta expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando tiver terminado, na barra de ferramentas da estruturador, escolha **guardar**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Ligar a pesquisa do Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando lhe for pedido para obter informações de ligação, forneça estes detalhes:

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Ligação | Sim | <*connection-name*> | O nome para criar para a sua ligação |
   | Versão da API | Sim | <*Versão de API*> | Por predefinição, a versão de API de pesquisa do Bing está definida para a versão atual. Pode selecionar uma versão anterior, se necessário. |
   | Chave de API | Sim | <*API-key*> | A chave de API de pesquisa do Bing que recebeu anteriormente. Se não tiver uma chave, obter seu [chave de API agora](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Por exemplo:

   ![Criar ligação](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando tiver terminado, escolha **Create** (Criar).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como disparadores, ações e limites, conforme descrito pelo OpenAPI do conector (anteriormente Swagger) de ficheiros, consulte a [página de referência do conector](/connectors/bingsearch/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
