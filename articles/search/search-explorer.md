---
title: Ferramenta de consulta de explorador de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste quickstart do portal Azure, utilize o Search Explorer para aprender sintaxe de consulta, expressões de consulta de teste ou inspecione um documento de pesquisa. Índices de consultas de exploradores de pesquisa em Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/07/2020
ms.openlocfilehash: 19d46c034d56c1c54f8a00f08a7e3e72e758984f
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488210"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Quickstart: Use o explorador de pesquisa para executar consultas no portal

**O Explorador** de Pesquisa é uma ferramenta de consulta incorporada usada para executar consultas contra um índice de pesquisa em Azure Cognitive Search. Esta ferramenta facilita a aprendizagem da sintaxe de consulta, testa uma consulta ou expressão de filtro, ou confirma a atualização de dados verificando se existem novos conteúdos no índice.

Este quickstart utiliza um índice existente para demonstrar o explorador de pesquisa. Os pedidos são formulados utilizando a [API Search REST,](https://docs.microsoft.com/rest/api/searchservice/)com respostas devolvidas como documentos JSON.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ O *índice de amostras realestate-us é* usado para este arranque rápido. Utilize o assistente [**de dados De importação**](search-import-data-portal.md) para criar o índice. No primeiro passo, quando solicitado pela fonte de dados, escolha **amostras** e, em seguida, selecione a fonte de dados **da amostra realestate-us.** Aceite todas as falhas do assistente para criar o índice.

## <a name="start-search-explorer"></a>Iniciar explorador de pesquisa

1. No [portal Azure,](https://portal.azure.com)abra a página de serviço de pesquisa do painel de instrumentos ou [encontre o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Explorador de pesquisa aberta da barra de comando:

   ![Procurar comando do explorador no portal](./media/search-explorer/search-explorer-cmd2.png "Procurar comando do explorador no portal")

    Ou use o **separador de explorador de pesquisa** incorporado num índice aberto:

   ![Separador de explorador de pesquisa](./media/search-explorer/search-explorer-tab.png "Separador de explorador de pesquisa")

## <a name="unspecified-query"></a>Consulta não especificada

Para uma primeira olhada no conteúdo, execute uma pesquisa vazia clicando **em Procurar** sem termos fornecidos. Uma pesquisa vazia é útil como primeira consulta porque devolve documentos inteiros para que possa rever a composição do documento. Numa busca vazia, não existe um posto de pesquisa e os documentos são devolvidos por ordem arbitrária `"@search.score": 1` (para todos os documentos). Por predefinição, 50 documentos são devolvidos num pedido de pesquisa.

Sintaxe equivalente para uma procura vazia é `*` ou `search=*` .
   
   ```http
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Exemplo de consulta não qualificada ou vazia")

## <a name="free-text-search"></a>Pesquisa de texto gratuita

Consultas de formulário livre, com ou sem operadores, são úteis para simular consultas definidas pelo utilizador enviadas de uma aplicação personalizada para a Azure Cognitive Search. Apenas os campos atribuídos como **Pespáveis** na definição de índice são digitalizados para fósforos. 

Note que quando fornece critérios de pesquisa, tais como termos de consulta ou expressões, o rank de pesquisa entra em jogo. O seguinte exemplo ilustra uma pesquisa gratuita de texto.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar dentro de resultados para termos de interesse específicos.

   ![Exemplo de consulta de texto gratuito](./media/search-explorer/search-explorer-example-freetext.png "Exemplo de consulta de texto gratuito")

## <a name="count-of-matching-documents"></a>Contagem de documentos correspondentes 

Adicione **$count=verdade** para obter o número de fósforos encontrados num índice. Numa busca vazia, a contagem é o número total de documentos no índice. Numa pesquisa qualificada, é o número de documentos correspondentes à entrada de consulta.

   ```http
   $count=true
   ```

   **Resultados**

   ![Contagem de documentos exemplo](./media/search-explorer/search-explorer-example-count.png "Contagem de documentos correspondentes em índice")

## <a name="limit-fields-in-search-results"></a>Limite os campos nos resultados da pesquisa

Adicione [**$select**](search-query-odata-select.md) para limitar os resultados aos campos explicitamente nomeados para uma saída mais legível no **explorador de pesquisa**. Para manter o fio de pesquisa e **$count=verdadeiro,** prefixo com **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Exemplo de campos limite](./media/search-explorer/search-explorer-example-selectfield.png "Restringir campos nos resultados de pesquisa")

## <a name="return-next-batch-of-results"></a>Devolva o próximo lote de resultados

A Azure Cognitive Search devolve os 50 melhores jogos com base no ranking de pesquisa. Para obter o próximo conjunto de documentos correspondentes, apêndice **$top=100&$skip=50** para aumentar o resultado definido para 100 documentos (o padrão é 50, máximo é 1000), ignorando os primeiros 50 documentos. Lembre-se que precisa de fornecer critérios de pesquisa, como um termo de consulta ou expressão, para obter resultados classificados. Note que as pontuações de pesquisa diminuem quanto mais profundo chegar aos resultados da pesquisa.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   ![Resultados da pesquisa de lote](./media/search-explorer/search-explorer-example-topskip.png "Devolva o próximo lote de resultados de pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maiores do que, menos do que, iguais)

Utilize o parâmetro [**$filter**](search-query-odata-filter.md) quando pretender especificar critérios precisos em vez de pesquisa por texto livre. O campo deve ser atribuído como **filtrado** no índice. Este exemplo procura quartos superiores a 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   ![Expressão de filtro](./media/search-explorer/search-explorer-example-filter.png "Filtrar por critérios")

## <a name="order-by-expressions"></a>Expressões ordenadas

Adicione [**$orderby**](search-query-odata-orderby.md) para classificar resultados por outro campo além da pontuação de pesquisa. O campo deve ser atribuído como **Classificado** no índice. Uma expressão de exemplo que pode usar para testar isto é:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   ![Expressão ordem](./media/search-explorer/search-explorer-example-ordery.png "Alterar a sequência de ordenação")

Expressões **$filter** e **$orderby** são construções OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Conclusões

Neste quickstart, utilizou **o Explorador de Pesquisa** para consultar um índice utilizando a API REST.

+ Os resultados são devolvidos como documentos verbosos da JSON para que possa ver a construção e o conteúdo do documento, na totalidade. Pode utilizar expressões de consulta, mostradas nos exemplos, para limitar quais os campos que são devolvidos.

+ Os documentos são compostos por todos os campos marcados como **Recuperáveis** no índice. Para ver os atributos do índice no portal, clique na *amostra realestate-us* na lista **de Índices** na página de visão geral da pesquisa.

+ Consultas de formulário livre, semelhantes ao que você pode introduzir num navegador web comercial, são úteis para testar uma experiência de utilizador final. Por exemplo, assumindo o índice de amostras de imóveis incorporado, você poderia entrar em "Seattle apartments lake washington", e então você pode usar Ctrl-F para encontrar termos dentro dos resultados da pesquisa. 

+ As expressões de consulta e filtro são articuladas numa sintaxe suportada pela Azure Cognitive Search. O padrão é uma [sintaxe simples,](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)mas pode opcionalmente usar [Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [As expressões de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe OData.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre estruturas de consulta e sintaxe, use o Carteiro ou uma ferramenta equivalente para criar expressões de consulta que alavancam mais partes da API. A [API Search REST](https://docs.microsoft.com/rest/api/searchservice/) é especialmente útil para a aprendizagem e exploração.

> [!div class="nextstepaction"]
> [Criar uma consulta básica no Carteiro](search-query-simple-examples.md)