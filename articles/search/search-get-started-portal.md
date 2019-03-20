---
title: Tutorial de início rápido de indexação e consulta através do portal do Azure - Azure Search
description: Saiba como criar e consultar o seu primeiro índice na Azure Search utilizando o Assistente para importar dados e dados de exemplo incorporado no portal do Azure.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 85fc8a42f5220aa8f05495aa260d05ce009884f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137832"
---
# <a name="quickstart-use-built-in-portal-tools-for-azure-search-import-indexing-and-queries"></a>Início rápido: Utilizar ferramentas do portal internas para importação de Azure Search, indexação e consultas

Para obter um rápido recorrem aos conceitos de Azure Search, experimente as ferramentas incorporadas no portal do Azure. Assistentes e editores não oferecem uma paridade completa com o .NET e REST APIs, mas pode começar a utilizar rapidamente com uma introdução sem código, escrever consultas interessantes em relação a dados de exemplo numa questão de minutos.

> [!div class="checklist"]
> * Começar com um conjunto de dados de exemplo público gratuito alojado no Azure
> * Executar o **importar dados** assistente no Azure Search para carregar dados e gerar um índice
> * Monitorizar o progresso de indexação no portal
> * Ver um índice existente e as opções para modificá-la
> * Explorar a pesquisa em texto completo, filtros, facetas, a pesquisa difusa e a pesquisa geográfica com **Explorador de pesquisa**

Se as ferramentas são demasiado limitação, pode considerar uma [baseadas em código introdução à programação do Azure Search no .NET](search-howto-dotnet-sdk.md) ou utilize [Postman ou Fiddler para fazer chamadas de REST API](search-fiddler.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Também pode ver uma demonstração de seis minutos dos passos deste tutorial a partir do terceiro minuto deste [Vídeo de Descrição Geral do Azure Search](https://channel9.msdn.com/Events/Connect/2016/138).

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este início rápido. 

### <a name="check-for-space"></a>Verificar o espaço

Muitos clientes começam com o serviço gratuito. Esta versão está limitada a três índices, três origens de dados e três indexadores. Certifique-se de que tem espaço para itens adicionais antes de começar. Este tutorial cria um objeto de cada.

Seções no dashboard do serviço mostram quantos índices, indexadores e origens de dados que já tem. 

![Listas de índices, indexadores e origens de dados](media/search-get-started-portal/tiles-indexers-datasources2.png)

## <a name="create-index"></a> Criar um índice e carregar dados

As consultas de pesquisa iteram num [*índice*](search-what-is-an-index.md) que contém dados pesquisáveis, metadados e construções adicionais que otimizam determinados comportamentos de pesquisa.

Neste tutorial, utilizamos um conjunto de dados de exemplo incorporado que pode ser pesquisado com um [*indexador*](search-indexer-overview.md) através do assistente para **Importar dados**. Um indexador é um crawler específico da origem que pode ler metadados e conteúdo de origens de dados suportadas pelo Azure. Normalmente, indexadores são utilizados por meio de programação, mas no portal, pode acessá-los através da **importar dados** assistente. 

### <a name="step-1---start-the-import-data-wizard-and-create-a-data-source"></a>Passo 1 - iniciar o Assistente de importação de dados e criar uma origem de dados

1. No dashboard do serviço Azure Search, clique em **importar dados** na barra de comandos para criar e preencher um índice de pesquisa.

   ![Comando de importação de dados](media/search-get-started-portal/import-data-cmd2.png)

2. No assistente, clique em **Ligar aos seus dados** > **Exemplos** > **realestate-us-sample**. Esta origem de dados está incorporada. Se for criar sua própria origem de dados, terá de especificar um nome, tipo e informações de ligação. Depois de criada, torna-se uma “origem de dados existente”, que pode ser reutilizada noutras operações de importação.

   ![Selecionar o conjunto de dados de exemplo](media/search-get-started-portal/import-datasource-sample2.png)

3. Continue para a página seguinte.

   ![Botão seguinte da página de pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

### <a name="step-2---skip-cognitive-skills"></a>Passo 2 – capacidades cognitivas a ignorar

O assistente suporta a criação de um [pipeline de capacidades cognitivas](cognitive-search-concept-intro.md) para incorporar os algoritmos de ia de serviços cognitivos na indexação. 

Vamos ignorar este passo por agora e avançar diretamente para **personalizar o índice de destino**.

   ![Ignorar o passo de capacidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

> [!TIP]
> Pode experimentar a funcionalidade de pré-visualização da pesquisa cognitiva do Azure Search no [início rápido](cognitive-search-quickstart-blob.md) ou no [tutorial da pesquisa cognitiva](cognitive-search-tutorial-blob.md).

### <a name="step-3---configure-index"></a>Passo 3 - configurar o índice

Normalmente, a criação de índices é um exercício baseada em código, concluído antes de carregar os dados. No entanto, como indica que este tutorial, o assistente pode gerar um índice básico para qualquer origem de dados que consiga pesquisar. No mínimo, os índices precisam de um nome e de uma coleção de campos; um dos campos deve ser marcado como a chave do documento para identificar exclusivamente cada documento. Além disso, pode especificar sugestores ou analisadores de idioma se desejar preenchimento automático ou consultas sugeridas.

Os campos têm tipos de dados e atributos. As caixas de verificação na parte superior são *atributos de índice* que controlam a forma como o campo é utilizado.

* **Recuperável** significa que aparece na lista de resultados da pesquisa. Pode marcar campos individuais como inacessíveis para os resultados da pesquisa ao desmarcar esta caixa de verificação, por exemplo, quando os campos são utilizados apenas em expressões de filtro.
* **Chave** é o identificador exclusivo do documento. É sempre uma cadeia de caracteres, e é necessário.
* **Filtrável**, **ordenável**, e **Facetável** determinar se os campos são utilizados num filtro, ordenação ou estrutura de navegação por facetas.
* **Pesquisável** significa que um campo está incluído na pesquisa de texto completo. As cadeias são pesquisáveis. Os campos numéricos e booleanos são frequentemente marcados como não pesquisáveis.

Requisitos de armazenamento não varia devido a sua seleção. Por exemplo, se definir o **recuperável** atributo em múltiplos campos, não subir o requisitos de armazenamento.

Por predefinição, o assistente verifica a origem de dados relativamente a identificadores exclusivos como a base do campo de chaves. *Cadeias de caracteres* são atribuídas como **recuperável** e **pesquisável**. *Números inteiros* são atribuídas como **recuperável**, **filtrável**, **ordenável**, e **Facetável**.

1. Aceite as predefinições. 

   Se executar novamente o assistente uma segunda vez usando uma origem de dados de realestate existente, o índice não será configurado com atributos predefinidos. Terá de selecionar manualmente os atributos no imports futuras.

   ![Índice de realestate gerado](media/search-get-started-portal/realestateindex2.png)

2. Continue para a página seguinte.

   ![Próxima página Criar indexador](media/search-get-started-portal/next-button-create-indexer.png)

### <a name="step-4---configure-indexer"></a>Passo 4 - configurar o indexador

Ainda no assistente para **Importar dados**, clique em **Indexador** > **Nome** e escreva um nome para o indexador.

Este objeto define um processo executável. Pode colocá-lo na agenda recorrente, mas por agora, utilize a opção predefinida para executar o indexador uma vez, imediatamente.

Clique em **submeter** para criar e executar simultaneamente o indexador.

  ![indexador de realestate](media/search-get-started-portal/realestate-indexer2.png)

## <a name="monitor-progress"></a>Monitorize o progresso

O assistente deverá demorar para a lista de indexadores onde pode monitorizar o progresso. Para uma navegação automática, vá para a descrição geral da página e clique em **indexadores**.

Pode demorar alguns minutos para o portal atualizar a página, mas deve ver o indexador recém-criado na lista, com o estado a indicar "em curso" ou com êxito, juntamente com o número de documentos indexados.

   ![Mensagem de indexador em curso](media/search-get-started-portal/indexers-inprogress2.png)

## <a name="view-the-index"></a>Ver o índice

A página principal de serviço fornece ligações para os recursos criados no seu serviço de Azure Search.  Para ver o índice que acabou de criar, clique em **índices** na lista de links. 

   ![Lista de índices no dashboard do serviço](media/search-get-started-portal/indexes-list.png)

Nesta lista, pode clicar no *realestate-us-sample* índice que acabou de criar, ver o esquema de índice. e, opcionalmente, adicionar novos campos. 

O **campos** separador mostra o esquema de índice. Desloque-se para a parte inferior da lista para introduzir um novo campo. Na maioria dos casos, não é possível alterar os campos existentes. Os campos existentes têm uma representação física no Azure Search e são, portanto, não modificáveis, nem mesmo no código. Para alterar fundamentalmente um campo existente, crie um novo índice, remover o original.

   ![definição de índice de exemplo](media/search-get-started-portal/sample-index-def.png)

Outras construções, como a classificação de perfis e as opções de CORS, podem ser adicionadas a qualquer momento.

Para compreender claramente o que pode e não pode editar durante o design do índice, dispense um minuto para ver as opções de definição do índice. As opções desativadas são um indicador de que um valor não pode ser alterado nem eliminado. 

## <a name="query-index"></a> Consultar com o Explorador de pesquisa

Mais adiante, já deve ter um índice de pesquisa pronto para consultar com a página de consulta [**Explorador de procura**](search-explorer.md) incorporada. Fornece uma caixa de pesquisa para que possa testar as cadeias de consulta arbitrária.

**Explorador de pesquisa** só está preparado para processar [solicitações da API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), mas ela aceita sintaxes de [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e [completa de analisador de consultas de Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), Além de todos os parâmetros de pesquisa disponíveis no [API REST de pesquisa de documento](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) operações.

> [!TIP]
> Os passos seguintes são demonstrados a partir dos 6m08 segundos do [vídeo Azure Search Overview](https://channel9.msdn.com/Events/Connect/2016/138) (Descrição geral do Azure Search).
>

1. Clique em **Explorador de pesquisa** na barra de comando.

   ![Comando do Explorador de pesquisa](media/search-get-started-portal/search-explorer-cmd2.png)

2. Clique em **Alterar índice**, na barra de comandos, para mudar para *realestate-us-sample*. Clique em **Definir versão da API**, na barra de comandos, para ver que APIs REST estão disponíveis. Para as consultas abaixo, utilize a versão disponível geralmente (2017-11-11).

   ![Comandos de índice e de API](media/search-get-started-portal/search-explorer-changeindex-se2.png)

3. Na barra de pesquisa, cole as cadeias de caracteres de consulta abaixo e clique em **pesquisa**.

   ![Botão de pesquisa e a cadeia de caracteres de consulta](media/search-get-started-portal/search-explorer-query-string-example.png)

## <a name="example-queries"></a>Consultas de exemplo

Pode introduzir termos e expressões, semelhantes ao que pode fazer numa pesquisa do Bing ou o Google ou expressões de consulta completamente especificado. Os resultados são retornados como verbosos de documentos JSON.

### <a name="simple-query-with-top-n-results"></a>Consulta simples com N principais resultados

#### <a name="example-string-query-searchseattle"></a>Exemplo (consulta de cadeia de caracteres): `search=seattle`

* O parâmetro **search** é utilizado para introduzir uma pesquisa de palavras-chave para pesquisa em texto completo; neste caso, devolve imóveis em King County, no estado norte-americano de Washington, que contenham *Seattle* em qualquer campo pesquisável do documento.

* O **Explorador de pesquisa** devolve resultados em JSON, que é verboso e difícil de ler se os documentos tiverem uma estrutura densa. Isto é intencional; visibilidade em todo o documento é importante para fins de desenvolvimento, especialmente durante os testes. Para uma melhor experiência de utilizador, terá de escrever código que [processa resultados de pesquisa](search-pagination-page-layout.md) para reproduzir elementos importantes.

* Os documentos são compostos por todos os campos marcados como "recuperáveis" no índice. Para ver os atributos de índice no portal, clique em *realestate-us-sample* no **índices** lista.

#### <a name="example-parameterized-query-searchseattlecounttruetop100"></a>Exemplo (consulta parametrizada): `search=seattle&$count=true&$top=100`

* O símbolo **&** é utilizado para acrescentar os parâmetros da pesquisa, que podem ser especificados por qualquer ordem.

* O parâmetro **$count=true** devolve a contagem total de todos os documentos devolvidos. Este valor é apresentado junto à parte superior dos resultados da pesquisa. Pode verificar as consultas de filtro através da monitorização das alterações comunicadas por **$count=true**. As contagens mais pequenas indicam que o filtro está a funcionar.

* **$top=100** devolve os cem documentos com a melhor classificação de entre o total. Por predefinição, o Azure Search devolve as 50 melhores correspondências. Pode aumentar ou diminuir a quantidade através de **$top**.

### <a name="filter-query"></a>Filtrar a consulta

Os filtros são incluídos nos pedidos de pesquisa se acrescentar o parâmetro **$filter**. 

#### <a name="example-filtered-searchseattlefilterbeds-gt-3"></a>Exemplo (filtrado): `search=seattle&$filter=beds gt 3`

* O parâmetro **$filter** devolve resultados que correspondem aos critérios que indicou. Neste caso, quartos acima de 3.

* A sintaxe do filtro é uma construção OData. Para obter mais informações, veja [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

### <a name="facet-query"></a> Facetar a consulta

Os filtros de faceta são incluídos nos resultados da pesquisa. Pode utilizar o parâmetro de faceta para devolver uma contagem agregada dos documentos que correspondam a um valor de faceta que indicar.

#### <a name="example-faceted-with-scope-reduction-searchfacetcitytop2"></a>Exemplo (por facetas com redução de âmbito): `search=*&facet=city&$top=2`

* **search=*** é uma consulta vazia. As pesquisas em branco pesquisam em tudo. Uma das razões para submeter uma consulta em branco é para filtrar ou especificar facetas no conjunto completo de documentos. Por exemplo, pretende que uma estrutura de navegação por facetas seja constituída por todas as cidades no índice.

* **facet** devolve uma estrutura de navegação que pode ser transmitida a um controlo de IU. Devolve categorias e uma contagem. Neste caso, as categorias baseiam-se no número de cidades. Não existe agregação no Azure Search, mas pode aproximar a agregação através de `facet`, que dá uma contagem de documentos em cada categoria.

* **$top=2** devolve dois documentos, o que exemplifica que pode utilizar `top` para reduzir ou aumentar os resultados.

#### <a name="example-facet-on-numeric-values-searchseattlefacetbeds"></a>Exemplo (faceta em valores numéricos): `search=seattle&facet=beds`**

* Esta consulta é a faceta para «beds», numa pesquisa de texto por *Seattle*. O termo *beds* pode ser especificado como faceta, porque o campo está marcado como campo recuperável, filtrável e facetável no índice e os valores nele contidos (numéricos, 1 a 5) são adequados para categorizar imóveis em grupos (listas com três quartos, quatro quartos).

* Apenas os campos filtráveis podem ser facetados. Apenas os campos recuperáveis podem ser devolvidos nos resultados.

### <a name="highlight-query"></a> Realçar os resultados de pesquisa

O detetor de ocorrências refere-se à formatação no texto que corresponde à palavra-chave, tendo em conta que as correspondências são encontradas num determinado campo. Se o termo da sua pesquisa estiver embrenhado numa descrição, pode adicionar o detetor de ocorrências para que seja mais fácil encontrá-lo.

#### <a name="example-highlighter-searchgranite-countertopshighlightdescription"></a>Exemplo (marcador): `search=granite countertops&highlight=description`

* Neste exemplo, é mais fácil encontrar a expressão formatada *granite countertops* (bancadas em granito) no campo da descrição.

#### <a name="example-linguistic-analysis-searchmicehighlightdescription"></a>Exemplo (análise linguística): `search=mice&highlight=description`

* A pesquisa em texto completo localiza formas de palavras com uma semântica parecida. Neste caso, os resultados da pesquisa contêm texto realçado para “rato”, para casas que tenham uma praga de ratos, em resposta a uma pesquisa de palavras-chave sobre “rato”. Podem aparecer nos resultados diferentes formas da mesma palavra, devido à análise linguística.

* O Azure Search suporta 56 analisadores do Lucene e da Microsoft. Por predefinição, o Azure Search utiliza o analisador padrão do Lucene.

### <a name="fuzzy-search"></a> Experimentar a pesquisa difusa

Por predefinição, os termos de consulta com erros ortográficos, como *samamish*para o planalto Samammish, na área de Seattle, não conseguem devolver correspondências em pesquisas típicas. O exemplo seguinte não devolve nenhum resultado.

#### <a name="example-misspelled-term-unhandled-searchsamamish"></a>Exemplo (termo com erros ortográficos, não processado): `search=samamish`

Para processar erros ortográficos, pode utilizar a pesquisa difusa. A pesquisa difusa é ativada quando utiliza a sintaxe de consulta Lucene completa, que ocorre quando faz duas coisas: define **queryType=full** na consulta e anexa **~** à cadeia de pesquisa.

#### <a name="example-misspelled-term-handled-searchsamamishquerytypefull"></a>Exemplo (termo com erros ortográficos, processado): `search=samamish~&queryType=full`

Este exemplo devolve agora documentos que incluem correspondências de "Sammamish".

Quando **queryType** não é especificado, é utilizado o analisador de consultas simples predefinido. O analisador de consultas simples é mais rápido, mas se precisar de pesquisa difusa, de expressões regulares, da pesquisa de proximidade ou de outros tipos de consultas avançadas, será necessária a sintaxe completa.

A pesquisa difusa e a pesquisa com carateres universais têm implicações no resultado da pesquisa. A análise linguística não é realizada nestes formatos de consulta. Antes de utilizar a pesquisa difusa e a pesquisa com carateres universais, reveja [Como funciona a pesquisa em texto completo no Azure Search](search-lucene-query-architecture.md#stage-2-lexical-analysis) e procure a secção sobre as exceções à análise lexical.

Para obter mais informações sobre os cenários de consultas que o analisador de consultas completo permite, veja [Lucene query syntax in Azure Search (Sintaxe de consultas do Lucene no Azure Search)](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search).

### <a name="geo-search"></a> Experimentar a pesquisa geoespacial

A pesquisa geoespacial é suportada através do [tipo de dados edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) em campos que contenham coordenadas. A pesquisa geográfica é um tipo de filtros especificado em [Filter OData syntax (Sintaxe de Filtros OData)](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

#### <a name="example-geo-coordinate-filters-searchcounttruefiltergeodistancelocationgeographypoint-122121513-47673988-le-5"></a>Exemplo (filtros de coordenadas geográficas): `search=*&$count=true&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5`

A consulta de exemplo filtra todos os resultados relativamente a dados posicionais, em que os resultados estejam a menos de cinco quilómetros de distância de um determinado ponto (especificado como coordenadas de latitude e longitude). Ao adicionar **$count**, pode ver quantos resultados são devolvidos quando alterar a distância ou as coordenadas.

A pesquisa geoespacial é útil se a sua aplicação de pesquisa tiver uma funcionalidade “encontrar perto de mim” ou se utilizar a navegação por mapa. Contudo, não é uma pesquisa em texto completo. Se os seus utilizadores tiverem de pesquisar por nome de cidade ou país, adicione campos que contenham nomes de cidades ou países, para além de coordenadas.

## <a name="takeaways"></a>Conclusões

Este tutorial fornecida uma breve introdução à Azure Search no portal do Azure.

Aprendeu a criar um índice de pesquisa com o assistente para **Importar dados**. Aprendeu sobre [indexadores](search-indexer-overview.md), bem como o fluxo de trabalho básico para o design de índices, incluindo [modificações suportadas num índice publicado](https://docs.microsoft.com/rest/api/searchservice/update-index).

Ao utilizar o **explorador do Search** no portal do Azure, aprendeu a sintaxe básica das consultas através de exemplos práticos que demonstram as capacidades principais, como filtros, detetor de ocorrências, pesquisa difusa e pesquisa geográfica.

Também aprendeu como localizar índices, indexadores e origens de dados no portal. Utilizando uma origem de dados nova no futuro, pode utilizar o portal para verificar rapidamente as definições ou as coleções de campos da mesma com um esforço mínimo.

## <a name="clean-up"></a>Limpeza

Se este tutorial foi a primeira utilização do serviço Azure Search, elimine o grupo de recursos que contém o serviço Azure Search. Se não, procure o nome do grupo de recursos certo na lista de serviços e elimine o adequado.

## <a name="next-steps"></a>Passos Seguintes

Pode utilizar as ferramentas programáticas para explorar mais o Azure Search:

* [Criar um índice com o .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
* [Criar um índice utilizando REST APIs](https://docs.microsoft.com/azure/search/search-create-index-rest-api)
* [Criar um índice com o Postman ou Fiddler e as APIs de REST de pesquisa do Azure](search-fiddler.md)