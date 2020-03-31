---
title: Introdução à loja de conhecimento (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Envie documentos enriquecidos para o Azure Storage onde pode ver, remodelar e consumir documentos enriquecidos em Pesquisa Cognitiva Azure e noutras aplicações. Esta funcionalidade está em pré-visualização pública.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0ad780c04954c09ddfd432b3c7de3dc65f0841bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942985"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Introdução a lojas de conhecimento em Pesquisa Cognitiva Azure

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

A knowledge store é uma característica da Pesquisa Cognitiva Azure que persiste a produção de um oleoduto de enriquecimento de [IA](cognitive-search-concept-intro.md) para análise independente ou processamento a jusante. Um *documento enriquecido* é a saída de um oleoduto, criado a partir de conteúdos que foram extraídos, estruturados e analisados utilizando processos de IA. Num oleoduto normal de IA, os documentos enriquecidos são transitórios, utilizados apenas durante a indexação e depois descartados. Com a loja de conhecimento, os documentos enriquecidos são preservados. 

Se já usou habilidades cognitivas no passado, já sabe que as *habilidades* movem um documento através de uma sequência de enriquecimentos. O resultado pode ser um índice de pesquisa, ou (novas nesta pré-visualização) projeções numa loja de conhecimento. As duas saídas, índice de pesquisa e loja de conhecimento, são produtos do mesmo oleoduto; derivado das mesmas entradas, mas resultando numa saída estruturada, armazenada e usada de formas muito diferentes.

Fisicamente, uma loja de conhecimentos é [o Azure Storage,](https://docs.microsoft.com/azure/storage/common/storage-account-overview)ou o armazenamento de mesa Azure, armazenamento Azure Blob, ou ambos. Qualquer ferramenta ou processo que possa ligar-se ao Armazenamento Azure pode consumir o conteúdo de uma loja de conhecimento.

![Loja de conhecimento no diagrama do oleoduto](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Loja de conhecimento no diagrama do oleoduto")

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimentos dá-lhe estrutura, contexto e conteúdo real - recolhido sem estruturae e semi-estruturado ficheiros de dados como bolhas, ficheiros de imagem que foram submetidos a análise, ou mesmo dados estruturados, remodelados em novas formas. Numa [passagem passo a passo,](knowledge-store-create-rest.md)pode ver em primeira mão como um denso documento JSON é dividido em subestruturas, reconstituído em novas estruturas, e de outra forma disponibilizado para processos a jusante como machine learning e data science workloads.

Embora seja útil ver o que um oleoduto de enriquecimento de IA pode produzir, o potencial real de uma loja de conhecimento é a capacidade de remodelar dados. Você pode começar com um skillset básico, e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que você pode então combinar em novas estruturas, consumível em outras apps além de Azure Cognitive Search.

Enumerados, os benefícios da loja de conhecimento incluem o seguinte:

+ Consumir documentos enriquecidos em [análise e ferramentas de reporte](#tools-and-apps) que não seja pesquisa. Power BI com Power Query é uma escolha convincente, mas qualquer ferramenta ou app que possa ligar-se ao Azure Storage pode retirar de uma loja de conhecimento que cria.

+ Refinar um gasoduto de indexação de IA enquanto depura passos e definições de skillset. Uma loja de conhecimentos mostra-lhe o produto de uma definição de skillset num pipeline de indexação de IA. Você pode usar esses resultados para projetar uma habilidade melhor porque você pode ver exatamente como os enriquecimentos são. Pode utilizar o [Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no Armazenamento Azure para ver o conteúdo de uma loja de conhecimento.

+ Moldar os dados em novas formas. A remodelação é codificada em habilidades, mas a questão é que um skillset pode agora fornecer esta capacidade. A [habilidade shaper](cognitive-search-skill-shaper.md) em Azure Cognitive Search foi estendida para acomodar esta tarefa. A remodelação permite-lhe definir uma projeção que se alinha com a utilização pretendida dos dados, preservando as relações.

> [!Note]
> Novo no enriquecimento de IA e habilidades cognitivas? A Pesquisa Cognitiva Azure integra-se com funcionalidades de Visão e Linguagem de Serviços Cognitivos para extrair e enriquecer dados de origem usando o Reconhecimento ótico de caracteres (OCR) sobre ficheiros de imagem, reconhecimento de entidades e extração de frases-chave de ficheiros de texto, e muito mais. Para mais informações, consulte o [enriquecimento de IA em Pesquisa Cognitiva Azure.](cognitive-search-concept-intro.md)

## <a name="physical-storage"></a>Armazenamento físico

A expressão física de uma loja `projections` de conhecimento `knowledgeStore` é articulada através do elemento de uma definição num Skillset. A projeção define uma estrutura da saída de modo a corresponder à utilização pretendida.

As projeções podem ser articuladas como mesas, objetos ou ficheiros.

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

O tipo de projeção que especifica nesta estrutura determina o tipo de armazenamento utilizado pela loja de conhecimento.

+ O armazenamento de mesa `tables`é utilizado quando se define . Defina uma projeção de tabela quando necessitar de estruturas tabulares de reporte para inputs a ferramentas analíticas ou exportar como quadros de dados para outras lojas de dados. Pode especificar `tables` vários para obter uma secção de subconjunto ou cruz de documentos enriquecidos. Dentro do mesmo grupo de projeção, as relações de mesa são preservadas para que possa trabalhar com todos eles.

+ O armazenamento de bolhas `objects` é `files`utilizado quando define ou . A representação `object` física de um é uma estrutura hierárquica jSON que representa um documento enriquecido. A `file` é uma imagem extraída de um documento, transferida intacta para o armazenamento blob.

Um único objeto de `tables`projeção contém um conjunto de, `objects`e `files`para muitos cenários, criar uma projeção pode ser suficiente. 

No entanto, é possível `table` - `object` - `file` criar vários conjuntos de projeções, e pode fazê-lo se quiser relações de dados diferentes. Dentro de um conjunto, os dados estão relacionados, assumindo que essas relações existem e podem ser detetadas. Se criar conjuntos adicionais, os documentos de cada grupo nunca estão relacionados. Um exemplo de utilização de vários grupos de projeção pode ser se quiser os mesmos dados projetados para uso com o seu sistema online e precisa de ser representado de uma forma específica, também quer os mesmos dados projetados para uso num pipeline de ciência de dados que está representado diferente.

## <a name="requirements"></a>Requisitos 

É necessário [armazenamento azure.](https://docs.microsoft.com/azure/storage/) Fornece armazenamento físico. Pode utilizar o armazenamento blob, o armazenamento de mesa ou ambos. O armazenamento de blob é usado para documentos enriquecidos intactos, geralmente quando a saída vai para processos a jusante. O armazenamento de mesa supor fatias de documentos enriquecidos, comumente utilizados para análise e reporte.

[É](cognitive-search-working-with-skillsets.md) necessário um conjunto de habilidades. Contém a `knowledgeStore` definição, e determina a estrutura e composição de um documento enriquecido. Não é possível criar uma loja de conhecimentos usando uma habilidade vazia. Deve ter pelo menos uma habilidade numa habilidade.

[O indexante](search-indexer-overview.md) é necessário. Uma habilidade é invocada por um indexante, que impulsiona a execução. Os indexadores vêm com o seu próprio conjunto de requisitos e atributos. Vários destes atributos têm uma influência direta numa loja de conhecimento:

+ Os indexadores requerem uma fonte de [dados Azure suportada](search-indexer-overview.md#supported-data-sources) (o pipeline que acaba por criar a loja de conhecimento começa por retirar dados de uma fonte apoiada no Azure). 

+ Os indexadores requerem um índice de pesquisa. Um indexante requer que forneça um esquema de índice, mesmo que nunca planeie usá-lo. Um índice mínimo tem um campo de cordas, designado como a chave.

+ Os indexadores fornecem mapeamentos de campo opcionais, usados para alitar um campo de origem para um campo de destino. Se um mapeamento de campo predefinido necessitar de modificação (para usar um nome ou tipo diferente), pode criar um [mapeamento](search-indexer-field-mappings.md) de campo dentro de um indexante. Para a saída da loja de conhecimento, o destino pode ser um campo em um objeto ou mesa blob.

+ Os indexadores têm horários e outras propriedades, tais como mecanismos de deteção de alterações fornecidos por várias fontes de dados, também podem ser aplicados a uma loja de conhecimento. Por exemplo, pode [agendar](search-howto-schedule-indexers.md) o enriquecimento em intervalos regulares para refrescar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar uma loja de conhecimento

Para criar uma loja de conhecimento, utilize`api-version=2019-05-06-Preview`o portal ou a pré-visualização REST API ( ).

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O assistente de **dados de Importação** inclui opções para a criação de uma loja de conhecimento. Para a exploração inicial, crie a sua primeira loja de [conhecimentos em quatro passos.](knowledge-store-connect-power-bi.md)

1. Selecione uma fonte de dados suportada.

1. Especificar o enriquecimento: anexe um recurso, selecione competências e especifique uma loja de conhecimentos. 

1. Crie um esquema de índice. O feiticeiro requer e pode inferir um para si.

1. Corre o feiticeiro. Extração, enriquecimento e armazenamento ocorrem neste último passo.

### <a name="use-create-skillset-and-the-preview-rest-api"></a>Use Criar Skillset e a pré-visualização REST API

A `knowledgeStore` é definido dentro de um [skillset](cognitive-search-working-with-skillsets.md), que por sua vez é invocado por um [indexante](search-indexer-overview.md). Durante o enriquecimento, a Azure Cognitive Search cria um espaço na sua conta de Armazenamento Azure e projeta os documentos enriquecidos como bolhas ou em tabelas, dependendo da sua configuração.

Atualmente, a api de pré-visualização REST API é o único mecanismo através do qual pode criar uma loja de conhecimentos programáticamente. Uma maneira fácil de explorar é criar a sua primeira loja de [conhecimento usando o Carteiro e a API REST](knowledge-store-create-rest.md).

O conteúdo de referência para esta função de pré-visualização está localizado na secção de referência da [API](#kstore-rest-api) deste artigo. 

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como se conectar com ferramentas e aplicativos

Uma vez que os enriquecimentos existam no armazenamento, qualquer ferramenta ou tecnologia que se ligue ao armazenamento de Blob Azure ou mesa pode ser usada para explorar, analisar ou consumir o conteúdo. A seguinte lista é um começo:

+ [Explorador de Armazenamento](knowledge-store-view-storage-explorer.md) para visualizar estrutura e conteúdo de documentos enriquecidos. Considere isto como a sua ferramenta de base para visualizar conteúdos de lojas de conhecimento.

+ [Power BI](knowledge-store-connect-power-bi.md) para reporte e análise. 

+ Fábrica de [Dados Azure](https://docs.microsoft.com/azure/data-factory/) para mais manipulação.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Referência da API

A versão `2019-05-06-Preview` REST API fornece loja de conhecimento através de definições adicionais sobre habilidades. Além da referência, consulte Criar uma loja de [conhecimentos utilizando o Carteiro](knowledge-store-create-rest.md) para obter detalhes sobre como ligar para as APIs.

+ [Criar Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-skillset) 
+ [Atualização Skillset (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) 


## <a name="next-steps"></a>Passos seguintes

A Knowledge Store oferece persistência de documentos enriquecidos, úteis na conceção de um conjunto de competências, ou a criação de novas estruturas e conteúdos para consumo por qualquer aplicação de cliente capaz de aceder a uma conta de Armazenamento Azure.

A abordagem mais simples para a criação de documentos enriquecidos é [através do portal](knowledge-store-create-portal.md), mas também pode usar o Carteiro e a Rest API, o que é mais útil se quiser saber como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimentos usando o Carteiro e o REST](knowledge-store-create-rest.md)

Para saber mais sobre projeções, as capacidades e como [as define num skillset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções em uma loja de conhecimento](knowledge-store-projection-overview.md)

Para um tutorial que cobre projeções avançadas conceitos como corte, formação inline e relacionamentos, comece com [projeções de definição em uma loja](knowledge-store-projections-examples.md) de conhecimento

> [!div class="nextstepaction"]
> [Defina projeções numa loja de conhecimento](knowledge-store-projections-examples.md)