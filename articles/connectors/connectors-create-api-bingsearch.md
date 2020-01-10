---
title: Conectar-se ao Pesquisa do Bing
description: Automatizar tarefas e fluxos de trabalho que localizam resultados em Pesquisa do Bing usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: e547ae59f7b3260f46756825bca2bef1c10bcc97
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665892"
---
# <a name="find-results-in-bing-search-by-using-azure-logic-apps"></a>Localizar resultados em Pesquisa do Bing usando os aplicativos lógicos do Azure

Este artigo mostra como você pode encontrar notícias, vídeos e outros itens por meio de Pesquisa do Bing de dentro de um aplicativo lógico com o conector de Pesquisa do Bing. Dessa forma, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para processar resultados da pesquisa e tornar esses itens disponíveis para outras ações. 

Por exemplo, você pode encontrar itens de notícias com base em critérios de pesquisa e fazer com que o Twitter poste esses itens como tweets no seu feed do Twitter.

Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). Se você for novo em aplicativos lógicos, examine [o que é o início rápido e aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) [: Crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Para obter informações técnicas específicas do conector, consulte a [referência do conector do pesquisa do Bing](https://docs.microsoft.com/connectors/bingsearch/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta de serviços cognitivas](../cognitive-services/cognitive-services-apis-create-account.md)

* Uma [chave de API pesquisa do Bing](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), que fornece acesso de seu aplicativo lógico ao APIs de pesquisa do Bing

* O aplicativo lógico no qual você deseja acessar o Hub de eventos. Para iniciar seu aplicativo lógico com um gatilho Pesquisa do Bing, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Adicionar um gatilho de Pesquisa do Bing

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. Na caixa de pesquisa, insira "pesquisa do Bing" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   Este exemplo usa este gatilho: **pesquisa do Bing novo artigo de notícias**

   ![Localizar Pesquisa do Bing gatilho](./media/connectors-create-api-bing-search/add-trigger.png)

3. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão pesquisa do Bing agora](#create-connection).
Ou, se sua conexão já existir, forneça as informações necessárias para o gatilho.

   Para este exemplo, forneça critérios para retornar artigos de notícias correspondentes de Pesquisa do Bing.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Search Query | Sim | <*search-words*> | Insira as palavras-chave de pesquisa que você deseja usar. |
   | Comercializar | Sim | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas você pode selecionar outro valor. |
   | Safe Search | Sim | <*search-level*> | O nível de filtro para excluir conteúdo adulto. O padrão é "moderado", mas você seleciona outro nível. |
   | Contagem | Não | <*results-count*> | Retorna o número de resultados especificado. O padrão é 20, mas você pode especificar outro valor. O número real de resultados retornados pode ser menor que o número especificado. |
   | Desvio | Não | > *de <de ignorar valor* | O número de resultados a serem ignorados antes de retornar os resultados |
   |||||

   Por exemplo:

   ![Configurar gatilho](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Selecione o intervalo e a frequência para a frequência com que você deseja que o gatilho Verifique os resultados.

5. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

6. Agora, continue adicionando uma ou mais ações ao seu aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Adicionar uma ação de Pesquisa do Bing

Em aplicativos lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa no fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com um gatilho Pesquisa do Bing que retorna artigos de notícias que correspondem aos critérios especificados.

1. No portal do Azure ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico. Este exemplo usa o portal do Azure.

2. No gatilho ou ação, selecione **nova etapa** > **Adicionar uma ação**.

   Este exemplo usa este gatilho:

   **Pesquisa do Bing-no novo artigo de notícias**

   ![Adicionar ação](./media/connectors-create-api-bing-search/add-action.png)

   Para adicionar uma ação entre as etapas existentes, mova o mouse sobre a seta de conexão. 
   Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "pesquisa do Bing" como filtro.
Na lista ações, selecione a ação desejada.

   Este exemplo usa esta ação:

   **Pesquisa do Bing-listar notícias por consulta**

   ![Pesquisa do Bing ação de localizar](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Se você for solicitado a fornecer detalhes de conexão, [Crie sua conexão pesquisa do Bing agora](#create-connection). Ou, se sua conexão já existir, forneça as informações necessárias para a ação.

   Para este exemplo, forneça os critérios para retornar um subconjunto dos resultados do gatilho.

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Search Query | Sim | <*search-expression*> | Insira uma expressão para consultar os resultados do gatilho. Você pode selecionar um dos campos na lista de conteúdo dinâmico ou criar uma expressão com o construtor de expressões. |
   | Comercializar | Sim | <*locale*> | A localidade de pesquisa. O padrão é "en-US", mas você pode selecionar outro valor. |
   | Safe Search | Sim | <*search-level*> | O nível de filtro para excluir conteúdo adulto. O padrão é "moderado", mas você seleciona outro nível. |
   | Contagem | Não | <*results-count*> | Retorna o número de resultados especificado. O padrão é 20, mas você pode especificar outro valor. O número real de resultados retornados pode ser menor que o número especificado. |
   | Desvio | Não | > *de <de ignorar valor* | O número de resultados a serem ignorados antes de retornar os resultados |
   |||||

   Por exemplo, suponha que você queira os resultados cujo nome de categoria inclua a palavra "Tech".

   1. Clique na caixa de **consulta de pesquisa** para que a lista de conteúdo dinâmico seja exibida. 
   Nessa lista, selecione **expressão** para que o construtor de expressões seja exibido. 

      ![Gatilho de Pesquisa do Bing](./media/connectors-create-api-bing-search/bing-search-action.png)

      Agora você pode começar a criar sua expressão.

   2. Na lista funções, selecione a função **Contains ()** , que aparece na caixa expressão. Clique em **conteúdo dinâmico** para que a lista de campos reapareça, mas verifique se o cursor permanece dentro dos parênteses.

      ![Selecionar uma função](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Na lista de campos, selecione **categoria**, que converte para um parâmetro. 
   Adicione uma vírgula após o primeiro parâmetro e, depois da vírgula, adicione esta palavra: `'tech'` 

      ![Selecionar um campo](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Quando tiver terminado, selecione **OK**.

      A expressão agora aparece na caixa de **consulta de pesquisa** neste formato:

      ![Expressão concluída](./media/connectors-create-api-bing-search/resolved-expression.png)

      Na exibição de código, essa expressão aparece neste formato:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Conectar-se ao Pesquisa do Bing

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Quando as informações de conexão forem solicitadas, forneça estes detalhes:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | Nome da Ligação | Sim | <*nome da conexão*> | O nome a ser criado para a conexão |
   | Versão da API | Sim | API <> *de versão* | Por padrão, a versão Pesquisa do Bing API é definida como a versão atual. Você pode selecionar uma versão anterior conforme necessário. |
   | Chave de API | Sim | <*API-key*> | A chave de API Pesquisa do Bing que você obteve anteriormente. Se você não tiver uma chave, obtenha sua [chave de API agora mesmo](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Por exemplo:

   ![Criar ligação](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Quando terminar, selecione **Criar**.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/bingsearch/).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
