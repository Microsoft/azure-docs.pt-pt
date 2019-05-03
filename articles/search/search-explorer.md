---
title: Ferramenta do Explorador de pesquisa para consultar dados no portal do Azure - Azure Search
description: Utilize ferramentas do portal do Azure, como o Explorador de pesquisa para índices de consulta no Azure Search. Introduza os termos de pesquisa ou cadeias de procura completamente qualificado com a sintaxe avançada.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 392699182859a090c13304f63d28a78b95a65ec7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024029"
---
# <a name="search-explorer-for-querying-data-in-azure-search"></a>Explorador de pesquisa para consultar dados no Azure Search 

Este artigo mostra-lhe como consultar um através de índice de pesquisa do Azure existentes **Explorador de pesquisa** no portal do Azure. Pode utilizar o Explorador de procura para submeter simples ou completas cadeias de consulta de Lucene a um índice existente no seu serviço. 

   ![Comando do Explorador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "comando do Explorador de pesquisa no portal")


Para obter ajuda na introdução, consulte [Explorador de pesquisa iniciar](#start-search-explorer).

## <a name="basic-search-strings"></a>Cadeias de caracteres de pesquisa básica

Os exemplos seguintes partem do princípio do índice de exemplo de realestate incorporada. Para ajudar a criar este índice, consulte [início rápido: Importar, índice e a consulta no portal do Azure](search-get-started-portal.md).

### <a name="example-1---empty-search"></a>Exemplo 1 - pesquisa em branco

Para o primeiro contato com o seu conteúdo, executar uma pesquisa em branco ao clicar em **pesquisa** com não existem termos fornecidos. Uma pesquisa em branco é útil quando uma primeira consulta porque retorna todos os documentos, para que possa rever composição de documento. Numa pesquisa em branco, não há nenhuma classificação de pesquisa e documentos são devolvidos por ordem arbitrária (`"@search.score": 1` para todos os documentos). Por predefinição, os 50 documentos são retornados numa solicitação de pesquisa.

Sintaxe equivalente de uma pesquisa em branco é `*` ou `search=*`.

   ```Input
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Unqualified ou de exemplo de consulta vazia")

### <a name="example-2---free-text-search"></a>Exemplo 2 - pesquisa de texto livre

Consultas de forma livre, com ou sem operadores, são úteis para simular o consultas definidas pelo utilizador enviadas a partir de uma aplicação personalizada para o Azure Search. Tenha em atenção que, quando fornecer os termos de consulta ou expressões, classificação de pesquisa entra em cena. O exemplo seguinte ilustra uma pesquisa de texto livre.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Pode utilizar Ctrl-F para pesquisar resultados para termos específicos de interesse.

   ![Exemplo de consulta de texto livre](./media/search-explorer/search-explorer-example-freetext.png "exemplo de consulta de texto livre")

### <a name="example-3---count-of-matching-documents"></a>Exemplo 3 - contagem de documentos correspondentes 

Adicione **$count** para obter o número de correspondências encontradas num índice. Numa pesquisa em branco, a contagem é o número total de documentos no índice. Numa pesquisa qualificada, é o número de documentos correspondentes à entrada de consulta.

   ```Input1
   $count=true
   ```
   **Resultados**

   ![Contagem de exemplo de documentos](./media/search-explorer/search-explorer-example-count.png "contagem de documentos no índice correspondentes")

### <a name="example-4---restrict-fields-in-search-results"></a>Exemplo 4 - restringir os campos nos resultados da pesquisa

Adicione **$select** para limitar os resultados para os campos explicitamente nomeados de saída mais legível em **Explorador de pesquisa**. Para manter a cadeia de procura e **$count = true**, prefixo argumentos com **&**. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Exemplo de campos de limite](./media/search-explorer/search-explorer-example-selectfield.png "restringir os campos nos resultados da pesquisa")

### <a name="example-5---return-next-batch-of-results"></a>Exemplo 5 - retorno lote seguinte de resultados

O Azure Search devolve as 50 melhores correspondências com base na classificação de pesquisa. Para obter o próximo conjunto de documentos correspondentes, acrescente **$top = 100 e $skip = 50** para aumentar o conjunto de resultados para 100 documentos (a predefinição é 50, máximo é de 1000), a ignorar os primeiros 50 documentos. Lembre-se de que tem de fornecer critérios de pesquisa, como um termo de consulta ou uma expressão, para obter com a classificação de resultados. Tenha em atenção que as pontuações de pesquisa diminuir o mais profundo atingir resultados da pesquisa.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100,&$skip=50
   ```

   **Resultados**

   ![Os resultados da pesquisa do batch](./media/search-explorer/search-explorer-example-topskip.png "retorno lote seguinte de resultados da pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Filtrar expressões (maiores que, menos do que, iguais a)

Utilize o **$filter** parâmetro para especificar critérios precisos, em vez de pesquisa de texto livre. Neste exemplo procura quartos acima de 3: `search=seattle condo&$filter=beds gt 3&$count=true`

   ![Expressão de filtro](./media/search-explorer/search-explorer-example-filter.png "filtrar por critérios")

## <a name="order-by-expressions"></a>Expressões order by

Adicione **$orderby** para classificar resultados por outro campo, além de pontuação de pesquisa. Uma expressão de exemplo, que pode usar para testar este limite é `search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc`

   ![Expressão de OrderBy](./media/search-explorer/search-explorer-example-ordery.png "alterar a ordem de classificação")

Ambos **$filter** e **$orderby** as expressões são construções de OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Como iniciar o Explorador de pesquisa

1. Na [portal do Azure](https://portal.azure.com), abra a página do serviço de pesquisa a partir do dashboard ou [encontrar o seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de descrição geral do serviço, clique em **Explorador de pesquisa**.

   ![Comando do Explorador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "comando do Explorador de pesquisa no portal")

3. Selecione o índice para consulta.

   ![Selecione o índice para consultar](./media/search-explorer/search-explorer-changeindex-se2.png "selecione o índice")

4. Opcionalmente, defina a versão de API. Por predefinição, está selecionada a versão de API atual disponível em geral, mas pode escolher uma pré-visualização ou mais antiga API se a sintaxe que pretende utilizar é específica da versão.

5. Uma vez o índice e a versão de API está selecionado, introduza os termos de pesquisa ou expressões de consulta completamente qualificado na barra de pesquisa e clique em **pesquisa** para executar.

   ![Introduza os termos de pesquisa e clique em Procurar](./media/search-explorer/search-explorer-query-string-example.png "Enter pesquisa de termos e clique em Procurar")

Sugestões de pesquisa na **Explorador de pesquisa**:

+ Os resultados são devolvidos como verbosos documentos JSON para que possa visualizar a construção de documento e conteúdo, na totalidade. Pode usar as expressões de consulta, mostradas nos exemplos, para os campos são retornados de limite.

+ Documentos são compostos por todos os campos marcados como **recuperável** no índice. Para ver os atributos de índice no portal, clique em *realestate-us-sample* no **índices** lista na página de descrição geral da pesquisa.

+ Consultas de forma livre, semelhantes a que pode inserir num navegador da web comercial, são úteis para fins de teste de uma experiência de utilizador final. Por exemplo, supondo que o índice de realestate incorporada de exemplo, poderia digitar "Seattle apartments lake washington" e, em seguida, pode utilizar Ctrl-F para encontrar os termos nos resultados da pesquisa. 

+ Expressões de consulta e o filtro devem ser articuladas numa sintaxe suportada pelo Azure Search. A predefinição é um [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), mas pode utilizar opcionalmente [total Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais eficientes. [Expressões de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe de OData.


## <a name="next-steps"></a>Passos Seguintes

Os seguintes recursos fornecem informações de sintaxe de consulta adicionais e exemplos.

 + [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemplos de consultas Lucene](search-query-lucene-examples.md) 
 + [Sintaxe de expressão de Filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 