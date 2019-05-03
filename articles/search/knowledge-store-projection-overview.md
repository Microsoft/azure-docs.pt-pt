---
title: Trabalhar com projeções num arquivo de dados de conhecimento - Azure Search
description: Guardar e formatar os dados plena do pipeline de indexação de ia para utilização em cenários que não seja de pesquisa
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: vikurpad
ms.custom: seomay2019
ms.openlocfilehash: 3ab5ffafd1b20eb0e3e453d3e730840baf9233e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028369"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-search"></a>Trabalhar com projeções num arquivo de dados de conhecimento no Azure Search

O Azure Search permite que a melhoria do conteúdo através de capacidades cognitivas de IA e competências personalizadas como parte de indexação. Possível adicionam estrutura a seus documentos e tornar a pesquisa mais eficientes. Em muitos casos, os documentos plena são úteis para outros cenários além da pesquisa, como para extração de dados de conhecimento.

Projeções, um componente do [o arquivo de dados de conhecimento (pré-visualização)](knowledge-store-concept-intro.md), são os modos de exibição de documentos plena que podem ser guardados num armazenamento físico para fins de mineração de dados de conhecimento. Uma projeção permite-lhe "projeto" os seus dados para um formato que se alinha com suas necessidades, preservando as relações para que ferramentas como o Power BI podem ler os dados sem esforço adicional. 

Projeções podem ser tabulares, com dados armazenados em linhas e colunas no armazenamento de tabelas do Azure, ou objetos JSON armazenados no armazenamento de Blobs do Azure. Pode definir várias projeções de seus dados à medida que está a ser enriquecido. Isto é útil quando pretender que os mesmos dados em forma de forma diferente para casos de utilização individual. 

O arquivo de dados de conhecimento suporta dois tipos de projeções:

+ **Tabelas**: Para os dados que é a melhor forma de linhas e colunas, projeções de tabela permitem-lhe definir uma forma esquematizada ou a projeção no armazenamento de tabelas. 

+ **Objetos**: Quando precisar de uma representação JSON de seus dados e o possível, projeções de objeto são guardadas como blobs.

Para ver as projeções definidas no contexto, siga os passos [como começar com o arquivo de dados de conhecimento](knowledge-store-howto.md)

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, precisará de sua plena dados em diferentes formas para atender aos diferentes objetivos do projeto. O arquivo de dados de conhecimento permite-lhe definir vários grupos de projeções. Grupos de projeção tem as seguintes características de chave de exclusividade mútua e relatedness.

### <a name="mutually-exclusivity"></a>Exclusividade mutuamente

Todo o conteúdo feito num único grupo é independente de dados projetados para outros grupos de projeção. Isso implica que pode ter os mesmos dados em forma de forma diferente, embora repetido em cada grupo de projeção. 

Uma restrição imposta em grupos de projeção é a exclusividade mútua dos tipos de projecção com um grupo de projeção. Pode apenas definir projeções de tabela ou projeções de objeto dentro de um único grupo. Se pretender tabelas e objetos, defina um grupo de projeção para tabelas e um segundo grupo de projeção para objetos.

### <a name="relatedness"></a>Relatedness

Todo o conteúdo projetado dentro de um grupo de projeção único preserva relações nos dados. Relações de baseiam-se uma chave gerada e cada nó filho mantém uma referência para o nó principal. As relações não se propagam a grupos de projeção e tabelas ou objetos criados num grupo de projeção têm qualquer relação com dados gerados noutros grupos de projeção.

## <a name="input-shaping"></a>Formatação de entrada
Obter os seus dados na forma correta ou estrutura é a utilização de chaves para eficaz, seja tabelas ou objetos. A capacidade de moldar ou estruturar os dados com base em como pretende aceder e utilizá-lo é uma capacidade essencial exposta como o **Modelador** habilidade dentro do conjunto de capacidades.  

Projeções são mais fáceis de definir quando tem um objeto na árvore da melhoria que corresponde ao esquema da projeção. A atualização [habilidade Modelador](cognitive-search-skill-shaper.md) permite-lhe compor um objeto de diferentes nós da árvore enriquecimento e principal-los num novo nó. O **Modelador** habilidade permite-lhe definir os tipos complexos com objetos aninhados.

Quando tem uma forma nova definida, que contém todos os elementos que precisa para projetar o, agora, pode utilizar esta forma como a origem para as suas projeções ou como uma entrada para outra habilidade.

## <a name="table-projections"></a>Projeções de tabela

Porque torna mais fácil de importar, recomendamos que projeções de tabela para exploração de dados com o Power BI. Além disso, as projeções de tabela, permitir que a alteração de alterar a cardinalidade entre a relação entre tabelas. 

Pode projetar um único documento no seu índice por várias tabelas, preservando as relações. Ao projetar a várias tabelas, a forma completa irá ser projetada para cada tabela, a menos que um nó filho é a fonte de outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definir uma projeção de tabela

Ao definir uma projeção de tabela dentro do `knowledgeStore` elemento do conjunto de capacidades, comece por um nó na árvore de enriquecimento de mapeamento para a origem de tabela. Normalmente, este nó é o resultado de uma **Modelador** habilidade que adicionou à lista de habilidades para produzir uma forma específica que precisa para projetar em tabelas. O nó a que se optar por projeto pode ser segmentado ao projeto por várias tabelas. A definição de tabelas é uma lista de tabelas que pretende do projeto. 

Cada tabela requer três propriedades:

+ tableName: O nome da tabela no armazenamento do Azure.

+ generatedKeyName: O nome da coluna da chave que identifica exclusivamente esta linha.

+ Origem: O nó da árvore de enriquecimento onde está a extrair o possível de. Isto é, normalmente, a saída de um Modelador, mas pode ser o resultado de qualquer uma das habilidades.

Eis um exemplo de projeções de tabela.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [
            
          ]
        }
      ]
    }
}
```
Como demonstrado neste exemplo, as expressões-chave e as entidades são modeladas em tabelas diferentes e conterão uma referência de volta para o pai (MainTable) para cada linha. 

A ilustração seguinte é uma referência para o exercício de Caselaw na [como começar com o arquivo de dados de conhecimento](knowledge-store-howto.md). Num cenário onde um caso tem várias opiniões e cada opinião é enriquecido pela identificação de entidades contidas nela, pudesse modelar as projeções, conforme mostrado aqui.

![Entidades e relações em tabelas](media/knowledge-store-projection-overview/TableRelationships.png "modelando as relações no projeções de tabela")

## <a name="object-projections"></a>Projeções de objeto

Projeções de objeto são representações de JSON da árvore enriquecimento que pode ser obtido a partir de qualquer nó. Em muitos casos, o mesmo **Modelador** habilidade que cria uma projeção de tabela pode ser utilizada para gerar uma projeção de objeto. 

```json
{
 
    "name": "your-skillset",
    "skills": [
      …your skills
      
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
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

Gerar uma projeção de objeto requer alguns atributos específicos do objeto:

+ storageContainer: O contentor onde os objetos serão guardados
+ Origem: O caminho para o nó da árvore enriquecimento que é a raiz da projeção
+ Chave: Um caminho que representa uma chave exclusiva para o objeto a ser armazenados. Será utilizado para criar o nome do blob no contentor.

## <a name="projection-lifecycle"></a>Ciclo de vida de projeção

Suas projeções têm um ciclo de vida que está associado aos dados de origem na sua origem de dados. Como os dados são atualizados e reindexados, suas projeções são atualizadas com os resultados da possível garantir que suas projeções são eventualmente consistentes com os dados na sua origem de dados. As projeções herdam a política de eliminação que configurou para o seu índice. 

## <a name="using-projections"></a>O uso de projeções

Depois do indexador é executado, é possível ler dos dados projetados no contentores ou tabelas especificadas por meio de projeções. 

Para análise, a exploração no Power BI é tão simples como definir o armazenamento de tabelas do Azure como a origem de dados. Pode criar facilmente um conjunto de visualizações nos seus dados, aproveitando as relações dentro.

Em alternativa, se tiver de utilizar os dados plena num pipeline de ciência de dados, poderia [carregar os dados de blobs para um Pandas DataFrame](../machine-learning/team-data-science-process/explore-data-blob.md).

Por fim, se precisar de exportar os dados a partir do arquivo de dados de conhecimento, o Azure Data Factory tem conectores para exportar os dados e ele encaminhado para a base de dados da sua preferência. 

## <a name="next-steps"></a>Passos Seguintes

Como passo seguinte, crie seu primeiro armazenamento de dados de conhecimento utilizando dados de exemplo e instruções.

> [!div class="nextstepaction"]
> [Como criar um arquivo de dados de conhecimento](knowledge-store-howto.md).