---
title: Introdução à loja de conhecimento (visualização)
titleSuffix: Azure Cognitive Search
description: Envie documentos aprimorados para o armazenamento do Azure, onde você pode exibir, remodelar e consumir documentos aprimorados no Azure Pesquisa Cognitiva e em outros aplicativos.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e6d20a1eca7a6b3281e33d8534ab3456e79ccdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485092"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução às lojas de conhecimento no Azure Pesquisa Cognitiva

> [!Note]
> A loja de conhecimento está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece esse recurso. Não há suporte para o SDK do .NET no momento.
>

O repositório de conhecimento é um recurso do Azure Pesquisa Cognitiva que persiste a saída de um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) para análise posterior ou outro processamento downstream. Um *documento aprimorado* é a saída de um pipeline, criada a partir do conteúdo que foi extraído, estruturado e analisado usando processos de ia. Em um pipeline de ia padrão, os documentos aprimorados são transitórios, usados somente durante a indexação e, em seguida, descartados. Com a loja de conhecimento, os documentos aprimorados são preservados. 

Se você usou habilidades cognitivas com o Azure Pesquisa Cognitiva no passado, já sabe que o *habilidades* move um documento por uma sequência de aprimoramentos. O resultado pode ser um índice de pesquisa ou (novo nesta visualização) projeções em uma loja de conhecimento. As duas saídas, o índice de pesquisa e o repositório de conhecimento, compartilham o mesmo conteúdo, mas são armazenados e usados de maneiras muito diferentes.

Fisicamente, uma loja de conhecimento é o [armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), o armazenamento de tabelas do Azure, o armazenamento de BLOBs do Azure ou ambos. Qualquer ferramenta ou processo que possa se conectar ao armazenamento do Azure pode consumir o conteúdo de uma loja de conhecimento.

![Repositório de conhecimento no diagrama de pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Repositório de conhecimento no diagrama de pipeline")

Para usar o repositório de conhecimento, adicione um elemento de `knowledgeStore` a um conconhecedor que defina operações passo-a-bit em um pipeline de indexação. Durante a execução, o Azure Pesquisa Cognitiva cria um espaço em sua conta de armazenamento do Azure e projeta os documentos aprimorados como BLOBs ou tabelas, dependendo de sua configuração.

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimento fornece estrutura, contexto e conteúdo real, obtidas de arquivos de dados não estruturados e semiestruturados, como BLOBs, arquivos de imagem que têm análise passou ou até mesmo dados estruturados que são remodelados em novos formulários. Em um [passo a](knowledge-store-howto.md)passo, você pode ver a primeira mão de como um documento JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream como aprendizado de máquina e ciência de dados cargas.

Embora seja útil ver o que um pipeline de enriquecimento de ia pode produzir, o poder real da loja de conhecimento é a capacidade de remodelar os dados. Você pode começar com um habilidades básicas e iterar sobre ele para adicionar níveis cada vez maiores de estrutura, que você pode combinar em novas estruturas, consumíveis em outros aplicativos além do Azure Pesquisa Cognitiva.

Enumerado, os benefícios da loja de conhecimento incluem o seguinte:

+ Consuma documentos aprimorados em [ferramentas de análise e relatórios](#tools-and-apps) diferentes de pesquisa. Power BI com Power Query é uma opção atraente, mas qualquer ferramenta ou aplicativo que possa se conectar ao armazenamento do Azure pode efetuar pull de uma loja de conhecimento que você criar.

+ Refinar um pipeline de indexação de AI durante a depuração de etapas e de definições de habilidades. Uma loja de conhecimento mostra o produto de uma definição de conconhecedor em um pipeline de indexação de AI. Você pode usar esses resultados para criar um melhor conhecimento, pois você pode ver exatamente de que forma os aprimoramentos se parecem. Você pode usar [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de uma loja de conhecimento.

+ Formate os dados em novos formulários. A remodelagem é codificados em habilidades, mas o ponto é que um con agora pode fornecer esse recurso. A [habilidade do modelador](cognitive-search-skill-shaper.md) no Azure pesquisa cognitiva foi estendida para acomodar essa tarefa. A remodelagem permite que você defina uma projeção que se alinhe com o uso pretendido dos dados, preservando as relações.

> [!Note]
> Novidade para aprimoramento de ia e habilidades cognitivas? O Azure Pesquisa Cognitiva integra-se com recursos de linguagem e visão de serviços cognitivas para extrair e enriquecer dados de origem usando OCR (reconhecimento óptico de caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, consulte [enriquecimento de ia no Azure pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Criando uma loja de conhecimento

Uma loja de conhecimento faz parte de um [qualificante](cognitive-search-working-with-skillsets.md)que, por sua vez, faz parte de um [indexador](search-indexer-overview.md). 

Nesta versão prévia, você pode criar uma loja de conhecimento usando a API REST e `api-version=2019-05-06-Preview`ou por meio do assistente de **importação de dados** no Portal.

### <a name="json-representation-of-a-knowledge-store"></a>Representação JSON de uma loja de conhecimento

O JSON a seguir especifica um `knowledgeStore`, que faz parte de um configurador de qualificações, que é invocado por um indexador (não mostrado). Se você já estiver familiarizado com o enriquecimento de ia, um configurador de qualificações determinará a criação, a organização e a substância de cada documento aprimorado. Um configurador de qualificações deve conter pelo menos uma habilidade, provavelmente uma habilidade de forma, se você estiver modulating estruturas de dados.

Uma `knowledgeStore` consiste em uma conexão e projeções. 

+ A conexão é para uma conta de armazenamento na mesma região que o Pesquisa Cognitiva do Azure. 

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

Se uma loja de conhecimento for uma saída de um pipeline de enriquecimento de ia, quais são as entradas? Os dados originais que você deseja extrair, enriquecer e eventualmente salvar em uma loja de conhecimento podem se originar de qualquer fonte de dados do Azure com suporte dos indexadores de pesquisa: 

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
| index | [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Um esquema que expressa um índice de pesquisa. Os campos no índice são mapeados para campos em dados de origem ou para campos fabricados durante a fase de enriquecimento (por exemplo, um campo para nomes de organização criados pelo reconhecimento de entidade). |
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

## <a name="next-steps"></a>Passos seguintes

A loja de conhecimento oferece persistência de documentos aprimorados, útil ao criar um qualificable ou à criação de novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é por meio do assistente de **importação de dados** , mas você também pode usar o postmaster e a API REST, o que é mais útil se você quiser obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimento usando o portal](knowledge-store-create-portal.md)
> [criar uma loja de conhecimento usando o postmaster e a API REST](knowledge-store-create-rest.md)
