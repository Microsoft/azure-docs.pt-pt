---
title: Create a query (Criar uma consulta)
titleSuffix: Azure Cognitive Search
description: Aprenda a construir um pedido de consulta na Pesquisa Cognitiva, quais as ferramentas e APIs a usar para testes e códigos, e como as decisões de consulta começam com o design do índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 4f5cc0d5eefd5969566040e4148ca7358d348736
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951509"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Criação de consultas na Pesquisa Cognitiva Azure

Se estiver a construir uma consulta pela primeira vez, este artigo descreve abordagens e métodos para configurar consultas. Também introduz um pedido de consulta, e explica como os atributos de campo e os analisadores linguísticos podem impactar os resultados da consulta.

## <a name="whats-a-query-request"></a>O que é um pedido de consulta?

Uma consulta é um pedido apenas de leitura contra a recolha de docs de um único índice de pesquisa. Especifica um parâmetro de 'pesquisa' que contém a expressão de consulta, composta por termos, frases incluídas nas cotações e operadores.

Os parâmetros adicionais proporcionam mais definição à consulta e à resposta. Por exemplo, os âmbitos de 'searchFields' consultam a execução de campos específicos, 'selecione' especifica quais os campos devolvidos nos resultados e 'contagem' devolve o número de partidas encontradas no índice.

O exemplo a seguir dá-lhe uma ideia geral de um pedido de consulta mostrando um subconjunto dos parâmetros disponíveis. Para obter mais informações sobre a composição de consultas, consulte [tipos de consultas e composições](search-query-overview.md) e [documentos de pesquisa (REST)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Escolha um cliente

Você precisará de uma ferramenta como o portal Azure ou o Carteiro, ou código que instantaneamente um cliente de consulta usando APIs. Recomendamos o portal Azure ou ASP DE REST para o desenvolvimento precoce e testes de prova de conceito.

### <a name="permissions"></a>Permissões

Qualquer operação, incluindo pedidos de consulta, funcionará sob [uma chave API de administração,](search-security-api-keys.md)mas os pedidos de consulta podem opcionalmente usar uma [chave API de consulta](search-security-api-keys.md#create-query-keys). As chaves API de consulta são fortemente recomendadas. Pode criar até 50 por serviço e atribuir chaves diferentes a diferentes aplicações.

No portal Azure, o acesso a ferramentas, assistentes e objetos requerem adesão na função Contribuinte ou acima no serviço. 

### <a name="use-azure-portal-to-query-an-index"></a>Use o portal Azure para consultar um índice

[Search explorer (portal)](search-explorer.md) é uma interface de consulta no portal Azure que executa consultas contra índices no serviço de pesquisa subjacente. Internamente, o portal faz pedidos [de Documentos de Pesquisa,](/rest/api/searchservice/search-documents) mas não pode invocar Autocomplete, Sugestões ou Pesquisa de Documentos. 

Pode selecionar qualquer versão de API index e REST, incluindo pré-visualização. Uma cadeia de consulta pode usar sintaxe simples ou completa, com suporte para todos os parâmetros de consulta (filtro, seleção, searchFields, e assim por diante). No portal, quando abre um índice, pode trabalhar com o Search Explorer ao lado da definição de Índice JSON em separadores lado a lado para facilitar o acesso aos atributos de campo. Verifique quais os campos pesmáveis, tributáveis, filtrados e facetable enquanto testa consultas.

### <a name="use-a-rest-client"></a>Use um cliente REST

Tanto o Código do Carteiro como o Código do Estúdio Visual (com uma extensão para Azure Cognitive Search) podem funcionar como cliente de consulta. Utilizando qualquer uma das ferramentas, pode ligar-se ao seu serviço de pesquisa e enviar pedidos [de Documentos de Busca (REST).](/rest/api/searchservice/search-documents) Numerosos tutoriais e exemplos demonstram clientes REST para consulta de indexação. 

Comece com qualquer um destes artigos para aprender sobre cada cliente (ambos incluem instruções para consultas):

+ [Criar um índice de pesquisa usando REST e Carteiro](search-get-started-rest.md)
+ [Começar com Visual Studio Code e Azure Cognitive Search](search-get-started-vs-code.md)

Cada pedido é autónomo, por isso deve fornecer o ponto final, nome de índice e versão API em cada pedido. Outras propriedades, Tipo de Conteúdo e chave API, são transmitidas no cabeçalho de pedido. Para obter mais informações, consulte [Documentos de Pesquisa (REST)](/rest/api/searchservice/search-documents) para obter ajuda na formulação de pedidos de consulta.

### <a name="use-an-sdk"></a>Use um SDK

Para pesquisa cognitiva, os Azure SDKs implementam funcionalidades geralmente disponíveis. Como tal, pode utilizar qualquer um dos SDKs para consultar um índice. Todos eles fornecem um **SearchClient** que tem métodos para interagir com um índice, desde carregar um índice com documentos de pesquisa, até formular pedidos de consulta.

| Azure SDK | Cliente | Exemplos |
|-----------|--------|----------|
| .NET | [PesquisaDesseient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [PesquisaDesseient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample.java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [PesquisaDesseient](/javascript/api/@azure/search-documents/searchclient) | Pendente. |
| Python | [PesquisaDesseient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query.py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Escolha um tipo de consulta: | simples cheio

Se a sua consulta for a pesquisa por texto completo, será utilizado um parser de consulta para processar qualquer texto que seja passado como termos e frases de pesquisa. A Azure Cognitive Search oferece dois parsers de consulta. 

+ O simples parser compreende a [simples sintaxe de consulta.](query-simple-syntax.md) Este parser foi selecionado como o padrão para a sua velocidade e eficácia em consultas de texto de forma livre. A sintaxe suporta operadores de pesquisa comuns (E, OU, NÃO) para pesquisas de termos e frases, e `*` prefixo (como em "sea*" para Seattle e Beira-Mar). Uma recomendação geral é experimentar primeiro o simples parser e, em seguida, passar para o parser completo se os requisitos de aplicação exigirem consultas mais poderosas.

+ A [sintaxe de consulta lucene completa,](query-Lucene-syntax.md#bkmk_syntax)ativada quando adiciona `queryType=full` ao pedido, baseia-se no [Apache Lucene Parser](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

A sintaxe completa e a sintaxe simples sobrepõem-se ao ponto de ambos suportarem as mesmas operações de prefixo e boolean, mas a sintaxe completa proporciona mais operadores. Na íntegra, há mais operadores para expressões booleanas, e mais operadores para consultas avançadas, tais como pesquisa duvidosa, pesquisa de wildcard, pesquisa de proximidade e expressões regulares.

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

Se tiver revisto previamente [os tipos de consulta e composição,](search-query-overview.md)talvez se lembre que os parâmetros do pedido de consulta dependem da forma como os campos são atribuídos num índice. Por exemplo, para ser utilizado numa consulta, filtro ou ordem de classificação, um campo deve ser *pesculável,* *filtrado* e *ordenado*. Da mesma forma, apenas os campos marcados como *recuperáveis* podem aparecer em resultados. À medida que começa a especificar os `search` `filter` parâmetros , e `orderby` parâmetros no seu pedido, certifique-se de verificar atributos à medida que vai para evitar resultados inesperados.

No portal screenshot abaixo do índice de amostra de [hotéis,](search-get-started-portal.md)apenas os dois últimos campos "LastRenovationDate" e "Rating" podem ser usados numa `"$orderby"` única cláusula.

![Definição de índice para a amostra do hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para a amostra do hotel")

Para obter uma descrição dos atributos de campo, consulte [Criar Índice (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Conheça os seus tokens

Durante a indexação, o motor de busca utiliza um analisador para realizar análises de texto em cordas, maximizando o potencial de correspondência no tempo de consulta. No mínimo, as cordas são inferiores, mas também podem sofrer de lematização e parar a remoção de palavras. Cordas maiores ou palavras compostas são tipicamente divididas por espaços brancos, hífenes ou traços, e indexadas como símbolos separados. 

O ponto a retirar aqui é que o que você pensa que o seu índice contém, e o que realmente está nele, pode ser diferente. Se as consultas não devolverem os resultados esperados, pode inspecionar os tokens criados pelo analisador através do [Texto de Análise (REST API)](/rest/api/searchservice/test-analyzer). Para obter mais informações sobre a tokenização e o impacto nas consultas, consulte [a pesquisa parcial e padrões com caracteres especiais.](search-query-partial-matching.md)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma melhor compreensão de como os pedidos de consulta funcionam, experimente os seguintes quickstarts para experiência prática.

+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em REST](search-get-started-rest.md)
+ [Como consultar em .NET](search-get-started-dotnet.md)
+ [Como consultar em Python](search-get-started-python.md)
+ [Como consultar em JavaScript](search-get-started-javascript.md)