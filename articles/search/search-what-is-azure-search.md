---
title: Introdução ao Azure Search-Azure Search
description: Azure Search é um serviço de pesquisa de nuvem hospedado totalmente gerenciado da Microsoft. Examine as descrições de recursos, o fluxo de trabalho de desenvolvimento, comparações a outros produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/13/2019
ms.author: heidist
ms.openlocfilehash: 1f3e4d69d3fdba8eba2e7d3cadc3c29703bffcaf
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558608"
---
# <a name="what-is-azure-search"></a>O que é o Azure Search?

O Azure Search é uma solução de nuvem de pesquisa como serviço que oferece aos desenvolvedores APIs e ferramentas para adicionar uma experiência de pesquisa avançada sobre conteúdo privado e heterogêneo em aplicativos Web, móveis e empresariais. O código personalizado invoca a ingestão de dados (indexação), emite solicitações de consulta e processa respostas. A experiência de pesquisa é definida no código do cliente usando a funcionalidade do Azure Search, com a execução da consulta em um índice persistente que você cria, tem e armazena em Azure Search.

![Arquitetura de Azure Search](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura de Azure Search")

<!-- + Build a search index containing only your data, sourced from multiple content types and platforms. 
+ Leverage AI enrichments to extract text and features from image files, or entities and key phrases from raw text.
+ Create intuitive search experiences with facet navigation and filters, synonyms, autocomplete, and text analysis for "did you mean" autocorrected search terms. Get relevance tuning through functions and boosting logic.
+ Create search apps for specific use-cases. Geo-search supports a "find near me" experience. Multi-lingual search is supported through language analyzers for non-English full text search. -->

A funcionalidade é exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente da obtenção de informações. Para além das APIs, o Portal do Azure fornece suporte à administração e à gestão de conteúdos, com as ferramentas para fazer o protótipo e consultar os índices. Por o serviço executar na cloud, as infraestruturas e disponibilidade são geridas pela Microsoft.

## <a name="when-to-use-azure-search"></a>Quando usar Azure Search

Azure Search é bem adequado para os seguintes cenários de aplicativo:

+ Consolidação de tipos de conteúdo heterogêneo em um índice privado, único e pesquisável. As consultas são sempre sobre um índice que você cria e carrega com documentos e o índice sempre reside na nuvem em seu serviço de Azure Search. Você pode popular um índice com fluxos de documentos JSON de qualquer origem ou plataforma. Como alternativa, para o conteúdo originado no Azure, você pode usar um indexador para efetuar pull de dados em um índice. A definição de índice e gerenciamento/propriedade é um motivo importante para usar Azure Search.

+ Implementação fácil de recursos relacionados à pesquisa. As APIs de Azure Search simplificam a construção da consulta, a navegação facetada, os filtros (incluindo a pesquisa espacial geográfica), o mapeamento de sinônimos, as consultas typeahead e o ajuste de relevância. Usando recursos internos, você pode satisfazer as expectativas do usuário final para uma experiência de pesquisa semelhante aos mecanismos de pesquisa na Web comercial.

+ Indexação de texto não estruturado ou extração de texto e informações de arquivos de imagem. O recurso de pesquisa cognitiva do Azure Search adiciona o processamento de ia a um pipeline de indexação. Alguns casos de uso comuns incluem OCR sobre documentos digitalizados, reconhecimento de entidade e extração de frases-chave em documentos grandes, detecção de idioma e tradução de texto e análise de sentimentos.

+ Requisitos linguísticos satisfeitos usando os analisadores personalizado e de idioma de Azure Search. Se você tiver conteúdo diferente do inglês, o Azure Search dará suporte aos analisadores do Lucene e aos processadores de idioma natural da Microsoft. Você também pode configurar analisadores para obter processamento especializado de conteúdo bruto, como filtragem de sinais diacríticos.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Descrições de recursos

| Pesquisa&nbsp;principal&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funcionalidades |
|-------------------|----------|
|Pesquisa em texto de forma livre | A [**pesquisa de texto completo**](search-lucene-query-architecture.md) é um caso de uso primário para a maioria dos aplicativos baseados em pesquisa. As consultas podem ser formuladas com uma sintaxe suportada. <br/><br/>A [**Sintaxe de consulta simples** ](query-simple-syntax.md) fornece operadores lógicos, operadores de pesquisa de expressão, operadores de sufixo e operadores de precedência.<br/><br/>A [**Sintaxe de consulta Lucene** ](query-lucene-syntax.md) inclui todas as operações numa sintaxe simples, com as extensões para pesquisa difusa, pesquisa de proximidade, aumentos de termos e expressões regulares.|
| Relevância | A [**Classificação simples**](index-add-scoring-profiles.md) é uma vantagem primária do Azure Search. Os perfis de classificação são utilizados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, pode querer que produtos mais recentes ou produtos em desconto apareçam mais em cima nos resultados da pesquisa. Também pode criar perfis de classificação com etiquetas para uma classificação personalizada baseada nas preferências de pesquisa dos clientes que acompanhou e armazenou em separado. |
| Pesquisa geográfica | O Azure Search processa, filtra e exibe localizações geográficas. Permite que os utilizadores explorem dados com base na proximidade de um resultado de pesquisa a uma localização física. [Veja este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [reveja este exemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para saber mais. |
| Filtros e facetas | A [**Navegação por facetas**](search-faceted-navigation.md) é ativada através de um parâmetro de consulta simples. O Azure Search devolve uma estrutura de navegação facetada que pode utilizar como o código por trás de uma lista de categorias, para filtragem auto-direcionada (por exemplo, para filtrar itens de catálogo de acordo com o preço ou marca). <br/><br/> Os [**Filtros**](query-odata-filter-orderby-syntax.md) podem ser utilizados para incorporar a navegação por facetas na IU da sua aplicação, melhorar a formulação de consulta e filtrar com base em critérios especificados pelo utilizador ou programador. Criar filtros com a sintaxe OData. |
| Funcionalidades de experiência do utilizador | O [**preenchimento automático**](search-autocomplete-tutorial.md) pode ser habilitado para consultas de tipo antecipado em uma barra de pesquisa. <br/><br/>As [**Sugestões de pesquisa**](https://docs.microsoft.com/rest/api/searchservice/suggesters) também funcionam em entradas de texto parciais numa barra de pesquisa, mas os resultados são documentos reais no seu índice, em vez de termos de consulta. <br/><br/>Os [**Sinónimos** ](search-synonyms.md) associam termos equivalentes que expandem implicitamente o âmbito de uma consulta, sem que o utilizador tenha de indicar os termos alternativos. <br/><br/>O [**Detetor de ocorrências** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica a formatação de texto a uma palavra-chave correspondente nos resultados de pesquisa. Pode escolher quais os campos que devolvem fragmentos destacados.<br/><br/>A [**ordenação**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é disponibilizado para vários campos através do esquema de índice e, em seguida, ativada/desativada durante a consulta com um único parâmetro de procura.<br/><br/> A [**Paginação**](search-pagination-page-layout.md) e a limitação dos seus resultados de pesquisa é algo simples com o controlo ajustado que o Azure Search lhe dá sobre os seus resultados de pesquisa.  <br/><br/>|

| Enriquecimento&nbsp;de ia&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funcionalidades |
|-------------------|----------|
|Documentos de AI aprimorados | A [**pesquisa cognitiva**](cognitive-search-concept-intro.md) de imagem e análise de texto pode ser aplicada a um pipeline de indexação para extrair informações de texto de conteúdo bruto. Alguns exemplos das [competências incorporadas](cognitive-search-predefined-skills.md) incluem reconhecimento ótico de carateres (ao tornar JPEGs digitalizados pesquisáveis), reconhecimento de entidade (ao identificar uma organização, nome ou localização) e ao reconhecer frases-chave. Também pode [criar código de competências personalizadas](cognitive-search-create-custom-skill-example.md) para associar ao pipeline. |
| Aprimoramentos armazenados para análise e consumo| A [**loja de conhecimento (versão prévia)** ](knowledge-store-concept-intro.md) é uma extensão da indexação baseada em ia. Com o armazenamento do Azure como um back-end, você pode salvar os aprimoramentos criados durante a indexação. Esses artefatos podem ser usados para ajudá-lo a criar melhores habilidades, ou criar forma e estrutura fora de amorfos ou de dados ambíguos. Você pode criar projeções dessas estruturas que se destinam a cargas de trabalho ou usuários específicos. Você também pode analisar diretamente os dados extraídos ou carregá-los em outros aplicativos.<br/><br/> |

| Importação&nbsp;/indexação de dados | Funcionalidades |
|----------------------------------|----------|
| Origens de dados | Os índices do Azure Search aceitam dados de qualquer origem, desde que sejam enviadas como uma estrutura de dados JSON. <br/><br/> [**Indexadores**](search-indexer-overview.md) automatizam a ingestão de dados para fontes de dados do Azure com suporte e manipulam a serialização JSON. Conecte-se ao banco de dados [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)ou [armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md) para extrair o conteúdo pesquisável em armazenamentos primários. Os indexadores de Blobs do Azure podem realizar *aberturas de documentos* para [extrair texto dos principais formatos de ficheiro](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML. |
| Estruturas de dados hierárquicas e aninhadas | [**Tipos**](search-howto-complex-data-types.md) e coleções complexos permitem modelar praticamente qualquer tipo de estrutura JSON como um índice de Azure Search. A cardinalidade de um para muitos e muitos para muitos pode ser expressa nativamente por meio de coleções, tipos complexos e coleções de tipos complexos.|
| Análise linguística | Os analisadores são componentes utilizados para processamento de texto durante as operações de indexação e pesquisa. Existem dois tipos. <br/><br/>Os [**Analisadores lexicais personalizados**](index-add-custom-analyzers.md) são utilizados para consultas de pesquisa complexas que utilizam a correspondência fonética e expressões regulares. <br/><br/>Os [**Analisadores de idioma**](index-add-language-analyzers.md) da Lucene ou da Microsoft são utilizados para processar inteligentemente a linguística específica de um idioma, incluindo formas verbais, género, nomes plurais irregulares (por exemplo "ananás" e "ananases"), decomposição de palavras, quebra de palavras (para idiomas sem espaços), entre outros. <br/><br/>|


| Nível&nbsp;de plataforma&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funcionalidades |
|-------------------|----------|
| Ferramentas para criação de protótipos e inspeção | No portal, pode utilizar o [**Assistente de importação de dados**](search-import-data-portal.md) para configurar os indexados, o estruturador de índice para funcionar como índice e o [**Explorador de pesquisa**](search-explorer.md) para testar as consultas e refine os perfis de classificação. Também pode abrir qualquer índice para ver o seu esquema. |
| Monitorização e diagnóstico | [**Habilite os recursos de monitoramento**](search-monitor-usage.md) para ir além das métricas em resumo que estão sempre visíveis no Portal. As métricas sobre as consultas por segundo, latência e limitação são recolhidas e comunicadas em páginas de portal sem ser necessária configuração adicional. <br/><br/>A [**análise de tráfego de pesquisa**](search-traffic-analytics.md) é outra alternativa de monitoramento, em que os dados do lado do servidor e do cliente são coletados e analisados para revelar informações sobre o que os usuários estão digitando na caixa de pesquisa. |
| Encriptação do lado do servidor | A [**criptografia gerenciada pela Microsoft em repouso**](search-security-overview.md#encrypted-transmission-and-storage) é incorporada à camada de armazenamento interna e é irrevogável. Opcionalmente, você pode complementar a criptografia padrão com [**chaves de criptografia gerenciadas pelo cliente (versão prévia)** ](search-security-manage-encryption-keys.md). As chaves que você cria e gerencia no Azure Key Vault são usadas para criptografar índices e mapas de sinônimos no Azure Search. |
| Infraestrutura | A **plataforma altamente disponível** garante uma experiência de serviço de pesquisa extremamente fiável. Quando dimensionado corretamente, o [Azure Search oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Completamente gerido e dimensionável** como uma solução de ponto-a-ponto, o Azure Search não necessita absolutamente de nenhuma gestão de infraestrutura. O seu serviço pode ser ajustado às suas necessidades ao dimensionar em duas dimensões para processar mais armazenamento de documentos, maiores cargas de consulta ou ambos.<br/><br/>|

## <a name="how-to-use-azure-search"></a>Como utilizar o Azure Search
### <a name="step-1-provision-service"></a>Passo 1: Provisionar serviço
Pode aprovisionar um serviço do Azure Search no [portal do Azure](https://portal.azure.com/) ou através da [API de Gestão de Recursos do Azure](/rest/api/searchmanagement/). Pode escolher ou o serviço gratuito partilhado com outros subscritores, ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados apenas pelo seu serviço. Para os escalões pagos, pode dimensionar um serviço em duas dimensões: 

- Adicionar Réplicas para fazer crescer a sua capacidade de processar cargas de consultas pesadas.   
- Adicione Partições para aumentar o armazenamento para mais documentos. 

Ao processar o armazenamento de documentos e débito de consultas em separado, pode calibrar os recursos com base nos requisitos de produção.

### <a name="step-2-create-index"></a>Passo 2: Criar índice
Antes de poder carregar conteúdo pesquisável, deve primeiro definir um índice do Azure Search. Um índice é como uma tabela de base de dados que contém os seus dados e pode aceitar consultas de pesquisa. Pode definir o esquema do índice para mapear e refletir a estrutura dos documentos que quer pesquisar, parecido aos campos numa base de dados.

Um esquema pode ser criado no Portal do Azure, ou de forma programática com o [.NET SDK](search-howto-dotnet-sdk.md) ou [API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Passo 3: Carregar dados
Depois de definir um índice, está pronto para carregar conteúdo. Pode utilizar um modelo push ou pull.

O modelo pull obtém dados a partir de origens de dados externas. É suportado através de *indexadores* que simplificam e automatizam aspetos da ingestão de dados, como a ligação, leitura e serialização de dados. Os [Indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para o Azure Cosmos DB, Base de Dados SQL do Azure, Armazenamento de Blobs do Azure e SQL Server alojado numa VM do Azure. Pode configurar um indexador para uma atualização de dados sob pedido ou agendada.

O modelo de push é fornecido através da SDK ou de APIs REST, utilizado para enviar documentos atualizados para um índice. Pode enviar dados a partir de praticamente qualquer conjunto de dados ao utilizar o formato JSON. Veja [Adicionar, atualizar ou eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [Como utilizar o SDK .NET)](search-howto-dotnet-sdk.md) para obter diretrizes sobre carregar dados.

### <a name="step-4-search"></a>Passo 4: Pesquisa
Após preencher um índice, pode [emitir consultas de pesquisa](/rest/api/searchservice/Search-Documents) ao seu ponto final de serviço com simples pedidos HTTP com a API REST ou a SDK .NET.

## <a name="how-it-compares"></a>Como se compara

Os clientes costumam perguntar como é que se compara o Azure Search a outras soluções relacionadas com pesquisa. A tabela seguinte resume as diferenças principais.

| Em comparação com | Diferenças principais |
|-------------|-----------------|
|Bing | A [API de Pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) procura os índices em Bing.com para os termos correspondentes ao que enviar. Os índices são criados a partir de HTML, XML e outros conteúdos web em sites públicos. Criada com base nos mesmos fundamentos, a [Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia crawler para tipos de conteúdo Web, no âmbito de sites individuais.<br/><br/>O Azure Search pesquisa por um índice que definir, preenchido com dados e documentos seus, normalmente de origens diversas. O Azure Search tem funcionalidades de crawler para algumas origens de dados através de [indexadores](search-indexer-overview.md), mas pode enviar qualquer documento JSON que esteja em conformidade com o seu esquema de índice num único recurso pesquisável consolidado. |
|Pesquisa de base de dados | Muitas plataformas da base de dados incluem uma experiência de pesquisa incorporada. O SQL Server tem [pesquisa em texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). O Cosmos DB e as tecnologias semelhantes têm índices consultáveis. Quando avaliar os produtos que combinam a pesquisa e o armazenamento, pode ser difícil determinar qual escolher. Muitas soluções usam ambos: DBMS para armazenamento e Azure Search para recursos de pesquisa especializados.<br/><br/>Em comparação com a pesquisa do DBMS, o Azure Search armazena conteúdo de fontes heterogêneas e oferece recursos de processamento de texto especializados, como processamento de texto com reconhecimento lingüístico (lematização, lematização, formas de palavras) em [56 idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support). Também suporta a correção automática de palavras com erros ortográficos, [sinónimos](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions), [controlos de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](https://docs.microsoft.com/azure/search/search-filters-facets)e [atomização personalizada](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) no Azure Search está incorporado no Apache Lucene, uma norma da indústria para a obtenção de informações. Apesar do Azure Search manter os dados sob a forma de um índice invertido, raramente é uma substituição de armazenamento de dados verdadeiro. Para obter mais informações, veja esta [mensagem no fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>A utilização de recursos é outro ponto de flexão nesta categoria. A indexação e algumas operações de consulta, muitas vezes, são intensivas em termos de computação. O descarregamento da pesquisa do DBMS para uma solução dedicada na cloud preserva os recursos do sistema para o processamento de transações. Além disso, ao externalizar a pesquisa, pode facilmente ajustar o dimensionamento para corresponder ao volume de consultas.|
|Solução de pesquisa dedicada | Partindo do princípio de que escolheu a pesquisa dedicada com funcionalidade de espetro completo, a comparação categórica final será entre as soluções no local ou um serviço cloud. Muitas tecnologias de pesquisa oferecem controlo sobre os pipelines de indexação e consulta, acesso a uma sintaxe de consulta e filtragem mais rica, controlo sobre a classificação e relevância e funcionalidades para uma pesquisa inteligente e auto-direcionada. <br/><br/>Um serviço de nuvem é a escolha certa se você quiser uma solução de ponta com sobrecarga e manutenção mínimas e escala ajustável. <br/><br/>Dentro do paradigma da cloud, vários fornecedores oferecem funcionalidades de linha de base comparáveis, com pesquisa de texto inteiro, pesquisa geográfica e a capacidade de processar um determinado nível de ambiguidade nas entradas de pesquisa. Normalmente, é uma [funcionalidade especializada](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, das ferramentas e a gestão que determinam qual a melhor opção. |

Entre os fornecedores de cloud, o Azure Search é o mais forte para cargas de trabalho de pesquisa de texto inteiro em arquivos de conteúdos e bases de dados no Azure, para aplicações que dependam sobretudo na pesquisa para recuperação de informação e navegação em conteúdo. 

As principais vantagens incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Portal do Azure para gestão central
+ Dimensionamento, fiabilidade e disponibilidade de classe mundial do Azure
+ O processamento de ia de dados brutos para torná-lo mais pesquisável, incluindo texto de imagens ou localização de padrões em conteúdo não estruturado.
+ A análise linguística e personalizada, com analisadores de pesquisa de texto completo sólidos em 56 idiomas
+ [Principais funcionalidades das aplicações focadas na pesquisa](#feature-drilldown): classificação, facetamento, sugestões, sinónimos, pesquisa geográfica e muito mais.

> [!Note]
> As origens de dados não pertencentes ao Azure são completamente suportadas, mas dependem de uma metodologia push com código intensivo em vez de indexadores. Com APIs, pode encaminhar qualquer conjunto de documentos JSON para um índice do Azure Search.

Entre os nossos clientes, os que são capazes de tirar partido do maior leque de funcionalidades no Azure Search incluem catálogos online, programas de linha de negócios e aplicações de deteção de documentos.

## <a name="rest-api--net-sdk"></a>API REST | SDK DO .NET

Enquanto muitas tarefas podem ser executadas no portal, o Azure Search é suposto para os programadores que querem integrar a funcionalidade de pesquisa em aplicações já existentes. Estão disponíveis as seguintes interfaces de programação.

|Plataforma |Descrição |
|-----|------------|
|[REST](/rest/api/searchservice/) | Os comandos HTTP suportados por qualquer plataforma ou linguagem de programação, incluindo Xamarin, Java e JavaScript|
|[SDK do .NET](search-howto-dotnet-sdk.md) | O wrapper .NET para a API REST oferece uma programação eficiente em C# e noutras linguagens de código gerido direcionadas para o .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os subscritores do Azure podem [aprovisionar um serviço no Escalão gratuito](search-create-service-portal.md).

Se não for um subscritor, pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Recebe créditos por experimentar os serviços pagos do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Como alternativa, você pode [ativar os benefícios](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)do assinante do MSDN: A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Crie um [serviço gratuito](search-create-service-portal.md). Todos os inícios rápidos e tutoriais podem ser concluídos no serviço gratuito.

2. Siga os passos no [tutorial sobre a utilização interna de ferramentas de indexação e consulta](search-get-started-portal.md). Aprenda conceitos importantes e ganhe familiaridade com as informações dadas pelo portal.

3. Avance com código com o .NET ou a API REST:

   + [Como utilizar o SDK .NET](search-howto-dotnet-sdk.md) demonstra o fluxo de trabalho principal no código gerido.  
   + [Introdução à REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra os mesmos passos com a API REST. Você também pode usar este guia de início rápido para chamar as APIs REST do postmaster ou Fiddler: [Explore Azure Search APIs REST](search-get-started-postman.md).

## <a name="watch-this-video"></a>Assistir a este vídeo

Os motores de busca são os controladores comuns de obtenção de informações em aplicações móveis, na web e nos arquivos de dados empresariais. O Azure Search dá-lhe as ferramentas para criar uma experiência de pesquisa parecida às dos grandes sites web comerciais.

Neste vídeo de 9 minutos do gestor de programa Liam Cavanagh, aprenda como integrar um motor de pesquisa que pode beneficiar a sua aplicação. As curtas demonstrações mostram as funcionalidades-chave no Azure Search e como funciona um fluxo de trabalho normal. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Os 0-3 minutos abrangem as funcionalidades principais e casos de utilização.
+ Os 3-4 minutos abrangem o aprovisionamento do serviço. 
+ Os 4-6 minutos abrangem o assistente de Importar Dados utilizado para criar um índice a utilizar um conjunto de dados de imobiliário incorporado.
+ Os 6-9 minutos abrangem o Explorador de Pesquisa e várias consultas.
