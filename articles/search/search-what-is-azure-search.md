---
title: O que é o Azure Search | Microsoft Docs
description: O Azure Search é um serviço de pesquisa na cloud alojada completamente gerido. Saiba mais nesta descrição geral da funcionalidade.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 06/28/2018
ms.author: heidist
ms.openlocfilehash: 908d018c6fd3c8fc996e74ef9085416b1b8244ea
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112563"
---
# <a name="what-is-azure-search"></a>O que é o Azure Search?
O Azure Search é uma solução de pesquisa como serviço na cloud que oferece aos programadores APIs e ferramentas para apresentarem uma experiência de pesquisa avançada para os seus conteúdos privados e heterogéneos nas aplicações Web, móveis e empresariais.

A funcionalidade é exposta através de uma simples [REST API](/rest/api/searchservice/) ou [.NET SDK](search-howto-dotnet-sdk.md) que dissimula a complexidade inerente da obtenção de informações. Para além das APIs, o Portal do Azure fornece suporte à administração e à gestão de conteúdos, com as ferramentas para fazer o protótipo e consultar os índices. Por o serviço executar na cloud, as infraestruturas e disponibilidade são geridas pela Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Resumo de funcionalidade

| Categoria | Funcionalidades |
|----------|----------|
|Pesquisa em texto completo e análise de texto | A [Pesquisa em texto completo](search-lucene-query-architecture.md) é um caso de utilização primária para a maior parte das aplicações baseadas na pesquisa. As consultas podem ser formuladas com uma sintaxe suportada. <br/><br/>A [**Sintaxe de consulta simples** ](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) fornece operadores lógicos, operadores de pesquisa de expressão, operadores de sufixo e operadores de precedência.<br/><br/>A [**Sintaxe de consulta Lucene** ](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) inclui todas as operações numa sintaxe simples, com as extensões para pesquisa difusa, pesquisa de proximidade, aumentos de termos e expressões regulares.|
|Pesquisa cognitiva (pré-visualização) | Os [algoritmos com IA](cognitive-search-concept-intro.md) para análise de texto e imagem podem ser aplicados a um pipeline de indexação para extrair informação de texto de conteúdo não processado. Alguns exemplos das [competências incorporadas](cognitive-search-predefined-skills.md) incluem reconhecimento ótico de carateres (ao tornar JPEGs digitalizados pesquisáveis), reconhecimento de entidade (ao identificar uma organização, nome ou localização) e ao reconhecer frases-chave. Também pode [criar código de competências personalizadas](cognitive-search-create-custom-skill-example.md) para associar ao pipeline. |
| Integração de dados | Os índices do Azure Search aceitam dados de qualquer origem, desde que sejam enviadas como uma estrutura de dados JSON. <br/><br/> Opcionalmente, para as origens de dados suportadas no Azure, pode utilizar [**indexadores**](search-indexer-overview.md) para pesquisar automaticamente na [Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) ou [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md) para sincronizar os conteúdos do seu índice de pesquisa com o arquivo de dados principal. Os indexadores de Blobs do Azure podem efetuar *aberturas de documentos* para [indexação nos principais formatos de ficheiro](search-howto-indexing-azure-blob-storage.md), incluindo documentos do Microsoft Office, PDF e HTML. |
| Análise linguística | Os analisadores são componentes utilizados para processamento de texto durante as operações de indexação e pesquisa. Existem dois tipos. <br/><br/>Os [**Analisadores lexicais personalizados**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) são utilizados para consultas de pesquisa complexas que utilizam a correspondência fonética e expressões regulares. <br/><br/>Os [**Analisadores de idioma**](https://docs.microsoft.com/rest/api/searchservice/language-support) da Lucene ou da Microsoft são utilizados para processar inteligentemente a linguística específica de um idioma, incluindo formas verbais, género, nomes plurais irregulares (por exemplo "ananás" e "ananases"), decomposição de palavras, quebra de palavras (para idiomas sem espaços), entre outros. |
| Pesquisa geográfica | O Azure Search processa, filtra e exibe localizações geográficas. Permite que os utilizadores explorem dados com base na proximidade de um resultado de pesquisa a uma localização física. [Veja este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) ou [reveja este exemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para saber mais. |
| Funcionalidades de experiência do utilizador | A [**Conclusão automática (pré-visualização)**](search-autocomplete-tutorial.md) pode ser ativada para consultas com antecipação numa barra de pesquisa. <br/><br/>As [**Sugestões de pesquisa**](https://docs.microsoft.com/rest/api/searchservice/suggesters) também funcionam em entradas de texto parciais numa barra de pesquisa, mas os resultados são documentos reais no seu índice, em vez de termos de consulta. <br/><br/>Os [**Sinónimos** ](search-synonyms.md) associam termos equivalentes que expandem implicitamente o âmbito de uma consulta, sem que o utilizador tenha de indicar os termos alternativos. <br/><br/>A [**Navegação por facetas**](search-faceted-navigation.md) é ativada através de um parâmetro de consulta simples. O Azure Search devolve uma estrutura de navegação facetada que pode utilizar como o código por trás de uma lista de categorias, para filtragem auto-direcionada (por exemplo, para filtrar itens de catálogo de acordo com o preço ou marca). <br/><br/> Os [**Filtros**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) podem ser utilizados para incorporar a navegação por facetas na IU da sua aplicação, melhorar a formulação de consulta e filtrar com base em critérios especificados pelo utilizador ou programador. Criar filtros com a sintaxe OData.<br/><br/> O [**Detetor de ocorrências** ](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica a formatação de texto a uma palavra-chave correspondente nos resultados de pesquisa. Pode escolher quais os campos que devolvem fragmentos destacados.<br/><br/>A [**ordenação**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) é disponibilizado para vários campos através do esquema de índice e, em seguida, ativada/desativada durante a consulta com um único parâmetro de procura.<br/><br/> A [**Paginação**](search-pagination-page-layout.md) e a limitação dos seus resultados de pesquisa é algo simples com o controlo ajustado que o Azure Search lhe dá sobre os seus resultados de pesquisa.  
| Relevância | A [**Classificação simples**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) é uma vantagem primária do Azure Search. Os perfis de classificação são utilizados para modelar a relevância como uma função de valores nos próprios documentos. Por exemplo, pode querer que produtos mais recentes ou produtos em desconto apareçam mais em cima nos resultados da pesquisa. Também pode criar perfis de classificação com etiquetas para uma classificação personalizada baseada nas preferências de pesquisa dos clientes que acompanhou e armazenou em separado. |
| Monitorização e relatórios | [**Análise de tráfego de pesquisa**](search-traffic-analytics.md) é recolhida e analisada para desbloquear informações do que os utilizadores estão a escrever na caixa de pesquisa. <br/><br/>As métricas sobre as consultas por segundo, latência e limitação são recolhidas e comunicadas em páginas de portal sem ser necessária configuração adicional. Também pode monitorizar facilmente as contagens de índice e de documento para que possa ajustar a capacidade conforme seja necessário. Para mais informações, consulte [Administração do serviço](search-manage.md) |
| Ferramentas para criação de protótipos e inspeção | No portal, pode utilizar o [**Assistente de importação de dados**](search-import-data-portal.md) para configurar os indexados, o estruturador de índice para funcionar como índice e o [**Explorador de pesquisa**](search-explorer.md) para testar as consultas e refine os perfis de classificação. Também pode abrir qualquer índice para ver o seu esquema. |
| Infraestrutura | A **plataforma altamente disponível** garante uma experiência de serviço de pesquisa extremamente fiável. Quando dimensionado corretamente, o [Azure Search oferece um SLA de 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Completamente gerido e dimensionável** como uma solução de ponto-a-ponto, o Azure Search não necessita absolutamente de nenhuma gestão de infraestrutura. O seu serviço pode ser ajustado às suas necessidades ao dimensionar em duas dimensões para processar mais armazenamento de documentos, maiores cargas de consulta ou ambos.

## <a name="how-to-use-azure-search"></a>Como utilizar o Azure Search
### <a name="step-1-provision-service"></a>Passo 1: Aprovisionar o serviço
Pode aprovisionar um serviço do Azure Search no [portal do Azure](https://portal.azure.com/) ou através da [API de Gestão de Recursos do Azure](/rest/api/searchmanagement/). Pode escolher ou o serviço gratuito partilhado com outros subscritores, ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados apenas pelo seu serviço. Para os escalões pagos, pode dimensionar um serviço em duas dimensões: 

- Adicionar Réplicas para fazer crescer a sua capacidade de processar cargas de consultas pesadas.   
- Adicione Partições para aumentar o armazenamento para mais documentos. 

Ao processar o armazenamento de documentos e débito de consultas em separado, pode calibrar os recursos com base nos requisitos de produção.

### <a name="step-2-create-index"></a>Passo 2: Criar o índice
Antes de poder carregar conteúdo pesquisável, deve primeiro definir um índice do Azure Search. Um índice é como uma tabela de base de dados que contém os seus dados e pode aceitar consultas de pesquisa. Pode definir o esquema do índice para mapear e refletir a estrutura dos documentos que quer pesquisar, parecido aos campos numa base de dados.

Um esquema pode ser criado no Portal do Azure, ou de forma programática com o [.NET SDK](search-howto-dotnet-sdk.md) ou [API REST](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>Passo 3: Dados do índice
Depois de definir um índice, está pronto para carregar conteúdo. Pode utilizar um modelo push ou pull.

O modelo pull obtém dados a partir de origens de dados externas. É suportado através de *indexadores* que simplificam e automatizam aspetos da ingestão de dados, como a ligação, leitura e serialização de dados. Os [Indexadores](/rest/api/searchservice/Indexer-operations) estão disponíveis para o Azure Cosmos DB, Base de Dados SQL do Azure, Armazenamento de Blobs do Azure e SQL Server alojado numa VM do Azure. Pode configurar um indexador para uma atualização de dados sob pedido ou agendada.

O modelo de push é fornecido através da SDK ou de APIs REST, utilizado para enviar documentos atualizados para um índice. Pode enviar dados a partir de praticamente qualquer conjunto de dados ao utilizar o formato JSON. Veja [Adicionar, atualizar ou eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents) ou [Como utilizar o SDK .NET)](search-howto-dotnet-sdk.md) para obter diretrizes sobre carregar dados.

### <a name="step-4-search"></a>Passo 4: Pesquisa
Após preencher um índice, pode [emitir consultas de pesquisa](/rest/api/searchservice/Search-Documents) ao seu ponto final de serviço com simples pedidos HTTP com a API REST ou a SDK .NET.

## <a name="how-azure-search-compares"></a>Como se compara o Azure Search

Os clientes costumam perguntar como é que se compara o Azure Search a outras soluções relacionadas com pesquisa. A tabela seguinte resume as diferenças principais.

| Em comparação com | Diferenças principais |
|-------------|-----------------|
|Bing | A [API de Pesquisa na Web do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) procura os índices em Bing.com para os termos correspondentes ao que enviar. Os índices são criados a partir de HTML, XML e outros conteúdos web em sites públicos. Criada com base nos mesmos fundamentos, a [Pesquisa Personalizada do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia crawler para tipos de conteúdo Web, no âmbito de sites individuais.<br/><br/>O Azure Search pesquisa por um índice que definir, preenchido com dados e documentos seus, normalmente de origens diversas. O Azure Search tem funcionalidades de crawler para algumas origens de dados através de [indexadores](search-indexer-overview.md), mas pode enviar qualquer documento JSON que esteja em conformidade com o seu esquema de índice num único recurso pesquisável consolidado. |
|Pesquisa de base de dados | Muitas plataformas da base de dados incluem uma experiência de pesquisa incorporada. O SQL Server tem [pesquisa em texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). O Cosmos DB e as tecnologias semelhantes têm índices consultáveis. Quando avaliar os produtos que combinam a pesquisa e o armazenamento, pode ser difícil determinar qual escolher. Muitas soluções utilizam ambos: DBMS para armazenamento e Azure Search para funcionalidades de pesquisa especializadas.<br/><br/>Em comparação com a pesquisa DBMS, o Azure Search armazena conteúdo de origens heterogéneas e oferece funcionalidades de processamento de texto especializadas, tais como o processamento de texto com deteção de linguística (stemização, lematização, formas da palavra) em mais de [55 idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support). Também suporta a correção automática de palavras com erros ortográficos, [sinónimos](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugestões](https://docs.microsoft.com/rest/api/searchservice/suggestions), [controlos de classificação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](https://docs.microsoft.com/azure/search/search-filters-facets)e [atomização personalizada](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). O [motor de pesquisa de texto completo](search-lucene-query-architecture.md) no Azure Search está incorporado no Apache Lucene, uma norma da indústria para a obtenção de informações. Apesar do Azure Search manter os dados sob a forma de um índice invertido, raramente é uma substituição de armazenamento de dados verdadeiro. Para obter mais informações, veja esta [mensagem no fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>A utilização de recursos é outro ponto de flexão nesta categoria. A indexação e algumas operações de consulta, muitas vezes, são intensivas em termos de computação. O descarregamento da pesquisa do DBMS para uma solução dedicada na cloud preserva os recursos do sistema para o processamento de transações. Além disso, ao externalizar a pesquisa, pode facilmente ajustar o dimensionamento para corresponder ao volume de consultas.|
|Solução de pesquisa dedicada | Partindo do princípio de que escolheu a pesquisa dedicada com funcionalidade de espetro completo, a comparação categórica final será entre as soluções no local ou um serviço cloud. Muitas tecnologias de pesquisa oferecem controlo sobre os pipelines de indexação e consulta, acesso a uma sintaxe de consulta e filtragem mais rica, controlo sobre a classificação e relevância e funcionalidades para uma pesquisa inteligente e auto-direcionada. <br/><br/>Um serviço cloud é a escolha certa se quiser uma [solução imediata com o mínimo de custos gerais e de manutenção e com um dimensionamento ajustável](#cloud-service-advantage). <br/><br/>Dentro do paradigma da cloud, vários fornecedores oferecem funcionalidades de linha de base comparáveis, com pesquisa de texto inteiro, pesquisa geográfica e a capacidade de processar um determinado nível de ambiguidade nas entradas de pesquisa. Normalmente, é uma [funcionalidade especializada](#feature-drilldown) ou a facilidade e simplicidade geral das APIs, das ferramentas e a gestão que determinam qual a melhor opção. |

Entre os fornecedores de cloud, o Azure Search é o mais forte para cargas de trabalho de pesquisa de texto inteiro em arquivos de conteúdos e bases de dados no Azure, para aplicações que dependam sobretudo na pesquisa para recuperação de informação e navegação em conteúdo. 

As principais vantagens incluem:

+ Integração de dados do Azure (crawlers) na camada de indexação
+ Portal do Azure para gestão central
+ Dimensionamento, fiabilidade e disponibilidade de classe mundial do Azure
+ A análise linguística e personalizada, com analisadores de pesquisa de texto completo sólidos em 56 idiomas
+ [Principais funcionalidades das aplicações focadas na pesquisa](#feature-drilldown): classificação, facetamento, sugestões, sinónimos, pesquisa geográfica e muito mais.

> [!Note]
> As origens de dados não pertencentes ao Azure são completamente suportadas, mas dependem de uma metodologia push com código intensivo em vez de indexadores. Com APIs, pode encaminhar qualquer conjunto de documentos JSON para um índice do Azure Search.

Entre os nossos clientes, os que são capazes de tirar partido do maior leque de funcionalidades no Azure Search incluem catálogos online, programas de linha de negócios e aplicações de deteção de documentos.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

Enquanto muitas tarefas podem ser executadas no portal, o Azure Search é suposto para os programadores que querem integrar a funcionalidade de pesquisa em aplicações já existentes. Estão disponíveis as seguintes interfaces de programação.

|Plataforma |Descrição |
|-----|------------|
|[REST](/rest/api/searchservice/) | Os comandos HTTP suportados por qualquer plataforma ou linguagem de programação, incluindo Xamarin, Java e JavaScript|
|[SDK do .NET](search-howto-dotnet-sdk.md) | O wrapper .NET para a API REST oferece uma programação eficiente em C# e noutras linguagens de código gerido direcionadas para o .NET Framework |

## <a name="free-trial"></a>Avaliação gratuita
Os subscritores do Azure podem [aprovisionar um serviço no Escalão gratuito](search-create-service-portal.md).

Se não for um subscritor, pode [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Recebe créditos por experimentar os serviços pagos do Azure. Depois de serem utilizados, pode manter a conta e utilizar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Como alternativa, pode [ativar os benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): a sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="how-to-get-started"></a>Como começar

1. Criar um serviço no [Escalão gratuito](search-create-service-portal.md).

2. Siga os passos de um ou mais dos seguintes tutoriais. 

  + [Como utilizar o SDK .NET](search-howto-dotnet-sdk.md) demonstra os passos principais no código gerido.  
  + [Introdução à REST API](https://github.com/Azure-Samples/search-rest-api-getting-started) mostra os mesmos passos com a API REST.  
  + [Criar o seu primeiro índice no portal](search-get-started-portal.md) ao utilizar funcionalidades de indexação e de protótipo incorporadas.   

Os motores de busca são os controladores comuns de obtenção de informações em aplicações móveis, na web e nos arquivos de dados empresariais. O Azure Search dá-lhe as ferramentas para criar uma experiência de pesquisa parecida às dos grandes sites web comerciais.

Neste vídeo de 9 minutos do gestor de programa Liam Cavanagh, aprenda como integrar um motor de pesquisa que pode beneficiar a sua aplicação. As curtas demonstrações mostram as funcionalidades-chave no Azure Search e como funciona um fluxo de trabalho normal. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Os 0-3 minutos abrangem as funcionalidades principais e casos de utilização.
+ Os 3-4 minutos abrangem o aprovisionamento do serviço. 
+ Os 4-6 minutos abrangem o assistente de Importar Dados utilizado para criar um índice a utilizar um conjunto de dados de imobiliário incorporado.
+ Os 6-9 minutos abrangem o Explorador de Pesquisa e várias consultas.


