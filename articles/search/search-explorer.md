---
title: Ferramenta de consulta de explorador de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste quickstart do portal Azure, utilize o Search Explorer para aprender sintaxe de consulta, expressões de consulta de teste ou inspecione um documento de pesquisa. Índices de consultas de exploradores de pesquisa em Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: e9607a71ed6b045ac704c43bf4ea54c9f181bbf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179780"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Quickstart: Use o explorador de pesquisa para executar consultas no portal

**O Explorador** de Pesquisa é uma ferramenta de consulta incorporada usada para executar consultas contra um índice de pesquisa em Azure Cognitive Search. Esta ferramenta facilita a aprendizagem da sintaxe de consulta, testa uma consulta ou expressão de filtro, ou confirma a atualização de dados verificando se existem novos conteúdos no índice.

Este quickstart utiliza um índice existente para demonstrar o explorador de pesquisa. Os pedidos são formulados utilizando a [API Search REST,](/rest/api/searchservice/search-documents)com respostas devolvidas como documentos verbosos JSON.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha os seguintes pré-requisitos no lugar:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ O *índice de amostras realestate-us é* usado para este arranque rápido. Utilize o [Quickstart: Crie um índice](search-import-data-portal.md) para criar o índice utilizando valores predefinidos. Uma fonte de dados incorporada de amostras hospedada pela Microsoft **(realestate-us-sample)** fornece os dados.

## <a name="start-search-explorer"></a>Iniciar explorador de pesquisa

1. No [portal Azure,](https://portal.azure.com)abra a página geral de pesquisa do painel de instrumentos ou [encontre o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Explorador de pesquisa aberta da barra de comando:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Procurar comando do explorador no portal" border="true":::

    Ou use o **separador de explorador de pesquisa** incorporado num índice aberto:

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Separador de explorador de pesquisa" border="true":::

## <a name="unspecified-query"></a>Consulta não especificada

Para uma primeira olhada no conteúdo, execute uma pesquisa vazia clicando **em Procurar** sem termos fornecidos. Uma pesquisa vazia é útil como primeira consulta porque devolve documentos inteiros para que possa rever a composição do documento. Numa busca vazia, não existe um posto de pesquisa e os documentos são devolvidos por ordem arbitrária `"@search.score": 1` (para todos os documentos). Por predefinição, 50 documentos são devolvidos num pedido de pesquisa.

Sintaxe equivalente para uma procura vazia é `*` ou `search=*` .
   
   ```http
   search=*
   ```

   **Resultados**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Exemplo de consulta não qualificada ou vazia" border="true":::

## <a name="free-text-search"></a>Pesquisa de texto gratuita

Consultas de formulário livre, com ou sem operadores, são úteis para simular consultas definidas pelo utilizador enviadas de uma aplicação personalizada para a Azure Cognitive Search. Apenas os campos atribuídos como **Pespáveis** na definição de índice são digitalizados para fósforos. 

Note que quando fornece critérios de pesquisa, tais como termos de consulta ou expressões, o rank de pesquisa entra em jogo. O seguinte exemplo ilustra uma pesquisa gratuita de texto.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar dentro de resultados para termos de interesse específicos.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Exemplo de consulta de texto gratuito" border="true":::

## <a name="count-of-matching-documents"></a>Contagem de documentos correspondentes 

Adicione **$count=verdade** para obter o número de fósforos encontrados num índice. Numa busca vazia, a contagem é o número total de documentos no índice. Numa pesquisa qualificada, é o número de documentos correspondentes à entrada de consulta. Lembre-se que o serviço devolve os 50 melhores jogos por padrão, para que possa ter mais correspondências no índice do que o que está incluído nos resultados.

   ```http
   $count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Contagem de documentos correspondentes em índice" border="true":::

## <a name="limit-fields-in-search-results"></a>Limite os campos nos resultados da pesquisa

Adicione [**$select**](search-query-odata-select.md) para limitar os resultados aos campos explicitamente nomeados para uma saída mais legível no **explorador de pesquisa**. Para manter o fio de pesquisa e **$count=verdadeiro,** prefixo com **&** . 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Restringir campos nos resultados de pesquisa" border="true":::

## <a name="return-next-batch-of-results"></a>Devolva o próximo lote de resultados

A Azure Cognitive Search devolve os 50 melhores jogos com base no ranking de pesquisa. Para obter o próximo conjunto de documentos correspondentes, apêndice **$top=100&$skip=50** para aumentar o resultado definido para 100 documentos (o padrão é 50, máximo é 1000), ignorando os primeiros 50 documentos. Pode consultar a chave do documento (listingID) para identificar um documento. 

Lembre-se que precisa de fornecer critérios de pesquisa, como um termo de consulta ou expressão, para obter resultados classificados. Note que as pontuações de pesquisa diminuem quanto mais profundo chegar aos resultados da pesquisa.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Devolva o próximo lote de resultados de pesquisa" border="true":::

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maiores do que, menos do que, iguais)

Utilize o parâmetro [**$filter**](search-query-odata-filter.md) quando pretender especificar critérios precisos em vez de pesquisa por texto livre. O campo deve ser atribuído como **filtrado** no índice. Este exemplo procura quartos superiores a 3:

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Filtrar por critérios" border="true":::

## <a name="order-by-expressions"></a>Expressões ordenadas

Adicione [**$orderby**](search-query-odata-orderby.md) para classificar resultados por outro campo além da pontuação de pesquisa. O campo deve ser atribuído como **Classificado** no índice. Uma expressão de exemplo que pode usar para testar isto é:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   :::image type="content" source="media/search-explorer/search-explorer-example-ordery.png" alt-text="Alterar a sequência de ordenação" border="true":::

Expressões **$filter** e **$orderby** são construções OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Conclusões

Neste quickstart, utilizou **o Explorador de Pesquisa** para consultar um índice utilizando a API REST.

+ Os resultados são devolvidos como documentos verbosos da JSON para que possa ver a construção e o conteúdo do documento, na totalidade. O **parâmetro $select** numa expressão de consulta pode limitar quais os campos devolvidos.

+ Os documentos são compostos por todos os campos marcados como **Recuperáveis** no índice. Para ver os atributos do índice no portal, clique na *amostra realestate-us* na lista **de Índices** na página de visão geral da pesquisa.

+ Consultas de formulário livre, semelhantes ao que você pode introduzir num navegador web comercial, são úteis para testar uma experiência de utilizador final. Por exemplo, assumindo o índice de amostras de imóveis incorporado, você poderia entrar em "Seattle apartments lake washington", e então você pode usar Ctrl-F para encontrar termos dentro dos resultados da pesquisa. 

+ As expressões de consulta e filtro são articuladas numa sintaxe implementada pela Azure Cognitive Search. O padrão é uma [sintaxe simples,](/rest/api/searchservice/simple-query-syntax-in-azure-search)mas pode opcionalmente usar [Lucene completo](/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [As expressões de filtro](/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe OData.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre estruturas de consulta e sintaxe, use o Carteiro ou uma ferramenta equivalente para criar expressões de consulta que alavancam mais partes da API. A [API Search REST](/rest/api/searchservice/search-documents) é especialmente útil para a aprendizagem e exploração.

> [!div class="nextstepaction"]
> [Criar uma consulta básica no Carteiro](search-get-started-rest.md)