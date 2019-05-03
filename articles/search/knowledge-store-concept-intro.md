---
title: Introdução de dados de conhecimento Store e a descrição geral - Azure Search
description: Envie documentos plena ao armazenamento do Azure, onde pode ver, reformatar e consumir documentos plena no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 3000016de934aaa3faab96821f9747ea4b571ef7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026999"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>O que é a Store de dados de conhecimento no Azure Search?

Dados de conhecimento Store é uma funcionalidade opcional do Azure Search, atualmente em pré-visualização pública, que salva documentos plena e metadados criados por um pipeline de indexação baseada em IA [(pesquisa cognitiva)](cognitive-search-concept-intro.md). Dados de conhecimento Store é apoiada por uma conta de armazenamento do Azure que configura como parte do pipeline. Quando ativada, o serviço de pesquisa utiliza esta conta de armazenamento em cache uma representação de cada documento plena. 

Se tiver utilizado a pesquisa cognitiva no passado, já sabe que os conjuntos de habilidades podem ser utilizados para mover um documento por meio de uma seqüência de possível. O resultado pode ser um índice da Azure Search, ou (novo nesta pré-visualização) projeções num arquivo de dados de conhecimento.

Projeções são o mecanismo para estruturar dados para consumo num aplicativo de downstream. Pode usar [Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) criado para o armazenamento do Azure ou qualquer aplicação que liga ao armazenamento do Azure, que abre novas possibilidades para o consumo de enriquecida documentos. Alguns exemplos são pipelines de ciência de dados e análises personalizadas.

![Dados de conhecimento Store no diagrama de pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Store de dados de conhecimento no diagrama de pipeline")

Para utilizar dados de conhecimento Store, adicione um `knowledgeStore` elemento para um conjunto de capacidades que define sequencial operações num pipeline de indexação. Durante a execução, pesquisa do Azure cria um espaço na sua conta de armazenamento do Azure e a preenche com definições e conteúdo criado pelo pipeline.

## <a name="benefits-of-knowledge-store"></a>Benefícios do Store de dados de conhecimento

Um conhecimento armazenar dá estruturação, contexto e conteúdo real - obtidas a partir dos ficheiros de dados não estruturados e semiestruturados, como blobs, ficheiros de imagem que já passaram por análise, ou até mesmo os dados que são remodelados em novas formas de estruturados. Num [instruções passo a passo](knowledge-store-howto.md) escrito para esta pré-visualização, pode ver primeira mão como um documento JSON densa é particionado horizontalmente em substructures, reconstituted em novas estruturas e disponibilizado de outra forma de downstream processos, como o machine learning e os dados ciência cargas de trabalho.

Embora seja útil ver o que pode produzir um pipeline de indexação baseada em IA, o verdadeiro poder do conhecimento Store é a capacidade de reformatar os dados. Pode começar com um conjunto de capacidades básico e, em seguida, iterar sobre-lo para adicionar níveis de cada vez maiores de estrutura, que, em seguida, pode combinar em novas estruturas, consumíveis noutras aplicações além do Azure Search.

Os benefícios de dados de conhecimento Store enumerado, incluem o seguinte:

+ Consumir documentos plena [análises e relatórios ferramentas](#tools-and-apps) diferente de pesquisa. Power BI com o Power Query é uma opção atraente, mas qualquer ferramenta ou a aplicação que pode ligar-se ao armazenamento do Azure pode extrair a partir de um arquivo de dados de conhecimento que criar.

+ Refine um pipeline de indexação de IA durante a depuração de passos e as definições do conjunto de capacidades. Um arquivo de dados de conhecimento mostra-lhe o produto de uma definição de conjunto de capacidades num pipeline de indexação de IA. Pode utilizar esses resultados para um melhor conjunto de capacidades de design, pois pode ver exatamente o que o possível aspeto. Pode usar [Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para ver os conteúdos de um arquivo de dados de conhecimento.

+ Formatar os dados em novas formas. A reformulação é codificada em conjuntos de habilidades, mas a questão é que um conjunto de capacidades agora pode proporcionar esta capacidade. O [habilidade Modelador](cognitive-search-skill-shaper.md) no Azure Search foi estendido para acomodar esta tarefa. Reformulação de permite-lhe definir uma projeção que se alinha com a sua utilização prevista dos dados, preservando a relações.

> [!Note]
> Não estão familiarizados com baseada em IA indexação com os serviços cognitivos? O Azure Search é integrado com recursos de linguagem e de visão de serviços cognitivos para extrair e enriquecer os dados de origem com o reconhecimento Ótico de carateres (OCR) em arquivos de imagem, reconhecimento de entidades e extração de expressões-chave dos arquivos de texto e muito mais. Para obter mais informações, consulte [o que é a pesquisa cognitiva?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Criar um arquivo de dados de conhecimento

Um arquivo de dados de conhecimento é parte de uma definição de conjunto de capacidades. Nesta pré-visualização, criá-lo requer a API REST, usando `api-version=2019-05-06-Preview` ou o **importar dados** assistente no portal.

O JSON seguinte especifica um `knowledgeStore`, que faz parte de um conjunto de capacidades, o que é invocado por um indexador (não mostrado). A especificação de projeções dentro do `knowledgeStore` determina se as tabelas ou objetos são criados no armazenamento do Azure.

Se já estiver familiarizado com base em IA indexação, a definição do conjunto de capacidades determina a criação, a organização e a substância de cada documento plena.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Componentes de um arquivo de dados de conhecimento de segurança

Para criar um arquivo de dados de conhecimento, terá dos seguintes serviços e artefatos.

### <a name="1---source-data"></a>1 - origem de dados

Os dados ou documentos que pretende enriquecer tem de existir uma origem de dados do Azure suportada pelo indexadores do Azure Search: 

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)

[Armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md) pode ser utilizado para dados de saída num armazenamento de dados de conhecimento, mas não pode ser utilizado como um recurso de dados de entrada para um pipeline de indexação baseada em IA.

### <a name="2---azure-search-service"></a>2 - o serviço de pesquisa as do azure

Também precisa de um serviço Azure Search e a API REST para criar e configurar objetos usados para enriquecimento de dados. A API de REST para a criação de um arquivo de dados de conhecimento é `api-version=2019-05-06-Preview`.

O Azure Search fornece a funcionalidade de indexador e indexadores são usados para orientar a todo o processo ponto-a-ponto, resultando em documentos plena persistentes no armazenamento do Azure. Indexadores utilizam uma origem de dados, um índice e um conjunto de capacidades - todos os quais são necessários para criar e preencher um arquivo de dados de conhecimento.

| Object | API REST | Descrição |
|--------|----------|-------------|
| origem de dados | [Criar Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso de identificar a origem de dados do Azure externa a fornecer os dados de origem utilizados para criar documentos plena.  |
| Conjunto de capacidades | [Criar conjunto de capacidades (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso de coordenar o uso de [habilidades internas](cognitive-search-predefined-skills.md) e [personalizadas capacidades cognitivas](cognitive-search-custom-skill-interface.md) usado num pipeline de enriquecimento durante a indexação. |
| índice | [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema de expressar um índice da Azure Search. Campos no índice mapeiam para campos numa origem de dados ou para campos fabricados durante a fase de melhoria (por exemplo, um campo para nomes de organização criado pelo reconhecimento de entidades). |
| indexador | [Criar indexador (api-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso definindo componentes utilizados durante a indexação: incluindo uma origem de dados, um conjunto de capacidades, associações de campo da origem e de estruturas de dados intermédios ao índice de destino e o índice em si. É o acionador para ingestão de dados e enriquecimento de executar o indexador. O resultado é um índice de pesquisa com base no esquema de índice, preenchido com dados de origem, enriquecidos através de conjuntos de competências.  |

### <a name="3---cognitive-services"></a>3 - cognitivas Services

Possível especificado num conjunto de capacidades baseiam-se nos recursos de imagem digitalizada e idioma nos serviços cognitivos. Funcionalidade de serviços cognitiva é utilizada durante a indexação por meio de seu conjunto de capacidades. Um conjunto de capacidades é uma composição de habilidades e habilidades estão vinculadas a recursos específicos de imagem digitalizada e idioma. Para integrar os serviços cognitivos, vai [anexar um recurso dos serviços cognitivos](cognitive-search-attach-cognitive-services.md) para um conjunto de capacidades.

### <a name="4---storage-account"></a>4 - conta de armazenamento de

Na sua conta de armazenamento do Azure, o Azure Search cria um contentor de BLOBs ou tabelas, dependendo de como configurar um conjunto de capacidades. Se os dados são originados por armazenamento de Blobs do Azure ou tabela, já está definidas. Caso contrário, terá de criar uma conta de armazenamento do Azure. Tabelas e objetos no armazenamento do Azure contêm os documentos plena criados pelo pipeline de indexação baseada em IA.

A conta de armazenamento é especificada no conjunto de capacidades. No `api-version=2019-05-06-Preview`, uma definição de conjunto de capacidades inclui uma definição de arquivo de dados de conhecimento, para que pode fornecer informações de conta.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5 - acessem e consumam

Depois do possível existe no armazenamento, qualquer ferramenta ou tecnologia que liga-se ao armazenamento de Blobs do Azure ou de tabela pode ser utilizada para explorar, analisar ou consumir o conteúdo. A lista seguinte é um começo:

+ [Explorador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) para ver a estrutura do documento plena e o conteúdo. Considere isso como a ferramenta de linha de base para a visualização de conteúdo do arquivo de dados de conhecimento.

+ [Power BI com o Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) para consultas de linguagem natural, ou utilize a análise e relatórios de ferramentas se tiver dados numéricos.

+ [O Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) para manipulação adicional.

+ Índice de pesquisa do Azure para pesquisa em texto completo ao longo do conteúdo de indexar usando [pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Persistência de documento

Dentro da conta de armazenamento, o possível pode ser expresso como tabelas no armazenamento de tabelas do Azure ou como objetos no armazenamento de Blobs do Azure. Lembre-se de que possível, uma vez armazenados, pode ser utilizado como uma origem para carregar dados para outras bases de dados e ferramentas,

+ Armazenamento de tabelas é útil quando desejar uma representação com suporte para o esquema dos dados em formato tabular. Se quiser reformatar ou recombiná elementos de novas maneiras, o armazenamento de tabelas dá-lhe a granularidade necessária.

+ Armazenamento de BLOBs cria uma representação do JSON totalmente inclusivos de cada documento. Pode utilizar ambas as opções de armazenamento num conjunto de capacidades para obter uma gama completa de expressões.

+ O Azure Search persiste conteúdo num índice. Se o seu cenário é não-relacionados com a pesquisa, por exemplo, se seu objetivo é a análise em outra ferramenta, pode eliminar o índice que cria o pipeline. Mas poderia manter o índice e utilizar uma ferramenta interna, como também [Explorador de pesquisa](search-explorer.md) como um meio de terceiro (por trás do Explorador de armazenamento e a aplicação de análise) para interagir com o seu conteúdo.

Juntamente com o conteúdo de documentos, documentos plena incluem os metadados da versão do conjunto de capacidades que produziu o possível.  

## <a name="inside-a-knowledge-store"></a>Dentro de um arquivo de dados de conhecimento

O arquivo de dados de conhecimento consiste num cache de anotação e projeções. O *cache* é utilizada pelo serviço internamente para colocar em cache os resultados de suas habilidades e controlar as alterações. R *projeção* define o esquema e a estrutura do possível que correspondem ao seu uso pretendido. Há um cache por arquivo de dados de conhecimento, mas as várias projeções. 

A cache é sempre um contentor de BLOBs, mas projeções podem ser articuladas como tabelas ou de objetos:

+ Como um objeto, a projeção é mapeado para o armazenamento de BLOBs, onde a projeção é guardada para um contentor, que nos quais são os objetos ou hierárquicas representações em JSON para cenários como um pipeline de ciência de dados.

+ Como uma tabela, a projeção é mapeado para o armazenamento de tabelas. Uma representação em tabela preserva as relações para cenários como a análise de dados ou exportar como quadros de dados para machine learning. As projeções plena, em seguida, podem ser facilmente importadas para outros arquivos de dados. 

Pode criar várias projeções num arquivo de dados de conhecimento para acomodar os diversos clientes na sua organização. Um desenvolvedor poderá ter acesso para a representação JSON completo de um documento plena, enquanto os cientistas de dados ou analistas podem desejar em forma, o conjunto de capacidades de estruturas de dados de granulares ou modular.

Por exemplo, se um dos objetivos do processo de melhoria é criar também um conjunto de dados utilizado para formar um modelo, os dados são projetados para o arquivo de objeto seria uma forma de utilizar os dados em seus pipelines de ciência de dados. Em alternativa, se pretender criar um dashboard do Power BI rápido com base em documentos plena a projeção tabular funcione bem.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>Por onde devo começar?

Recomendamos o serviço gratuito para efeitos de aprendizagem, mas lembre-se de que o número de transações gratuitos está limitado a 20 documentos por dia, por subscrição.

Ao utilizar múltiplos serviços, crie todos os seus serviços na mesma região para um melhor desempenho e para minimizar os custos. Não é-lhe cobrada a largura de banda para dados de entrada ou de dados de saída que vai para outro serviço na mesma região.

**Passo 1: [Criar um recurso do Azure Search](search-create-service-portal.md)** 

**Passo 2: [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Passo 3: [Criar um recurso de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Passo 4: [Começar com o portal](cognitive-search-quickstart-blob.md) - ou - [começar com dados de exemplo usando REST e o Postman](knowledge-store-howto.md)** 

Pode utilizar o REST `api-version=2019-05-06-Preview` para construir um pipeline com base em IA, que inclui dados de conhecimento Store. A API de pré-visualização mais recente, o objeto de conjunto de capacidades fornece o `knowledgeStore` definição.

## <a name="takeaways"></a>Conclusões

Dados de conhecimento Store oferece uma variedade de benefícios, incluindo mas não se limitando a ativar a utilização dos documentos plena em outros cenários além da pesquisa, controles de custos e gerir desvios em seu processo de melhoria. Estas funcionalidades estão disponíveis para utilizar simplesmente ao adicionar uma conta de armazenamento para o conjunto de capacidades e usando a linguagem de expressão atualizado conforme descrito em [como começar com dados de conhecimento Store](knowledge-store-howto.md). 

## <a name="next-steps"></a>Passos Seguintes

A abordagem mais simples para criar documentos plena é através da **importar dados** assistente.

> [!div class="nextstepaction"]
> [Quickstart: Experimentar a pesquisa cognitiva numa instruções do portal](cognitive-search-quickstart-blob.md)
