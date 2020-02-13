---
title: Defina projeções numa loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns sobre como projetar documentos enriquecidos na loja de conhecimentos para utilização com Power BI ou Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165517"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Projeções de lojas de conhecimento: Como moldar e exportar enriquecimentos para a loja de conhecimento

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

As projeções são a expressão física de documentos enriquecidos numa loja de conhecimento. O uso eficaz dos seus documentos enriquecidos requer estrutura. Neste artigo, você vai explorar tanto a estrutura como as relações, aprendendo como construir propriedades de projeção, bem como como relacionar dados entre os tipos de projeção que cria. 

Para criar uma projeção, deve moldar os dados utilizando uma habilidade de modelar para criar um objeto personalizado ou utilizar a sintaxe de formação inline. Uma forma de dados contém todos os dados que pretende projetar. Este documento dá-lhe um exemplo de cada opção, pode optar por utilizar qualquer uma das opções para projeções que cria.


Existem três tipos de projeções:
+ Tabelas
+ Objetos
+ Ficheiros

As projeções da tabela são armazenadas no armazenamento da Mesa Azure. As projeções de objetos e ficheiros são escritas para armazenamento de bolhas, as projeções de objetos são guardadas como ficheiros JSON e podem conter conteúdo do documento e quaisquer saídas ou enriquecimentos de habilidades. O gasoduto de enriquecimento também pode extrair binários como imagens, estes binários são projetados como projeções de ficheiros. Quando um objeto binário é projetado como uma projeção de objeto, apenas os metadados associados a ele são guardados como uma bolha JSON. 

Para entender a intersecção entre a modelação de dados e as projeções, usaremos o seguinte skillset como base para explorar várias configurações. Este skillset processa imagem crua e conteúdo de texto. As projeções serão definidas a partir do conteúdo do documento e das saídas das competências, para os cenários que queremos apoiar.

Em alternativa, pode descarregar e utilizar uma [amostra REST API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) com todas as chamadas neste walkthrough.

> [!IMPORTANT] 
> Ao experimentar as projeções, é útil definir a propriedade de [cache indexante](search-howto-incremental-index.md) para garantir o controlo de custos. As projeções de edição resultarão em que todo o documento seja novamente enriquecido se a cache do indexante não estiver definida. Quando a cache é definida e apenas as projeções atualizadas, as execuções de skillset para documentos anteriormente enriquecidos não resultam em quaisquer encargos dos Serviços Cognitivos.


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Podemos agora adicionar o `knowledgeStore` objeto e configurar as projeções para cada um dos cenários conforme necessário. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Projetando para tabelas para cenários como Power BI

> [!NOTE] 
> Como a loja de conhecimentos é uma conta de Armazenamento Azure, as projeções de tabela são tabelas de armazenamento Azure e são regidas pelos limites de armazenamento nas mesas, para mais informações, ver limites de armazenamento de [mesas.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model) É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior do que 64 KB. Estes constrangimentos fazem das tabelas uma boa solução para armazenar um grande número de pequenas entidades.

O Power BI pode ler a partir de mesas e descobrir relações com base nas teclas que as projeções da loja de conhecimento criam, o que faz das tabelas uma boa opção para projetar dados quando está a tentar construir um dashboard nos seus dados enriquecidos. Assumindo que estamos a tentar construir um dashboard onde possamos visualizar as frases-chave extraídas de documentos como uma nuvem de palavras, podemos adicionar uma habilidade de shaper ao skillset para criar uma forma personalizada que tenha os detalhes específicos do documento e frases-chave. Adicione a habilidade do shaper à habilidade para criar um novo enriquecimento chamado ```pbiShape``` no ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade shaper para criar uma forma personalizada

Crie uma forma personalizada que possa projetar para armazenamento de mesa. Sem uma forma personalizada, uma projeção só pode referir um único nó (uma projeção por saída). Criar uma forma personalizada permite-lhe agregar vários elementos num novo todo lógico que pode ser projetado como uma única mesa, ou fatiado e distribuído por uma coleção de mesas. Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto chama-se pbiShape e é pai sob `/document`. 

> [!IMPORTANT] 
> Os caminhos-de-origem para enriquecimento saem para serem objetos JSON bem formados, antes de poderem ser projetados. A árvore de enriquecimento pode representar enriquecimentos que não estão bem formados JSON, por exemplo quando um enriquecimento é pai de um primitve como uma corda. Note como `KeyPhrases` e `Entities` são embrulhados num objeto JSON válido com o `sourceContext`, isto é necessário como `keyphrases` e `entities` são enriquecimentos em primitivos e precisam ser convertidos para JSON válido antes de poderem ser projetados.

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForTables",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_content_type",
                    "source": "/document/metadata_storage_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_storage_path",
                    "source": "/document/metadata_storage_path",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "metadata_content_type",
                    "source": "/document/metadata_content_type",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "Entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "pbiShape"
                }
            ]
        }
```
Adicione a habilidade do shaper que definimos para a lista de habilidades no skillset. 

Agora que temos todos os dados necessários para projetar para as tabelas, atualizar o objeto knowledgeStore com as definições de tabela. 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

Deteto a propriedade ```storageConnectionString``` para uma cadeia de conexão de conta de armazenamento de propósito geral V2 válida. Neste cenário definimos três tabelas no objeto de projeção, definindo as propriedades ```tableName```, ```source``` e ```generatedKeyName```. Agora pode atualizar a habilidade através da emissão do pedido PUT.

### <a name="slicing"></a>Corte 

Ao começar com uma forma consolidada onde todo o conteúdo que precisa de ser projetado está numa única forma (ou nó de enriquecimento), o corte proporciona-lhe a capacidade de cortar um único nó em várias mesas ou objetos. Aqui, o objeto ```pbiShape``` é cortado em várias mesas. A função de corte permite-lhe retirar partes da forma, ```keyPhrases``` e ```Entities``` em mesas separadas. Isto é útil, uma vez que várias entidades e teclas Estão associadas a cada documento. Cortar a implicitamente gera uma relação entre as mesas dos pais e das crianças, utilizando o ```generatedKeyName``` na mesa dos pais para criar uma coluna com o mesmo nome na mesa da criança. 

### <a name="naming-relationships"></a>Relações de nomeação
As propriedades ```generatedKeyName``` e ```referenceKeyName``` são usadas para relacionar dados entre tabelas ou mesmo em tipos de projeção. Cada linha na tabela/projeção da criança tem uma propriedade que aponta para o progenitor. O nome da coluna ou propriedade da criança é o ```referenceKeyName``` do progenitor. Quando o ```referenceKeyName``` não é prestado, o serviço desfaz-se do ```generatedKeyName``` do progenitor. O PowerBI baseia-se nestas chaves geradas para descobrir relações dentro das tabelas. Se precisar da coluna na mesa infantil com o nome diferente, coloque a propriedade ```referenceKeyName``` na mesa dos pais. Um exemplo seria definir o ```generatedKeyName``` como ID na tabela pbiDocument e o ```referenceKeyName``` como DocumentID. Isto resultaria na coluna nas tabelas pbiEntidades e pbiKeyPhrases que continham o id do documento sendo nomeado DocumentID.

Guarde o skillset atualizado e execute o indexador, agora tem uma projeção de trabalho com três tabelas. Importar estas tabelas para o Power BI deve resultar na descoberta automática do Power BI.

## <a name="projecting-to-objects"></a>Projeto para Objetos

As projeções de objetos não têm as mesmas limitações que as projeções de tabela, são mais adequadas para a projeção de grandes documentos. Neste exemplo, projetamos todo o documento para uma projeção de objetos. As projeções dos objetos limitam-se a uma única projeção num recipiente.
Para definir uma projeção de objetos, usaremos a matriz ```objects``` nas projeções. Pode gerar uma nova forma utilizando a habilidade do shaper ou utilizar a formação linear da projeção do objeto. Embora o exemplo das tabelas tenha demonstrado a abordagem de criar uma forma e cortar, este exemplo demonstra o uso da formação linear. A formação inline é a capacidade de criar uma nova forma na definição das inputs para uma projeção. A formação inline cria um objeto anónimo que é idêntico ao que um modelador similar produziria. A formação inline é útil se estiver a definir uma forma que não planeia reutilizar.
A propriedade de projeções é uma matriz, para este exemplo estamos adicionando um novo exemplo de projeção à matriz. Atualize a definição knowledgeStore com as projeções definidas inline, não precisa de uma habilidade de shaper ao utilizar projeções lineais.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```
## <a name="file-projections"></a>Projeções de Ficheiros

As projeções de ficheiros são imagens que são extraídas do documento de origem ou saídas de enriquecimentos que podem ser projetados fora do processo de enriquecimento. As projeções de ficheiros, semelhantes às projeções de objetos, são implementadas como bolhas e contêm a imagem. Para gerar uma projeção de ficheiros, usamos a matriz ```files``` no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contentor chamado `samplefile`.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Projetando para vários tipos

Um cenário mais complexo pode exigir que você projete conteúdo através de tipos de projeção. Por exemplo, se precisar de projetar alguns dados como frases-chave e entidades para as tabelas, guarde os resultados do OCR de texto e texto de layout como objetos e projete as imagens como ficheiros. Esta atualização para o skillset:

1. Crie uma tabela com uma linha para cada documento.
2. Crie uma tabela relacionada com a tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
3. Crie uma tabela relacionada com a tabela de documentos com cada entidade identificada como uma linha nesta tabela.
4. Crie uma projeção de objeto com o texto de layout para cada imagem.
5. Crie uma projeção de ficheiros, projetando cada imagem extraída.
6. Crie uma tabela de referência cruzada que contenha referências à tabela de documentos, projeção de objetos com o texto de layout e a projeção do ficheiro.

Comece por adicionar uma nova habilidade de shaper à matriz de habilidades que cria um objeto em forma. 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "metadata_storage_name",
                    "source": "/document/metadata_storage_name",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyPhrases",
                    "source": null,
                    "sourceContext": "/document/merged_content/keyphrases/*",
                    "inputs": [
                        {
                            "name": "KeyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        }

                    ]
                },
                {
                    "name": "entities",
                    "source": null,
                    "sourceContext": "/document/merged_content/entities/*",
                    "inputs": [
                        {
                            "name": "Entities",
                            "source": "/document/merged_content/entities/*/name"
                        }

                    ]
                },
                {
                    "name": "images",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*",
                    "inputs": [
                        {
                            "name": "image",
                            "source": "/document/normalized_images/*"
                        },
                        {
                            "name": "layoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                        ]
                }
                
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "crossProjection"
                }
            ]
        }
```

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

As projeções do objeto requerem um nome de recipiente para cada projeção, projeções de objetos ou projeções de ficheiros não podem partilhar um recipiente. 

### <a name="relationships"></a>Relações

Este exemplo também destaca outra característica das projeções, definindo vários tipos de projeções dentro do mesmo objeto de projeção, existe uma relação expressa dentro e em todos os diferentes tipos (tabelas, objetos, ficheiros) de projeções, permitindo para começar com uma linha de mesa para um documento e encontrar todo o texto oCR para as imagens dentro desse documento na projeção do objeto. Se não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção, por exemplo, o seguinte corte resultará na relação das tabelas, mas sem relações entre as tabelas e as projeções de texto do OCR. Os grupos de projeção são úteis quando se pretende projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel power bi e outro grupo de projeção para usar os dados para treinar um modelo de IA para uma habilidade.
Ao construir projeções de diferentes tipos, as projeções de ficheiros e objetos são geradas primeiro e os caminhos são adicionados às tabelas.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

Estes exemplos demonstraram os padrões comuns sobre como usar as projeções, agora você deve agora também ter uma boa compreensão dos conceitos sobre a construção de uma projeção para o seu cenário específico.

## <a name="common-issues"></a>Problemas Comuns

Ao definir uma projeção, existem algumas questões comuns que podem causar resultados inesperados.

1. Não moldar enriquecimentos de cordas. Quando as cordas são enriquecidas, por exemplo ```merged_content``` enriquecidas com frases-chave, a propriedade enriquecida é representada como uma criança de merged_content dentro da árvore de enriquecimento. Mas no momento da projeção, isto precisa de ser transformado num objeto JSON válido com um nome e um valor.
2. Omitindo o ```/*``` no final de um caminho de origem. Se, por exemplo, a fonte de uma projeção for ```/document/pbiShape/keyPhrases``` a matriz de frases-chave é projetada como um único objeto/linha. Definir o caminho de origem para ```/document/pbiShape/keyPhrases/*``` produz uma única linha ou objeto para cada uma das frases-chave.
3. Os selecionadores de trajetória são sensíveis ao caso e podem levar a falta de avisos de entrada se não utilizar a caixa exata para o seletor.

