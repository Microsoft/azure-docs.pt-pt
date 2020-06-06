---
title: Introdução à Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: A azure Cognitive Search é um serviço de pesquisa em nuvem totalmente gerido da Microsoft. Leia descrições de funcionalidades, fluxo de trabalho de desenvolvimento, comparações com outros produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: 87295278f39f7e7097b1f4d1ce1c729a71599c9f
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466226"
---
# <a name="what-is-azure-cognitive-search"></a>O que é a Pesquisa Cognitiva do Azure?

A Azure Cognitive Search ([anteriormente conhecida como "Azure Search"](whats-new.md#new-service-name)é uma solução de nuvem de pesquisa como um serviço que dá aos desenvolvedores APIs e ferramentas para adicionar uma rica experiência de pesquisa sobre conteúdo privado, heterogéneo em aplicações web, móveis e empresariais. O seu código ou ferramenta invoca a ingestão de dados (indexação) para criar e carregar um índice. Opcionalmente, pode adicionar habilidades cognitivas para aplicar processos de IA durante a indexação. Ao fazê-lo, pode adicionar novas informações e estruturas úteis para pesquisa e outros cenários.

Do outro lado do seu serviço, o seu código de aplicação emite pedidos de consulta e trata de respostas. A experiência de pesquisa é definida no seu cliente utilizando a funcionalidade a partir da Azure Cognitive Search, com execução de consulta sobre um índice persistido que cria, possui e armazena no seu serviço.

![Arquitetura de Pesquisa Cognitiva Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura de Pesquisa Cognitiva Azure")

A funcionalidade é exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente da obtenção de informações. Para além das APIs, o Portal do Azure fornece suporte à administração e à gestão de conteúdos, com as ferramentas para fazer o protótipo e consultar os índices. Por o serviço executar na cloud, as infraestruturas e disponibilidade são geridas pela Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Quando usar a Pesquisa Cognitiva Azure

A Azure Cognitive Search é adequada para os seguintes cenários de aplicação:

+ Consolidação de tipos de conteúdo heterogéneo num índice privado, único e pes pespável. As consultas são sempre sobre um índice que cria e carrega com documentos, e o índice reside sempre na nuvem do seu serviço de Pesquisa Cognitiva Azure. Pode preencher um índice com fluxos de documentos JSON de qualquer fonte ou plataforma. Em alternativa, para o conteúdo produzido no Azure, pode utilizar um *indexante* para puxar dados para um índice. A definição de índice e gestão/propriedade é uma razão fundamental para a utilização da Pesquisa Cognitiva Azure.

+ O conteúdo bruto é um grande texto indiferenciado, ficheiros de imagem ou ficheiros de aplicações, tais como tipos de conteúdo do Office numa fonte de dados Azure, como o armazenamento de Azure Blob ou o Cosmos DB. Pode aplicar habilidades cognitivas durante a indexação para adicionar significado de estrutura ou extrato a partir de ficheiros de imagem e aplicação.

+ Fácil implementação de funcionalidades relacionadas com a pesquisa. Azure Cognitive Search APIs simplifica a construção de consultas, navegação frontal, filtros (incluindo pesquisa geo-espacial), mapeamento de sinónimo, consultas de tipo de cabeça e afinação de relevância. Utilizando funcionalidades incorporadas, pode satisfazer as expectativas dos utilizadores finais para uma experiência de pesquisa semelhante a motores de pesquisa web comerciais.

+ Indexar texto não estruturado ou extrair texto e informação de ficheiros de imagem. A funcionalidade de [enriquecimento](cognitive-search-concept-intro.md) de IA da Azure Cognitive Search adiciona o processamento de IA a um pipeline de indexação. Alguns casos de uso comuns incluem OCR sobre documento digitalizado, reconhecimento de entidades e extração de frases-chave sobre grandes documentos, deteção de linguagem e tradução de texto, e análise de sentimento.

+ Requisitos linguísticos satisfeitos usando os analisadores personalizados e linguísticos da Azure Cognitive Search. Se tiver conteúdo não inglês, o Azure Cognitive Search suporta tanto os analisadores de Lucene como os processadores de linguagem natural da Microsoft. Também pode configurar analisadores para obter um processamento especializado de conteúdo bruto, como filtrar diacríticos.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Descrições das funcionalidades

| Pesquisa &nbsp; do núcleo&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funcionalidades |
|-------------------|----------|
|Pesquisa em texto de forma livre | [**A pesquisa por texto completo**](search-lucene-query-architecture.md) é um caso de utilização primária para a maioria das aplicações baseadas em pesquisa. As consultas podem ser formuladas com uma sintaxe suportada. <br/><br/>A [**Sintaxe de consulta simples**](query-simple-syntax.md) fornece operadores lógicos, operadores de pesquisa de expressão, operadores de sufixo e operadores de precedência.<br/><br/>A [**Sintaxe de consulta Lucene**](query-lucene-syntax.md) inclui todas as operações numa sintaxe simples, com as extensões para pesquisa difusa, pesquisa de proximidade, aumentos de termos e expressões regulares.|
| Relevância | [**A pontuação simples**](index-add-scoring-profiles.md) é um benefício fundamental da Pesquisa Cognitiva Azure. Os perfis de classificação são utilizados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, pode querer que produtos mais recentes ou produtos em desconto apareçam mais em cima nos resultados da pesquisa. Também pode criar perfis de classificação com etiquetas para uma classificação personalizada baseada nas preferências de pesquisa dos clientes que acompanhou e armazenou em separado. |
| Pesquisa geográfica | Azure Cognitive Search processa, filtra e exibe localizações geográficas. Permite que os utilizadores explorem dados com base na proximidade de um resultado de pesquisa a uma localização física. [Veja este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [reveja este exemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para saber mais. |
| Filtros e facetas | A [**Navegação por facetas**](search-faceted-navigation.md) é ativada através de um parâmetro de consulta simples. A Azure Cognitive Search devolve uma estrutura de navegação frontal que pode usar como código por trás de uma lista de categorias, para filtragem auto-dirigida (por exemplo, para filtrar itens de catálogo por gama de preços ou marca). <br/><br/> Os [**Filtros**](query-odata-filter-orderby-syntax.md) podem ser utilizados para incorporar a navegação por facetas na IU da sua aplicação, melhorar a formulação de consulta e filtrar com base em critérios especificados pelo utilizador ou programador. Criar filtros com a sintaxe OData. |
| Funcionalidades de experiência do utilizador | [**O preconto automático**](search-autocomplete-tutorial.md) pode ser ativado para consultas de tipo-a-frente numa barra de pesquisa. <br/><br/>As [**Sugestões de pesquisa**](https://docs.microsoft.com/rest/api/searchservice/suggesters) também funcionam em entradas de texto parciais numa barra de pesquisa, mas os resultados são documentos reais no seu índice, em vez de termos de consulta. <br/><br/>Os [**Sinónimos**](search-synonyms.md) associam termos equivalentes que expandem implicitamente o âmbito de uma consulta, sem que o utilizador tenha de indicar os termos alternativos. <br/><br/>O [**Detetor de ocorrências**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica a formatação de texto a uma palavra-chave correspondente nos resultados de pesquisa. Pode escolher quais os campos que devolvem fragmentos destacados.<br/><br/>A [**ordenação**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é disponibilizado para vários campos através do esquema de índice e, em seguida, ativada/desativada durante a consulta com um único parâmetro de procura.<br/><br/> [**A palpagem**](search-pagination-page-layout.md) e a aceleração dos resultados da sua pesquisa são simples com o controlo afinado que a Azure Cognitive Search oferece sobre os resultados da sua pesquisa.  <br/><br/>|

| &nbsp;Enriquecimento de IA&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funcionalidades |
|-------------------|----------|
|Processamento de IA durante a indexação | [**O enriquecimento de IA**](cognitive-search-concept-intro.md) para análise de imagem e texto pode ser aplicado a um gasoduto de indexação para extrair informações de texto de conteúdo bruto. Alguns exemplos das [competências incorporadas](cognitive-search-predefined-skills.md) incluem reconhecimento ótico de carateres (ao tornar JPEGs digitalizados pesquisáveis), reconhecimento de entidade (ao identificar uma organização, nome ou localização) e ao reconhecer frases-chave. Também pode [criar código de competências personalizadas](cognitive-search-create-custom-skill-example.md) para associar ao pipeline. |
| Armazenar conteúdo enriquecido para análise e consumo em cenários de não pesquisa | [**A loja de conhecimento (pré-visualização)**](knowledge-store-concept-intro.md) é uma extensão da indexação baseada em IA. Com o armazenamento Azure como backend, pode guardar enriquecimentos criados durante a indexação. Estes artefactos podem ser usados para ajudá-lo a projetar melhores habilidades, ou criar forma e estrutura a partir de dados amorfos ou ambíguos. Pode criar projeções destas estruturas que visam cargas de trabalho específicas ou utilizadores. Também pode analisar diretamente os dados extraídos ou carregá-los noutras aplicações.<br/><br/> |
| Conteúdo em cache | [**O enriquecimento incremental (pré-visualização)**](cognitive-search-incremental-indexing-conceptual.md) limita o processamento apenas aos documentos que são alterados por edição específica para o pipeline, utilizando conteúdo em cache para as partes do pipeline que não mudam. |

| &nbsp;Importação/indexação de dados | Funcionalidades |
|----------------------------------|----------|
| Origens de dados | Os índices de Pesquisa Cognitiva Azure aceitam dados de qualquer fonte, desde que sejam submetidos como uma estrutura de dados JSON. <br/><br/> [**Os indexantes**](search-indexer-overview.md) automatizam a ingestão de dados para fontes de dados suportadas do Azure e lidam com a serialização do JSON. Ligue-se à [Base de Dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)ou [armazenamento Azure Blob](search-howto-indexing-azure-blob-storage.md) para extrair conteúdo pesquisável em lojas de dados primárias. Os indexadores de Blobs do Azure podem realizar *aberturas de documentos* para [extrair texto dos principais formatos de ficheiro](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML. |
| Estruturas hierárquicas e aninhadas de dados | Tipos e coleções [**complexos**](search-howto-complex-data-types.md) permitem modelar praticamente qualquer tipo de estrutura JSON como um índice de Pesquisa Cognitiva Azure. Um a muitos e muitos cardeais podem ser expressos de forma nativa através de coleções, tipos complexos e coleções de tipos complexos.|
| Análise linguística | Os analisadores são componentes utilizados para processamento de texto durante as operações de indexação e pesquisa. Existem dois tipos. <br/><br/>Os [**Analisadores lexicais personalizados**](index-add-custom-analyzers.md) são utilizados para consultas de pesquisa complexas que utilizam a correspondência fonética e expressões regulares. <br/><br/>Os [**Analisadores de idioma**](index-add-language-analyzers.md) da Lucene ou da Microsoft são utilizados para processar inteligentemente a linguística específica de um idioma, incluindo formas verbais, género, nomes plurais irregulares (por exemplo "ananás" e "ananases"), decomposição de palavras, quebra de palavras (para idiomas sem espaços), entre outros. <br/><br/>|


| Nível de plataforma &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funcionalidades |
|-------------------|----------|
| Ferramentas para criação de protótipos e inspeção | No portal, pode utilizar o [**Assistente de importação de dados**](search-import-data-portal.md) para configurar os indexados, o estruturador de índice para funcionar como índice e o [**Explorador de pesquisa**](search-explorer.md) para testar as consultas e refine os perfis de classificação. Também pode abrir qualquer índice para ver o seu esquema. |
| Monitorização e diagnóstico | [**Permitir que as funcionalidades de monitorização**](search-monitor-usage.md) ultrapassem as métricas que são sempre visíveis no portal. As métricas sobre as consultas por segundo, latência e limitação são recolhidas e comunicadas em páginas de portal sem ser necessária configuração adicional.|
| Encriptação do lado do servidor | [**A encriptação gerida pela Microsoft em repouso**](search-security-overview.md#encrypted-transmissions-and-storage) é incorporada na camada interna de armazenamento e é irrevogável. Opcionalmente, pode complementar a encriptação padrão com [**as chaves de encriptação geridas pelo cliente**](search-security-manage-encryption-keys.md). As teclas que cria e gere no Azure Key Vault são usadas para encriptar índices e mapas de sinónimos em Azure Cognitive Search. |
| Infraestrutura | A **plataforma altamente disponível** garante uma experiência de serviço de pesquisa extremamente fiável. Quando dimensionado corretamente, [a Azure Cognitive Search oferece um SLA de 99,9%.](https://azure.microsoft.com/support/legal/sla/search/v1_0/)<br/><br/> **Totalmente gerido e escalável** como solução de ponta a ponta, a Azure Cognitive Search não requer absolutamente nenhuma gestão de infraestrutura. O seu serviço pode ser ajustado às suas necessidades ao dimensionar em duas dimensões para processar mais armazenamento de documentos, maiores cargas de consulta ou ambos.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Como usar a Pesquisa Cognitiva Azure
### <a name="step-1-provision-service"></a>Passo 1: Aprovisionar o serviço
Pode providenciar um serviço de Pesquisa Cognitiva Azure no [portal Azure](https://portal.azure.com/) ou através da API de [Gestão de Recursos Azure.](/rest/api/searchmanagement/) Pode escolher ou o serviço gratuito partilhado com outros subscritores, ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados apenas pelo seu serviço. Para os escalões pagos, pode dimensionar um serviço em duas dimensões: 

- Adicionar Réplicas para fazer crescer a sua capacidade de processar cargas de consultas pesadas.   
- Adicione Partições para aumentar o armazenamento para mais documentos. 

Ao processar o armazenamento de documentos e débito de consultas em separado, pode calibrar os recursos com base nos requisitos de produção.

### <a name="step-2-create-index"></a>Passo 2: Criar o índice
Antes de poder carregar conteúdo pesmável, tem primeiro de definir um índice de Pesquisa Cognitiva Azure. Um índice é como uma tabela de base de dados que contém os seus dados e pode aceitar consultas de pesquisa. Pode definir o esquema do índice para mapear e refletir a estrutura dos documentos que quer pesquisar, parecido aos campos numa base de dados.

Um esquema pode ser criado no Portal do Azure, ou de forma programática com o [.NET SDK](search-howto-dotnet-sdk.md) ou [API REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Passo 3: Carregar dados
Depois de definir um índice, está pronto para carregar conteúdo. Pode utilizar um modelo push ou pull.

O modelo pull obtém dados a partir de origens de dados externas. É suportado através de *indexadores* que simplificam e automatizam aspetos da ingestão de dados, como a ligação, leitura e serialização de dados. Os [Indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para o Azure Cosmos DB, Base de Dados SQL do Azure, Armazenamento de Blobs do Azure e SQL Server alojado numa VM do Azure. Pode configurar um indexador para uma atualização de dados sob pedido ou agendada.

O modelo de push é fornecido através da SDK ou de APIs REST, utilizado para enviar documentos atualizados para um índice. Pode enviar dados a partir de praticamente qualquer conjunto de dados ao utilizar o formato JSON. Veja [Adicionar, atualizar ou eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [Como utilizar o SDK .NET)](search-howto-dotnet-sdk.md) para obter diretrizes sobre carregar dados.

### <a name="step-4-search"></a>Passo 4: Pesquisa
Depois de povoar um índice, pode [emitir consultas de pesquisa](search-query-overview.md) no seu ponto final de serviço utilizando pedidos HTTP simples com [API REST](/rest/api/searchservice/Search-Documents) ou o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Passe por [Criar a sua primeira aplicação de pesquisa](tutorial-csharp-create-first-app.md) para construir e, em seguida, estender uma página web que recolhe a entrada do utilizador e lida com os resultados. Também pode utilizar o Carteiro para chamadas [interativas REST](search-get-started-postman.md) ou o Explorador de [Pesquisa](search-explorer.md) incorporado no portal Azure para consultar um índice existente.

## <a name="how-it-compares"></a>Como se compara

Os clientes perguntam frequentemente como a Azure Cognitive Search se compara com outras soluções relacionadas com a pesquisa. A tabela seguinte resume as diferenças principais.

| Em comparação com | Diferenças principais |
|-------------|-----------------|
|Bing | A [API de Pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) procura os índices em Bing.com para os termos correspondentes ao que enviar. Os índices são criados a partir de HTML, XML e outros conteúdos web em sites públicos. Criada com base nos mesmos fundamentos, a [Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia crawler para tipos de conteúdo Web, no âmbito de sites individuais.<br/><br/>A Azure Cognitive Search procura um índice que define, preenchido com dados e documentos que possui, muitas vezes de diversas fontes. O Azure Cognitive Search tem capacidades de rastreio para algumas fontes de dados através de [indexadores](search-indexer-overview.md), mas pode empurrar qualquer documento JSON que esteja em conformidade com o seu esquema de índice num único recurso pesquisável consolidado. |
|Pesquisa de base de dados | Muitas plataformas da base de dados incluem uma experiência de pesquisa incorporada. O SQL Server tem [pesquisa em texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). O Cosmos DB e as tecnologias semelhantes têm índices consultáveis. Quando avaliar os produtos que combinam a pesquisa e o armazenamento, pode ser difícil determinar qual escolher. Muitas soluções utilizam ambas: DBMS para armazenamento, e Azure Cognitive Search para funcionalidades de pesquisa especializadas.<br/><br/>Em comparação com a pesquisa DBMS, a Azure Cognitive Search armazena conteúdos de fontes heterogéneas e oferece funcionalidades especializadas de processamento de texto, tais como processamento de textos conscientes da linguística (stemming, lematização, formas de [palavras) em 56 idiomas.](https://docs.microsoft.com/rest/api/searchservice/language-support) Também suporta a correção automática de palavras com erros ortográficos, [sinónimos](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions), [controlos de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](https://docs.microsoft.com/azure/search/search-filters-facets)e [atomização personalizada](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) em Azure Cognitive Search é construído em Apache Lucene, um padrão da indústria na recuperação de informação. Embora a Azure Cognitive Search persista dados sob a forma de um índice invertido, raramente é um substituto para o verdadeiro armazenamento de dados. Para obter mais informações, veja esta [mensagem no fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>A utilização de recursos é outro ponto de flexão nesta categoria. A indexação e algumas operações de consulta, muitas vezes, são intensivas em termos de computação. O descarregamento da pesquisa do DBMS para uma solução dedicada na cloud preserva os recursos do sistema para o processamento de transações. Além disso, ao externalizar a pesquisa, pode facilmente ajustar o dimensionamento para corresponder ao volume de consultas.|
|Solução de pesquisa dedicada | Partindo do princípio de que escolheu a pesquisa dedicada com funcionalidade de espetro completo, a comparação categórica final será entre as soluções no local ou um serviço cloud. Muitas tecnologias de pesquisa oferecem controlo sobre os pipelines de indexação e consulta, acesso a uma sintaxe de consulta e filtragem mais rica, controlo sobre a classificação e relevância e funcionalidades para uma pesquisa inteligente e auto-direcionada. <br/><br/>Um serviço cloud é a escolha certa se quiser uma solução imediata com o mínimo de custos gerais e de manutenção e com um dimensionamento ajustável. <br/><br/>Dentro do paradigma da cloud, vários fornecedores oferecem funcionalidades de linha de base comparáveis, com pesquisa de texto inteiro, pesquisa geográfica e a capacidade de processar um determinado nível de ambiguidade nas entradas de pesquisa. Normalmente, é uma [funcionalidade especializada](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, das ferramentas e a gestão que determinam qual a melhor opção. |

Entre os fornecedores de nuvem, o Azure Cognitive Search é o mais forte para a pesquisa completa de textos sobre lojas de conteúdos e bases de dados no Azure, para apps que dependem principalmente da pesquisa tanto para a recuperação de informações como para a navegação de conteúdos. 

As principais vantagens incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Portal do Azure para gestão central
+ Dimensionamento, fiabilidade e disponibilidade de classe mundial do Azure
+ Processamento de IA de dados brutos para torná-lo mais pes pes pes pes pes pes pestado, incluindo texto a partir de imagens, ou encontrar padrões em conteúdo não estruturado.
+ A análise linguística e personalizada, com analisadores de pesquisa de texto completo sólidos em 56 idiomas
+ [Principais funcionalidades das aplicações focadas na pesquisa](#feature-drilldown): classificação, facetamento, sugestões, sinónimos, pesquisa geográfica e muito mais.

> [!Note]
> As origens de dados não pertencentes ao Azure são completamente suportadas, mas dependem de uma metodologia push com código intensivo em vez de indexadores. Utilizando APIs, pode canalizar qualquer coleção de documentos JSON para um índice de Pesquisa Cognitiva Azure.

Entre os nossos clientes, aqueles que são capazes de aproveitar a mais ampla gama de funcionalidades na Azure Cognitive Search incluem catálogos online, programas de linha de negócios e aplicações de descoberta de documentos.

## <a name="rest-api--net-sdk"></a>REST API [.NET SDK

Apesar de muitas tarefas poderem ser realizadas no portal, a Azure Cognitive Search destina-se a programadores que pretendam integrar a funcionalidade de pesquisa nas aplicações existentes. Estão disponíveis as seguintes interfaces de programação.

|Plataforma |Descrição |
|-----|------------|
|[REST](/rest/api/searchservice/) | Os comandos HTTP suportados por qualquer plataforma ou linguagem de programação, incluindo Xamarin, Java e JavaScript|
|[SDK .NET](search-howto-dotnet-sdk.md) | O wrapper .NET para a API REST oferece uma programação eficiente em C# e noutras linguagens de código gerido direcionadas para o .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os subscritores do Azure podem [aprovisionar um serviço no Escalão gratuito](search-create-service-portal.md).

Se não for um subscritor, pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Recebe créditos por experimentar os serviços pagos do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Como alternativa, pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): a sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Crie um [serviço gratuito](search-create-service-portal.md). Todos os inícios rápidos e tutoriais podem ser concluídos no serviço gratuito.

2. Siga os passos no [tutorial sobre a utilização interna de ferramentas de indexação e consulta](search-get-started-portal.md). Aprenda conceitos importantes e ganhe familiaridade com as informações dadas pelo portal.

3. Avance com código com o .NET ou a API REST:

   + [Como utilizar o SDK .NET](search-howto-dotnet-sdk.md) demonstra o fluxo de trabalho principal no código gerido.  
   + [Introdução à REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra os mesmos passos com a API REST. Também pode utilizar este quickstart para ligar para REST APIs do Carteiro ou Violinista: [Explore Azure Cognitive Search REST APIs](search-get-started-postman.md).

## <a name="watch-this-video"></a>Assistir a este vídeo

Os motores de busca são os controladores comuns de obtenção de informações em aplicações móveis, na web e nos arquivos de dados empresariais. A Azure Cognitive Search dá-lhe ferramentas para criar uma experiência de pesquisa semelhante à dos grandes sites comerciais.

Neste vídeo de 15 minutos, o gestor de programas Luis Cabrera apresenta a Azure Cognitive Search. 

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]