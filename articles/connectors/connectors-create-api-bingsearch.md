---
title: Ligar à Pesquisa Bing
description: Automatizar tarefas e fluxos de trabalho que encontrem resultados em Bing Search utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 52bf42434640dc965999895549b4fa12a139dcce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284069"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Encontre resultados em Bing Search utilizando aplicações Azure Logic

Este artigo mostra como pode encontrar notícias, vídeos e outros itens através de Bing Search a partir de dentro de uma aplicação lógica com o conector Bing Search. Desta forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para processar resultados de pesquisa e disponibilizar esses itens para outras ações. 

Por exemplo, pode encontrar itens noticiosos baseados em critérios de pesquisa e ter o Twitter a publicar esses itens como tweets no seu feed do Twitter.

Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a referência do [conector Bing Search](/connectors/bingsearch/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md)

* Uma [chave API de pesquisa de Bing,](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)que fornece acesso da sua aplicação lógica às APIs de pesquisa de Bing

* A aplicação lógica onde pretende aceder ao seu Centro de Eventos. Para iniciar a sua aplicação lógica com um gatilho Bing Search, precisa de uma [aplicação lógica em branco.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicione um gatilho de pesquisa Bing

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância lógica de aplicações e começa a executar o fluxo de trabalho da sua aplicação.

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

2. Na caixa de pesquisa, introduza "Bing search" como filtro. A partir da lista de gatilhos, selecione o gatilho que deseja.

   Este exemplo usa este gatilho: **Bing Search - Em novas notícias artigo**

   ![Encontre o gatilho de pesquisa de Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação Bing Search](#create-connection).
Ou, se a sua ligação já existe, forneça as informações necessárias para o gatilho.

   Para este exemplo, forneça critérios para devolver artigos de notícias correspondentes da Bing Search.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de pesquisa | Sim | <*palavras de pesquisa*> | Introduza as palavras-chave de pesquisa que pretende utilizar. |
   | Mercado | Sim | <*local*> | O local de busca. O padrão é "en-US", mas pode selecionar outro valor. |
   | Pesquisa segura | Sim | <*nível de pesquisa*> | O nível do filtro para excluir o conteúdo adulto. O padrão é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*contagem de resultados*> | Devolva o número especificado de resultados. O padrão é de 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*valor de salto*> | O número de resultados a saltar antes de devolver os resultados |
   |||||

   Por exemplo:

   ![Configurar o gatilho](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecione o intervalo e a frequência para a frequência com que pretende que o gatilho verifique se há resultados.

5. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

6. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Adicione uma ação de pesquisa de Bing

Nas Azure Logic Apps, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com um gatilho Bing Search que devolve artigos de notícias que correspondem aos critérios especificados.

1. No portal Azure ou Visual Studio, abra a sua aplicação lógica no Logic App Designer. Este exemplo utiliza o portal Azure.

2. Sob o gatilho ou ação, selecione **Novo passo**Adicione  >  **uma ação**.

   Este exemplo utiliza este gatilho:

   **Bing Search - Em novas notícias artigo**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. 
   Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

3. Na caixa de pesquisa, introduza "Bing search" como filtro.
Na lista de ações, selecione a ação desejada.

   Este exemplo utiliza esta ação:

   **Bing Search - List notícias por consulta**

   ![Encontre ação de pesquisa de Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se for solicitado para obter detalhes de ligação, [crie já a sua ligação Bing Search](#create-connection). Ou, se a sua ligação já existir, forneça as informações necessárias para a ação.

   Para este exemplo, forneça os critérios para a devolução de um subconjunto dos resultados do gatilho.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Consulta de pesquisa | Sim | <*expressão de pesquisa*> | Introduza uma expressão para consulta dos resultados do gatilho. Pode selecionar a partir dos campos na lista de conteúdos dinâmicos ou criar uma expressão com o construtor de expressão. |
   | Mercado | Sim | <*local*> | O local de busca. O padrão é "en-US", mas pode selecionar outro valor. |
   | Pesquisa segura | Sim | <*nível de pesquisa*> | O nível do filtro para excluir o conteúdo adulto. O padrão é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*contagem de resultados*> | Devolva o número especificado de resultados. O padrão é de 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*valor de salto*> | O número de resultados a saltar antes de devolver os resultados |
   |||||

   Por exemplo, suponha que queira os resultados cujo nome de categoria inclui a palavra "tech".

   1. Clique na caixa **de consulta de pesquisa** para que a lista de conteúdos dinâmicos apareça. 
   A partir dessa lista, selecione **Expression** para que o construtor de expressão apareça. 

      ![Bing Search trigger](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora pode começar a criar a sua expressão.

   2. A partir da lista de funções, selecione a função **contém()** que aparece na caixa de expressão. Clique **em Conteúdo Dinâmico** para que a lista de campo reapareça, mas certifique-se de que o seu cursor permanece dentro dos parênteses.

      ![Selecionar uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Da lista de campos, selecione **Category**, que se converte para um parâmetro. 
   Adicione uma vírgula após o primeiro parâmetro, e depois da vírgula, adicione esta palavra: `'tech'` 

      ![Selecione um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando tiver terminado, selecione **OK**.

      A expressão aparece agora na caixa **de consulta de pesquisa** neste formato:

      ![Expressão acabada](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na visão de código, esta expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando terminar, na barra de ferramentas do designer, **selecione Save**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Ligar à Pesquisa Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando lhe for solicitada informação de ligação, forneça estes detalhes:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Ligação | Sim | <*nome de conexão*> | O nome para criar para a sua ligação |
   | Versão da API | Sim | <*Versão API*> | Por predefinição, a versão API de Pesquisa Bing está definida para a versão atual. Pode selecionar uma versão anterior, se necessário. |
   | Chave de API | Sim | <*Chave API*> | A chave de API de Pesquisa Bing que recebeu anteriormente. Se não tiver uma chave, obtenha já a sua [chave API.](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api) |  
   |||||  

   Por exemplo:

   ![Criar ligação](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando concluir, selecione **Criar**.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/bingsearch/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

