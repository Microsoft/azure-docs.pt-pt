---
title: Introdução à loja de conhecimento (versão prévia)-Azure Search
description: Envie documentos aprimorados para o armazenamento do Azure, onde você pode exibir, remodelar e consumir documentos aprimorados no Azure Search e em outros aplicativos.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: b092c7251bc2a6794db36f8eaa279a7eeb931723
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533774"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>O que é o repositório de conhecimento em Azure Search?

> [!Note]
> A loja de conhecimento está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

O repositório de conhecimento é um recurso do Azure Search que persiste a saída de um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) para análise posterior ou outro processamento downstream. Um *documento aprimorado* é a saída de um pipeline, criada a partir de conteúdo que foi extraído, estruturado e analisado usando recursos em serviços cognitivas. Em um pipeline baseado em ia padrão, os documentos aprimorados são transitórios, usados somente durante a indexação e, em seguida, descartados. Com a loja de conhecimento, os documentos são salvos para uso em outros aplicativos ou cargas de trabalho de ciência de dados downstream. 

Se você usou habilidades de ia com Azure Search no passado, já sabe que *habilidades* são usadas para mover um documento por uma sequência de aprimoramentos. O resultado pode ser um índice Azure Search ou (novo nesta visualização) projeções em uma loja de conhecimento. As duas saídas, índice de pesquisa e repositório de conhecimento são fisicamente distintas umas das outras. Eles compartilham o mesmo conteúdo, mas são armazenados e usados de maneiras muito diferentes.

Fisicamente, uma loja de conhecimento é uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), como o armazenamento de tabelas do Azure, o armazenamento de BLOBs do Azure ou ambos, dependendo de como você configura o pipeline. Qualquer ferramenta ou processo que possa se conectar a uma conta de armazenamento do Azure pode consumir o conteúdo de uma loja de conhecimento.

As projeções são seu mecanismo para estruturar dados em uma loja de conhecimento. Por exemplo, por meio de projeções, você pode escolher se a saída é salva como um único BLOB ou uma coleção de tabelas relacionadas. Uma maneira fácil de exibir o conteúdo da loja de conhecimento é por meio do [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) interno para o armazenamento do Azure.

![Repositório de conhecimento no diagrama de pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Repositório de conhecimento no diagrama de pipeline")

Para usar o repositório de conhecimento, adicione um elemento de `knowledgeStore` a um conconhecedor que defina operações passo-a-bit em um pipeline de indexação. Durante a execução, Azure Search cria um espaço em sua conta de armazenamento do Azure e projeta os documentos aprimorados com a definição criada no pipeline.

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimento fornece estrutura, contexto e conteúdo real, obtidas de arquivos de dados não estruturados e semiestruturados, como BLOBs, arquivos de imagem que têm análise passou ou até mesmo dados estruturados que são remodelados em novos formulários. Em uma [explicação](knowledge-store-howto.md) passo a passo escrita para essa versão prévia, você pode ver em primeira mão como um documento JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream como o computador cargas de trabalho de aprendizado e ciência de dados.

Embora seja útil ver o que um pipeline de indexação baseado em ia pode produzir, o poder real da loja de conhecimento é a capacidade de remodelar os dados. Você pode começar com um habilidades básicas e, em seguida, iterar sobre ele para adicionar níveis cada vez maiores de estrutura, que você pode combinar em novas estruturas, consumíveis em outros aplicativos além Azure Search.

Enumerado, os benefícios da loja de conhecimento incluem o seguinte:

+ Consuma documentos aprimorados em [ferramentas de análise e relatórios](#tools-and-apps) diferentes de pesquisa. Power BI com Power Query é uma opção atraente, mas qualquer ferramenta ou aplicativo que possa se conectar ao armazenamento do Azure pode efetuar pull de uma loja de conhecimento que você criar.

+ Refinar um pipeline de indexação de AI durante a depuração de etapas e de definições de habilidades. Uma loja de conhecimento mostra o produto de uma definição de conconhecedor em um pipeline de indexação de AI. Você pode usar esses resultados para criar um melhor conhecimento, pois você pode ver exatamente de que forma os aprimoramentos se parecem. Você pode usar [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de uma loja de conhecimento.

+ Formate os dados em novos formulários. A remodelagem é codificados em habilidades, mas o ponto é que um con agora pode fornecer esse recurso. A [habilidade do modelador](cognitive-search-skill-shaper.md) no Azure Search foi estendida para acomodar essa tarefa. A remodelagem permite que você defina uma projeção que se alinhe com o uso pretendido dos dados, preservando as relações.

> [!Note]
> Não está familiarizado com a indexação baseada em ia usando serviços cognitivas? O Azure Search integra-se à visão de serviços cognitivas e aos recursos de linguagem para extrair e enriquecer dados de origem usando OCR (reconhecimento óptico de caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, consulte [o que é pesquisa cognitiva?](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Criando uma loja de conhecimento

Uma loja de conhecimento faz parte de um [qualificante](cognitive-search-working-with-skillsets.md)que, por sua vez, faz parte de um [indexador](search-indexer-overview.md). 

Nesta versão prévia, você pode criar uma loja de conhecimento usando a API REST e `api-version=2019-05-06-Preview` ou por meio do assistente de **importação de dados** no Portal.

### <a name="json-representation-of-a-knowledge-store"></a>Representação JSON de uma loja de conhecimento

O JSON a seguir especifica um `knowledgeStore`, que faz parte de um configurador de qualificações, que é invocado por um indexador (não mostrado). Se você já estiver familiarizado com o enriquecimento de ia, um configurador de qualificações determinará a criação, a organização e a substância de cada documento aprimorado. Um configurador de qualificações deve conter pelo menos uma habilidade, provavelmente uma habilidade de forma, se você estiver modulating estruturas de dados.

Uma `knowledgeStore` consiste em uma conexão e projeções. 

+ A conexão é para uma conta de armazenamento na mesma região que Azure Search. 

+ As projeções são pares de tabelas-objetos. `Tables` definir a expressão física de documentos aprimorados no armazenamento de tabelas do Azure. `Objects` definir os objetos físicos no armazenamento de BLOBs do Azure.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
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
               
            ]      
        },
        { 
            "tables": [ 
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

### <a name="sources-of-data-for-a-knowledge-store"></a>Fontes de dados para uma loja de conhecimento

Se uma loja de conhecimento for uma saída de um pipeline de enriquecimento de ia, quais são as entradas? Os dados originais que você deseja extrair, enriquecer e, eventualmente, salvar em uma loja de conhecimento podem se originar de qualquer fonte de dados do Azure com suporte dos indexadores Azure Search: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)

* [Armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md)

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Os indexadores e habilidades você cria extrair e enriquecer ou transformar esse conteúdo como parte de uma carga de trabalho de indexação e, em seguida, salvar os resultados em uma loja de conhecimento.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>APIs REST usadas na criação de uma loja de conhecimento

Somente duas APIs têm as extensões necessárias para criar um repositório de conhecimento (criar o conconhecedor e criar indexador). Outras APIs são usadas no estado em que se encontram.

| Object | API REST | Descrição |
|--------|----------|-------------|
| Fonte de dados | [Criar Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa do Azure que fornece dados de origem usados para criar documentos aprimorados.  |
| qualificações | [Criar Qualificable (API-Version = 2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades internas](cognitive-search-predefined-skills.md) e [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) usadas em um pipeline de enriquecimento durante a indexação. Um qualificable tem uma definição de `knowledgeStore` como um elemento filho. |
| index | [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de Azure Search. Os campos no índice são mapeados para campos em dados de origem ou para campos fabricados durante a fase de enriquecimento (por exemplo, um campo para nomes de organização criados pelo reconhecimento de entidade). |
| indexador | [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, uma habilidade, associações de campo das estruturas de dados de origem e intermediária para o índice de destino e o próprio índice. A execução do indexador é o gatilho para a ingestão e o enriquecimento de dados. A saída é um índice de pesquisa baseado no esquema de índice, preenchido com dados de origem, aprimorado por meio de habilidades.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Composição física de uma loja de conhecimento

 Uma *projeção*, que é um elemento de uma definição de `knowledgeStore`, articula o esquema e a estrutura de saída para que ele corresponda ao uso pretendido. Você pode definir várias projeções se tiver aplicativos que consomem os dados em formatos e formas diferentes. 

As projeções podem ser articuladas como objetos ou tabelas:

+ Como um objeto, a projeção é mapeada para o armazenamento de BLOBs, onde a projeção é salva em um contêiner, dentro dos quais são os objetos ou representações hierárquicas em JSON para cenários como um pipeline de ciência de dados.

+ Como uma tabela, a projeção é mapeada para o armazenamento de tabela. Uma representação tabular preserva relações para cenários como análise de dados ou exportação como quadros de dados para aprendizado de máquina. As projeções aprimoradas podem ser facilmente importadas para outros armazenamentos de dados. 

Você pode criar várias projeções em uma loja de conhecimento para acomodar vários Constituencies em sua organização. Um desenvolvedor pode precisar de acesso à representação JSON completa de um documento aprimorado, enquanto os cientistas ou analistas de dados podem querer estruturas de dados granulares ou modulares moldadas por seu qualificante.

Por exemplo, se uma das metas do processo de enriquecimento for também criar um conjunto de dados usado para treinar um modelo, a projeção dos dados para o repositório de objetos seria uma maneira de usar o dado em seus pipelines de ciência de dados. Como alternativa, se você quiser criar um painel de Power BI rápida com base nos documentos aprimorados, a projeção tabular funcionaria bem.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Conectando-se com ferramentas e aplicativos

Depois que os aprimoramentos existem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de BLOBs ou tabelas do Azure pode ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um início:

+ [Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md) para exibir conteúdo e estrutura de documentos aprimorados. Considere isso como sua ferramenta de linha de base para exibir o conteúdo da loja de conhecimento.

+ [Power bi](knowledge-store-connect-power-bi.md) para as ferramentas de relatório e análise se você tiver dados numéricos.

+ [Azure data Factory](https://docs.microsoft.com/azure/data-factory/) para manipulação adicional.


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

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Passos seguintes

A loja de conhecimento oferece persistência de documentos aprimorados, útil ao criar um qualificable ou à criação de novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é por meio do assistente de **importação de dados** , mas você também pode usar o postmaster e a API REST, o que é mais útil se você quiser obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimento usando o portal](knowledge-store-create-portal.md) 
> [criar uma loja de conhecimento usando o postmaster e a API REST](knowledge-store-create-rest.md)
