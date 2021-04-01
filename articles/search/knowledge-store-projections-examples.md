---
title: Definir projeções numa loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns sobre como projetar documentos enriquecidos na loja de conhecimento para uso com Power BI ou Azure ML.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3985564d49ce8a5c62b15f9537364418c0a8f5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97509925"
---
# <a name="how-to-shape-and-export-enrichments"></a>Como moldar e exportar enriquecimentos

As projeções são a expressão física de documentos enriquecidos numa loja de conhecimento. A utilização eficaz de documentos enriquecidos requer estrutura. Neste artigo, você vai explorar tanto a estrutura como as relações, aprendendo a construir propriedades de projeção, bem como como relacionar dados entre os tipos de projeção criados. 

Para criar uma projeção, os dados são moldados usando uma [habilidade Shaper](cognitive-search-skill-shaper.md) para criar um objeto personalizado ou usando a sintaxe de modelação inline dentro de uma definição de projeção. 

Uma forma de dados contém todos os dados destinados a projetar, formados como uma hierarquia de nós. Este artigo mostra várias técnicas para moldar dados para que possa ser projetado em estruturas físicas propícias a relatórios, análises ou processamento a jusante. 

Os exemplos apresentados neste artigo podem ser encontrados nesta [amostra rest API,](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)que pode descarregar e executar num cliente HTTP.

## <a name="introduction-to-projection-examples"></a>Introdução aos exemplos de projeção

Existem três tipos de [projeções:](knowledge-store-projection-overview.md)

+ Tables
+ Objetos
+ Ficheiros

As projeções da tabela são armazenadas no armazenamento da Mesa Azure. As projeções de objetos e ficheiros são escritas para o armazenamento de blob, onde as projeções de objetos são guardadas como ficheiros JSON, e podem conter conteúdo do documento de origem, bem como quaisquer saídas de habilidades ou enriquecimentos. O gasoduto de enriquecimento também pode extrair binários como imagens, estes binários são projetados como projeções de arquivos. Quando um objeto binário é projetado como uma projeção de objeto, apenas os metadados associados a ele são guardados como uma bolha JSON. 

Para entender a intersecção entre a formação de dados e as projeções, usaremos o seguinte skillset como base para explorar várias configurações. Este skillset processa a imagem crua e o conteúdo de texto. As projeções serão definidas a partir do conteúdo do documento e dos resultados das competências, para os cenários desejados.

> [!IMPORTANT] 
> Ao experimentar com projeções, é útil [definir a propriedade de cache indexante](search-howto-incremental-index.md) para garantir o controlo de custos. As projeções de edição resultarão em todo o documento enriquecido novamente se a cache do indexante não estiver definida. Quando a cache está definida e apenas as projeções atualizadas, as execuções de skillset para documentos previamente enriquecidos não resultam em novas taxas dos Serviços Cognitivos.

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

Utilizando este skillset, com o seu nulo `knowledgeStore` como base, o nosso primeiro exemplo preenche o `knowledgeStore` objeto, configurado com projeções que criam estruturas de dados tabulares que podemos usar noutros cenários. 

## <a name="projecting-to-tables"></a>Projetar para mesas

Projetar para tabelas no Azure Storage é útil para relatórios e análises usando ferramentas como o Power BI. O Power BI pode ler a partir de tabelas e descobrir relações com base nas teclas geradas durante a projeção. Se estiver a tentar construir um dashboard, ter dados relacionados simplificará essa tarefa. 

Vamos construir um dashboard para visualizar as frases-chave extraídas de documentos como uma nuvem de palavras. Para criar a estrutura de dados certa, adicione uma habilidade Shaper ao skillset para criar uma forma personalizada que tenha os detalhes específicos do documento e frases-chave. A forma personalizada será chamada `pbiShape` no `document` nó raiz.

> [!NOTE] 
> As projeções da tabela são tabelas de armazenamento Azure, regidas pelos limites de armazenamento impostos pela Azure Storage. Para obter mais informações, consulte [os limites de armazenamento de mesas.](/rest/api/storageservices/understanding-the-table-service-data-model) É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior que 64 KB. Estes constrangimentos fazem das mesas uma boa solução para armazenar um grande número de pequenas entidades.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade shaper para criar uma forma personalizada

Crie uma forma personalizada que possa projetar para o armazenamento de mesas. Sem uma forma personalizada, uma projeção só pode referenciar um único nó (uma projeção por saída). Criar uma forma personalizada agrega vários elementos num novo todo lógico que pode ser projetado como uma única mesa, ou fatiado e distribuído por uma coleção de tabelas. 

Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto é chamado `pbiShape` e é parentado sob `/document` . 

> [!IMPORTANT] 
> Um dos objetivos da modelação é garantir que todos os nós de enriquecimento sejam expressos em JSON bem formado, que é necessário para projetar em loja de conhecimento. Isto é especialmente verdade quando uma árvore de enriquecimento contém nóns que não são JSON bem formados (por exemplo, quando um enriquecimento é parentado para um primitivo como uma corda).
>
> Reparem nos dois últimos `KeyPhrases` nós, `Entities` e. Estes são embrulhados num objeto JSON válido com o `sourceContext` . Isto é necessário como `keyphrases` e `entities` são enriquecimentos em primitivos e precisam ser convertidos para JSON válido antes de poderem ser projetados.
>


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

Adicione a habilidade do Shaper acima ao skillset. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Agora que temos todos os dados necessários para projetar para as tabelas, atualizar o objeto knowledgeStore com as definições de tabela. Neste exemplo, temos três tabelas, definidas pela definição do `tableName` , `source` e `generatedKeyName` propriedades.

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

Pode processar o seu trabalho seguindo estes passos:

1. Desagravar a ```storageConnectionString``` propriedade a uma cadeia de ligação de conta de armazenamento de propósito geral válido V2.  

1. Atualize o skillset emitindo o pedido PUT.

1. Depois de atualizar o skillset, execute o indexante. 

Agora tem uma projeção de trabalho com três mesas. Importar estas tabelas para o Power BI deve resultar na auto-descoberta das relações do Power BI.

Antes de passar ao próximo exemplo, vamos revisitar aspetos da projeção da tabela para entender a mecânica do corte e da relação de dados.

### <a name="slicing"></a>Corte 

Cortar é uma técnica que subdivide toda uma forma consolidada em partes constituintes. O resultado consiste em tabelas separadas mas relacionadas com as qual pode trabalhar individualmente.

No exemplo, `pbiShape` encontra-se a forma consolidada (ou nó de enriquecimento). Na definição de projeção, `pbiShape` é cortado em mesas adicionais, o que lhe permite retirar partes da forma, ```keyPhrases``` e . ```Entities``` . No Power BI, isto é útil, uma vez que várias entidades e keyPhrases estão associadas a cada documento, e obterá mais informações se conseguir ver entidades e frases-chave como dados categorizados.

O corte gera implicitamente uma relação entre as mesas dos pais e das crianças, utilizando a ```generatedKeyName``` tabela dos pais para criar uma coluna com o mesmo nome na mesa da criança. 

### <a name="naming-relationships"></a>Nomeação de relações

As ```generatedKeyName``` propriedades e propriedades são ```referenceKeyName``` usadas para relacionar dados entre tabelas ou mesmo entre tipos de projeção. Cada linha na mesa/projeção da criança tem uma propriedade que aponta para o progenitor. O nome da coluna ou propriedade na criança é ```referenceKeyName``` do progenitor. Quando o ```referenceKeyName``` não é fornecido, o serviço predefini-o para ```generatedKeyName``` o do progenitor. 

Power BI depende destas chaves geradas para descobrir relacionamentos dentro das tabelas. Se precisar da coluna na mesa da criança com o nome diferente, coloque a ```referenceKeyName``` propriedade na mesa dos pais. Um exemplo seria definir o ```generatedKeyName``` ID como iD na tabela de pbiDocument e como ```referenceKeyName``` DocumentID. Isto resultaria na coluna nas tabelas pbiEntities e pbiKeyPhrases contendo o documento ID sendo nomeado DocumentID.

## <a name="projecting-to-objects"></a>Projetar para objetos

As projeções de objetos não têm as mesmas limitações que as projeções de tabelas e são mais adequadas para projetar documentos grandes. Neste exemplo, todo o documento é enviado como uma projeção de objeto. As projeções dos objetos limitam-se a uma única projeção num recipiente e não podem ser cortadas.

Para definir uma projeção de objeto, use a ```objects``` matriz nas projeções. Pode gerar uma nova forma utilizando a habilidade do Shaper ou utilizar a configuração inline da projeção do objeto. Embora o exemplo das tabelas tenha demonstrado a abordagem de criar uma forma e cortar, este exemplo demonstra o uso da formação inline. 

A formação em linha é a capacidade de criar uma nova forma na definição das entradas para uma projeção. A formação inline cria um objeto anónimo idêntico ao que uma habilidade shaper produziria (no nosso caso, `pbiShape` ). A formação em linha é útil se estiver a definir uma forma que não planeia reutilizar.

A propriedade das projeções é uma matriz. Este exemplo adiciona uma nova instância de projeção à matriz, onde a definição de knowledgeStore contém projeções inline. Ao utilizar projeções inline, pode omitir a habilidade do Shaper.

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

## <a name="projecting-to-file"></a>Projetar para arquivar

As projeções dos ficheiros são imagens que são extraídas do documento de origem ou saídas de enriquecimento que podem ser projetadas fora do processo de enriquecimento. As projeções de ficheiros, semelhantes às projeções de objetos, são implementadas como bolhas no Azure Storage, e contêm a imagem. 

Para gerar uma projeção de ficheiro, utilize a `files` matriz no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contentor chamado `samplefile` .

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

## <a name="projecting-to-multiple-types"></a>Projetar para vários tipos

Um cenário mais complexo pode exigir que projete conteúdo através de tipos de projeção. Por exemplo, se precisar de projetar alguns dados como frases-chave e entidades para tabelas, guarde os resultados do OCR de texto e layout como objetos e, em seguida, projete as imagens como ficheiros. 

Este exemplo atualiza o skillset com as seguintes alterações:

1. Crie uma tabela com uma linha para cada documento.
1. Crie uma tabela relacionada com a tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
1. Crie uma tabela relacionada com a tabela de documentos com cada entidade identificada como uma linha nesta tabela.
1. Crie uma projeção de objeto com o texto de layout para cada imagem.
1. Crie uma projeção de ficheiros, projetando cada imagem extraída.
1. Crie uma tabela de referência cruzada que contenha referências à tabela de documentos, projeção de objetos com o texto de layout e a projeção do ficheiro.

Estas alterações refletem-se na definição de KnowledgeStore mais abaixo. 

### <a name="shape-data-for-cross-projection"></a>Dados de forma para projeção cruzada

Para obter as formas necessárias para estas projeções, comece por adicionar uma nova habilidade shaper que cria um objeto em forma chamado `crossProjection` . 

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

### <a name="define-table-object-and-file-projections"></a>Defina projeções de tabela, objeto e arquivo

A partir do objeto de proteção cruzada consolidado, corte o objeto em várias tabelas, capture a saída de OCR como bolhas e, em seguida, guarde a imagem como ficheiros (também no armazenamento Blob).

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

As projeções dos objetos requerem um nome de recipiente para cada projeção, projeções de objetos ou projeções de ficheiros não podem partilhar um recipiente. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relacionamentos entre tabela, objeto e projeções de arquivos

Este exemplo também destaca outra característica das projeções. Ao definir vários tipos de projeções dentro do mesmo objeto de projeção, existe uma relação expressa dentro e fora dos diferentes tipos (tabelas, objetos, ficheiros). Isto permite-lhe começar com uma fila de mesa para um documento e encontrar todo o texto OCR para as imagens dentro desse documento na projeção do objeto. 

Se não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção. Por exemplo, o seguinte corte resultará na relação das tabelas, mas sem relações entre as tabelas e as projeções do objeto (texto OCR). 

Os grupos de projeção são úteis quando pretende projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel Power BI, e outro grupo de projeção para capturar dados usados para treinar um modelo de aprendizagem automática envolto numa habilidade personalizada.

Quando se acumulam projeções de diferentes tipos, as projeções de ficheiros e objetos são geradas primeiro, e os caminhos são adicionados às tabelas.

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

## <a name="common-issues"></a>Problemas Comuns

Ao definir uma projeção, existem algumas questões comuns que podem causar resultados inesperados. Verifique se a produção na loja de conhecimento não é o que se espera.

+ Não moldar enriquecimentos de cordas em JSON válido. Quando as cordas são enriquecidas, por exemplo `merged_content` enriquecidas com frases-chave, a propriedade enriquecida é representada como uma criança dentro `merged_content` da árvore do enriquecimento. A representação padrão não é JSON bem formada. Assim, na hora da projeção, certifique-se de transformar o enriquecimento em um objeto JSON válido com um nome e um valor.

+ Omitindo o ```/*``` no final de um caminho de origem. Se a fonte de uma projeção `/document/pbiShape/keyPhrases` for, a matriz de frases-chave é projetada como um único objeto/linha. Em vez disso, desajei o caminho da origem `/document/pbiShape/keyPhrases/*` para produzir uma única linha ou objeto para cada uma das frases-chave.

+ Erros de sintaxe do caminho. Os seletores de caminhos são sensíveis ao caso e podem levar a avisos de entrada em falta se não utilizar a caixa exata para o seletor.

## <a name="next-steps"></a>Passos seguintes

Os exemplos deste artigo demonstram padrões comuns sobre como criar projeções. Agora que tem uma boa compreensão dos conceitos, está mais bem equipado para construir projeções para o seu cenário específico.

Ao explorar novas funcionalidades, considere o enriquecimento incremental como o seu próximo passo. O enriquecimento incremental baseia-se no caching, que permite reutilizar quaisquer enriquecimentos que não sejam afetados por uma modificação de skillset. Isto é especialmente útil para oleodutos que incluem OCR e análise de imagem.

> [!div class="nextstepaction"]
> [Introdução ao enriquecimento incremental e caching](cognitive-search-incremental-indexing-conceptual.md)

Para uma visão geral sobre as projeções, saiba mais sobre capacidades como grupos e corte, e como [as define num skillset](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções numa loja de conhecimento](knowledge-store-projection-overview.md)