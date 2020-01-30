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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837736"
---
# <a name="document-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de documentos

> [!IMPORTANT] 
> Esta habilidade está atualmente em pré-visualização pública. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Atualmente não existe nenhum suporte de Portal ou .NET SDK.

A habilidade de extração de **documentos** extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. Isto permite-lhe aproveitar o passo de extração de documentos que normalmente acontece antes da execução do skillset com ficheiros que podem ser gerados por outras habilidades.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). Os encargos acumulam-se quando se ligam para apis nos Serviços Cognitivos, e para a extração de imagem como parte da fase de quebra de documentos na indexação. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis aos casos.

| Entradas            | Valores Permitidos | Descrição |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Decidiu `default` para a extração de documentos a partir de ficheiros que não sejam texto puro ou json. Definir para `text` para melhorar o desempenho em ficheiros de texto simples. Decidiu `json` extrair conteúdo estruturado a partir de ficheiros JSON. Se `parsingMode` não for definido explicitamente, será definido para `default`. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Decidiu `contentAndMetadata` extrair todos os metadados e conteúdos texuais de cada ficheiro. Definir para `allMetadata` extrair apenas os [metadados específicos do tipo de conteúdo](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por exemplo, metadados únicos a penas .png). Se `dataToExtract` não for definido explicitamente, será definido para `contentAndMetadata`. |
| `configuration` | Veja abaixo. | Um dicionário de parâmetros opcionais que ajustam a forma como a extração do documento é realizada. Consulte a tabela abaixo para obter descrições de propriedades de configuração suportadas. |

| Parâmetro de configuração   | Valores Permitidos | Descrição |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Despleida para `none` ignorar imagens incorporadas ou ficheiros de imagem no conjunto de dados. Este é o padrão. <br/>Para a análise de [imagens utilizando habilidades cognitivas,](cognitive-search-concept-image-scenarios.md)definir para `generateNormalizedImages` para que a habilidade crie uma variedade de imagens normalizadas como parte da quebra de documentos. Esta ação exige que `parsingMode` `default` e `dataToExtract` esteja prevista para `contentAndMetadata`. Uma imagem normalizada refere-se a um processamento adicional, resultando numa saída uniforme de imagem, dimensionada e girada para promover uma renderização consistente quando se inclui imagens em resultados de pesquisa visual (por exemplo, fotografias do mesmo tamanho num controlo de gráficos como visto na demonstração do [JFK).](https://github.com/Microsoft/AzureSearch_JFK_Files) Esta informação é gerada para cada imagem quando utiliza esta opção.  <br/>Se definir para `generateNormalizedImagePerPage`, os ficheiros PDF serão tratados de forma diferente, na medida em que, em vez de extrair imagens embutidas, cada página será renderizada como uma imagem e normalizada em conformidade.  Os tipos de ficheiros não PDF serão tratados da mesma forma que `generateNormalizedImages` foi definido.
| `normalizedImageMaxWidth` | Qualquer inteiro entre 50-10000 | A largura máxima (em pixels) para imagens normalizadas geradas. O padrão é 2000. | 
| `normalizedImageMaxHeight` | Qualquer inteiro entre 50-10000 | A altura máxima (em pixels) para imagens normalizadas geradas. O padrão é 2000. |

> [!NOTE]
> O padrão de 2000 pixels para as imagens normalizadas a largura e altura máximas baseia-se nos tamanhos máximos suportados pela [habilidade OCR](cognitive-search-skill-ocr.md) e pela habilidade de análise de [imagem.](cognitive-search-skill-image-analysis.md) A [habilidade OCR](cognitive-search-skill-ocr.md) suporta uma largura máxima e altura de 4200 para línguas não inglesas, e 10000 para inglês.  Se aumentar os limites máximos, o processamento poderá falhar em imagens maiores, dependendo da definição de skillset e da linguagem dos documentos. 
## <a name="skill-inputs"></a>Entradas de habilidades

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

 - Definindo o parâmetro `allowSkillsetToReadFileData` na definição do seu indexante para "verdadeiro".  Isto criará um caminho `/document/file_data` que é um objeto que representa os dados originais do ficheiro descarregados a partir da sua fonte de dados blob. Este parâmetro aplica-se apenas aos dados no armazenamento blob.

 - Regulação do parâmetro `imageAction` na definição do indexador para um valor diferente do `none`.  Isto cria um conjunto de imagens que seguem a convenção necessária para a entrada nesta habilidade se forem passadas individualmente (isto é, `/document/normalized_images/*`).

 - Ter uma habilidade personalizada devolve um objeto json definido exatamente como acima.  O parâmetro `$type` deve ser definido exatamente `file` e o parâmetro `data` deve ser a base 64 codificada dos dados de matriz de byte do conteúdo do ficheiro.

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída    | Descrição |
|--------------|-------------|
| conteúdo | O conteúdo textual do documento. |
| normalized_images | Quando o `imageAction` for definido para um valor diferente do `none`, o novo campo *normalized_images* conterá um conjunto de imagens. Consulte [a documentação para a extração](cognitive-search-concept-image-scenarios.md) de imagem para obter mais detalhes sobre o formato de saída de cada imagem. |

##  <a name="sample-definition"></a>Definição de exemplo

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

## <a name="see-also"></a>Ver também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como processar e extrair informações de imagens em cenários de pesquisa cognitiva](cognitive-search-concept-image-scenarios.md)