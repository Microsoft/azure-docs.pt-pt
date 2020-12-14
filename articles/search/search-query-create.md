---
title: Criar uma consulta básica
titleSuffix: Azure Cognitive Search
description: Aprenda a construir um pedido de consulta na Pesquisa Cognitiva, quais as ferramentas e APIs a usar para testes e códigos, e como as decisões de consulta começam com o design do índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: a02d51d66b9d2b8bf3c08d4515713ecb062e0c8e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400221"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Criar uma consulta na Pesquisa Cognitiva Azure

Se estiver a construir uma consulta pela primeira vez, este artigo descreve as ferramentas e APIs de que precisa, quais os métodos utilizados para criar uma consulta, e como a estrutura de índice e o conteúdo podem impactar os resultados da consulta. Para uma introdução ao aspeto de um pedido de consulta, comece com [tipos e composições de consulta.](search-query-overview.md)

## <a name="choose-tools-and-apis"></a>Escolha ferramentas e APIs

Você precisará de uma ferramenta ou API para criar uma consulta. Qualquer uma das seguintes sugestões é útil para testar e produzir cargas de trabalho.

| Metodologia | Descrição |
|-------------|-------------|
| Portal| [Search explorer (portal)](search-explorer.md) é uma interface de consulta no portal Azure que executa consultas contra índices no serviço de pesquisa subjacente. O portal faz chamadas de API REST nos bastidores para a operação [De Documentos de Busca,](/rest/api/searchservice/search-documents) mas não pode invocar Autocomplete, Sugestões ou Pesquisa de Documentos.<br/><br/> Pode selecionar qualquer versão de API index e REST, incluindo pré-visualização. Uma cadeia de consulta pode usar sintaxe simples ou completa, com suporte para todos os parâmetros de consulta (filtro, seleção, searchFields, e assim por diante). No portal, quando abre um índice, pode trabalhar com o Search Explorer ao lado da definição de Índice JSON em separadores lado a lado para facilitar o acesso aos atributos de campo. Verifique quais os campos pesmáveis, tributáveis, filtrados e facetable enquanto testa consultas. <br/>Recomendado para investigação, teste e validação antecipadas. [Saiba mais.](search-explorer.md) |
| Ferramentas de teste web| [O Código do Carteiro ou do Estúdio Visual](search-get-started-rest.md) são escolhas fortes para formular um pedido de [Documentos de Busca,](/rest/api/searchservice/search-documents) e qualquer outro pedido, em REST. As APIs REST suportam todas as possíveis operações programáticas em Azure Cognitive Search, e quando utiliza uma ferramenta como o Postman ou o Visual Studio Code, pode emitir pedidos interativamente para entender como a funcionalidade funciona antes de investir em código. Uma ferramenta de teste web é uma boa escolha se não tiver direitos de colaborador ou administrativo no portal Azure. Desde que tenha um URL de pesquisa e uma chave API de consulta, pode usar as ferramentas para executar consultas contra um índice existente. |
| Azure SDK | Quando estiver pronto para escrever código, pode utilizar as bibliotecas de clientes Azure.Search.Docnos SDKs Azure para .NET, Python, JavaScript ou Java. Cada SDK está na sua própria programação de lançamento, mas pode criar e consultar índices em todos eles. <br/><br/>[SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em C#.  [Saiba mais.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em Python. [Saiba mais.](search-get-started-python.md)<br/><br/>[SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em JavaScript. [Saiba mais.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Criar um cliente de pesquisa

Um cliente de pesquisa autentica-se no serviço de pesquisa, envia pedidos e trata de respostas. Independentemente da ferramenta ou API que utilize, um cliente de pesquisa deve ter o seguinte:

| Propriedades | Descrição |
|------------|-------------|
| Ponto final | Um serviço de pesquisa é o endereço URL neste formato: `https://[service-name].search.windows.net` . |
| Chave de acesso API (administração ou consulta) | Autentica o pedido para o serviço de pesquisa. |
| Nome do índice | As consultas são sempre direcionadas para a recolha de documentos de um único índice. Não é possível aderir a índices ou criar estruturas de dados personalizadas ou temporárias como alvo de consulta. |
| Versão API | As chamadas REST requerem explicitamente `api-version` o pedido. Em contrapartida, as bibliotecas de clientes do Azure SDK são versadas contra uma versão específica da API REST. Para os SDKs, o `api-version` está implícito. |

### <a name="in-the-portal"></a>No portal

O Search Explorer e outras ferramentas do portal têm uma ligação ao cliente incorporada ao serviço, com índices de acesso direto e outros objetos a partir de páginas do portal. O acesso a ferramentas, assistentes e objetos requerem adesão na função Contribuinte ou acima no serviço. 

### <a name="using-rest"></a>Utilizar REST

Para chamadas REST, pode utilizar [o Carteiro ou ferramentas semelhantes](search-get-started-rest.md) às do cliente para especificar um pedido [de Documentos de Busca.](/rest/api/searchservice/search-documents) Cada pedido é autónomo, por isso deve fornecer o ponto final, nome de índice e versão API em cada pedido. Outras propriedades, Tipo de Conteúdo e chave API, são transmitidas no cabeçalho de pedido. 

Pode utilizar POST ou GET para consultar um índice. POST, com parâmetros especificados no corpo de pedido, é mais fácil de trabalhar com. Se utilizar o POST, certifique-se de incluir `docs/search` no URL:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Usando Azure SDKs

Se estiver a usar um Azure SDK, criará o cliente em código. Todos os SDKs fornecem clientes de pesquisa que podem persistir, permitindo a reutilização de ligação. Para operações de consulta, você instantaneamente a **`SearchClient`** e dará valores para as seguintes propriedades: Endpoint, Chave, Índice. Em seguida, pode chamar o **`Search method`** para passar na cadeia de consulta. 

| Idioma | Cliente | Exemplo |
|----------|--------|---------|
| C# e .NET | [PesquisaDesseient](/dotnet/api/azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [PesquisaDesseient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [PesquisaDesseient](/java/api/com.azure.search.documents.searchclient) | [Envie a sua primeira consulta de pesquisa em Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [PesquisaDesseient](/javascript/api/@azure/search-documents/searchclient) | [Envie a sua primeira consulta de pesquisa em JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Escolha um parser: simples / cheio

Se a sua consulta for a pesquisa completa por texto, será utilizado um analisador para processar o conteúdo do parâmetro de pesquisa. A Azure Cognitive Search oferece dois parsers de consulta. O simples parser compreende a [simples sintaxe de consulta.](query-simple-syntax.md) Este parser foi selecionado como o padrão para a sua velocidade e eficácia em consultas de texto de forma livre. A sintaxe suporta operadores de pesquisa comuns (E, OU, NÃO) para pesquisas de termos e frases, e `*` prefixo (como em "sea*" para Seattle e Beira-Mar). Uma recomendação geral é experimentar primeiro o simples parser e, em seguida, passar para o parser completo se os requisitos de aplicação exigirem consultas mais poderosas.

A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando adiciona `queryType=full` ao pedido, baseia-se no [Apache Lucene Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

A sintaxe completa é uma extensão da sintaxe simples, com mais operadores para que possa construir consultas avançadas como pesquisa duvidosa, pesquisa de wildcard, pesquisa de proximidade e expressões regulares. Os exemplos a seguir ilustram o ponto: mesma consulta, mas com **`queryType`** configurações diferentes, que produzem resultados diferentes. Na primeira consulta simples, o `^3` seguinte é tratado como parte do termo de `historic` pesquisa. O resultado mais bem classificado para esta consulta é o "Marquês Plaza & Suites", que tem *oceano* na sua descrição.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

A mesma consulta utilizando o parser lucene completo interpreta `^3` como um impulsionador de prazos em campo. A troca de parsers muda de posição, com os resultados que contêm o termo *histórico* movendo-se para o topo.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="choose-query-methods"></a>Escolha métodos de consulta

A pesquisa é fundamentalmente um exercício orientado pelo utilizador, onde termos ou frases são recolhidos a partir de uma caixa de pesquisa, ou a partir de eventos de clique numa página. A tabela a seguir resume os mecanismos através dos quais pode recolher a entrada do utilizador, juntamente com a experiência de pesquisa esperada.

| Entrada | Experiência |
|-------|---------|
| [Método de pesquisa](/rest/api/searchservice/search-documents) | Um utilizador escreve termos ou frases numa caixa de pesquisa, com ou sem operadores, e clica em Procurar para enviar o pedido. A pesquisa pode ser usada com filtros no mesmo pedido, mas não com auto-precon possível ou sugestões. |
| [Método autocompleto](/rest/api/searchservice/autocomplete) | Um utilizador escreve alguns caracteres e as consultas são iniciadas após a dactilografada cada nova personagem. A resposta é uma cadeia completa do índice. Se a cadeia fornecida for válida, o utilizador clica em Procurar enviar essa consulta para o serviço. |
| [Método de sugestões](/rest/api/searchservice/suggestions) | Tal como acontece com o autocomplete, um utilizador escreve alguns caracteres e são geradas consultas incrementais. A resposta é uma lista de documentos correspondentes, tipicamente representados por alguns campos únicos ou descritivos. Se alguma das seleções for válida, o utilizador clica numa e o documento de correspondência é devolvido. |
| [Navegação por facetas](/rest/api/searchservice/search-documents#query-parameters) | Uma página mostra links de navegação clicáveis ou migalhas de pão que estreitam o âmbito da pesquisa. Uma estrutura de navegação facetada é composta dinamicamente com base numa consulta inicial. Por exemplo, `search=*` para povoar uma árvore de navegação facetada composta por todas as categorias possíveis. Uma estrutura de navegação facetada é criada a partir de uma resposta de consulta, mas também é um mecanismo para expressar a próxima consulta. n Referência API REST, `facets` é documentado como um parâmetro de consulta de uma operação de Documentos de Busca, mas pode ser usado sem o `search` parâmetro.|
| [Método de filtragem](/rest/api/searchservice/search-documents#query-parameters) | Os filtros são utilizados com facetas para reduzir os resultados. Também pode implementar um filtro atrás da página, por exemplo, para inicializar a página com campos específicos da linguagem. Na referência REST API, `$filter` é documentado como um parâmetro de consulta de uma operação de Documentos de Busca, mas pode ser usado sem o `search` parâmetro.|

## <a name="know-your-field-attributes"></a>Conheça os seus atributos de campo

Se tiver revisto previamente os [fundamentos de um pedido de consulta,](search-query-overview.md)talvez se lembre que os parâmetros do pedido de consulta dependem da forma como os campos são atribuídos num índice. Por exemplo, para ser utilizado numa consulta, filtro ou ordem de classificação, um campo deve ser *pesculável,* *filtrado* e *ordenado*. Da mesma forma, apenas os campos marcados como *recuperáveis* podem aparecer em resultados. À medida que começa a especificar os `search` `filter` parâmetros , e `orderby` parâmetros no seu pedido, certifique-se de verificar atributos à medida que vai para evitar resultados inesperados.

No portal screenshot abaixo do índice de amostra de [hotéis,](search-get-started-portal.md)apenas os dois últimos campos "LastRenovationDate" e "Rating" podem ser usados numa `"$orderby"` única cláusula.

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

Para obter uma descrição dos atributos de campo, consulte [Criar Índice (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conheça os seus tokens

Durante a indexação, o motor de consulta utiliza um analisador para realizar análises de texto em cordas, maximizando o potencial de correspondência no tempo de consulta. No mínimo, as cordas são inferiores, mas também podem sofrer de lematização e parar a remoção de palavras. Cordas maiores ou palavras compostas são tipicamente divididas por espaços brancos, hífenes ou traços, e indexadas como símbolos separados. 

O ponto a retirar aqui é que o que você pensa que o seu índice contém, e o que realmente está nele, pode ser diferente. Se as consultas não devolverem os resultados esperados, pode inspecionar os tokens criados pelo analisador através do [Texto de Análise (REST API)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre a tokenização e o impacto nas consultas, consulte [a pesquisa parcial e padrões com caracteres especiais.](search-query-partial-matching.md)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma melhor compreensão de como um pedido de consulta é construído, experimente os seguintes quickstarts para experiência prática.

+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em REST](search-get-started-rest.md)
+ [Como consultar em .NET](search-get-started-dotnet.md)
+ [Como consultar em Python](search-get-started-python.md)
+ [Como consultar em JavaScript](search-get-started-javascript.md)