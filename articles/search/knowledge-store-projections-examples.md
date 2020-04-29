---
title: Defina projeções numa loja de conhecimento
titleSuffix: Azure Cognitive Search
description: Exemplos de padrões comuns sobre como projetar documentos enriquecidos na loja de conhecimentos para utilização com Power BI ou Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943669"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Projeções de lojas de conhecimento: Como moldar e exportar enriquecimentos

> [!IMPORTANT] 
> A loja de conhecimento está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . A [versão REST API 2019-05-06-Preview](search-api-preview.md) fornece funcionalidades de pré-visualização. Existe atualmente um suporte de portal limitado e nenhum suporte sdk .NET.

As projeções são a expressão física de documentos enriquecidos numa loja de conhecimento. O uso eficaz dos seus documentos enriquecidos requer estrutura. Neste artigo, você vai explorar tanto a estrutura como as relações, aprendendo como construir propriedades de projeção, bem como como relacionar dados entre os tipos de projeção que cria. 

Para criar uma projeção, deve moldar os dados utilizando uma [habilidade Shaper](cognitive-search-skill-shaper.md) para criar um objeto personalizado ou utilizar a sintaxe de formação inline dentro de uma definição de projeção. 

Uma forma de dados contém todos os dados que pretende projetar, formados como uma hierarquia de nós. Este artigo mostra-lhe várias técnicas para moldar dados para que possa ser projetado em estruturas físicas que sejam propícias a relatórios, análises ou processamento a jusante. 

Os exemplos apresentados neste artigo podem ser encontrados nesta amostra REST [API](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), que pode descarregar e executar num cliente HTTP.

## <a name="introduction-to-the-examples"></a>Introdução aos exemplos

Se estiver familiarizado com [as projeções,](knowledge-store-projection-overview.md)lembrar-se-á de que existem três tipos:

+ Tabelas
+ Objetos
+ Ficheiros

As projeções da tabela são armazenadas no armazenamento da Mesa Azure. As projeções de objetos e ficheiros são escritas para armazenamento de bolhas, onde as projeções de objetos são guardadas como ficheiros JSON, e podem conter conteúdo do documento de origem, bem como quaisquer saídas ou enriquecimentos de habilidades. O gasoduto de enriquecimento também pode extrair binários como imagens, estes binários são projetados como projeções de ficheiros. Quando um objeto binário é projetado como uma projeção de objeto, apenas os metadados associados a ele são guardados como uma bolha JSON. 

Para entender a intersecção entre a modelação de dados e as projeções, usaremos o seguinte skillset como base para explorar várias configurações. Este skillset processa imagem crua e conteúdo de texto. As projeções serão definidas a partir do conteúdo do documento e das saídas das competências, para os cenários que queremos apoiar.

> [!IMPORTANT] 
> Ao experimentar as projeções, é útil definir a propriedade de [cache indexante](search-howto-incremental-index.md) para garantir o controlo de custos. As projeções de edição resultarão em que todo o documento seja novamente enriquecido se a cache do indexante não estiver definida. Quando a cache é definida e apenas as projeções atualizadas, as execuções de skillset para documentos anteriormente enriquecidos não resultam em quaisquer novas taxas de Serviços Cognitivos.

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

Utilizando este skillset, `knowledgeStore` com o seu nulo como base, o nosso primeiro exemplo preenche o `knowledgeStore` objeto, configurado com projeções que criam estruturas de dados tabular que podemos usar noutros cenários. 

## <a name="projecting-to-tables"></a>Projeção para mesas

Projetar para tabelas em Armazenamento Azure é útil para reportar e analisar usando ferramentas como Power BI. O Power BI pode ler a partir de mesas e descobrir relações com base nas teclas que são geradas durante a projeção. Se está a tentar construir um dashboard, ter dados relacionados simplificará essa tarefa. 

Vamos supor que estamos a tentar construir um painel onde possamos visualizar as frases-chave extraídas de documentos como uma nuvem de palavras. Para criar a estrutura de dados certa, podemos adicionar uma habilidade Shaper ao skillset para criar uma forma personalizada que tenha os detalhes específicos do documento e frases-chave. A forma personalizada `pbiShape` será `document` chamada no nó raiz.

> [!NOTE] 
> As projeções de tabelas são tabelas de armazenamento Azure, regidas pelos limites de armazenamento impostos pelo Armazenamento Azure. Para mais informações, consulte [os limites](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)de armazenamento da mesa. É útil saber que o tamanho da entidade não pode exceder 1 MB e uma única propriedade não pode ser maior do que 64 KB. Estes constrangimentos fazem das tabelas uma boa solução para armazenar um grande número de pequenas entidades.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Usando uma habilidade shaper para criar uma forma personalizada

Crie uma forma personalizada que possa projetar para armazenamento de mesa. Sem uma forma personalizada, uma projeção só pode referir um único nó (uma projeção por saída). Criar uma forma personalizada permite-lhe agregar vários elementos num novo todo lógico que pode ser projetado como uma única mesa, ou fatiado e distribuído por uma coleção de mesas. 

Neste exemplo, a forma personalizada combina metadados e entidades identificadas e frases-chave. O objeto `pbiShape` é chamado e `/document`é pai sob . 

> [!IMPORTANT] 
> Um dos objetivos da formação é garantir que todos os nós de enriquecimento sejam expressos em JSON bem formado, que é necessário para projetar para a loja de conhecimento. Isto é especialmente verdade quando uma árvore de enriquecimento contém nós que não são bem formados JSON (por exemplo, quando um enriquecimento é pai de um primitivo como uma corda).
>
> Reparem nos últimos `KeyPhrases` dois `Entities`nós, e. Estes são embrulhados num objeto JSON válido com o `sourceContext`. Isto é necessário `keyphrases` `entities` como e são enriquecimentos em primitivos e precisa ser convertido para JSON válido antes de ser projetado.
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

Adicione a habilidade de Shaper acima à habilidade. 

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

Agora que temos todos os dados necessários para projetar para as tabelas, atualizar o objeto knowledgeStore com as definições de tabela. Neste exemplo, temos três tabelas, `tableName`definidas pela definição do , `source` e `generatedKeyName` propriedades.

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

1. Deteto a ```storageConnectionString``` propriedade numa cadeia de ligação de conta de armazenamento de uso geral V2 válida.  

1. Atualize a habilidade através da emissão do pedido PUT.

1. Depois de atualizar a habilidade, executar o indexador. 

Agora tem uma projeção de trabalho com três mesas. Importar estas tabelas para o Power BI deve resultar na descoberta automática do Power BI das relações.

Antes de seguir em frente no exemplo seguinte, vamos revisitar aspetos da projeção da tabela para entender a mecânica do corte e relacionar dados.

### <a name="slicing"></a>Corte 

Cortar é uma técnica que subdivide toda uma forma consolidada em partes constituintes. O resultado consiste em tabelas separadas mas relacionadas com as que pode trabalhar individualmente.

No exemplo, `pbiShape` é a forma consolidada (ou nó de enriquecimento). Na definição `pbiShape` de projeção, é cortado em tabelas adicionais, ```keyPhrases``` o ```Entities```que lhe permite retirar partes da forma, e . No Power BI, isto é útil, uma vez que várias entidades e teclas Estão associadas a cada documento, e você terá mais insights se você pode ver entidades e chavePhrases como dados categorizados.

Cortar implicitamente gera uma relação entre as mesas ```generatedKeyName``` dos pais e das crianças, utilizando a tabela dos pais para criar uma coluna com o mesmo nome na mesa da criança. 

### <a name="naming-relationships"></a>Relações de nomeação

As ```generatedKeyName``` ```referenceKeyName``` propriedades e propriedades são usadas para relacionar dados entre tabelas ou mesmo em tipos de projeção. Cada linha na tabela/projeção da criança tem uma propriedade que aponta para o progenitor. O nome da coluna ou propriedade ```referenceKeyName``` da criança é do progenitor. Quando ```referenceKeyName``` o não é fornecido, o ```generatedKeyName``` serviço desfaz-o ao do progenitor. 

O Power BI baseia-se nestas chaves geradas para descobrir relações dentro das tabelas. Se precisar da coluna na mesa infantil com ```referenceKeyName``` o nome diferente, coloque a propriedade na mesa dos pais. Um exemplo seria definir ```generatedKeyName``` o ID como ID na ```referenceKeyName``` tabela pbiDocument e no documentoID. Isto resultaria na coluna nas tabelas pbiEntidades e pbiKeyPhrases que continham o id do documento sendo nomeado DocumentID.

## <a name="projecting-to-objects"></a>Projeção de objetos

As projeções de objetos não têm as mesmas limitações que as projeções de tabela e são mais adequadas para a projeção de grandes documentos. Neste exemplo, projetamos todo o documento para uma projeção de objetos. As projeções dos objetos limitam-se a uma única projeção num recipiente e não podem ser cortadas.

Para definir uma projeção de ```objects``` objetos, usaremos a matriz nas projeções. Pode gerar uma nova forma utilizando a habilidade Shaper ou utilizar a formação linear da projeção do objeto. Embora o exemplo das tabelas tenha demonstrado a abordagem de criar uma forma e cortar, este exemplo demonstra o uso da formação linear. 

A formação inline é a capacidade de criar uma nova forma na definição das inputs para uma projeção. A formação inline cria um objeto anónimo idêntico ao que uma `pbiShape`habilidade Shaper produziria (no nosso caso, ). A formação inline é útil se estiver a definir uma forma que não planeia reutilizar.

A propriedade de projeções é uma matriz. Para este exemplo, estamos adicionando uma nova exemplo de projeção à matriz, onde a definição de knowledgeStore contém projeções line. Ao utilizar projeções inline, pode omitir a habilidade shaper.

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

## <a name="projecting-to-file"></a>Projetando arquivar

As projeções de ficheiros são imagens que são extraídas do documento de origem ou saídas de enriquecimento que podem ser projetadas fora do processo de enriquecimento. As projeções de ficheiros, semelhantes às projeções de objetos, são implementadas como bolhas no Armazenamento Azure, e contêm a imagem. 

Para gerar uma projeção `files` de ficheiros, usamos a matriz no objeto de projeção. Este exemplo projeta todas as imagens extraídas do documento para um contentor chamado `samplefile`.

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

Um cenário mais complexo pode exigir que você projete conteúdo através de tipos de projeção. Por exemplo, se precisar de projetar alguns dados como frases-chave e entidades para as tabelas, guarde os resultados do OCR de texto e texto de layout como objetos e, em seguida, projete as imagens como ficheiros. 

Neste exemplo, as atualizações ao skillset incluem as seguintes alterações:

1. Crie uma tabela com uma linha para cada documento.
1. Crie uma tabela relacionada com a tabela de documentos com cada frase-chave identificada como uma linha nesta tabela.
1. Crie uma tabela relacionada com a tabela de documentos com cada entidade identificada como uma linha nesta tabela.
1. Crie uma projeção de objeto com o texto de layout para cada imagem.
1. Crie uma projeção de ficheiros, projetando cada imagem extraída.
1. Crie uma tabela de referência cruzada que contenha referências à tabela de documentos, projeção de objetos com o texto de layout e a projeção do ficheiro.

Estas alterações refletem-se na definição do knowledgeStore mais abaixo. 

### <a name="shape-data-for-cross-projection"></a>Dados de forma para projeção cruzada

Para obter as formas que precisamos para estas projeções, comece por `crossProjection`adicionar uma nova habilidade Shaper que cria um objeto em forma chamado . 

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

### <a name="define-table-object-and-file-projections"></a>Definir projeções de tabela, objeto e arquivo

A partir do objeto de projeção transversal consolidado, podemos cortar o objeto em várias tabelas, capturar a saída de OCR como bolhas e, em seguida, guardar a imagem como ficheiros (também no armazenamento blob).

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

### <a name="relationships-among-table-object-and-file-projections"></a>Relações entre projeções de mesa, objeto e arquivo

Este exemplo também destaca outra característica das projeções. Ao definir vários tipos de projeções dentro do mesmo objeto de projeção, existe uma relação expressa dentro e em todos os diferentes tipos (tabelas, objetos, ficheiros), permitindo-lhe começar com uma linha de mesa para um documento e encontrar todo o texto oCR para as imagens dentro desse documento na projeção do objeto. 

Se não quiser os dados relacionados, defina as projeções em diferentes objetos de projeção. Por exemplo, o seguinte corte resultará na relação das tabelas, mas sem relações entre as tabelas e as projeções do objeto (texto OCR). 

Os grupos de projeção são úteis quando se pretende projetar os mesmos dados em diferentes formas para diferentes necessidades. Por exemplo, um grupo de projeção para o painel power bi, e outro grupo de projeção para capturar dados usados para treinar um modelo de aprendizagem automática envolto numa habilidade personalizada.

Ao construir projeções de diferentes tipos, as projeções de ficheiros e objetos são geradas primeiro, e os caminhos são adicionados às tabelas.

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

Ao definir uma projeção, existem algumas questões comuns que podem causar resultados inesperados. Verifique estes problemas se a produção na loja de conhecimento não é o que se espera.

+ Não moldar enriquecimentos de cordas em JSON válido. Quando as cordas são enriquecidas, por exemplo `merged_content` enriquecidas com frases-chave, a propriedade enriquecida é representada como uma criança dentro da árvore de `merged_content` enriquecimento. A representação padrão não é bem formada JSON. Assim, no momento da projeção, certifique-se de transformar o enriquecimento num objeto JSON válido com um nome e um valor.

+ Omitindo o ```/*``` final de um caminho de origem. Se a fonte de `/document/pbiShape/keyPhrases`uma projeção for, a matriz de frases-chave é projetada como um único objeto/linha. Em vez disso, `/document/pbiShape/keyPhrases/*` desloque o caminho de origem para produzir uma única linha ou objeto para cada uma das frases-chave.

+ Erros de sintaxe de caminho. Os selecionadores de trajetória são sensíveis a casos e podem levar a avisos de entrada em falta se não utilizar a caixa exata para o seletor.

## <a name="next-steps"></a>Passos seguintes

Os exemplos deste artigo demonstram padrões comuns sobre como criar projeções. Agora que tem uma boa compreensão dos conceitos, está mais bem equipado para construir projeções para o seu cenário específico.

Ao explorar novas funcionalidades, considere o enriquecimento incremental como o seu próximo passo. O enriquecimento incremental baseia-se no cache, que permite reutilizar quaisquer enriquecimentos que não sejam afetados por uma modificação de skillset. Isto é especialmente útil para oleodutos que incluem OCR e análise de imagem.

> [!div class="nextstepaction"]
> [Introdução ao enriquecimento incremental e ao cache](cognitive-search-incremental-indexing-conceptual.md)

Para uma visão geral das projeções, saiba mais sobre capacidades como grupos e corte, e como as define num conjunto de [habilidades](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Projeções em uma loja de conhecimento](knowledge-store-projection-overview.md)

