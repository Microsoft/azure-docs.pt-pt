---
title: Projeções numa loja de conhecimento (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Guarde e molde os seus dados enriquecidos do pipeline de indexação de enriquecimento de IA para uma loja de conhecimentos para utilização em outros cenários que não a pesquisa completa de texto. A loja de conhecimento está atualmente em pré-visualização pública.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: d264768bf27967d1a778400ae4e9e6f2e054d746
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942978"
---
# <a name="projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Projeções em uma loja de conhecimento em Azure Cognitive Search

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

A Pesquisa Cognitiva Azure permite o enriquecimento de conteúdo através de habilidades cognitivas incorporadas e habilidades personalizadas como parte da indexação. Os enriquecimentos criam novas informações onde nenhuma existia anteriormente: extrair informação de imagens, detetar sentimentos, frases-chave e entidades de texto, para citar alguns. Os enriquecimentos também adicionam estrutura ao texto indiferenciado. Todos estes processos resultam em documentos que tornam a procura completa de texto mais eficaz. Em muitos casos, documentos enriquecidos são úteis para cenários que não a pesquisa, como a mineração de conhecimento.

As projeções, uma componente da loja de [conhecimento,](knowledge-store-concept-intro.md)são vistas de documentos enriquecidos que podem ser guardados para armazenamento físico para fins de mineração de conhecimento. Uma projeção permite "projetar" os seus dados numa forma que se alinha com as suas necessidades, preservando relações para que ferramentas como o Power BI possam ler os dados sem esforço adicional.

As projeções podem ser tabular, com dados armazenados em linhas e colunas no armazenamento da Tabela Azure, ou objetos JSON armazenados no armazenamento da Blob Azure. Pode definir várias projeções dos seus dados como estando a ser enriquecida. Várias projeções são úteis quando se deseja os mesmos dados moldados de forma diferente para casos de utilização individual.

A loja de conhecimentos suporta três tipos de projeções:

+ **Tabelas**: Para dados que melhor estejam representados como linhas e colunas, as projeções da tabela permitem definir uma forma ou projeção esquematizada no armazenamento de tabelas. Apenas objetos JSON válidos podem ser projetados como tabelas, o documento enriquecido pode conter nós que não são chamados objetos JSON e ao projetar estes objetos, criar um objeto JSON válido com uma habilidade de shaper ou formação linear.

+ **Objetos**: Quando necessita de uma representação JSON dos seus dados e enriquecimentos, as projeções de objetos são guardadas como bolhas. Apenas objetos JSON válidos podem ser projetados como objetos, o documento enriquecido pode conter nós que não são chamados objetos JSON e ao projetar estes objetos, criar um objeto JSON válido com uma habilidade de shaper ou formação linear.

+ **Ficheiros**: Quando necessitar de guardar as imagens extraídas dos documentos, as projeções de ficheiros permitem-lhe guardar as imagens normalizadas para o armazenamento de bolhas.

Para ver as projeções definidas no contexto, passe por Criar uma loja de [conhecimentos em REST](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, terá de projetar os seus dados enriquecidos de diferentes formas para atingir diferentes objetivos. A loja de conhecimentos permite definir vários grupos de projeções. Os grupos de projeção têm as seguintes características-chave de exclusividade mútua e de aconexão.

### <a name="mutual-exclusivity"></a>Exclusividade mútua

Todos os conteúdos projetados num único grupo são independentes dos dados projetados para outros grupos de projeção.
Esta independência implica que você pode ter os mesmos dados moldados de forma diferente, mas repetido em cada grupo de projeção.

### <a name="relatedness"></a>Relacionados

Os grupos de projeção permitem agora projetar os seus documentos através de tipos de projeção, preservando as relações através de tipos de projeção. Todos os conteúdos projetados num único grupo de projeção preservam as relações dentro dos dados através dos tipos de projeção. Dentro das tabelas, as relações baseiam-se numa chave gerada e cada nó infantil mantém uma referência ao nó dos pais. Entre tipos (tabelas, objetos e ficheiros), as relações são preservadas quando um único nó é projetado em diferentes tipos. Por exemplo, considere um cenário em que tenha um documento contendo imagens e texto. Pode projetar o texto para tabelas ou objetos e as imagens para ficheiros onde as tabelas ou objetos têm uma coluna/propriedade contendo o URL do ficheiro.

## <a name="input-shaping"></a>Formação de entrada

Obter os seus dados na forma ou estrutura corretas é fundamental para uma utilização eficaz, seja tabelas ou objetos. A capacidade de moldar ou estruturar os seus dados com base na forma como planeia aceder e usá-lo é uma capacidade chave exposta como a habilidade **Shaper** dentro do skillset.  

As projeções são mais fáceis de definir quando se tem um objeto na árvore de enriquecimento que corresponde ao esquema da projeção. A habilidade de [Shaper](cognitive-search-skill-shaper.md) atualizada permite-lhe compor um objeto de diferentes nódosos da árvore de enriquecimento e progenitor sob um novo nó. A habilidade **Shaper** permite definir tipos complexos com objetos aninhados.

Quando tiver uma nova forma definida que contenha todos os elementos que precisa de projetar, pode agora usar esta forma como fonte para as suas projeções ou como entrada para outra habilidade.

## <a name="projection-slicing"></a>Corte de projeção

Ao definir um grupo de projeção, um único nó na árvore de enriquecimento pode ser cortado em várias mesas ou objetos relacionados. A adição de uma projeção com um caminho de origem que é uma criança de uma projeção existente resultará na fatia do nó da criança do nó principal e projetada para a nova mesa ou objeto ainda relacionado. Esta técnica permite definir um único nó numa habilidade de formador que pode ser a fonte para todas as suas projeções.

## <a name="table-projections"></a>Projeções de tabelas

Como facilita a importação, recomendamos projeções de tabelas para a exploração de dados com o Power BI. Além disso, as projeções de tabela permitem alterar a cardinalidade entre as relações de mesa. 

Pode projetar um único documento no seu índice em várias tabelas, preservando as relações. Ao projetar para várias tabelas, a forma completa será projetada em cada mesa, a menos que um nó infantil seja a fonte de outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definindo uma projeção de tabela

Ao definir uma projeção de tabela dentro do elemento `knowledgeStore` da sua habilidade, comece por mapear um nó na árvore de enriquecimento até à fonte da mesa. Tipicamente este nó é a saída de uma habilidade **Shaper** que você adicionou à lista de habilidades para produzir uma forma específica que você precisa projetar em tabelas. O nó que você escolher projetar pode ser cortado para projetar em várias tabelas. A definição de tabelas é uma lista de tabelas que pretende projetar.

Cada tabela requer três propriedades:

+ nome do quadro: O nome da tabela em Armazenamento Azure.

+ nome chave: o nome da coluna para a chave que identifica exclusivamente esta linha.

+ fonte: O nó da árvore de enriquecimento de onde está a extrair os seus enriquecimentos. Este nó é geralmente a saída de um shaper, mas pode ser a saída de qualquer uma das habilidades.

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

Como demonstrado neste exemplo, as frases e entidades-chave são modeladas em diferentes tabelas e conterão uma referência de volta ao progenitor (MainTable) para cada linha.

## <a name="object-projections"></a>Projeções de objetos

As projeções de objetos são representações json da árvore de enriquecimento que pode ser proveniente de qualquer nó. Em muitos casos, a mesma habilidade **Shaper** que cria uma projeção de tabela pode ser usada para gerar uma projeção de objeto. 

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

Gerar uma projeção de objectorequer alguns atributos específicos do objeto:

+ recipiente de armazenamento: O recipiente de bolha onde os objetos serão guardados
+ fonte: O caminho para o nó da árvore de enriquecimento que é a raiz da projeção

## <a name="file-projection"></a>Projeção de ficheiros

As projeções de ficheiros são semelhantes às projeções de objetos e apenas atuam na coleção `normalized_images`. Semelhante supõe-se às projeções de objetos, as projeções de ficheiros são guardadas no recipiente blob com prefixo de pasta do valor codificado base64 do ID do documento. As projeções de ficheiros não podem partilhar o mesmo recipiente que as projeções de objetos e precisam de ser projetadas num recipiente diferente.

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

As suas projeções têm um ciclo de vida que está ligado aos dados de origem na sua fonte de dados. À medida que os seus dados são atualizados e reindexados, as suas projeções são atualizadas com os resultados dos enriquecimentos, garantindo que as suas projeções são eventualmente consistentes com os dados na sua fonte de dados. As projeções herdam a política de eliminação que configuraste para o seu índice. As projeções não são eliminadas quando o indexador ou o próprio serviço de pesquisa são eliminados.

## <a name="using-projections"></a>Usando projeções

Após a execução do indexante, pode ler os dados projetados nos recipientes ou tabelas especificados através de projeções.

Para análise, a exploração no Power BI é tão simples como definir o armazenamento da Tabela Azure como a fonte de dados. Pode facilmente criar um conjunto de visualizações nos seus dados utilizando as relações dentro.

Em alternativa, se precisar de utilizar os dados enriquecidos num pipeline de ciência de dados, pode [carregar os dados de blobs para um DataFrame pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Finalmente, se precisar de exportar os seus dados da loja de conhecimentos, a Azure Data Factory dispõe de conectores para exportar os dados e aterrar na base de dados à sua escolha. 

## <a name="next-steps"></a>Passos Seguintes

Como próximo passo, crie a sua primeira loja de conhecimentos utilizando dados e instruções de amostra.

> [!div class="nextstepaction"]
> [Criar uma loja de conhecimentos em REST.](knowledge-store-create-rest.md)

Para um tutorial que cobre projeções avançadas conceitos como corte, formação inline e relacionamentos, comece com [projeções de definição em uma loja](knowledge-store-projections-examples.md) de conhecimento

> [!div class="nextstepaction"]
> [Defina projeções numa loja de conhecimento](knowledge-store-projections-examples.md)
