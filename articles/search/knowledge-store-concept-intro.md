---
title: Introdução à loja de conhecimento (versão prévia)-Azure Search
description: Envie documentos aprimorados para o armazenamento do Azure, onde você pode exibir, remodelar e consumir documentos aprimorados no Azure Search e em outros aplicativos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 21530ca309a18c95e947a265b951fe78044aec93
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828485"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>O que é o repositório de conhecimento em Azure Search?

> [!Note]
> A loja de conhecimento está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

O repositório de conhecimento é um recurso do Azure Search que salva documentos e metadados aprimorados criados por um pipeline de indexação baseado em ia [(pesquisa cognitiva)](cognitive-search-concept-intro.md). Um documento aprimorado é a saída de um pipeline, criada a partir de conteúdo que foi extraído, estruturado e analisado usando recursos em serviços cognitivas. Em um pipeline baseado em ia padrão, os documentos aprimorados são transitórios, usados somente durante a indexação e, em seguida, descartados. Com a loja de conhecimento, os documentos são salvos para avaliação subsequente, exploração e potencialmente podem se tornar entradas para uma carga de trabalho de ciência de dados downstream. 

Se você usou a pesquisa cognitiva no passado, já sabe que os habilidades são usados para mover um documento por uma sequência de aprimoramentos. O resultado pode ser um índice Azure Search ou (novo nesta visualização) projeções em uma loja de conhecimento. As duas saídas, índice de pesquisa e repositório de conhecimento são fisicamente distintas umas das outras. Eles compartilham o mesmo conteúdo, mas são armazenados e usados de maneiras muito diferentes.

Fisicamente, uma loja de conhecimento é criada em uma conta de armazenamento do Azure, seja como armazenamento de tabelas do Azure ou armazenamento de BLOB, dependendo de como você configura o pipeline. Qualquer ferramenta ou processo que possa se conectar ao armazenamento do Azure pode consumir o conteúdo de uma loja de conhecimento.

As projeções são seu mecanismo para estruturar dados em uma loja de conhecimento. Por exemplo, por meio de projeções, você pode escolher se a saída é salva como um único BLOB ou uma coleção de tabelas relacionadas. Uma maneira fácil de exibir o conteúdo da loja de conhecimento é por meio do [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) interno para o armazenamento do Azure.

![Repositório de conhecimento no diagrama de pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Repositório de conhecimento no diagrama de pipeline")

Para usar o repositório de conhecimento, `knowledgeStore` adicione um elemento a um conconhecedor que defina operações passo a passos em um pipeline de indexação. Durante a execução, Azure Search cria um espaço em sua conta de armazenamento do Azure e o preenche com definições e conteúdo criado pelo pipeline.

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimento fornece estrutura, contexto e conteúdo real, obtidas de arquivos de dados não estruturados e semiestruturados, como BLOBs, arquivos de imagem que têm análise passou ou até mesmo dados estruturados que são remodelados em novos formulários. Em uma [explicação](knowledge-store-howto.md) passo a passo escrita para essa versão prévia, você pode ver em primeira mão como um documento JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream como o computador cargas de trabalho de aprendizado e ciência de dados.

Embora seja útil ver o que um pipeline de indexação baseado em ia pode produzir, o poder real da loja de conhecimento é a capacidade de remodelar os dados. Você pode começar com um habilidades básicas e, em seguida, iterar sobre ele para adicionar níveis cada vez maiores de estrutura, que você pode combinar em novas estruturas, consumíveis em outros aplicativos além Azure Search.

Enumerado, os benefícios da loja de conhecimento incluem o seguinte:

+ Consuma documentos aprimorados em [ferramentas de análise e relatórios](#tools-and-apps) diferentes de pesquisa. Power BI com Power Query é uma opção atraente, mas qualquer ferramenta ou aplicativo que possa se conectar ao armazenamento do Azure pode efetuar pull de uma loja de conhecimento que você criar.

+ Refinar um pipeline de indexação de AI durante a depuração de etapas e de definições de habilidades. Uma loja de conhecimento mostra o produto de uma definição de conconhecedor em um pipeline de indexação de AI. Você pode usar esses resultados para criar um melhor conhecimento, pois você pode ver exatamente de que forma os aprimoramentos se parecem. Você pode usar [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de uma loja de conhecimento.

+ Formate os dados em novos formulários. A remodelagem é codificados em habilidades, mas o ponto é que um con agora pode fornecer esse recurso. A [habilidade](cognitive-search-skill-shaper.md) do modelador no Azure Search foi estendida para acomodar essa tarefa. A remodelagem permite que você defina uma projeção que se alinhe com o uso pretendido dos dados, preservando as relações.

> [!Note]
> Não está familiarizado com a indexação baseada em ia usando serviços cognitivas? O Azure Search integra-se à visão de serviços cognitivas e aos recursos de linguagem para extrair e enriquecer dados de origem usando OCR (reconhecimento óptico de caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, consulte [o que é pesquisa cognitiva?](cognitive-search-concept-intro.md).

## <a name="create-a-knowledge-store"></a>Criar uma loja de conhecimento

Uma loja de conhecimento faz parte de uma definição de contratação de habilidades. Nesta versão prévia, criá-la requer a API REST, `api-version=2019-05-06-Preview` usando o ou o assistente de **importação de dados** no Portal.

O JSON a seguir especifica `knowledgeStore`um, que faz parte de um qualificable, que é invocado por um indexador (não mostrado). A especificação das projeções dentro do `knowledgeStore` determina se as tabelas ou os objetos são criados no armazenamento do Azure.

Se você já estiver familiarizado com a indexação baseada em ia, a definição do Configurador de habilidades determinará a criação, a organização e a substância de cada documento aprimorado.

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

## <a name="components-backing-a-knowledge-store"></a>Componentes fazendo backup de uma loja de conhecimento

Para criar uma loja de conhecimento, você precisa dos seguintes serviços e artefatos.

### <a name="1---source-data"></a>1-dados de origem

Os dados ou documentos que você deseja enriquecer devem existir em uma fonte de dados do Azure com suporte de indexadores Azure Search: 

* [SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [BD do Cosmos para o Azure](search-howto-index-cosmosdb.md)

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)

O [armazenamento de tabelas do Azure](search-howto-indexing-azure-tables.md) pode ser usado para dados de saída em uma loja de conhecimento, mas não pode ser usado como um recurso para dados de entrada para um pipeline de indexação baseado em ia.

### <a name="2---azure-search-service"></a>serviço de Azure Search de 2

Você também precisa de um serviço Azure Search e da API REST para criar e configurar objetos usados para o enriquecimento de dados. A API REST para criar uma loja de conhecimento `api-version=2019-05-06-Preview`é.

Azure Search fornece o recurso de indexador, e os indexadores são usados para conduzir todo o processo de ponta a ponta, resultando em documentos persistentes e aprimorados no armazenamento do Azure. Os indexadores usam uma fonte de dados, um índice e um configurador de qualificações que são necessários para criar e preencher uma loja de conhecimento.

| Objeto | API REST | Descrição |
|--------|----------|-------------|
| origem de dados | [Criar Origem de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Um recurso que identifica uma fonte de dados externa do Azure que fornece dados de origem usados para criar documentos aprimorados.  |
| qualificações | [Criar Qualificable (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que coordena o uso de [habilidades internas](cognitive-search-predefined-skills.md) e [habilidades cognitivas personalizadas](cognitive-search-custom-skill-interface.md) usadas em um pipeline de enriquecimento durante a indexação. |
| index | [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de Azure Search. Os campos no índice são mapeados para campos em dados de origem ou para campos fabricados durante a fase de enriquecimento (por exemplo, um campo para nomes de organização criados pelo reconhecimento de entidade). |
| indexador | [Criar indexador (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Um recurso que define os componentes usados durante a indexação: incluindo uma fonte de dados, uma habilidade, associações de campo das estruturas de dados de origem e intermediária para o índice de destino e o próprio índice. A execução do indexador é o gatilho para a ingestão e o enriquecimento de dados. A saída é um índice de pesquisa baseado no esquema de índice, preenchido com dados de origem, aprimorado por meio de habilidades.  |

### <a name="3---cognitive-services"></a>3-serviços cognitivas

Os aprimoramentos especificados em um configurador de habilidades são baseados nos recursos de Pesquisa Visual Computacional e linguagem nos serviços cognitivas. A funcionalidade de serviços cognitivas é utilizada durante a indexação por meio do seu Skill. Um configurador de qualificações é uma composição de habilidades e as habilidades são associadas a recursos específicos de Pesquisa Visual Computacional e idioma. Para integrar serviços cognitivas, você [anexará um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md) a um configurador de qualificações.

### <a name="4---storage-account"></a>4-conta de armazenamento

Em sua conta de armazenamento do Azure, Azure Search cria um contêiner de BLOB ou tabelas, dependendo de como você configura um configurador de habilidades. Se seus dados forem provenientes do armazenamento de BLOBs ou de tabelas do Azure, você já estará definido. Caso contrário, será necessário criar uma conta de armazenamento do Azure. As tabelas e os objetos no armazenamento do Azure contêm os documentos aprimorados criados pelo pipeline de indexação baseado em ia.

A conta de armazenamento é especificada no deskillr. No `api-version=2019-05-06-Preview`, uma definição de conconhecedor inclui uma definição de repositório de conhecimento para que você possa fornecer informações de conta.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5-acessar e consumir

Depois que os aprimoramentos existem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de BLOBs ou tabelas do Azure pode ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um início:

+ [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) para exibir conteúdo e estrutura de documentos aprimorados. Considere isso como sua ferramenta de linha de base para exibir o conteúdo da loja de conhecimento.

+ [Power bi com Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) para consultas em linguagem natural ou use as ferramentas de relatório e análise se você tiver dados numéricos.

+ [Azure data Factory](https://docs.microsoft.com/azure/data-factory/) para manipulação adicional.

+ Azure Search índice para pesquisa de texto completo sobre o conteúdo que você indexou usando a [pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Persistência de documentos

Na conta de armazenamento, os aprimoramentos podem ser expressos como tabelas no armazenamento de tabelas do Azure ou como objetos no armazenamento de BLOBs do Azure. Lembre-se de que os aprimoramentos, uma vez armazenados, podem ser usados como uma fonte para carregar dados em outros bancos e ferramentas,

+ O armazenamento de tabela é útil quando você deseja uma representação com reconhecimento de esquema dos dados em formato de tabela. Se você quiser remodelar ou recombinar elementos de novas maneiras, o armazenamento de tabela fornecerá a granularidade necessária.

+ O armazenamento de BLOBs cria uma representação JSON completa de cada documento. Você pode usar ambas as opções de armazenamento em um conjunto de qualificações para obter uma gama completa de expressões.

+ Azure Search mantém o conteúdo em um índice. Se seu cenário não estiver relacionado à pesquisa, por exemplo, se seu objetivo for análise em outra ferramenta, você poderá excluir o índice criado pelo pipeline. Mas você também pode manter o índice e usar uma ferramenta interna como o [Gerenciador de pesquisa](search-explorer.md) como um terceiro meio (por trás Gerenciador de armazenamento e seu aplicativo de análise) para interagir com seu conteúdo.

Junto com o conteúdo do documento, os documentos aprimorados incluem os metadados da versão do conjunto de qualificações que produziu os aprimoramentos.  

## <a name="inside-a-knowledge-store"></a>Dentro de uma loja de conhecimento

A loja de conhecimento consiste em um cache de anotação e projeções. O *cache* é usado pelo serviço internamente para armazenar em cache os resultados de habilidades e controlar as alterações. Uma *projeção* define o esquema e a estrutura dos aprimoramentos que correspondem ao uso pretendido. Há um cache por repositório de conhecimento, mas várias projeções. 

O cache é sempre um contêiner de BLOB, mas as projeções podem ser articuladas como tabelas ou objetos:

+ Como um objeto, a projeção é mapeada para o armazenamento de BLOBs, onde a projeção é salva em um contêiner, dentro dos quais são os objetos ou representações hierárquicas em JSON para cenários como um pipeline de ciência de dados.

+ Como uma tabela, a projeção é mapeada para o armazenamento de tabela. Uma representação tabular preserva relações para cenários como análise de dados ou exportação como quadros de dados para aprendizado de máquina. As projeções aprimoradas podem ser facilmente importadas para outros armazenamentos de dados. 

Você pode criar várias projeções em uma loja de conhecimento para acomodar vários Constituencies em sua organização. Um desenvolvedor pode precisar de acesso à representação JSON completa de um documento aprimorado, enquanto os cientistas ou analistas de dados podem querer estruturas de dados granulares ou modulares moldadas por seu qualificante.

Por exemplo, se uma das metas do processo de enriquecimento for também criar um conjunto de dados usado para treinar um modelo, a projeção dos dados para o repositório de objetos seria uma maneira de usar o dado em seus pipelines de ciência de dados. Como alternativa, se você quiser criar um painel de Power BI rápida com base nos documentos aprimorados, a projeção tabular funcionaria bem.

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

Recomendamos o serviço gratuito para fins de aprendizado, mas lembre-se de que o número de transações gratuitas é limitado a 20 documentos por dia, por assinatura.

Ao usar vários serviços, crie todos os seus serviços na mesma região para obter o melhor desempenho e para minimizar os custos. Você não é cobrado pela largura de banda para dados de entrada ou dados de saída que vão para outro serviço na mesma região.

**Etapa 1: [Criar um recurso de Azure Search](search-create-service-portal.md)** 

**Etapa 2: [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Etapa 3: [Criar um recurso de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Etapa 4: Introdução ao [portal](cognitive-search-quickstart-blob.md) -ou-introdução [aos dados de exemplo usando REST e postmaster](knowledge-store-howto.md)** 

Você pode usar o `api-version=2019-05-06-Preview` REST para construir um pipeline baseado em ia que inclua o repositório de conhecimento. Na API de visualização mais recente, o objeto skillset fornece `knowledgeStore` a definição.

## <a name="takeaways"></a>Conclusões

A loja de conhecimento oferece uma variedade de benefícios, incluindo, mas não limitado, a habilitação do uso de documentos aprimorados em cenários diferentes de pesquisa, controles de custo e gerenciamento de descompasso em seu processo de enriquecimento. Esses recursos estão disponíveis para uso simples, adicionando uma conta de armazenamento ao seu Skills e usando a linguagem de expressão atualizada, conforme descrito em como começar a usar a [loja de conhecimento](knowledge-store-howto.md). 

## <a name="next-steps"></a>Passos Seguintes

A abordagem mais simples para a criação de documentos aprimorados é por meio do assistente de **importação de dados** .

> [!div class="nextstepaction"]
> [Quickstart: Instruções de pesquisa cognitiva em um portal](cognitive-search-quickstart-blob.md)
