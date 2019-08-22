---
title: 'Início rápido: Criar, carregar e consultar um índice usando portal do Azure-Azure Search'
description: Use o assistente para importar dados no portal do Azure para criar, carregar e consultar seu primeiro índice em Azure Search.
author: lobrien
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: laobri
ms.openlocfilehash: c2950b35f207f772f1190a2f8f104098eeb43375
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656352"
---
# <a name="quickstart-create-an-azure-search-index-using-the-azure-portal"></a>Início rápido: Criar um índice de Azure Search usando o portal do Azure
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Postman](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [C#](search-get-started-dotnet.md)

Para um avanço rápido nos conceitos de Azure Search, experimente as ferramentas internas no portal do Azure. Os assistentes e editores não oferecem paridade total com as APIs REST e .NET, mas você pode começar rapidamente com uma introdução sem código, escrevendo consultas interessantes em relação a dados de exemplo em minutos.

> [!div class="checklist"]
> * Comece com um conjunto de dados de exemplo público gratuito hospedado no Azure
> * Executar o assistente de **importação de dados** no Azure Search para carregar dados e gerar um índice
> * Monitorar o progresso da indexação no portal
> * Exibir um índice e opções existentes para modificá-lo
> * Explore a pesquisa de texto completo, filtros, facetas, pesquisa difusa e GeoSearch com o **Search Explorer**

Se as ferramentas estiverem muito limitadas, você poderá considerar uma [introdução baseada em código para programação Azure Search no .net](search-howto-dotnet-sdk.md) ou usar o [postmaster para fazer chamadas à API REST](search-get-started-postman.md). Também pode ver uma demonstração de seis minutos dos passos deste tutorial a partir do terceiro minuto deste [Vídeo de Descrição Geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

[Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada.

As seções no painel de serviço mostram quantos índices, indexadores e fontes de dados você já tem. 

![Listas de índices, indexadores e fontes de fonte](media/search-get-started-portal/tiles-indexers-datasources.png)

## <a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa iteram num [*índice*](search-what-is-an-index.md) que contém dados pesquisáveis, metadados e construções adicionais que otimizam determinados comportamentos de pesquisa.

Neste tutorial, utilizamos um conjunto de dados de exemplo incorporado que pode ser pesquisado com um [*indexador*](search-indexer-overview.md) através do assistente para **Importar dados**. Um indexador é um crawler específico da origem que pode ler metadados e conteúdo de origens de dados suportadas pelo Azure. Normalmente, os indexadores são usados programaticamente, mas no portal, você pode acessá-los por meio do assistente de **importação de dados** . 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Etapa 1-iniciar o assistente de importação de dados e criar uma fonte de dados

1. No painel do serviço Azure Search, clique em **importar dados** na barra de comandos para criar e preencher um índice de pesquisa.

   ![Comando de importação de dados](media/search-get-started-portal/import-data-cmd.png)

2. No assistente, clique em **conectar-se às** > **amostras** > de dados**Hotéis-exemplo**. Essa fonte de dados é interna. Se você estivesse criando sua própria fonte de dados, precisaria especificar um nome, tipo e informações de conexão. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

   ![Selecionar o conjunto de dados de exemplo](media/search-get-started-portal/import-datasource-sample.png)

3. Vá para a próxima página.

   ![Botão próxima página para pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Etapa 2 – ignorar habilidades cognitivas

O assistente dá suporte à criação de um [pipeline de habilidades cognitivas](cognitive-search-concept-intro.md) para incorporar os algoritmos de ai de serviços cognitivas à indexação. 

Vamos ignorar essa etapa por enquanto e passar diretamente para a personalização do **índice de destino**.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Você pode percorrer um exemplo de indexação de AI em um guia de [início rápido](cognitive-search-quickstart-blob.md) ou [tutorial](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Etapa 3 – configurar o índice

Normalmente, a criação de índice é um exercício baseado em código, concluída antes do carregamento de dados. No entanto, como este tutorial indica, o assistente pode gerar um índice básico para qualquer fonte de dados que possa rastrear. No mínimo, os índices precisam de um nome e de uma coleção de campos; um dos campos deve ser marcado como a chave do documento para identificar exclusivamente cada documento. Além disso, você pode especificar analisadores de idioma ou sugestores se desejar consultas de preenchimento automático ou sugerido.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado.

* **Recuperável** significa que aparece na lista de resultados da pesquisa. Você pode marcar campos individuais como limites para os resultados da pesquisa desmarcando essa caixa de seleção, por exemplo, para campos usados somente em expressões de filtro.
* **Chave** é o identificador exclusivo do documento. É sempre uma cadeia de caracteres e é obrigatório.
* **Filtrável**, **classificável**e **facetable** determinam se os campos são usados em uma estrutura de navegação de filtro, classificação ou facetada.
* **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis.

Os requisitos de armazenamento não variam como resultado de sua seleção. Por exemplo, se você definir o atributo recuperável em vários campos, os requisitos de armazenamento não vão para cima.

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. As cadeias de *caracteres* são atribuídas como **recuperáveis** e **pesquisáveis**. Os *inteiros* são atribuídos como **recuperáveis**, **filtráveis**, **classificável**e **face**.

1. Aceite as predefinições. 

   Se você executar novamente o assistente uma segunda vez usando uma fonte de dados de hotéis existente, o índice não será configurado com atributos padrão. Você terá que selecionar manualmente os atributos em importações futuras. 

   ![Índice de hotéis gerados](media/search-get-started-portal/hotelsindex.png)

2. Vá para a próxima página.

   ![Próxima página Criar indexador](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Etapa 4-configurar o indexador

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador.

Este objeto define um processo executável. Você pode colocá-lo no agendamento recorrente, mas, por enquanto, use a opção padrão para executar o indexador uma vez, imediatamente.

Clique em **Enviar** para criar e executar o indexador simultaneamente.

  ![indexador de hotéis](media/search-get-started-portal/hotels-indexer.png)

## <a name="monitor-progress"></a>Monitorar o progresso

O assistente deve levá-lo para a lista de indexadores onde você pode monitorar o progresso. Para navegação automática, vá para a página Visão geral e cliqueem indexadores.

Pode levar alguns minutos para o portal atualizar a página, mas você deve ver o indexador recém-criado na lista, com o status indicando "em andamento" ou êxito, juntamente com o número de documentos indexados.

   ![Mensagem de indexador em curso](media/search-get-started-portal/indexers-inprogress.png)

## <a name="view-the-index"></a>Ver o índice

A página principal do serviço fornece links para os recursos criados em seu serviço de Azure Search.  Para exibir o índice que você acabou de criar, clique em **índices** na lista de links. 

   ![Lista de índices no painel de serviço](media/search-get-started-portal/indexes-list.png)

Nessa lista, você pode clicar no índice de *exemplo de hotéis* que acabou de criar, exibir o esquema de índice. e, opcionalmente, adicionar novos campos. 

A guia **campos** mostra o esquema de índice. Role até a parte inferior da lista para inserir um novo campo. Na maioria dos casos, você não pode alterar os campos existentes. Os campos existentes têm uma representação física no Azure Search e são, portanto, não modificáveis, nem mesmo no código. Para alterar fundamentalmente um campo existente, crie um novo índice, descartando o original.

   ![definição de índice de exemplo](media/search-get-started-portal/sample-index-def.png)

Outras construções, como a classificação de perfis e as opções de CORS, podem ser adicionadas a qualquer momento.

Para compreender claramente o que pode e não pode editar durante o design do índice, dispense um minuto para ver as opções de definição do índice. As opções desativadas são um indicador de que um valor não pode ser alterado nem eliminado. 

## <a name="query-index"></a>Consultar usando o Gerenciador de pesquisa

Mais adiante, já deve ter um índice de pesquisa pronto para consultar com a página de consulta [**Explorador de procura**](search-explorer.md) incorporada. Fornece uma caixa de pesquisa para que possa testar as cadeias de consulta arbitrária.

**O Gerenciador de pesquisa** está equipado apenas para lidar com solicitações da [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), mas aceita sintaxe para sintaxe de [consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e analisador de [consulta Lucene completo](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), além de todos os parâmetros de pesquisa disponíveis na [API REST do documento de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) das.

> [!TIP]
> Os passos seguintes são demonstrados a partir dos 6m08 segundos do [vídeo Azure Search Overview](https://channel9.msdn.com/Events/Connect/2016/138) (Descrição geral do Azure Search).
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   ![Comando do Explorador de pesquisa](media/search-get-started-portal/search-explorer-cmd.png)

2. Na lista suspensa **índice** , escolha *Hotéis – exemplo*. Clique no menu suspenso **versão da API** para ver quais APIs REST estão disponíveis. Para as consultas abaixo, use a versão disponível para o público (2019-05-06).

   ![Comandos de índice e de API](media/search-get-started-portal/search-explorer-changeindex.png)

3. Na barra de pesquisa, Cole as cadeias de caracteres de consulta abaixo e clique em **Pesquisar**.

   ![Cadeia de caracteres de consulta e botão Pesquisar](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Exemplos de consultas

Você pode inserir termos e frases, semelhante ao que você pode fazer em uma pesquisa do Bing ou Google, ou expressões de consulta totalmente especificadas. Os resultados são retornados como documentos JSON detalhados.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com N principais resultados

#### <a name="example-string-query-searchspa"></a>Exemplo (consulta de cadeia de caracteres):`search=spa`

* O parâmetro **Search** é usado para inserir uma palavra-chave Search para pesquisa de texto completo, nesse caso, retornando dados do hotel para aqueles que contêm *Spa* em qualquer campo pesquisável no documento.

* O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. Isso é intencional; a visibilidade de todo o documento é importante para fins de desenvolvimento, especialmente durante o teste. Para uma melhor experiência de utilizador, terá de escrever código que [processa resultados de pesquisa](search-pagination-page-layout.md) para reproduzir elementos importantes.

* Os documentos são compostos por todos os campos marcados como "recuperáveis" no índice. Para exibir atributos de índice no portal, clique em *Hotéis – exemplo* na lista **índices** .

#### <a name="example-parameterized-query-searchspacounttruetop10"></a>Exemplo (consulta parametrizada):`search=spa&$count=true&$top=10`

* O símbolo **&** é utilizado para acrescentar os parâmetros da pesquisa, que podem ser especificados por qualquer ordem.

* O parâmetro **$Count = true** retorna a contagem total de todos os documentos retornados. Este valor é apresentado junto à parte superior dos resultados da pesquisa. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por **$count=true**. As contagens mais pequenas indicam que o filtro está a funcionar.

* O **$Top = 10** retorna os 10 documentos com classificação mais alta do total. Por predefinição, o Azure Search devolve as 50 melhores correspondências. Pode aumentar ou diminuir a quantidade através de **$top**.

### <a name="filter-query"></a>Filtrar a consulta

Os filtros são incluídos nos pedidos de pesquisa se acrescentar o parâmetro **$filter**. 

#### <a name="example-filtered-searchbeachfilterrating-gt-4"></a>Exemplo (filtrado): `search=beach&$filter=Rating gt 4`

* O parâmetro **$filter** devolve resultados que correspondem aos critérios que indicou. Nesse caso, as classificações maiores que 4.

* A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Facetar a consulta

Os filtros de faceta são incluídos nos resultados da pesquisa. Pode utilizar o parâmetro de faceta para devolver uma contagem agregada dos documentos que correspondam a um valor de faceta que indicar.

#### <a name="example-faceted-with-scope-reduction-searchfacetcategorytop2"></a>Exemplo (por facetas com redução de âmbito): `search=*&facet=Category&$top=2`

* **search=** * é uma consulta vazia. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. Por exemplo, você deseja que uma estrutura de navegação de faceta consista em todos os hotéis no índice.
* **facet** devolve uma estrutura de navegação que pode ser transmitida a um controlo de IU. Devolve categorias e uma contagem. Nesse caso, as categorias são baseadas em um campo chamado de *categoria*convenientemente. Não existe agregação no Azure Search, mas pode aproximar a agregação através de `facet`, que dá uma contagem de documentos em cada categoria.

* **$top=2** devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchspafacetrating"></a>Exemplo (faceta em valores numéricos):`search=spa&facet=Rating`

* Esta consulta é facetada para classificação, em uma pesquisa de texto para *Spa*. A *classificação* de termo pode ser especificada como uma faceta porque o campo está marcado como recuperável, filtrável e de facetable no índice, e os valores que ele contém (numérico, 1 a 5), são adequados para categorizar listagens em grupos.

* Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

* O campo de *classificação* é ponto flutuante de precisão dupla e o agrupamento será por valor preciso. Para obter mais informações sobre agrupamento por intervalo (por exemplo, "3 classificações de estrelas", "quatro classificações de estrelas", etc.), consulte [como implementar a navegação facetada no Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation#filter-based-on-a-range).


### <a name="highlight-query"></a> Realçar os resultados de pesquisa

O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo.

#### <a name="example-highlighter-searchbeachhighlightdescription"></a>Exemplo (marcador): `search=beach&highlight=Description`

* Neste exemplo, é mais fácil localizar a palavra-chave formatada no campo Descrição.

#### <a name="example-linguistic-analysis-searchbeacheshighlightdescription"></a>Exemplo (análise linguística): `search=beaches&highlight=Description`

* A pesquisa de texto completo reconhece variações básicas em formulários do Word. Nesse caso, os resultados da pesquisa contêm texto realçado para "praia", para hotéis que têm essa palavra em seus campos pesquisáveis, em resposta a uma pesquisa de palavra-chave em "praias". Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística. 

* O Azure Search suporta 56 analisadores do Lucene e da Microsoft. Por predefinição, o Azure Search utiliza o analisador padrão do Lucene.

### <a name="fuzzy-search"></a> Experimentar a pesquisa difusa

Por padrão, os termos de consulta digitados incorretamente, como *Seatle* para "Seattle", falham ao retornar correspondências na pesquisa típica. O exemplo seguinte não devolve nenhum resultado.

#### <a name="example-misspelled-term-unhandled-searchseatle"></a>Exemplo (termo com erros ortográficos, não processado): `search=seatle`

Para processar erros ortográficos, pode utilizar a pesquisa difusa. A pesquisa difusa é ativada quando utiliza a sintaxe de consulta Lucene completa, que ocorre quando faz duas coisas: define **queryType=full** na consulta e anexa **~** à cadeia de pesquisa.

#### <a name="example-misspelled-term-handled-searchseatlequerytypefull"></a>Exemplo (termo com erros ortográficos, processado): `search=seatle~&queryType=full`

Este exemplo agora retorna documentos que incluem correspondências em "Seattle".

Quando **queryType** não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa.

A pesquisa difusa e a pesquisa com carateres universais têm implicações no resultado da pesquisa. A análise linguística não é realizada nestes formatos de consulta. Antes de utilizar a pesquisa difusa e a pesquisa com carateres universais, reveja [Como funciona a pesquisa em texto completo no Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a secção sobre as exceções à análise lexical.

Para obter mais informações sobre os cenários de consultas que o analisador de consultas completo permite, veja [Lucene query syntax in Azure Search (Sintaxe de consultas do Lucene no Azure Search)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-12212-4767-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(Location,geography'POINT(-122.12 47.67)') le 5`

A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas.

A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou se utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. Se você tiver requisitos de usuário para pesquisar em uma cidade ou país/região por nome, adicione campos que contenham nomes de cidade ou país/região, além de coordenadas.

## <a name="takeaways"></a>Conclusões

Este tutorial forneceu uma breve introdução ao Azure Search usando o portal do Azure.

Aprendeu a criar um índice de pesquisa com o assistente para **Importar dados**. Aprendeu sobre [indexadores](search-indexer-overview.md), bem como o fluxo de trabalho básico para o design de índices, incluindo [modificações suportadas num índice publicado](https://docs.microsoft.com/rest/api/searchservice/update-index).

Ao utilizar o **explorador do Search** no portal do Azure, aprendeu a sintaxe básica das consultas através de exemplos práticos que demonstram as capacidades principais, como filtros, detetor de ocorrências, pesquisa difusa e pesquisa geográfica.

Você também aprendeu como localizar índices, indexadores e fontes de dados no Portal. Utilizando uma origem de dados nova no futuro, pode utilizar o portal para verificar rapidamente as definições ou as coleções de campos da mesma com um esforço mínimo.

## <a name="clean-up"></a>Limpeza

Quando você está trabalhando em sua própria assinatura, é uma boa ideia no final de um projeto identificar se você ainda precisa dos recursos que criou. Os recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir o conjunto inteiro de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Pode utilizar as ferramentas programáticas para explorar mais o Azure Search:

* [Criar um índice usando o SDK do .NET](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Criar um índice usando APIs REST](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Criar um índice usando o postmaster ou o Fiddler e as APIs REST do Azure Search](search-get-started-postman.md)
