---
title: Ligar à Pesquisa bing
description: Automatizar tarefas e fluxos de trabalho que encontram resultados na Pesquisa de Bing utilizando aplicações da Lógica Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665892"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Encontre resultados em Bing Search usando aplicações da Lógica Azure

Este artigo mostra como pode encontrar notícias, vídeos e outros itens através de Bing Search a partir de dentro de uma aplicação lógica com o conector Bing Search. Desta forma, pode criar aplicações lógicas que automatizam tarefas e fluxos de trabalho para processar resultados de pesquisa e disponibilizar esses itens para outras ações. 

Por exemplo, pode encontrar notícias com base em critérios de pesquisa e fazer com que o Twitter publique esses itens como tweets no seu feed do Twitter.

Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). Se você é novo em aplicações lógicas, reveja [o que são As Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e [Quickstart: Crie a sua primeira aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a referência do [conector Bing Search](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de Serviços Cognitivos](../cognitive-services/cognitive-services-apis-create-account.md)

* Uma [chave API bing search](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), que fornece acesso da sua aplicação lógica para as APIs de Pesquisa bing

* A aplicação lógica onde pretende aceder ao seu Hub de Eventos. Para iniciar a sua aplicação lógica com um gatilho bing Search, precisa de uma [aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicione um gatilho de pesquisa bing

Nas Aplicações Lógicas Do Azure, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é satisfeita. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação.

1. No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

2. Na caixa de pesquisa, introduza "Bing search" como filtro. A partir da lista de gatilhos, selecione o gatilho que deseja.

   Este exemplo usa este gatilho: Bing Search - Em novo artigo de **notícias**

   ![Encontre o gatilho de pesquisa de Bing](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se for solicitado para obter detalhes de ligação, [crie agora a sua ligação Bing Search](#create-connection).
Ou, se a sua ligação já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, forneça critérios para devolver artigos de notícias correspondentes da Bing Search.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Pesquisa de consulta | Sim | <*palavras de pesquisa*> | Introduza as palavras-chave de pesquisa que pretende utilizar. |
   | Mercado | Sim | <*local*> | O local de busca. O padrão é "en-US", mas pode selecionar outro valor. |
   | Busca segura | Sim | <*nível de pesquisa*> | O nível de filtro para excluir o conteúdo adulto. O padrão é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*contagem de resultados*> | Devolva o número especificado de resultados. O padrão é de 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*valor de salto*> | O número de resultados a saltar antes de devolver resultados |
   |||||

   Por exemplo:

   ![Configurar o gatilho](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecione o intervalo e a frequência para saber quantas vezes pretende que o gatilho verifique se há resultados.

5. Quando terminar, na barra de ferramentas de design, selecione **Save**.

6. Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Adicione uma ação de pesquisa bing

Nas Aplicações Lógicas Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um passo no seu fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, a aplicação lógica começa com um gatilho bing Search que devolve artigos de notícias que correspondem aos critérios especificados.

1. No portal Azure ou No Estúdio Visual, abra a sua aplicação lógica no Logic App Designer. Este exemplo utiliza o portal Azure.

2. Sob o gatilho ou ação, selecione **Novo passo** > **Adicione uma ação**.

   Este exemplo usa este gatilho:

   **Bing Search - Em novos artigos**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre os passos existentes, mova o rato sobre a seta de ligação. 
   Selecione o**+** sinal de mais ( ) que aparece e, em seguida, selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, introduza "Bing search" como filtro.
Na lista de ações, selecione a ação que deseja.

   Este exemplo utiliza esta ação:

   **Bing Search - Lista de notícias por consulta**

   ![Encontre a ação de pesquisa de Bing](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se for solicitado para obter detalhes de ligação, [crie agora a sua ligação Bing Search](#create-connection). Ou, se a sua ligação já existir, forneça as informações necessárias para a ação.

   Para este exemplo, forneça os critérios para a devolução de um subconjunto dos resultados do gatilho.

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Pesquisa de consulta | Sim | <*expressão de pesquisa*> | Introduza uma expressão para consultar os resultados do gatilho. Pode selecionar a partir dos campos da lista de conteúdos dinâmicos ou criar uma expressão com o construtor de expressão. |
   | Mercado | Sim | <*local*> | O local de busca. O padrão é "en-US", mas pode selecionar outro valor. |
   | Busca segura | Sim | <*nível de pesquisa*> | O nível de filtro para excluir o conteúdo adulto. O padrão é "Moderado", mas seleciona outro nível. |
   | Contagem | Não | <*contagem de resultados*> | Devolva o número especificado de resultados. O padrão é de 20, mas pode especificar outro valor. O número real de resultados devolvidos pode ser inferior ao número especificado. |
   | Desvio | Não | <*valor de salto*> | O número de resultados a saltar antes de devolver resultados |
   |||||

   Por exemplo, suponha que quer os resultados cujo nome de categoria inclui a palavra "tecnologia".

   1. Clique na caixa **De Pesquisa** para que a lista de conteúdos dinâmicos apareça. 
   A partir dessa lista, selecione **Expression** para que o construtor de expressão apareça. 

      ![Gatilho de pesquisa bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora pode começar a criar a sua expressão.

   2. Na lista de funções, selecione a função **contém()** que aparece na caixa de expressão. Clique em **conteúdo dinâmico** para que a lista de campo reapareça, mas certifique-se de que o cursor permanece dentro dos parênteses.

      ![Selecionar uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Da lista de campo, selecione **Categoria,** que se converte num parâmetro. 
   Adicione uma vírposta após o primeiro parâmetro, e depois da vírmeta, adicione esta palavra:`'tech'` 

      ![Selecione um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando tiver terminado, selecione **OK**.

      A expressão aparece agora na caixa **De Pesquisa** neste formato:

      ![Expressão acabada](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na vista de código, esta expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando terminar, na barra de ferramentas de design, selecione **Save**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Ligar à Pesquisa bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando for solicitado informações sobre ligação, forneça estes detalhes:

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Ligação | Sim | <*nome de ligação*> | O nome a criar para a sua ligação |
   | Versão da API | Sim | <*Versão API*> | Por predefinição, a versão API bing Search está definida para a versão atual. Pode selecionar uma versão anterior, se necessário. |
   | Chave da API | Sim | <*Chave API*> | A chave da API bing search que você tem mais cedo. Se não tem uma chave, pegue a sua [chave API agora.](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api) |  
   |||||  

   Por exemplo:

   ![Criar ligação](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando terminar, selecione **Criar**.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos, tais como gatilhos, ações e limites, conforme descrito pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/bingsearch/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
