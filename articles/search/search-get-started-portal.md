---
title: Criar um índice de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Crie, carregue e questione o seu primeiro índice de pesquisa utilizando o assistente de Dados de Importação no portal Azure. Este quickstart usa um conjunto de dados de hotel fictício para dados de amostragem.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1be165bfe7cca44e8a928933c3c8fe926ad7d4c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694840"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-the-azure-portal"></a>Quickstart: Criar um índice de Pesquisa Cognitiva Azure no portal Azure

Crie o seu primeiro índice de pesquisa utilizando o assistente **de dados de importação** e uma fonte de dados de amostra incorporada composta por dados fictícios do hotel. O assistente guia-o através da criação de um índice de pesquisa (hotéis-índice de amostra) para que possa escrever consultas interessantes em minutos. 

Embora não utilize as opções neste arranque rápido, o assistente inclui uma página para enriquecimento de IA para que possa extrair texto e estrutura a partir de ficheiros de imagem e texto não estruturado. Para uma passagem semelhante que inclua enriquecimento de IA, consulte [Quickstart: Crie uma habilidade cognitiva](cognitive-search-quickstart-blob.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure (qualquer nível, qualquer região). [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. O nível livre é limitado a três índices, três fontes de dados e três indexantes. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada.

Consulte a página geral do serviço para saber quantos índices, indexadores e fontes de dados já tem. 

:::image type="content" source="media/search-get-started-portal/tiles-indexers-datasources.png" alt-text="Listas de índices, indexadores e fontes de dados":::

## <a name="create-an-index-and-load-data"></a><a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa iteram num [*índice*](search-what-is-an-index.md) que contém dados pesquisáveis, metadados e construções adicionais que otimizam determinados comportamentos de pesquisa.

Para este tutorial, usamos um conjunto de dados de amostras incorporado que pode ser rastreado usando um [*indexante*](search-indexer-overview.md) através do assistente [ **de dados de importação.**](search-import-data-portal.md) Um indexador é um crawler específico da origem que pode ler metadados e conteúdo de origens de dados suportadas pelo Azure. Normalmente, os indexantes são utilizados programáticamente, mas no portal, pode aceder-lhes através do assistente **de dados de Importação.** 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Passo 1 - Inicie o assistente de dados de importação e crie uma fonte de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

1. [Encontre o seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e na página 'Vista Geral', clique em Importar **dados** na barra de comando para criar e preencher um índice de pesquisa.

   :::image type="content" source="media/search-get-started-portal/import-data-cmd.png" alt-text="Comando de importação de dados":::

1. No assistente, clique em **Ligar para os seus**  >  **dados Samples**  >  **hotels-sample**. Esta fonte de dados está incorporada. Se estava a criar a sua própria fonte de dados, precisaria de especificar um nome, tipo e informações de ligação. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

   :::image type="content" source="media/search-get-started-portal/import-datasource-sample.png" alt-text="Selecionar o conjunto de dados de exemplo":::

1. Continue para a próxima página.

### <a name="step-2---skip-the-enrich-content-page"></a>Passo 2 - Ignore a página "Enriquecer conteúdo"

O assistente suporta a criação de um oleoduto de [enriquecimento de IA](cognitive-search-concept-intro.md) para incorporar os algoritmos de IA dos Serviços Cognitivos na indexação. 

Vamos saltar este passo por enquanto, e passar diretamente para **o índice de alvos personalizado.**

   :::image type="content" source="media/search-get-started-portal/skip-cog-skill-step.png" alt-text="Ignorar o passo de capacidades cognitivas":::

> [!TIP]
> Pode passar por um exemplo de indexação de IA num [arranque rápido](cognitive-search-quickstart-blob.md) ou [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Passo 3 - Índice de configuração

Para o índice de amostra de hotéis incorporados, é definido um esquema de índice padrão para si. Com exceção de alguns exemplos avançados de filtro, as consultas na documentação e amostras que visam o índice de amostras de hotéis serão executadas nesta definição de índice:

:::image type="content" source="media/search-get-started-portal/hotelsindex.png" alt-text="Índice de hotéis gerados":::

Normalmente, num exercício baseado em código, a criação de índices é concluída antes de carregar dados. O assistente de dados de importação condensa estes passos gerando um índice básico para qualquer fonte de dados que possa rastejar. No mínimo, os índices precisam de um nome e de uma coleção de campos; um dos campos deve ser marcado como a chave do documento para identificar exclusivamente cada documento. Além disso, pode especificar analisadores de idiomas ou sugestores se quiser consultas autocompletas ou sugeridas.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado.

+ **Recuperável** significa que aparece na lista de resultados da pesquisa. Pode marcar campos individuais como fora dos limites para os resultados da pesquisa, limpando esta caixa de verificação, por exemplo, para campos utilizados apenas em expressões de filtro.
+ **A chave** é o identificador de documentos único. É sempre uma corda, e é necessário.
+ **Filtragem,** **ordenada** e **faceta determinam** se os campos são utilizados numa estrutura de navegação filtrada, ordenada ou facetada.
+ **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis.

Os requisitos de armazenamento não variam como resultado da sua seleção. Por exemplo, se definir o atributo **Retrievable** em vários campos, os requisitos de armazenamento não sobem.

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. As cordas são *atribuídas* como **Recuperáveis** e **Pesmáveis.** *Os inteiros* são atribuídos como **Recuperáveis,** **Filtrados,** **Ordenados** e **Facetable**.

1. Aceite as predefinições.

   Se refazer o assistente uma segunda vez utilizando uma fonte de dados de hotéis existente, o índice não será configurado com atributos predefinidos. Terá de selecionar manualmente atributos sobre importações futuras. 

1. Continue para a próxima página.

### <a name="step-4---configure-indexer"></a>Passo 4 - Indexador de configuração

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador.

Este objeto define um processo executável. Pode colocá-lo em horários recorrentes, mas por enquanto use a opção padrão para executar o indexante uma vez, imediatamente.

Clique **em Submeter** para criar e executar simultaneamente o indexante.

  :::image type="content" source="media/search-get-started-portal/hotels-indexer.png" alt-text="indexante de hotéis":::

## <a name="monitor-progress"></a>Monitorizar o progresso

O assistente deve levá-lo à lista de Indexantes onde pode monitorizar o progresso. Para auto-navegação, vá à página 'Vista Geral' e clique no **separador Indexantes.**

Pode levar alguns minutos para o portal atualizar a página, mas você deve ver o indexante recém-criado na lista, com o estado indicando "em progresso" ou sucesso, juntamente com o número de documentos indexados.

   :::image type="content" source="media/search-get-started-portal/indexers-inprogress.png" alt-text="Mensagem de indexador em curso":::

## <a name="view-the-index"></a>Ver o índice

A página geral do serviço fornece links para os recursos criados no seu serviço de Pesquisa Cognitiva Azure.  Para ver o índice que acabou de criar, clique em **Índices** a partir da lista de links. 

Aguarde que a página do portal se refresque. Após alguns minutos, deverá ver o índice com uma contagem de documentos e tamanho de armazenamento.

   :::image type="content" source="media/search-get-started-portal/indexes-list.png" alt-text="Lista de índices no painel de serviço":::

A partir desta lista, você pode clicar no índice *de amostra de hotéis* que acabou de criar, ver o esquema de índice. e opcionalmente adicionar novos campos. 

O separador **Fields** mostra o esquema de índice. Se estiver a escrever consultas e tiver de verificar se um campo é filtrado ou ordenado, este separador mostra-lhe os atributos.

Percorra a parte inferior da lista para entrar num novo campo. Embora possa sempre criar um novo campo, na maioria dos casos, não pode mudar os campos existentes. Os campos existentes têm representação física no seu serviço de pesquisa e, portanto, não são modificáveis, nem mesmo em código. Para alterar fundamentalmente um campo existente, crie um novo índice, deixando cair o original.

   :::image type="content" source="media/search-get-started-portal/sample-index-def.png" alt-text="definição de índice de exemplo":::

Outras construções, como a classificação de perfis e as opções de CORS, podem ser adicionadas a qualquer momento.

Para compreender claramente o que pode e não pode editar durante o design do índice, dispense um minuto para ver as opções de definição do índice. As opções desativadas são um indicador de que um valor não pode ser alterado nem eliminado. 

## <a name="query-using-search-explorer"></a><a name="query-index"></a> Consulta usando explorador de pesquisa

Mais adiante, já deve ter um índice de pesquisa pronto para consultar com a página de consulta [**Explorador de procura**](search-explorer.md) incorporada. Fornece uma caixa de pesquisa para que possa testar as cadeias de consulta arbitrária.

**O explorador de pesquisa** está apenas equipado para lidar com [pedidos de API REST,](/rest/api/searchservice/search-documents)mas aceita sintaxe tanto para [sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) como para o [parser de consulta lucene completo](/rest/api/searchservice/lucene-query-syntax-in-azure-search), além de todos os parâmetros de pesquisa disponíveis nas operações de [API do Documento de Busca](/rest/api/searchservice/search-documents#bkmk_examples) REST.

> [!TIP]
> Os seguintes passos são demonstrados a 6m08s no [vídeo Azure Cognitive Search Overview](https://channel9.msdn.com/Events/Connect/2016/138).
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   :::image type="content" source="media/search-get-started-portal/search-explorer-cmd.png" alt-text="Comando do Explorador de pesquisa":::

1. A partir da queda do **Índice,** escolha *hotéis-índice de amostra.* Clique no dropdown da **versão API,** para ver quais AS APIs REST estão disponíveis. Para as consultas abaixo, utilize a versão geralmente disponível (2020-06-30).

   :::image type="content" source="media/search-get-started-portal/search-explorer-changeindex.png" alt-text="Comandos de índice e de API":::

1. Na barra de pesquisa, cole nas cordas de consulta abaixo e clique em **Procurar**.

   :::image type="content" source="media/search-get-started-portal/search-explorer-query-string-example.png" alt-text="Botão de pesquisa e corda de consulta":::

## <a name="example-queries"></a>Consultas de exemplo

Pode introduzir termos e frases, semelhantes ao que pode fazer numa pesquisa de Bing ou Google, ou expressões de consulta totalmente especificadas. Os resultados são devolvidos como documentos verbosos da JSON.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com N principais resultados

#### <a name="example-string-query-searchspa"></a>Exemplo (consulta de cordas): `search=spa`

+ O parâmetro **de pesquisa** é utilizado para inserir uma pesquisa de palavras-chave para a pesquisa completa de texto, neste caso, devolvendo dados do hotel para aqueles que contenham *spa* em qualquer campo pesjável no documento.

+ O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. Isto é intencional; a visibilidade em todo o documento é importante para fins de desenvolvimento, especialmente durante os testes. Para uma melhor experiência de utilizador, terá de escrever código que [processa resultados de pesquisa](search-pagination-page-layout.md) para reproduzir elementos importantes.

+ Os documentos são compostos por todos os campos marcados como "recuperáveis" no índice. Para ver os atributos do índice no portal, clique em *hotéis-amostra* na lista **de Índices.**

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exemplo (consulta parametrizada): `search=spa&$count=true&$top=10`

+ O **&** símbolo é utilizado para anexar parâmetros de pesquisa, que podem ser especificados em qualquer ordem.

+ O **parâmetro $count=verdadeiro** devolve a contagem total de todos os documentos devolvidos. Este valor é apresentado junto à parte superior dos resultados da pesquisa. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por **$count=true**. As contagens mais pequenas indicam que o filtro está a funcionar.

+ O **$top=10** devolve os 10 documentos mais bem classificados do total. Por padrão, a Azure Cognitive Search devolve os primeiros 50 melhores jogos. Pode aumentar ou diminuir a quantidade através de **$top**.

### <a name="filter-the-query"></a><a name="filter-query"></a>Filtrar a consulta

Os filtros são incluídos nos pedidos de pesquisa se acrescentar o parâmetro **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exemplo (filtrado): `search=beach&$filter=Rating gt 4`

+ O parâmetro **$filter** devolve resultados que correspondem aos critérios que indicou. Neste caso, classificações superiores a 4.

+ A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-the-query"></a><a name="facet-query"></a> Facetar a consulta

Os filtros de faceta são incluídos nos resultados da pesquisa. Pode utilizar o parâmetro de faceta para devolver uma contagem agregada dos documentos que correspondam a um valor de faceta que indicar.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exemplo (por facetas com redução de âmbito): `search=*&facet=Category&$top=2`

+ **search=*** é uma consulta vazia. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. Por exemplo, você quer uma estrutura de navegação faceting para consistir de todos os hotéis no índice.
+ **facet** devolve uma estrutura de navegação que pode ser transmitida a um controlo de IU. Devolve categorias e uma contagem. Neste caso, as categorias baseiam-se num campo convenientemente chamado *categoria*. Não há agregação na Pesquisa Cognitiva do Azure, mas pode aproximar a agregação através `facet` de , o que dá uma contagem de documentos em cada categoria.

+ **$top=2** devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exemplo (faceta em valores numéricos): `search=spa&facet=Rating`

+ Esta consulta é faceta para classificação, em uma pesquisa de texto para *spa*. O termo *Classificação* pode ser especificado como uma faceta porque o campo é marcado como recuperável, filtrado e facetable no índice, e os valores que contém (numérico, 1 a 5), são adequados para categorizar listagens em grupos.

+ Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

+ O campo *rating* é um ponto flutuante de dupla precisão e o agrupamento será por valor preciso. Para obter mais informações sobre o agrupamento por intervalo (por exemplo, "3 estrelas ratings", "4 estrelas ratings", etc.), consulte [Como implementar a navegação faceta em Azure Cognitive Search](./search-faceted-navigation.md#filter-based-on-a-range).

### <a name="highlight-search-results"></a><a name="highlight-query"></a> Realçar os resultados de pesquisa

O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exemplo (marcador): `search=beach&highlight=Description`

+ Neste exemplo, a *praia* de palavras formatada é mais fácil de detetar no campo de descrição.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exemplo (análise linguística): `search=beaches&highlight=Description`

+ A pesquisa completa de texto reconhece variações básicas nos formulários de texto. Neste caso, os resultados da pesquisa contêm texto sonoro para "praia", para hotéis que tenham essa palavra nos seus campos pes pesjáveis, em resposta a uma pesquisa de palavras-chave em "praias". Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística. 

+ A Azure Cognitive Search suporta 56 analisadores tanto da Lucene como da Microsoft. O padrão usado pela Azure Cognitive Search é o analisador padrão lucene.

### <a name="try-fuzzy-search"></a><a name="fuzzy-search"></a> Experimentar a pesquisa difusa

Por padrão, termos de consulta mal escritos, como *assento* para "Seattle", não retornam os jogos em busca típica. O exemplo seguinte não devolve nenhum resultado.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exemplo (termo com erros ortográficos, não processado): `search=seatle`

Para processar erros ortográficos, pode utilizar a pesquisa difusa. A pesquisa difusa é ativada quando utiliza a sintaxe de consulta Lucene completa, que ocorre quando faz duas coisas: define **queryType=full** na consulta e anexa **~** à cadeia de pesquisa.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exemplo (termo com erros ortográficos, processado): `search=seatle~&queryType=full`

Este exemplo devolve agora documentos que incluem jogos em "Seattle".

Quando **queryType** não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa.

A pesquisa difusa e a pesquisa com carateres universais têm implicações no resultado da pesquisa. A análise linguística não é realizada nestes formatos de consulta. Antes de utilizar a pesquisa fuzzy e wildcard, [reveja como funciona a pesquisa completa de texto na Pesquisa Cognitiva Azure](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a secção sobre exceções à análise lexical.

Para obter mais informações sobre cenários de consulta habilitados pelo parser de consulta completo, consulte [a sintaxe de consulta lucene em Azure Cognitive Search](/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="try-geospatial-search"></a><a name="geo-search"></a> Experimentar a pesquisa geoespacial

 A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas.

A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou se utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. Se tiver requisitos do utilizador para pesquisar numa cidade ou país/região por nome, adicione campos que contenham nomes de cidade ou país/região, além de coordenadas.

## <a name="takeaways"></a>Conclusões

Este tutorial proporcionou uma rápida introdução à Pesquisa Cognitiva Azure utilizando o portal Azure.

Aprendeu a criar um índice de pesquisa com o assistente para **Importar dados**. Aprendeu sobre [indexadores](search-indexer-overview.md), bem como o fluxo de trabalho básico para o design de índices, incluindo [modificações suportadas num índice publicado](/rest/api/searchservice/update-index).

Ao utilizar o **explorador do Search** no portal do Azure, aprendeu a sintaxe básica das consultas através de exemplos práticos que demonstram as capacidades principais, como filtros, detetor de ocorrências, pesquisa difusa e pesquisa geográfica.

Também aprendeu a encontrar índices, indexadores e fontes de dados no portal. Utilizando uma origem de dados nova no futuro, pode utilizar o portal para verificar rapidamente as definições ou as coleções de campos da mesma com um esforço mínimo.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Utilize um assistente de portal para gerar uma aplicação web pronta a usar que funciona num browser. Pode experimentar este assistente no pequeno índice que acabou de criar ou utilizar um dos conjuntos de dados de amostras incorporados para uma experiência de pesquisa mais rica.

> [!div class="nextstepaction"]
> [Criar uma app de demonstração no portal](search-create-app-portal.md)