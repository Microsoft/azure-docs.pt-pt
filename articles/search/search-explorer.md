---
title: Ferramenta de consulta de explorador de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste portal Azure quickstart, utilize o Search Explorer para aprender sintaxe de consulta, testar expressões de consulta ou inspecionar um documento de pesquisa. O explorador de pesquisa consulta índices em Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369668"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Quickstart: Use explorador de pesquisa para executar consultas no portal

**O explorador** de pesquisa é uma ferramenta de consulta incorporada usada para executar consultas contra um índice de pesquisa em Pesquisa Cognitiva Azure. Esta ferramenta facilita a aprendizagem da sintaxe de consulta, testa uma consulta ou expressão de filtro, ou confirma os resultados de uma atualização do índice verificando a existência de conteúdo mais recente.

Este quickstart usa **o índice de amostras de imóveis-us-para** demonstrar o explorador de pesquisa. Os pedidos são formulados utilizando a API de Repouso de [Busca,](https://docs.microsoft.com/rest/api/searchservice/)com respostas devolvidas como documentos JSON.

## <a name="prerequisites"></a>Pré-requisitos

+ [Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido.

+ **realestate-us-sample-index** é usado para este arranque rápido. Atravesse o assistente de [**dados da Importação**](search-import-data-portal.md) para gerar o índice a partir da fonte de dados de amostras incorporadas.

## <a name="start-search-explorer"></a>Iniciar explorador de pesquisa

1. No [portal Azure,](https://portal.azure.com)abra a página de serviço de pesquisa a partir do painel de instrumentos ou encontre o [seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Explorador de pesquisa aberta a partir da barra de comando:

   ![Comando explorador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "Comando explorador de pesquisa no portal")

    Ou utilize o separador explorador de **pesquisa** incorporado num índice aberto:

   ![Separador explorador de pesquisa](./media/search-explorer/search-explorer-tab.png "Separador explorador de pesquisa")

## <a name="unspecified-query"></a>Consulta não especificada

Para uma primeira olhada no conteúdo, execute uma pesquisa vazia clicando em **Procurar** sem termos fornecidos. Uma pesquisa vazia é útil como uma primeira consulta porque devolve documentos inteiros para que possa rever a composição do documento. Numa busca vazia, não há posto de pesquisa e`"@search.score": 1` os documentos são devolvidos por ordem arbitrária (para todos os documentos). Por padrão, 50 documentos são devolvidos num pedido de pesquisa.

Sintaxe equivalente para `*` uma `search=*`pesquisa vazia é ou .
   
   ```http
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Exemplo de consulta não qualificada ou vazia")

## <a name="free-text-search"></a>Pesquisa gratuita de texto

As consultas de formulário sem liberdade, com ou sem operadores, são úteis para simular consultas definidas pelo utilizador enviadas de uma aplicação personalizada para a Pesquisa Cognitiva Azure. Apenas os campos atribuídos como **Pesquisáveis** na definição de índice são digitalizados para correspondências. 

Note que quando fornece critérios de pesquisa, tais como termos de consulta ou expressões, o rank de pesquisa entra em jogo. O exemplo que se segue ilustra uma pesquisa gratuita por texto.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Pode utilizar o CTRL-F para pesquisar dentro de resultados por termos de interesse específicos.

   ![Exemplo de consulta de texto gratuito](./media/search-explorer/search-explorer-example-freetext.png "Exemplo de consulta de texto gratuito")

## <a name="count-of-matching-documents"></a>Contagem de documentos correspondentes 

Adicione **$count=verdadeiro** para obter o número de fósforos encontrados num índice. Numa pesquisa vazia, a contagem é o número total de documentos no índice. Numa pesquisa qualificada, é o número de documentos que correspondem à entrada da consulta.

   ```http
   $count=true
   ```

   **Resultados**

   ![Exemplo de contagem de documentos](./media/search-explorer/search-explorer-example-count.png "Contagem de documentos correspondentes no índice")

## <a name="limit-fields-in-search-results"></a>Limite os campos nos resultados da pesquisa

Adicione [**$select**](search-query-odata-select.md) para limitar os resultados aos campos explicitamente nomeados para uma saída mais legível no **explorador de pesquisa**. Para manter a cadeia de pesquisa e **$count=verdade,** prefixe argumentos com **&**. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Limite de campos exemplo](./media/search-explorer/search-explorer-example-selectfield.png "Restringir os campos nos resultados da pesquisa")

## <a name="return-next-batch-of-results"></a>Devolver o próximo lote de resultados

A Pesquisa Cognitiva Azure devolve os 50 melhores jogos com base no posto de pesquisa. Para obter o próximo conjunto de documentos correspondentes, apreenda **$top=100&$skip=50** para aumentar o resultado definido para 100 documentos (o padrão é de 50, máximo é de 1000), ignorando os primeiros 50 documentos. Lembre-se que precisa de fornecer critérios de pesquisa, como um termo ou expressão de consulta, para obter resultados classificados. Note que as pontuações de pesquisa diminuem quanto mais se chega aos resultados da pesquisa.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   ![Resultados da pesquisa de lote](./media/search-explorer/search-explorer-example-topskip.png "Devolva o próximo lote de resultados de pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (superiores, inferiores a)

Utilize o parâmetro [**$filter**](search-query-odata-filter.md) quando pretender especificar critérios precisos em vez de procurar por texto gratuitamente. O campo deve ser atribuído como **Filterable** no índice. Este exemplo procura quartos com mais de 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   ![Expressão do filtro](./media/search-explorer/search-explorer-example-filter.png "Filtrar por critérios")

## <a name="order-by-expressions"></a>Expressões por ordem

Adicione [**$orderby**](search-query-odata-orderby.md) a classificar resultados por outro campo além da pontuação de pesquisa. O campo deve ser atribuído como **Sortable** no índice. Uma expressão de exemplo que pode usar para testar isto é:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   ![Expressão orderby](./media/search-explorer/search-explorer-example-ordery.png "Alterar a sequência de ordenação")

Tanto **$filter** como **$orderby** expressões são construções OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Conclusões

Neste arranque rápido, usou o explorador de **pesquisa** para consultar um índice usando a API REST.

+ Os resultados são devolvidos como documentos verbosos da JSON para que possa ver a construção e conteúdo do documento, na totalidade. Pode utilizar expressões de consulta, mostradas nos exemplos, para limitar quais os campos devolvidos.

+ Os documentos são compostos por todos os campos marcados como **Recuperáveis** no índice. Para ver os atributos do índice no portal, clique em *realestate-us-sample* na lista **de Índices** na página de visão geral da pesquisa.

+ As consultas de formulário sem conta, semelhantes às que pode entrar num navegador comercial, são úteis para testar uma experiência de utilizador final. Por exemplo, assumindo o índice de amostras imobiliárias incorporado, você poderia entrar em "Seattle apartments lake washington", e então você pode usar CTRL-F para encontrar termos dentro dos resultados da pesquisa. 

+ As expressões de consulta e filtro são articuladas numa sintaxe apoiada pela Azure Cognitive Search. O padrão é uma [simples sintaxe,](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)mas você pode opcionalmente usar [lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [As expressões](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) de filtro são uma sintaxe OData.

## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre estruturas de consulta e sintaxe, use o Carteiro ou uma ferramenta equivalente para criar expressões de consulta que alavancam mais partes da API. A [API de Repouso de Pesquisa](https://docs.microsoft.com/rest/api/searchservice/) é especialmente útil para a aprendizagem e exploração.

> [!div class="nextstepaction"]
> [Criar uma consulta básica no Carteiro](search-query-simple-examples.md)