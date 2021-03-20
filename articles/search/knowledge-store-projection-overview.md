---
title: Conceitos de projeção
titleSuffix: Azure Cognitive Search
description: Guarde e molde os seus dados enriquecidos do oleoduto de indexação do enriquecimento de IA numa loja de conhecimento para utilização em cenários que não a pesquisa completa de texto.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85565197"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Loja de conhecimento "projeções" em Pesquisa Cognitiva Azure

A Azure Cognitive Search permite o enriquecimento de conteúdos através de habilidades cognitivas incorporadas e habilidades personalizadas como parte da indexação. Os enriquecimentos criam novas informações onde nenhuma existia anteriormente: extrair informação de imagens, detetar sentimentos, frases-chave e entidades de texto, para citar alguns. Os enriquecimentos também acrescentam estrutura a texto indiferenciado. Todos estes processos resultam em documentos que tornam a pesquisa completa por texto mais eficaz. Em muitos casos, documentos enriquecidos são úteis para outros cenários que não a pesquisa, como para a mineração de conhecimento.

As projeções, uma componente da loja de [conhecimento,](knowledge-store-concept-intro.md)são vistas de documentos enriquecidos que podem ser guardados para armazenamento físico para fins de mineração de conhecimento. Uma projeção permite -lhe "projetar" os seus dados numa forma que se alinha com as suas necessidades, preservando relações para que ferramentas como o Power BI possam ler os dados sem esforço adicional.

As projeções podem ser tabulares, com dados armazenados em linhas e colunas no armazenamento da Mesa Azure ou objetos JSON armazenados no armazenamento da Azure Blob. Pode definir várias projeções dos seus dados à medida que estes estão a ser enriquecidos. Várias projeções são úteis quando se pretende que os mesmos dados sejam moldados de forma diferente para casos de uso individual.

A loja de conhecimento suporta três tipos de projeções:

+ **Tabelas**: Para dados que são melhor representados como linhas e colunas, as projeções de tabela permitem definir uma forma ou projeção schematizada no armazenamento de mesa. Apenas objetos JSON válidos podem ser projetados como tabelas, o documento enriquecido pode conter nós que não são chamados objetos JSON e ao projetar estes objetos, criar um objeto JSON válido com uma habilidade de formador ou formação inline.

+ **Objetos**: Quando precisa de uma representação JSON dos seus dados e enriquecimentos, as projeções dos objetos são guardadas como bolhas. Apenas objetos JSON válidos podem ser projetados como objetos, o documento enriquecido pode conter nós que não são chamados objetos JSON e ao projetar estes objetos, criar um objeto JSON válido com uma habilidade de formação ou formação inline.

+ **Ficheiros**: Quando é necessário guardar as imagens extraídas dos documentos, as projeções de ficheiros permitem guardar as imagens normalizadas para o armazenamento de bolhas.

Para ver as projeções definidas em contexto, passe por [criar uma loja de conhecimento em REST.](knowledge-store-create-rest.md)

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, você precisará projetar os seus dados enriquecidos em diferentes formas para cumprir diferentes objetivos. A loja de conhecimento permite definir vários grupos de projeções. Os grupos de projeção têm as seguintes características-chave da exclusividade mútua e da parentesca.

### <a name="mutual-exclusivity"></a>Exclusividade mútua

Todos os conteúdos projetados num único grupo são independentes dos dados projetados noutros grupos de projeção.
Esta independência implica que pode ter os mesmos dados moldados de forma diferente, mas repetidos em cada grupo de projeção.

### <a name="relatedness"></a>Parentesidade

Os grupos de projeção permitem-lhe agora projetar os seus documentos através de tipos de projeção, preservando as relações entre tipos de projeção. Todos os conteúdos projetados dentro de um único grupo de projeção preservam as relações dentro dos dados entre tipos de projeção. Dentro das tabelas, as relações baseiam-se numa chave gerada e cada nó de criança mantém uma referência ao nó-mãe. Entre tipos (tabelas, objetos e ficheiros), as relações são preservadas quando um único nó é projetado em diferentes tipos. Por exemplo, considere um cenário em que tenha um documento que contenha imagens e texto. Pode projetar o texto para mesas ou objetos e as imagens para ficheiros onde as tabelas ou objetos têm uma coluna/propriedade contendo o URL do ficheiro.

## <a name="input-shaping"></a>Formação de entrada

Obter os seus dados na forma ou estrutura corretas é a chave para uma utilização eficaz, sejam elas tabelas ou objetos. A capacidade de moldar ou estruturar os seus dados com base na forma como planeia aceder e usá-lo é uma capacidade chave exposta como a habilidade **do Shaper** dentro do skillset.  

As projeções são mais fáceis de definir quando se tem um objeto na árvore de enriquecimento que corresponde ao esquema da projeção. A [habilidade de Shaper](cognitive-search-skill-shaper.md) atualizada permite-lhe compor um objeto de diferentes nós da árvore de enriquecimento e pais-los sob um novo nó. A habilidade **Shaper** permite-lhe definir tipos complexos com objetos aninhados.

Quando tiver uma nova forma definida que contenha todos os elementos necessários para projetar, pode agora usar esta forma como fonte para as suas projeções ou como entrada para outra habilidade.

## <a name="projection-slicing"></a>Corte de projeção

Ao definir um grupo de projeção, um único nó na árvore de enriquecimento pode ser cortado em várias mesas ou objetos relacionados. A adição de uma projeção com um caminho de origem que é filho de uma projeção existente resultará na corte do nó do filho do nó do progenitor e projetado para a nova mesa ou objeto ainda relacionado. Esta técnica permite-lhe definir um único nó numa habilidade de formador que pode ser a fonte de todas as suas projeções.

## <a name="table-projections"></a>Projeções de tabelas

Porque facilita a importação, recomendamos projeções de tabelas para exploração de dados com o Power BI. Além disso, as projeções da tabela permitem alterar o cardinalício entre as relações de mesa. 

Pode projetar um único documento no seu índice em várias tabelas, preservando as relações. Ao projetar para várias mesas, a forma completa será projetada em cada mesa, a menos que um nó de criança seja a fonte de outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definição de uma projeção de tabela

Ao definir uma projeção de mesa dentro `knowledgeStore` do elemento do seu skillset, comece por mapear um nó na árvore de enriquecimento até à fonte de mesa. Tipicamente, este nó é a saída de uma habilidade **shaper** que adicionaste à lista de habilidades para produzir uma forma específica que precisas de projetar em tabelas. O nó que escolhe projetar pode ser cortado para projetar em várias mesas. A definição de tabelas é uma lista de tabelas que pretende projetar.

Cada tabela requer três propriedades:

+ nome de mesa: O nome da tabela no Azure Storage.

+ gerouKeyName: O nome da coluna para a chave que identifica exclusivamente esta linha.

+ fonte: O nó da árvore de enriquecimento de que está a extrair os seus enriquecimentos. Este nó é geralmente a saída de um shaper, mas pode ser a saída de qualquer uma das habilidades.

Aqui está um exemplo de projeções de mesa.

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
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Como demonstrado neste exemplo, as frases-chave e as entidades são modeladas em diferentes tabelas e conterão uma referência ao progenitor (MainTable) para cada linha.

## <a name="object-projections"></a>Projeções de objetos

As projeções dos objetos são representações JSON da árvore de enriquecimento que podem ser provenientes de qualquer nó. Em muitos casos, a mesma habilidade **shaper** que cria uma projeção de tabela pode ser usada para gerar uma projeção de objetos. 

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
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Gerar uma projeção de objeto requer alguns atributos específicos do objeto:

+ storageContainer: O recipiente blob onde os objetos serão guardados
+ fonte: O caminho para o nó da árvore de enriquecimento que é a raiz da projeção

## <a name="file-projection"></a>Projeção de arquivo

As projeções dos ficheiros são semelhantes às projeções de objetos e apenas atuam na `normalized_images` recolha. Semelhantes às projeções de objetos, as projeções de ficheiros são guardadas no recipiente blob com prefixo de pasta do valor codificado base64 do ID do documento. As projeções dos ficheiros não podem partilhar o mesmo recipiente que as projeções dos objetos e precisam de ser projetadas num recipiente diferente.

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
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Ciclo de vida de projeção

As suas projeções têm um ciclo de vida que está ligado aos dados de origem na sua fonte de dados. À medida que os seus dados são atualizados e reindexados, as suas projeções são atualizadas com os resultados dos enriquecimentos garantindo que as suas projeções são eventualmente consistentes com os dados na sua fonte de dados. As projeções herdam a política de eliminação que configuraste para o teu índice. As projeções não são eliminadas quando o indexante ou o próprio serviço de pesquisa são eliminados.

## <a name="using-projections"></a>Usando projeções

Depois de executar o indexante, pode ler os dados projetados nos recipientes ou tabelas que especificou através de projeções.

Para análise, a exploração no Power BI é tão simples como definir o armazenamento da Tabela Azure como a fonte de dados. Pode facilmente criar um conjunto de visualizações nos seus dados utilizando as relações dentro.

Em alternativa, se precisar de utilizar os dados enriquecidos num oleoduto de ciência [de dados, poderá carregar os dados de bolhas num DataFrame pandas.](../machine-learning/team-data-science-process/explore-data-blob.md)

Finalmente, se necessitar de exportar os seus dados da loja de conhecimentos, a Azure Data Factory dispõe de conectores para exportar os dados e aterrar na base de dados à sua escolha. 

## <a name="next-steps"></a>Passos seguintes

Como próximo passo, crie a sua primeira loja de conhecimentos utilizando dados e instruções de amostra.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimentos em REST.](knowledge-store-create-rest.md)

Para um tutorial que abrange conceitos avançados de projeções como cortar, moldar inline e relacionamentos, comece com [projeções de definição numa loja](knowledge-store-projections-examples.md) de conhecimento

> [!div class="nextstepaction"]
> [Definir projeções numa loja de conhecimento](knowledge-store-projections-examples.md)
