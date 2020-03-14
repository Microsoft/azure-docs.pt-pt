---
title: Crie um índice de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste portal, aprenda a usar o assistente de Dados de Importação para criar, carregar e consultar o seu primeiro índice de pesquisa em Pesquisa Cognitiva Azure.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 6a3bbdae0d3fa898621c1c805388252beb891ecf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241585"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Criar um índice de pesquisa cognitiva Azure no portal Azure
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Node.js](search-get-started-nodejs.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [python](search-get-started-python.md)

Utilize as ferramentas de explorador de **dados de importação** do portal e **pesquisa** para acelerar rapidamente os conceitos e escrever consultas interessantes contra um índice em poucos minutos.

Se as ferramentas forem demasiado limitadas, pode considerar uma [introdução baseada em código para programar a Pesquisa Cognitiva Azure em .NET](search-howto-dotnet-sdk.md) ou utilizar o Carteiro para fazer chamadas REST [API](search-get-started-postman.md). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

[Crie um serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada.

As secções no painel de instrumentos de serviço mostram quantos índices, indexadores e fontes de dados já tem. 

![Listas de índices, indexadores e fontes de dados](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa iteram num [*índice*](search-what-is-an-index.md) que contém dados pesquisáveis, metadados e construções adicionais que otimizam determinados comportamentos de pesquisa.

Para este tutorial, utilizamos um conjunto de dados de amostra incorporado que pode ser rastejado usando um [*indexador*](search-indexer-overview.md) através do assistente de [ **dados de importação** ](search-import-data-portal.md). Um indexador é um crawler específico da origem que pode ler metadados e conteúdo de origens de dados suportadas pelo Azure. Normalmente, os indexadores são usados programáticamente, mas no portal, você pode acessá-los através do assistente de **dados de Importação.** 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Passo 1 - Iniciar o assistente de dados de importação e criar uma fonte de dados

1. No painel de instrumentos de pesquisa cognitiva Azure, clique em **dados de importação** na barra de comando para criar e povoar um índice de pesquisa.

   ![Comando de importação de dados](media/search-get-started-portal/import-data-cmd.png)

2. No assistente, clique em **Ligar aos seus dados** > **Amostras** > **da amostra de hotéis**. Esta fonte de dados está incorporada. Se estivesse a criar a sua própria fonte de dados, teria de especificar um nome, tipo e informações de ligação. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

   ![Selecionar o conjunto de dados de exemplo](media/search-get-started-portal/import-datasource-sample.png)

3. Continue para a página seguinte.

### <a name="step-2---skip-the-enrich-content-page"></a>Passo 2 - Ignore a página "Enriquecer conteúdo"

O assistente apoia a criação de um oleoduto de enriquecimento de [IA](cognitive-search-concept-intro.md) para incorporar os algoritmos de IA dos Serviços Cognitivos na indexação. 

Vamos saltar este passo por agora, e passar diretamente para personalizar o índice de **alvo.**

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Você pode passar por um exemplo de indexação de IA em um [quickstart](cognitive-search-quickstart-blob.md) ou [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Passo 3 - Índice de configuração

Tipicamente, a criação de índices é um exercício baseado em código, concluído antes de carregar dados. No entanto, como este tutorial indica, o assistente pode gerar um índice básico para qualquer fonte de dados que possa rastejar. No mínimo, os índices precisam de um nome e de uma coleção de campos; um dos campos deve ser marcado como a chave do documento para identificar exclusivamente cada documento. Além disso, pode especificar analisadores de idiomas ou sugestionantes se quiser consultas autocompletas ou sugeridas.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado.

* **Recuperável** significa que aparece na lista de resultados da pesquisa. Pode marcar campos individuais como fora dos limites dos resultados de pesquisa, limpando esta caixa de verificação, por exemplo, para campos utilizados apenas em expressões de filtro.
* **A chave** é o identificador de documento único. É sempre uma corda, e é necessário.
* **Filterable**, **Sortable**, e **Facetable** determinam se os campos são usados num filtro, classificação ou estrutura de navegação facetada.
* **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis.

Os requisitos de armazenamento não variam em resultado da sua seleção. Por exemplo, se definir o atributo **Recuperável** em vários campos, os requisitos de armazenamento não sobem.

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. As cordas são *atribuídas* como **Recuperáveis** e **Pesquisáveis.** *Os inteiros* são atribuídos como **Recuperáveis,** **Filteráveis,** **Sortíveis**e **Facetable**.

1. Aceite as predefinições. 

   Se reexecutar o assistente uma segunda vez utilizando uma fonte de dados de hotéis existente, o índice não será configurado com atributos padrão. Terá de selecionar manualmente atributos em importações futuras. 

   ![Índice de hotéis gerados](media/search-get-started-portal/hotelsindex.png)

2. Continue para a página seguinte.


### <a name="step-4---configure-indexer"></a>Passo 4 - Indexante de configuração

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador.

Este objeto define um processo executável. Pode colocá-lo em horários recorrentes, mas por enquanto use a opção padrão para executar o indexante uma vez, imediatamente.

Clique em **Submeter** para criar e, simultaneamente, executar o indexador.

  ![indexador de hotéis](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitorizar o progresso

O assistente deve levá-lo à lista de Indexers onde pode monitorizar o progresso. Para auto-navegação, vá à página de visão geral e clique em **Indexers**.

Pode levar alguns minutos para o portal atualizar a página, mas deve ver o indexador recém-criado na lista, com o estado indicando "em curso" ou sucesso, juntamente com o número de documentos indexados.

   ![Mensagem de indexador em curso](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Ver o índice

A página principal de serviço fornece links para os recursos criados no seu serviço de Pesquisa Cognitiva Azure.  Para ver o índice que acabou de criar, clique em **Indexes** a partir da lista de links. 

Espere que a página do portal refresque. Após alguns minutos, deve ver o índice com uma contagem de documentos e tamanho de armazenamento.

   ![Lista de índices no painel de instrumentos de serviço](media/search-get-started-portal/indexes-list.png)

A partir desta lista, você pode clicar no índice *de amostrade hotéis* que você acabou de criar, ver o esquema de índice. e opcionalmente adicionar novos campos. 

O separador **Fields** mostra o esquema do índice. Percorra o fundo da lista para entrar num novo campo. Na maioria dos casos, não se pode mudar os campos existentes. Os campos existentes têm uma representação física na Pesquisa Cognitiva Azure e, portanto, não são modificáveis, nem mesmo em código. Para mudar fundamentalmente um campo existente, crie um novo índice, deixando cair o original.

   ![definição de índice de exemplo](media/search-get-started-portal/sample-index-def.png)

Outras construções, como a classificação de perfis e as opções de CORS, podem ser adicionadas a qualquer momento.

Para compreender claramente o que pode e não pode editar durante o design do índice, dispense um minuto para ver as opções de definição do índice. As opções desativadas são um indicador de que um valor não pode ser alterado nem eliminado. 

## <a name="query-index"></a>Consulta usando explorador de pesquisa

Mais adiante, já deve ter um índice de pesquisa pronto para consultar com a página de consulta [**Explorador de procura**](search-explorer.md) incorporada. Fornece uma caixa de pesquisa para que possa testar as cadeias de consulta arbitrária.

**O explorador** de pesquisa está apenas equipado para lidar com pedidos rest API , mas aceita sintaxe para [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e parser de consulta [lucene completo,](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)além de todos os [parâmetros](https://docs.microsoft.com/rest/api/searchservice/search-documents)de pesquisa disponíveis nas operações [da API](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) do Documento de Pesquisa.

> [!TIP]
> Os seguintes passos são demonstrados a 6m08s no vídeo de visão geral da [pesquisa cognitiva azure](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   ![Comando do Explorador de pesquisa](media/search-get-started-portal/search-explorer-cmd.png)

2. A partir da queda do **Índice,** escolha *hotéis-sample-index*. Clique na versão **API** dropdown, para ver quais ASAP REST estão disponíveis. Para as consultas abaixo, utilize a versão geralmente disponível (2019-05-06).

   ![Comandos de índice e de API](media/search-get-started-portal/search-explorer-changeindex.png)

3. Na barra de pesquisa, cola nas cordas de consulta abaixo e clique em **Procurar**.

   ![Fio de consulta e botão de pesquisa](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Consultas de exemplo

Pode introduzir termos e frases, semelhantes ao que pode fazer numa pesquisa de Bing ou Google, ou expressões de consulta totalmente especificadas. Os resultados são devolvidos como documentos verbosos da JSON.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com N principais resultados

#### <a name="example-string-query-searchspa"></a>Exemplo (consulta de cordas): `search=spa`

* O parâmetro de **pesquisa** é utilizado para inserir uma pesquisa de palavras-chave para pesquisa completa de texto, neste caso, devolvendo dados do hotel para aqueles que contêm *spa* em qualquer campo pesquisável no documento.

* O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. Isto é intencional; a visibilidade em todo o documento é importante para fins de desenvolvimento, especialmente durante os testes. Para uma melhor experiência de utilizador, terá de escrever código que [processa resultados de pesquisa](search-pagination-page-layout.md) para reproduzir elementos importantes.

* Os documentos são compostos por todos os campos marcados como "recuperáveis" no índice. Para ver os atributos de índice no portal, clique em *hotéis-sample* na lista de **Índices.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exemplo (consulta parametrizada): `search=spa&$count=true&$top=10`

* O símbolo **&** é utilizado para acrescentar os parâmetros da pesquisa, que podem ser especificados por qualquer ordem.

* O **parâmetro $count=verdadeiro** devolve a contagem total de todos os documentos devolvidos. Este valor é apresentado junto à parte superior dos resultados da pesquisa. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por **$count=true**. As contagens mais pequenas indicam que o filtro está a funcionar.

* O **$top=10** devolve os 10 documentos mais bem classificados do total. Por padrão, a Pesquisa Cognitiva Azure devolve os primeiros 50 melhores jogos. Pode aumentar ou diminuir a quantidade através de **$top**.

### <a name="filter-query"></a>Filtrar a consulta

Os filtros são incluídos nos pedidos de pesquisa se acrescentar o parâmetro **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exemplo (filtrado): `search=beach&$filter=Rating gt 4`

* O parâmetro **$filter** devolve resultados que correspondem aos critérios que indicou. Neste caso, classificações superiores a 4.

* A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Facetar a consulta

Os filtros de faceta são incluídos nos resultados da pesquisa. Pode utilizar o parâmetro de faceta para devolver uma contagem agregada dos documentos que correspondam a um valor de faceta que indicar.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exemplo (por facetas com redução de âmbito): `search=*&facet=Category&$top=2`

* **search=** * é uma consulta vazia. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. Por exemplo, você quer uma estrutura de navegação facetada para consistir em todos os hotéis no índice.
* **facet** devolve uma estrutura de navegação que pode ser transmitida a um controlo de IU. Devolve categorias e uma contagem. Neste caso, as categorias baseiam-se num campo convenientemente chamado *categoria*. Não há agregação na Pesquisa Cognitiva Azure, mas pode aproximar-se da agregação através de `facet`, o que dá uma contagem de documentos em cada categoria.

* **$top=2** devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exemplo (faceta sobre valores numéricos): `search=spa&facet=Rating`

* Esta consulta é faceta para classificação, em uma pesquisa de texto para *spa*. O termo *Rating* pode ser especificado como uma faceta porque o campo é marcado como recuperável, filterável e facetable no índice, e os valores que contém (numérico, 1 a 5), são adequados para categorizar listas em grupos.

* Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

* O campo *rating* é um ponto flutuante de dupla precisão e o agrupamento será por um valor preciso. Para obter mais informações sobre o agrupamento por intervalo (por exemplo, "classificações de 3 estrelas", "classificações de 4 estrelas", etc.), consulte [Como implementar a navegação facetada na Pesquisa Cognitiva azure](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Realçar os resultados de pesquisa

O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exemplo (marcador): `search=beach&highlight=Description`

* Neste exemplo, a *praia* de palavras formatada é mais fácil de detetar no campo de descrição.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exemplo (análise linguística): `search=beaches&highlight=Description`

* A pesquisa completa de texto reconhece variações básicas em formas de palavras. Neste caso, os resultados da pesquisa contêm texto destacado para "praia", para hotéis que têm essa palavra nos seus campos pesquisáveis, em resposta a uma pesquisa de palavras-chave em "praias". Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística. 

* A Azure Cognitive Search suporta 56 analisadores tanto da Lucene como da Microsoft. O padrão usado pela Azure Cognitive Search é o analisador lucene padrão.

### <a name="fuzzy-search"></a> Experimentar a pesquisa difusa

Por defeito, termos de consulta mal escritos, como *seatle* para "Seattle", não devolvem os jogos na pesquisa típica. O exemplo seguinte não devolve nenhum resultado.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exemplo (termo com erros ortográficos, não processado): `search=seatle`

Para processar erros ortográficos, pode utilizar a pesquisa difusa. A pesquisa difusa é ativada quando utiliza a sintaxe de consulta Lucene completa, que ocorre quando faz duas coisas: define **queryType=full** na consulta e anexa **~** à cadeia de pesquisa.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exemplo (termo com erros ortográficos, processado): `search=seatle~&queryType=full`

Este exemplo devolve agora documentos que incluem fósforos em "Seattle".

Quando **queryType** não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa.

A pesquisa difusa e a pesquisa com carateres universais têm implicações no resultado da pesquisa. A análise linguística não é realizada nestes formatos de consulta. Antes de utilizar a pesquisa fuzzy e wildcard, reveja como funciona a pesquisa completa de [texto em Azure Cognitive Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a secção sobre exceções à análise lexical.

Para obter mais informações sobre cenários de consulta habilitados pelo parser de consulta completa, consulte a [sintaxe de consulta Lucene em Pesquisa Cognitiva Azure](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas.

A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou se utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. Se tiver requisitos de utilização para pesquisar uma cidade ou país/região pelo nome, adicione campos que contenham nomes de cidade ou país/região, além de coordenadas.

## <a name="takeaways"></a>Conclusões

Este tutorial proporcionou uma rápida introdução à Pesquisa Cognitiva Azure utilizando o portal Azure.

Aprendeu a criar um índice de pesquisa com o assistente para **Importar dados**. Aprendeu sobre [indexadores](search-indexer-overview.md), bem como o fluxo de trabalho básico para o design de índices, incluindo [modificações suportadas num índice publicado](https://docs.microsoft.com/rest/api/searchservice/update-index).

Ao utilizar o **explorador do Search** no portal do Azure, aprendeu a sintaxe básica das consultas através de exemplos práticos que demonstram as capacidades principais, como filtros, detetor de ocorrências, pesquisa difusa e pesquisa geográfica.

Também aprendeu a encontrar índices, indexadores e fontes de dados no portal. Utilizando uma origem de dados nova no futuro, pode utilizar o portal para verificar rapidamente as definições ou as coleções de campos da mesma com um esforço mínimo.

## <a name="clean-up-resources"></a>Limpar recursos

Quando está a trabalhar na sua própria subscrição, é uma boa ideia no final de um projeto identificar se ainda precisa dos recursos que criou. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **De Todos os recursos** ou **grupos de Recursos** no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Utilize um assistente de portal para gerar uma aplicação web pronta a usar que funciona num browser. Pode experimentar este assistente no pequeno índice que acabou de criar, ou utilizar um dos conjuntos de dados de amostra sinuoso para uma experiência de pesquisa mais rica.

> [!div class="nextstepaction"]
> [Criar uma aplicação de pesquisa no portal](search-create-app-portal.md)