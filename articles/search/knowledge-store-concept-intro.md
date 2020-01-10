---
title: Introdução à loja de conhecimento (visualização)
titleSuffix: Azure Cognitive Search
description: Envie documentos aprimorados para o armazenamento do Azure, onde você pode exibir, remodelar e consumir documentos aprimorados no Azure Pesquisa Cognitiva e em outros aplicativos. Esta funcionalidade está em pré-visualização pública.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 9a6fa62384615f60da88bb41da8ad3538d34e62a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754104"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução às lojas de conhecimento no Azure Pesquisa Cognitiva

> [!IMPORTANT] 
> a loja de conhecimento está atualmente em visualização pública. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de visualização. Atualmente, há suporte ao portal limitado e não há suporte para o SDK do .NET.

O repositório de conhecimento é um recurso do Azure Pesquisa Cognitiva que persiste a saída de um [pipeline de enriquecimento de ia](cognitive-search-concept-intro.md) para análise independente ou processamento downstream. Um *documento aprimorado* é a saída de um pipeline, criada a partir do conteúdo que foi extraído, estruturado e analisado usando processos de ia. Em um pipeline de ia padrão, os documentos aprimorados são transitórios, usados somente durante a indexação e, em seguida, descartados. Com a loja de conhecimento, os documentos aprimorados são preservados. 

Se você usou habilidades cognitivas no passado, já sabe que o *habilidades* move um documento por uma sequência de aprimoramentos. O resultado pode ser um índice de pesquisa ou (novo nesta visualização) projeções em uma loja de conhecimento. As duas saídas, índice de pesquisa e repositório de conhecimento, são produtos do mesmo pipeline; derivado das mesmas entradas, mas resultando na saída que é estruturada, armazenada e usada de maneiras muito diferentes.

Fisicamente, uma loja de conhecimento é o [armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview), o armazenamento de tabelas do Azure, o armazenamento de BLOBs do Azure ou ambos. Qualquer ferramenta ou processo que possa se conectar ao armazenamento do Azure pode consumir o conteúdo de uma loja de conhecimento.

![Repositório de conhecimento no diagrama de pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Repositório de conhecimento no diagrama de pipeline")

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimento fornece estrutura, contexto e conteúdo real, obtidas por meio de arquivos de dados não estruturados e semiestruturados, como BLOBs, arquivos de imagem que passaram por análise ou até mesmo dados estruturados, remodelados em novos formulários. Em um [passo a](knowledge-store-create-rest.md)passo, você pode ver em primeira mão como um documento de JSON denso é particionado em subestruturações, reconstituído em novas estruturas e disponibilizado para processos downstream, como o aprendizado de máquina e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um pipeline de enriquecimento de ia pode produzir, o potencial real de uma loja de conhecimento é a capacidade de remodelar dados. Você pode começar com um habilidades básicas e iterar sobre ele para adicionar níveis cada vez maiores de estrutura, que você pode combinar em novas estruturas, consumíveis em outros aplicativos além do Azure Pesquisa Cognitiva.

Enumerado, os benefícios da loja de conhecimento incluem o seguinte:

+ Consuma documentos aprimorados em [ferramentas de análise e relatórios](#tools-and-apps) diferentes de pesquisa. Power BI com Power Query é uma opção atraente, mas qualquer ferramenta ou aplicativo que possa se conectar ao armazenamento do Azure pode efetuar pull de uma loja de conhecimento que você criar.

+ Refinar um pipeline de indexação de AI durante a depuração de etapas e de definições de habilidades. Uma loja de conhecimento mostra o produto de uma definição de conconhecedor em um pipeline de indexação de AI. Você pode usar esses resultados para criar um melhor conhecimento, pois você pode ver exatamente de que forma os aprimoramentos se parecem. Você pode usar [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no armazenamento do Azure para exibir o conteúdo de uma loja de conhecimento.

+ Formate os dados em novos formulários. A remodelagem é codificados em habilidades, mas o ponto é que um con agora pode fornecer esse recurso. A [habilidade do modelador](cognitive-search-skill-shaper.md) no Azure pesquisa cognitiva foi estendida para acomodar essa tarefa. A remodelagem permite que você defina uma projeção que se alinhe com o uso pretendido dos dados, preservando as relações.

> [!Note]
> Novidade para aprimoramento de ia e habilidades cognitivas? O Azure Pesquisa Cognitiva integra-se com recursos de linguagem e visão de serviços cognitivas para extrair e enriquecer dados de origem usando OCR (reconhecimento óptico de caracteres) em arquivos de imagem, reconhecimento de entidade e extração de frases-chave de arquivos de texto e muito mais. Para obter mais informações, consulte [enriquecimento de ia no Azure pesquisa cognitiva](cognitive-search-concept-intro.md).

## <a name="physical-storage"></a>Armazenamento físico

A expressão física de uma loja de conhecimento é articulada por meio do elemento `projections` de uma definição de `knowledgeStore` em um skillset. A projeção define uma estrutura da saída para que ela corresponda ao uso pretendido.

As projeções podem ser articuladas como tabelas, objetos ou arquivos.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

O tipo de projeção que você especificar nesta estrutura determina o tipo de armazenamento usado pela loja de conhecimento.

+ O armazenamento de tabela é usado quando você define `tables`. Defina uma projeção de tabela quando precisar de estruturas de relatório tabular para entradas em ferramentas analíticas ou exportar como quadros de dados para outros armazenamentos de dados. Você pode especificar várias `tables` para obter um subconjunto ou uma seção cruzada de documentos aprimorados. No mesmo grupo de projeção, as relações de tabela são preservadas para que você possa trabalhar com todas elas.

+ O armazenamento de BLOBs é usado quando você define `objects` ou `files`. A representação física de um `object` é uma estrutura de JSON hierárquica que representa um documento aprimorado. Uma `file` é uma imagem extraída de um documento, transferida intacta para o armazenamento de BLOBs.

Um único objeto de projeção contém um conjunto de `tables`, `objects`, `files`e para muitos cenários, a criação de uma projeção pode ser suficiente. 

No entanto, é possível criar vários conjuntos de `table`-`object`-projeções `file` e você pode fazer isso se desejar relações de dados diferentes. Dentro de um conjunto, os dados estão relacionados, supondo que essas relações existam e possam ser detectadas. Se você criar conjuntos adicionais, os documentos em cada grupo nunca serão relacionados. Um exemplo de uso de vários grupos de projeção pode ser se você quiser que os mesmos dados sejam projetados para uso com seu sistema online e ele precisar ser representado de uma maneira específica, você também deseja que os mesmos dados sejam projetados para uso em um pipeline de ciência de dados que é representado maneira.

## <a name="requirements"></a>Requisitos 

O [armazenamento do Azure](https://docs.microsoft.com/azure/storage/) é necessário. Ele fornece armazenamento físico. Você pode usar o armazenamento de BLOBs, o armazenamento de tabelas ou ambos. O armazenamento de BLOBs é usado para documentos inaprimorados intactos, geralmente quando a saída vai para processos de downstream. O armazenamento de tabela é para fatias de documentos aprimorados, normalmente usados para análise e relatórios.

O [qualificable](cognitive-search-working-with-skillsets.md) é necessário. Ele contém a definição de `knowledgeStore` e determina a estrutura e a composição de um documento aprimorado. Você não pode criar um repositório de conhecimento usando um conconhecedor de qualificações vazio. Você deve ter pelo menos uma habilidade em um skillset.

O [indexador](search-indexer-overview.md) é necessário. Um configurador de qualificações é invocado por um indexador, que orienta a execução. Os indexadores vêm com seu próprio conjunto de requisitos e atributos. Vários desses atributos têm um rumo direto em uma loja de conhecimento:

+ Os indexadores exigem uma [fonte de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources) (o pipeline que, por fim, cria o repositório de conhecimento começa obtendo dados de uma fonte com suporte no Azure). 

+ Os indexadores exigem um índice de pesquisa. Um indexador requer que você forneça um esquema de índice, mesmo que você nunca planeje usá-lo. Um índice mínimo tem um campo de cadeia de caracteres designado como a chave.

+ Os indexadores fornecem mapeamentos de campo opcionais, usados para o alias de um campo de origem para um campo de destino. Se um mapeamento de campo padrão precisar de modificação (para usar um nome ou tipo diferente), você poderá criar um [mapeamento de campo](search-indexer-field-mappings.md) dentro de um indexador. Para a saída da loja de conhecimento, o destino pode ser um campo em um objeto ou tabela de BLOB.

+ Os indexadores têm agendas e outras propriedades, como mecanismos de detecção de alteração fornecidos por várias fontes de dados, também podem ser aplicados a uma loja de conhecimento. Por exemplo, você pode [agendar](search-howto-schedule-indexers.md) o enriquecimento em intervalos regulares para atualizar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar uma loja de conhecimento

Para criar a loja de conhecimento, use o portal ou a API REST de visualização (`api-version=2019-05-06-Preview`).

### <a name="use-the-azure-portal"></a>Utilizar o Portal do Azure

O assistente de **importação de dados** inclui opções para criar uma loja de conhecimento. Para a exploração inicial, [crie seu primeiro armazenamento de conhecimento em quatro etapas](knowledge-store-connect-power-bi.md).

1. Selecione uma fonte de dados com suporte.

1. Especificar enriquecimento: anexe um recurso, selecione habilidades e especifique uma loja de conhecimento. 

1. Crie um esquema de índice. O assistente requer e pode inferir um para você.

1. Execute o assistente. A extração, o enriquecimento e o armazenamento ocorrem nesta última etapa.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Usar a API REST de criação e a versão prévia

Um `knowledgeStore` é definido em um configurador [que, por](cognitive-search-working-with-skillsets.md)sua vez, é invocado por um [indexador](search-indexer-overview.md). Durante o enriquecimento, o Azure Pesquisa Cognitiva cria um espaço em sua conta de armazenamento do Azure e projeta os documentos aprimorados como BLOBs ou tabelas, dependendo de sua configuração.

Atualmente, a API REST de visualização é o único mecanismo pelo qual você pode criar uma loja de conhecimento programaticamente. Uma maneira fácil de explorar é [criar sua primeira loja de conhecimento usando o postmaster e a API REST](knowledge-store-create-rest.md).

O conteúdo de referência para esse recurso de visualização está localizado na seção [referência de API](#kstore-rest-api) deste artigo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como se conectar com ferramentas e aplicativos

Depois que os aprimoramentos existem no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao armazenamento de BLOBs ou tabelas do Azure pode ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um início:

+ [Gerenciador de armazenamento](knowledge-store-view-storage-explorer.md) para exibir conteúdo e estrutura de documentos aprimorados. Considere isso como sua ferramenta de linha de base para exibir o conteúdo da loja de conhecimento.

+ [Power bi](knowledge-store-connect-power-bi.md) para relatório e análise. 

+ [Azure data Factory](https://docs.microsoft.com/azure/data-factory/) para manipulação adicional.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Referência de API

Esta seção é uma versão do documento de referência [criar o Skills (API REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset) , modificada para incluir uma definição de `knowledgeStore`. 

### <a name="example---knowledgestore-embedded-in-a-skillset"></a>Exemplo – knowledgeStore inserido em um skillset

O exemplo a seguir mostra `knowledgeStore` na parte inferior de uma definição de conconhecimento. 

* Use **post** ou **Put** para formular a solicitação.
* Use a versão `api-version=2019-05-06-Preview` da API REST para acessar a funcionalidade de armazenamento de conhecimento. 

```http
POST https://[servicename].search.windows.net/skillsets?api-version=2019-05-06-Preview
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é um documento JSON que define um qualificable, que inclui `knowledgeStore`.

```json
{
  "name": "my-skillset-name",
  "description": "Extract organization entities and generate a positive-negative sentiment score from each document.",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations",
          "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "score",
          "targetName": "mySentiment"
        }
      ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<YOUR-COGNITIVE-SERVICES-KEY>"
    },
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [  
                { "tableName": "Organizations", "generatedKeyName": "OrganizationId", "source": "/document/organizations*"}, 
                { "tableName": "Sentiment", "generatedKeyName": "SentimentId", "source": "/document/mySentiment"}
                ], 
                "objects": [ ], 
                "files": [  ]       
            }    
        ]     
    } 
}
```

### <a name="request-body-syntax"></a>Sintaxe do corpo da solicitação  

O JSON a seguir especifica um `knowledgeStore`, que faz parte de um [`skillset`](https://docs.microsoft.com/rest/api/searchservice/create-skillset), que é invocado por um `indexer` (não mostrado). Se você já estiver familiarizado com o enriquecimento de ia, um configurador de qualificações determinará a composição de um documento aprimorado. Um configurador de qualificações deve conter pelo menos uma habilidade, provavelmente uma habilidade de forma, se você estiver modulating estruturas de dados.

A sintaxe para estruturar a carga de solicitação é a seguinte.

```json
{   
    "name" : "Required for POST, optional for PUT requests which sets the name on the URI",  
    "description" : "Optional. Anything you want, or null",  
    "skills" : "Required. An array of skills. Each skill has an odata.type, name, input and output parameters",
    "cognitiveServices": "A key to Cognitive Services, used for billing.",
    "knowledgeStore": { 
        "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
        "projections": [ 
            { 
                "tables": [ 
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    { "tableName": "<NAME>", "generatedKeyName": "<FIELD-NAME>", "source": "<DOCUMENT-PATH>" },
                    . . .
                ], 
                "objects": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>", 
                    "source": "<DOCUMENT-PATH>", 
                    }
                ], 
                "files": [ 
                    {
                    "storageContainer": "<BLOB-CONTAINER-NAME>",
                    "source": "/document/normalized_images/*"
                    }
                ]  
            },
            {
                "tables": [ ],
                "objects": [ ],
                "files":  [ ]
            }  
        ]     
    } 
}
```

Uma `knowledgeStore` tem duas propriedades: uma `storageConnectionString` a uma conta de armazenamento do Azure e `projections` que define o armazenamento físico. Você pode usar qualquer conta de armazenamento, mas é econômico usar os serviços na mesma região.

Uma coleção de `projections` contém objetos de projeção. Cada objeto de projeção deve ter `tables`, `objects`, `files` (um de cada), que são especificados ou nulos. A sintaxe acima mostra dois objetos, um totalmente especificado e o outro totalmente nulo. Em um objeto de projeção, uma vez expresso no armazenamento, todas as relações entre os dados, se detectadas, são preservadas. 

Crie quantos objetos de projeção forem necessários para dar suporte ao isolamento e a cenários específicos (por exemplo, estruturas de dados usadas para exploração, versus aqueles necessários em uma carga de trabalho de ciência de dados). Você pode obter isolamento e personalização para cenários específicos definindo `source` e `storageContainer` ou `table` para valores diferentes dentro de um objeto. Para obter mais informações e exemplos, consulte [trabalhando com projeções em uma loja de conhecimento](knowledge-store-projection-overview.md).

|Propriedade      | Aplica-se a | Descrição|  
|--------------|------------|------------|  
|`storageConnectionString`| `knowledgeStore` | Necessário. Neste formato: `DefaultEndpointsProtocol=https;AccountName=<ACCOUNT-NAME>;AccountKey=<ACCOUNT-KEY>;EndpointSuffix=core.windows.net`|  
|`projections`| `knowledgeStore` | Necessário. Uma coleção de objetos de propriedade que consiste em `tables`, `objects`, `files` e suas respectivas propriedades. As projeções não utilizadas podem ser definidas como NULL.|  
|`source`| Todas as projeções| O caminho para o nó da árvore de enriquecimento que é a raiz da projeção. Esse nó é a saída de qualquer uma das habilidades no skillset. Os caminhos começam com `/document/`, representando o documento aprimorado, mas podem ser estendidos para `/document/content/` ou para nós dentro da árvore de documentos. Exemplos: `/document/countries/*` (todos os países) ou `/document/countries/*/states/*` (todos os Estados em todos os países). Para obter mais informações sobre caminhos de documento, consulte [conceitos e composição do Skills](cognitive-search-working-with-skillsets.md).|
|`tableName`| `tables`| Uma tabela a ser criada no armazenamento de tabelas do Azure. |
|`storageContainer`| `objects`, `files`| Nome de um contêiner a ser criado no armazenamento de BLOBs do Azure. |
|`generatedKeyName`| `tables`| Uma coluna criada na tabela que identifica exclusivamente um documento. O pipeline de enriquecimento popula essa coluna com valores gerados.|


### <a name="response"></a>Resposta  

 Para uma solicitação bem-sucedida, você deve ver o código de status "201 criado". Por padrão, o corpo da resposta conterá o JSON para a definição do Configurador de habilidades que foi criada. Lembre-se de que a loja de conhecimento não é criada até que você invoque um indexador que referencie esse concordador.

## <a name="next-steps"></a>Passos seguintes

A loja de conhecimento oferece persistência de documentos aprimorados, útil ao criar um qualificable ou à criação de novas estruturas e conteúdo para consumo por qualquer aplicativo cliente capaz de acessar uma conta de armazenamento do Azure.

A abordagem mais simples para a criação de documentos aprimorados é [por meio do portal](knowledge-store-create-portal.md), mas você também pode usar o postmaster e a API REST, que é mais útil se você quiser obter informações sobre como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimento usando o postmaster e o REST](knowledge-store-create-rest.md)
