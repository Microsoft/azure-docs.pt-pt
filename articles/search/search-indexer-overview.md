---
title: Descrição geral do Indexador
titleSuffix: Azure Cognitive Search
description: Base de Dados De Azure SQL Crawl, SQL Managed Instance, Azure Cosmos DB ou armazenamento Azure para extrair dados pesquisáveis e povoar um índice de Pesquisa Cognitiva azul.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6cce37a7c719c6a0c183e166fa28967ea926a221
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581652"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores na Pesquisa Cognitiva do Azure

Um *indexante* na Azure Cognitive Search é um crawler que extrai texto e metadados pes pesjáveis de uma fonte externa de dados Azure e povoa um índice de pesquisa usando mapeamentos de campo-a-campo entre dados de origem e o seu índice. Esta abordagem é por vezes referida como um "modelo de puxar" porque o serviço retira dados sem ter de escrever qualquer código que adicione dados a um índice. Os indexantes também impulsionam as capacidades de [enriquecimento](cognitive-search-concept-intro.md) de IA da Pesquisa Cognitiva, integrando o processamento externo de conteúdos a caminho de um índice.

Os indexantes são apenas azure, com indexadores individuais para [Azure SQL,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) [Azure Cosmos DB,](search-howto-index-cosmosdb.md) [Azure Table Storage](search-howto-indexing-azure-tables.md) e [Blob Storage](search-howto-indexing-azure-blob-storage.md). Ao configurar um indexante, irá especificar uma fonte de dados (origem), bem como um índice (destino). Várias fontes, como o armazenamento blob, têm propriedades de configuração adicionais específicas desse tipo de conteúdo.

Pode executar indexadores a pedido ou num calendário de atualização de dados recorrente que funciona com a frequência de cinco em cinco minutos. Atualizações mais frequentes requerem um ["modelo push"](search-what-is-data-import.md) que atualiza simultaneamente os dados tanto na Pesquisa Cognitiva Azure como na sua fonte de dados externa.

## <a name="usage-scenarios"></a>Cenários de utilização

Pode utilizar um indexante como único meio para a ingestão de dados, ou como parte de uma combinação de técnicas que carregam e transformam ou enriquecem opcionalmente o conteúdo ao longo do caminho. A tabela seguinte resume os cenários principais.

| Scenario |Estratégia |
|----------|---------|
| Fonte única de dados | Este padrão é o mais simples: uma fonte de dados é o único fornecedor de conteúdo para um índice de pesquisa. A partir da fonte, irá identificar um campo que contenha valores únicos para servir como chave de documento no índice de pesquisa. O valor único será usado como identificador. Todos os outros campos de origem são mapeados implicitamente ou explicitamente para campos correspondentes num índice. </br></br>Um takeaway importante é que o valor de uma chave de documento provém de dados de origem. Um serviço de pesquisa não gera valores-chave. Nas execuções subsequentes, são adicionados documentos com novas teclas, enquanto os documentos de entrada com chaves existentes são fundidos ou substituídos, dependendo se os campos de índice são nulos ou povoados. |
| Múltiplas fontes de dados | Um índice pode aceitar conteúdo de várias fontes, onde cada corrida traz novos conteúdos de uma fonte diferente. </br></br>Um dos resultados pode ser um índice que ganha documentos após cada execução do indexante, com documentos inteiros criados na íntegra a partir de cada fonte. Por exemplo, os documentos 1-100 são de armazenamento Blob, documentos 101-200 são de Azure SQL, e assim por diante. O desafio para este cenário reside na conceção de um esquema de índice que funciona para todos os dados recebidos, e uma estrutura chave documental que é uniforme no índice de pesquisa. De forma nativa, os valores que identificam exclusivamente um documento são metadata_storage_path num recipiente de bolhas e numa chave primária numa tabela SQL. Pode imaginar que uma ou ambas as fontes devem ser alteradas para fornecer valores-chave num formato comum, independentemente da origem do conteúdo. Para este cenário, deverá esperar realizar algum nível de pré-processamento para homogeneizar os dados para que possam ser puxados para um único índice. </br></br>Um resultado alternativo pode ser documentos de pesquisa que são parcialmente povoados na primeira execução, e depois mais povoados por corridas subsequentes para trazer valores de outras fontes. Por exemplo, os campos 1-10 são de armazenamento Blob, 11-20 de Azure SQL, e assim por diante. O desafio deste padrão é garantir que cada corrida de indexação está direcionada para o mesmo documento. A fusão de campos num documento existente requer uma correspondência na chave do documento. Para uma demonstração deste cenário, consulte [Tutorial: Índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
| Vários indexadores | Se estiver a utilizar várias fontes de dados, também poderá necessitar de vários indexadores se precisar de variar os parâmetros de tempo de execução, o horário ou os mapeamentos de campo. Embora vários conjuntos indexantes-fonte de dados possam visar o mesmo índice, tenha cuidado com as execuções do indexante que podem substituir os valores existentes no índice. Se um segundo indexador-fonte de dados tiver como alvo os mesmos documentos e campos, quaisquer valores da primeira execução serão substituídos. Os valores de campo são substituídos na totalidade; um indexante não pode fundir valores de várias corridas para o mesmo campo.</br></br>Outro caso de utilização multi-indexante é [a escala inter-região fora da Pesquisa Cognitiva.](search-performance-optimization.md#data-sync) Pode ter cópias do mesmo índice de pesquisa em diferentes regiões. Para sincronizar o conteúdo do índice de pesquisa, pode ter vários indexantes a puxar da mesma fonte de dados, onde cada indexante tem como alvo um índice de pesquisa diferente.</br></br>[A indexação paralela](search-howto-large-index.md#parallel-indexing) de conjuntos de dados muito grandes também requer uma estratégia multi-indexante. Cada indexante tem como alvo um subconjunto dos dados. |
| Transformação de conteúdos | A Cognitive Search suporta comportamentos de [enriquecimento](cognitive-search-concept-intro.md) de IA opcionais que adicionam análise de imagem e processamento de linguagem natural para criar novos conteúdos e estrutura pesmáveis. O enriquecimento de IA é orientado pelo indexante, através de um [skillset](cognitive-search-working-with-skillsets.md)anexado . Para realizar o enriquecimento de IA, o indexante ainda precisa de um índice e de uma fonte de dados Azure, mas neste cenário, adiciona o processamento de skillset à execução do indexante. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Supported data sources (Origens de dados suportadas)

Indexantes rastejam lojas de dados em Azure.

+ [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em pré-visualização)
+ [Table Storage do Azure](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Base de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Instância Gerida do SQL](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

As ligações indexantes a fontes de dados remotas podem ser feitas utilizando ligações padrão da Internet (públicas) ou ligações privadas encriptadas quando utiliza redes virtuais Azure para aplicações de clientes. Também pode configurar ligações para autenticar usando uma identidade de serviço fidedigna. Para obter mais informações sobre ligações seguras, consulte [conceder acesso através de pontos finais privados](search-indexer-securing-resources.md#granting-access-via-private-endpoints) e Ligar a uma fonte de [dados utilizando uma identidade gerida.](search-howto-managed-identities-data-sources.md)

## <a name="stages-of-indexing"></a>Fases de indexação

Numa execução inicial, quando o índice estiver vazio, um indexante lerá todos os dados fornecidos na tabela ou no recipiente. Nas execuções subsequentes, o indexante pode geralmente detetar e recuperar apenas os dados que mudaram. Para os dados blob, a deteção de alterações é automática. Para outras fontes de dados como Azure SQL ou Cosmos DB, a deteção de alterações deve ser ativada.

Para cada documento que recebe, um indexante implementa ou coordena vários passos, desde a recuperação do documento até um motor de busca final "handoff" para indexação. Opcionalmente, um indexante também é fundamental na execução e saídas de skillset de condução, assumindo que um skillset é definido.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Estágios indexantes" border="false":::

### <a name="stage-1-document-cracking"></a>Fase 1: Rachadura de documento

A quebra de documentos é o processo de abertura de ficheiros e de extração de conteúdos. Dependendo do tipo de fonte de dados, o indexante tentará realizar diferentes operações para extrair conteúdo potencialmente indexável.  

Exemplos:  

+ Quando o documento é um registo numa fonte de [dados Azure SQL,](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)o indexante extrairá cada um dos campos para o registo.
+ Quando o documento é um ficheiro PDF numa fonte de [dados de armazenamento Azure Blob,](search-howto-indexing-azure-blob-storage.md)o indexante extrairá texto, imagens e metadados.
+ Quando o documento é um registo numa fonte de [dados da Cosmos DB,](search-howto-index-cosmosdb.md)o indexante extrairá os campos e subcampos do documento do Cosmos DB.

### <a name="stage-2-field-mappings"></a>Fase 2: Mapeamentos de campo 

Um indexante extrai texto de um campo de origem e envia-o para um campo de destino numa loja de índices ou conhecimentos. Quando os nomes e tipos de campo coincidem, o caminho é claro. No entanto, pode querer nomes ou tipos diferentes na saída, caso em que precisa de dizer ao indexante como mapear o campo. 

Este passo ocorre após a quebra do documento, mas antes das transformações, quando o indexante está a ler a partir dos documentos de origem. Quando se define um mapeamento de [campo,](search-indexer-field-mappings.md)o valor do campo de origem é enviado como-é para o campo de destino sem modificações. 

### <a name="stage-3-skillset-execution"></a>Fase 3: Execução de Skillset

A execução de Skillset é um passo opcional que invoca o processamento de IA incorporado ou personalizado. Pode precisar dele para reconhecimento de caracteres óticos (OCR) sob a forma de análise de imagem se os dados de origem forem uma imagem binária, ou se precisar de tradução linguística se o conteúdo estiver em diferentes línguas. 

Qualquer que seja a transformação, a execução skillset é onde o enriquecimento ocorre. Se um indexante é um oleoduto, pode pensar num [skillset](cognitive-search-defining-skillset.md) como um "pipeline dentro do oleoduto".

### <a name="stage-4-output-field-mappings"></a>Fase 4: Mapeamentos de campo de saída

Se incluir um skillset, provavelmente terá de incluir mapeamentos de campo de saída. A produção de um skillset é realmente uma árvore de informação chamada *documento enriquecido.* Os mapeamentos de campo de saída permitem-lhe selecionar quais as partes desta árvore para mapear em campos no seu índice. Saiba como [definir mapeamentos de campo de saída.](cognitive-search-output-field-mapping.md)

Enquanto os mapeamentos de campo associam valores verbatim da fonte de dados aos campos de destino, os mapeamentos do campo de saída dizem ao indexante como associar os valores transformados no documento enriquecido aos campos de destino no índice. Ao contrário dos mapeamentos de campo, que são considerados opcionais, você sempre precisa definir um mapeamento de campo de saída para qualquer conteúdo transformado que precise residir em um índice.

A imagem seguinte mostra uma representação [da sessão de depuração do](cognitive-search-debug-session.md) indexante da amostra das fases indexantes: rachaduras de documentos, mapeamentos de campo, execução de skillset e mapeamentos de campo de saída.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="sessão de depurar amostra" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Fluxo de trabalho básico

Os indexadores podem oferecer funcionalidades que são exclusivas da origem de dados. Relativamente a isto, alguns aspetos de configuração do indexador ou da origem de dados irão variar consoante o tipo de indexador. No entanto, todos os indexadores partilham da mesma composição e requisitos básicos. Os passos que são comuns a todos os indexadores são abordados abaixo.

### <a name="step-1-create-a-data-source"></a>Passo 1: criar uma origem de dados

Os indexantes requerem um objeto *de fonte de dados* que forneça uma cadeia de ligação e possivelmente credenciais. Ligue para a [classe Create Data Source (REST)](/rest/api/searchservice/create-data-source) ou [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) para criar o recurso.

As origens de dados são configuradas e geridas independentemente dos indexadores que as utilizam, o que significa que uma origem de dados pode ser utilizada por vários indexadores para carregar mais de um índice de cada vez.

### <a name="step-2-create-an-index"></a>Passo 2: criar um índice

Um indexador irá automatizar algumas tarefas relacionadas com a ingestão de dados, mas geralmente a criação de um índice não é uma delas. Como pré-requisito, tem de ter um índice predefinido com campos que correspondam aos existentes na origem de dados externa. Os campos precisam de corresponder pelo nome e pelo tipo de dados. Caso contrário, pode [definir mapeamentos de campo](search-indexer-field-mappings.md) para estabelecer a associação. Para obter mais informações sobre a estruturação de um índice, consulte Criar uma classe [Índice (REST)](/rest/api/searchservice/Create-Index) ou [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Apesar de os indexadores não poderem gerar um índice para si, o assistente **Importar dados** do portal pode ser útil. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir dos metadados existentes na origem, apresentando um esquema de índice preliminar que pode editar em linha enquanto o assistente está ativo. Assim que o índice é criado no serviço, as outras edições no portal são limitadas principalmente à adição de novos campos. Considere o assistente para criar, mas não para rever um índice. Para aprendizagem prática, siga os passos no [portal de instruções](search-get-started-portal.md).

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Passo 3: Criar e executar (ou programar) o indexante

Um indexante funciona quando [cria um indexante](/rest/api/searchservice/Create-Indexer) no serviço de pesquisa. Só quando criar ou executar o indexante é que vai descobrir se a fonte de dados é acessível ou se o skillset é válido. Após a primeira execução, pode re-executá-lo a pedido usando [o Run Indexer,](/rest/api/searchservice/run-indexer)ou pode [definir um horário recorrente](search-howto-schedule-indexers.md). 

Pode monitorizar o estado do indexante no portal ou através da [API do Estado do Indexante](/rest/api/searchservice/get-indexer-status). Também deve [fazer consultas no índice](search-query-create.md) para verificar se o resultado é o que esperava.

## <a name="next-steps"></a>Passos seguintes

Agora que foi introduzido, o próximo passo é rever as propriedades e parâmetros indexantes, agendamento e monitorização do indexante. Em alternativa, pode voltar à lista de fontes de [dados apoiadas](#supported-data-sources) para obter mais informações sobre uma fonte específica.

+ [Criar indexadores](search-howto-create-indexers.md)
+ [Indexantes de reposição e execução](search-howto-run-reset-indexers.md)
+ [Indexadores de agenda](search-howto-schedule-indexers.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Monitorizar o estado do indexante](search-howto-monitor-indexers.md)