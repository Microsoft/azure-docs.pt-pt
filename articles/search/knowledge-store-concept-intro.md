---
title: Conceitos de loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Envie documentos enriquecidos para o Azure Storage onde pode ver, remodelar e consumir documentos enriquecidos na Pesquisa Cognitiva do Azure e noutras aplicações.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88924304"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Loja de conhecimento em Azure Cognitive Search

A loja de conhecimento é uma característica da Azure Cognitive Search que persiste na saída de um oleoduto de [enriquecimento](cognitive-search-concept-intro.md) de IA para análise independente ou processamento a jusante. Um *documento enriquecido* é a produção de um pipeline, criado a partir de conteúdos extraídos, estruturados e analisados através de processos de IA. Num gasoduto de IA padrão, os documentos enriquecidos são transitórios, utilizados apenas durante a indexação e depois descartados. A escolha de criar uma loja de conhecimento permitir-lhe-á preservar os documentos enriquecidos. 

Se já usou habilidades cognitivas no passado, já sabe que *as habilidades* movem um documento através de uma sequência de enriquecimentos. O resultado pode ser um índice de pesquisa, ou projeções numa loja de conhecimento. As duas saídas, índice de pesquisa e loja de conhecimento, são produtos do mesmo oleoduto; derivado das mesmas entradas, mas resultando numa saída estruturada, armazenada e usada de formas muito diferentes.

Fisicamente, uma loja de conhecimento é [a Azure Storage,](../storage/common/storage-account-overview.md)ou o armazenamento da Azure Table, o armazenamento Azure Blob, ou ambos. Qualquer ferramenta ou processo que possa ligar-se ao Azure Storage pode consumir o conteúdo de uma loja de conhecimento.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Loja de conhecimento em diagrama de pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Loja de conhecimento em diagrama de pipeline")

## <a name="benefits-of-knowledge-store"></a>Benefícios da loja de conhecimento

Uma loja de conhecimentos dá-lhe estrutura, contexto e conteúdo real - recolhidos de ficheiros de dados não estruturados e semi-estruturados como bolhas, ficheiros de imagem que foram submetidos a análises, ou mesmo dados estruturados, remodelados em novas formas. Numa [caminhada passo a passo,](knowledge-store-create-rest.md)pode ver em primeira mão como um documento JSON denso é dividido em subestruturas, reconstituído em novas estruturas, e de outra forma disponibilizado para processos a jusante como machine learning e cargas de trabalho de ciência de dados.

Embora seja útil ver o que um oleoduto de enriquecimento de IA pode produzir, o potencial real de uma loja de conhecimento é a capacidade de reformular dados. Você pode começar com um skillset básico, e depois iterar sobre ele para adicionar níveis crescentes de estrutura, que você pode então combinar em novas estruturas, consumíveis em outras apps além de Azure Cognitive Search.

Enumerados, os benefícios da loja de conhecimento incluem:

+ Consumir documentos enriquecidos em [ferramentas de análise e reporte que](#tools-and-apps) não a procura. Power BI com Power Query é uma escolha convincente, mas qualquer ferramenta ou app que possa ligar-se ao Azure Storage pode puxar de uma loja de conhecimento que cria.

+ Refinar um gasoduto de indexação de IA enquanto depura passos e definições de skillset. Uma loja de conhecimento mostra-lhe o produto de uma definição de skillset num oleoduto de indexação de IA. Você pode usar esses resultados para projetar um melhor skillset porque você pode ver exatamente como os enriquecimentos são. Pode utilizar [o Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) no Azure Storage para ver o conteúdo de uma loja de conhecimento.

+ Moldar os dados em novas formas. A remodelação é codificada em skillsets, mas a questão é que um skillset pode agora fornecer esta capacidade. A [habilidade do Shaper](cognitive-search-skill-shaper.md) em Azure Cognitive Search foi estendida para acomodar esta tarefa. A reformulação permite-lhe definir uma projeção que se alinha com a utilização pretendida dos dados, preservando as relações.

> [!Note]
> Novo no enriquecimento de IA e habilidades cognitivas? A Azure Cognitive Search integra-se com funcionalidades de Visão e Linguagem de Serviços Cognitivos para extrair e enriquecer dados de origem usando reconhecimento de caracteres óticos (OCR) sobre ficheiros de imagem, reconhecimento de entidades e extração de frases-chave a partir de ficheiros de texto, e muito mais. Para mais informações, consulte [o enriquecimento de IA na Azure Cognitive Search.](cognitive-search-concept-intro.md)

## <a name="physical-storage"></a>Armazenamento físico


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


A expressão física de uma loja de conhecimento é articulada através do `projections` elemento de uma `knowledgeStore` definição num Skillset. A projeção define uma estrutura da saída de modo a corresponder à utilização pretendida.

As projeções podem ser articuladas como tabelas, objetos ou ficheiros.

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

O tipo de projeção que se especifica nesta estrutura determina o tipo de armazenamento utilizado pela loja de conhecimento.

+ O armazenamento de mesa é utilizado quando se define `tables` . Defina uma projeção de tabela quando necessitar de estruturas de reporte tabular para entradas para ferramentas analíticas ou exportar como quadros de dados para outras lojas de dados. Pode especificar vários `tables` para obter um subconjunto ou secção transversal de documentos enriquecidos. Dentro do mesmo grupo de projeção, as relações de mesa são preservadas para que possa trabalhar com todas elas.

+ O armazenamento de bolhas é utilizado quando se define `objects` ou `files` . . A representação física de um `object` é uma estrutura hierárquica de JSON que representa um documento enriquecido. A `file` é uma imagem extraída de um documento, transferida intacta para o armazenamento da Blob.

Um único objeto de projeção contém um conjunto de `tables` `objects` , e para `files` muitos cenários, criar uma projeção pode ser suficiente. 

No entanto, é possível criar `table` - `object` - `file` vários conjuntos de projeções, e pode fazê-lo se quiser diferentes relações de dados. Dentro de um conjunto, os dados estão relacionados, assumindo que essas relações existem e podem ser detetadas. Se criar conjuntos adicionais, os documentos de cada grupo nunca estão relacionados. Um exemplo de utilização de vários grupos de projeção pode ser se você quiser os mesmos dados projetados para uso com o seu sistema on-line e ele precisa ser representado de uma forma específica, você também quer os mesmos dados projetados para uso em um pipeline de ciência de dados que é representado de forma diferente.

## <a name="requirements"></a>Requisitos 

É necessário [o armazenamento Azure.](../storage/index.yml) Fornece armazenamento físico. Você pode usar armazenamento Blob, armazenamento de mesa ou ambos. O armazenamento de bolhas é utilizado para documentos enriquecidos intactos, normalmente quando a saída vai para os processos a jusante. O armazenamento de mesa destina-se a fatias de documentos enriquecidos, normalmente utilizados para análise e reporte.

[Skillset](cognitive-search-working-with-skillsets.md) é necessário. Contém a `knowledgeStore` definição, e determina a estrutura e composição de um documento enriquecido. Não é possível criar uma loja de conhecimento usando um skillset vazio. Deve ter pelo menos uma habilidade numa habilidade.

[Indexante](search-indexer-overview.md) é necessário. Um skillset é invocado por um indexante, que impulsiona a execução. Os indexantes vêm com o seu próprio conjunto de requisitos e atributos. Vários destes atributos têm uma influência direta numa loja de conhecimento:

+ Os indexantes requerem uma [fonte de dados Azure suportada](search-indexer-overview.md#supported-data-sources) (o pipeline que, em última análise, cria a loja de conhecimentos começa por retirar dados de uma fonte suportada em Azure). 

+ Os indexantes requerem um índice de pesquisa. Um indexante requer que forneça um esquema de índice, mesmo que nunca planeie usá-lo. Um índice mínimo tem um campo de cordas, designado como a chave.

+ Os indexantes fornecem mapeamentos de campo opcionais, usados para alias um campo de origem para um campo de destino. Se um mapeamento de campo predefinido precisar de modificação (para usar um nome ou tipo diferente), pode criar um [mapeamento de campo](search-indexer-field-mappings.md) dentro de um indexante. Para a saída da loja de conhecimento, o destino pode ser um campo em um objeto ou mesa blob.

+ Os indexantes têm horários e outras propriedades, como mecanismos de deteção de alterações fornecidos por várias fontes de dados, também podem ser aplicados a uma loja de conhecimento. Por exemplo, pode [agendar](search-howto-schedule-indexers.md) o enriquecimento a intervalos regulares para refrescar o conteúdo. 

## <a name="how-to-create-a-knowledge-store"></a>Como criar uma loja de conhecimento

Para criar uma loja de conhecimentos, utilize o portal ou a API REST ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>Utilizar o portal do Azure

O assistente **de dados Import** inclui opções para a criação de uma loja de conhecimento. Para a exploração inicial, [crie a sua primeira loja de conhecimento em quatro passos.](knowledge-store-connect-power-bi.md)

1. Selecione uma fonte de dados suportada.

1. Especificar o enriquecimento: anexar um recurso, selecionar habilidades e especificar uma loja de conhecimentos. 

1. Criar um esquema de índice. O feiticeiro precisa e pode inferir um para si.

1. Executar o feiticeiro. A extração, enriquecimento e armazenamento ocorrem neste último passo.

### <a name="use-create-skillset-rest-api"></a>Utilizar Criar Skillset (REST API)]

A `knowledgeStore` é definida dentro de um [skillset](cognitive-search-working-with-skillsets.md), que por sua vez é invocado por um [indexante](search-indexer-overview.md). Durante o enriquecimento, a Azure Cognitive Search cria um espaço na sua conta de Armazenamento Azure e projeta os documentos enriquecidos como bolhas ou em mesas, dependendo da sua configuração.

A API REST é um mecanismo através do qual pode criar uma loja de conhecimento programáticamente. Uma maneira fácil de explorar é criar a [sua primeira loja de conhecimento usando o Carteiro e a API REST.](knowledge-store-create-rest.md)

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Como conectar-se com ferramentas e aplicativos

Uma vez que os enriquecimentos existam no armazenamento, qualquer ferramenta ou tecnologia que se conecte ao Azure Blob ou ao armazenamento de mesa pode ser usada para explorar, analisar ou consumir o conteúdo. A lista a seguir é um começo:

+ [Explorador de Armazenamento](knowledge-store-view-storage-explorer.md) para ver estrutura e conteúdo de documento enriquecido. Considere isto como a sua ferramenta de base para visualizar conteúdos da loja de conhecimento.

+ [Power BI](knowledge-store-connect-power-bi.md) para reportagem e análise. 

+ [Azure Data Factory](../data-factory/index.yml) para mais manipulação.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>Referência da API

A versão REST API `2020-06-30` fornece loja de conhecimento através de definições adicionais sobre skillsets. Além da referência, consulte  [Criar uma loja de conhecimento usando o Carteiro](knowledge-store-create-rest.md) para obter detalhes sobre como chamar as APIs.

+ [Create Skillset (versão api=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Atualização Skillset (versão api=2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Passos seguintes

A loja de conhecimento oferece persistência de documentos enriquecidos, úteis na conceção de um skillset, ou a criação de novas estruturas e conteúdos para consumo por quaisquer aplicações do cliente capazes de aceder a uma conta de Armazenamento Azure.

A abordagem mais simples para criar documentos enriquecidos é [através do portal](knowledge-store-create-portal.md), mas também pode utilizar o Carteiro e a API REST, o que é mais útil se quiser saber como os objetos são criados e referenciados.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimentos usando Carteiro e REST](knowledge-store-create-rest.md)

Para saber mais sobre projeções, as capacidades e como [as define num skillset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções numa loja de conhecimento](knowledge-store-projection-overview.md)

Para um tutorial que abrange conceitos avançados de projeções como cortar, moldar inline e relacionamentos, comece com [projeções de definição numa loja](knowledge-store-projections-examples.md) de conhecimento

> [!div class="nextstepaction"]
> [Definir projeções numa loja de conhecimento](knowledge-store-projections-examples.md)