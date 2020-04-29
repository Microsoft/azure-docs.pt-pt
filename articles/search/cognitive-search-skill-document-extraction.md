---
title: Habilidade cognitiva de extração de documentos (pré-visualização)
titleSuffix: Azure Cognitive Search
description: Extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. Esta habilidade está atualmente em pré-visualização pública.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76837736"
---
# <a name="document-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de documentos

> [!IMPORTANT] 
> Esta habilidade está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure . Atualmente não existe nenhum suporte de Portal ou .NET SDK.

A habilidade de extração de **documentos** extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. Isto permite-lhe aproveitar o passo de extração de documentos que normalmente acontece antes da execução do skillset com ficheiros que podem ser gerados por outras habilidades.

> [!NOTE]
> À medida que expande o âmbito aumentando a frequência do processamento, adicionando mais documentos, ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) Os encargos acumulam-se quando se ligam para apis nos Serviços Cognitivos, e para a extração de imagem como parte da fase de quebra de documentos na indexação. Não há encargos para a extração de texto de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas            | Valores Permitidos | Descrição |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Decidiu `default` para a extração de documentos a partir de ficheiros que não sejam texto puro ou json. Preparar `text` para melhorar o desempenho em ficheiros de texto simples. Definir `json` para extrair conteúdo estruturado a partir de ficheiros JSON. Se `parsingMode` não for definido explicitamente, `default`será definido para . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Deset `contentAndMetadata` para extrair todos os metadados e conteúdo textual de cada ficheiro. Definir `allMetadata` para extrair apenas os [metadados específicos do tipo de conteúdo](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por exemplo, metadados únicos apenas .ficheiros .png). Se `dataToExtract` não for definido explicitamente, `contentAndMetadata`será definido para . |
| `configuration` | Veja abaixo. | Um dicionário de parâmetros opcionais que ajustam a forma como a extração do documento é realizada. Consulte a tabela abaixo para obter descrições de propriedades de configuração suportadas. |

| Parâmetro de configuração   | Valores Permitidos | Descrição |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Despleida para `none` ignorar imagens incorporadas ou ficheiros de imagem no conjunto de dados. Esta é a predefinição. <br/>Para a análise de imagem `generateNormalizedImages` utilizando [habilidades cognitivas,](cognitive-search-concept-image-scenarios.md)definido para que a habilidade crie uma variedade de imagens normalizadas como parte da quebra de documentos. Esta ação requer `parsingMode` que `default` esteja `dataToExtract` definida `contentAndMetadata`e está definida para . Uma imagem normalizada refere-se a um processamento adicional, resultando numa saída uniforme de imagem, dimensionada e girada para promover uma renderização consistente quando se inclui imagens em resultados de pesquisa visual (por exemplo, fotografias do mesmo tamanho num controlo de gráficos como visto na demonstração do [JFK).](https://github.com/Microsoft/AzureSearch_JFK_Files) Esta informação é gerada para cada imagem quando utiliza esta opção.  <br/>Se definir `generateNormalizedImagePerPage`para , os ficheiros PDF serão tratados de forma diferente, na medida em que, em vez de extrair imagens incorporadas, cada página será renderizada como uma imagem e normalizada em conformidade.  Os tipos de ficheiros não PDF `generateNormalizedImages` serão tratados da mesma forma que foram definidos.
| `normalizedImageMaxWidth` | Qualquer inteiro entre 50-10000 | A largura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. | 
| `normalizedImageMaxHeight` | Qualquer inteiro entre 50-10000 | A altura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. |

> [!NOTE]
> O padrão de 2000 pixels para as imagens normalizadas a largura e altura máximas baseia-se nos tamanhos máximos suportados pela [habilidade OCR](cognitive-search-skill-ocr.md) e pela habilidade de análise de [imagem.](cognitive-search-skill-image-analysis.md) A [habilidade OCR](cognitive-search-skill-ocr.md) suporta uma largura máxima e altura de 4200 para línguas não inglesas, e 10000 para inglês.  Se aumentar os limites máximos, o processamento poderá falhar em imagens maiores, dependendo da definição de skillset e da linguagem dos documentos. 
## <a name="skill-inputs"></a>Inputs de habilidade

| Nome de entrada     | Descrição |
|--------------------|-------------|
| file_data | O ficheiro de que o conteúdo deve ser extraído. |

A entrada "file_data" deve ser um objeto definido da seguinte forma:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Este objeto de referência de ficheiro pode ser gerado de uma de 3 maneiras:

 - Definição `allowSkillsetToReadFileData` do parâmetro na definição do seu indexante para "verdadeiro".  Isto criará `/document/file_data` um caminho que representa um objeto que representa os dados originais do ficheiro descarregados a partir da sua fonte de dados blob. Este parâmetro aplica-se apenas aos dados no armazenamento blob.

 - Regulação `imageAction` do parâmetro na definição do `none`seu indexante para um valor diferente de .  Isto cria um conjunto de imagens que seguem a convenção necessária para `/document/normalized_images/*`a entrada nesta habilidade se forem passadas individualmente (isto é, ).

 - Ter uma habilidade personalizada devolve um objeto json definido exatamente como acima.  O `$type` parâmetro deve ser `file` definido `data` exatamente e o parâmetro deve ser a base 64 codificada dos dados de matriz de byte do conteúdo do ficheiro.

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome de saída    | Descrição |
|--------------|-------------|
| conteúdo | O conteúdo textual do documento. |
| normalized_images | Quando `imageAction` o campo for definido `none`para um valor diferente, o novo campo *de normalized_images* conterá um conjunto de imagens. Consulte [a documentação para a extração](cognitive-search-concept-image-scenarios.md) de imagem para obter mais detalhes sobre o formato de saída de cada imagem. |

##  <a name="sample-definition"></a>Definição de amostra

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Entrada da amostra

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Resultado da amostra

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Consulte também

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de habilidades](cognitive-search-defining-skillset.md)
+ [Como processar e extrair informações de imagens em cenários de pesquisa cognitiva](cognitive-search-concept-image-scenarios.md)