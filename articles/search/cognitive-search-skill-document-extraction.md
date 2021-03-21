---
title: Habilidade cognitiva de extração de documentos
titleSuffix: Azure Cognitive Search
description: Extrai conteúdo de um ficheiro dentro do gasoduto de enriquecimento. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: 144e8058e640f98dc6b0ef60534405525532b00e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547871"
---
# <a name="document-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de documentos

A habilidade **de extração** de documentos extrai o conteúdo de um ficheiro dentro do gasoduto de enriquecimento. Isto permite-lhe tirar partido do passo de extração de documentos que normalmente acontece antes da execução do skillset com ficheiros que podem ser gerados por outras habilidades.

> [!NOTE]
> À medida que expande o âmbito, aumentando a frequência do processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, terá de [anexar um recurso de Serviços Cognitivos faturados.](cognitive-search-attach-cognitive-services.md) As taxas acumulam-se ao chamar APIs em Serviços Cognitivos, e para a extração de imagem como parte da fase de cisão de documentos na indexação. Não há encargos para a extração de texto a partir de documentos.
>
> A execução de competências incorporadas é cobrada pelo preço de pagamento dos [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. Os preços de extração de imagem são descritos na [página de preços](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros são sensíveis às maiúsculas e minúsculas.

| Entradas | Valores Permitidos | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Configurar para `default` extração de documentos a partir de ficheiros que não são texto puro ou json. Definido `text` para melhorar o desempenho em ficheiros de texto simples. Configurar `json` para extrair conteúdo estruturado dos ficheiros json. Se `parsingMode` não for definido explicitamente, será definido para `default` . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Configurar `contentAndMetadata` para extrair todos os metadados e conteúdo textual de cada ficheiro. Configurado `allMetadata` para extrair apenas as propriedades de [metadados para o tipo de conteúdo](search-blob-metadata-properties.md) (por exemplo, metadados exclusivos de apenas .png ficheiros). Se `dataToExtract` não for definido explicitamente, será definido para `contentAndMetadata` . |
| `configuration` | Veja abaixo. | Um dicionário de parâmetros opcionais que ajustam a forma como a extração do documento é realizada. Consulte a tabela abaixo para obter descrições das propriedades de configuração suportadas. |

| Parâmetro de configuração   | Valores Permitidos | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Configurar `none` para ignorar imagens incorporadas ou ficheiros de imagem no conjunto de dados. Esta é a predefinição. <br/>Para [análise de imagem usando habilidades cognitivas,](cognitive-search-concept-image-scenarios.md)definido para ter a habilidade criar uma variedade de `generateNormalizedImages` imagens normalizadas como parte da quebra de documentos. Esta ação requer que `parsingMode` esteja definida e está definida para `default` `dataToExtract` `contentAndMetadata` . Uma imagem normalizada refere-se a um processamento adicional, resultando numa saída uniforme de imagem, dimensionada e rotativa para promover uma renderização consistente quando se incluem imagens em resultados de pesquisa visual (por exemplo, fotografias do mesmo tamanho num controlo de gráfico como visto na [demonstração do JFK).](https://github.com/Microsoft/AzureSearch_JFK_Files) Esta informação é gerada para cada imagem quando utiliza esta opção.  <br/>Se `generateNormalizedImagePerPage` configurar, os ficheiros PDF serão tratados de forma diferente, em vez de extrair imagens incorporadas, cada página será feita como uma imagem e normalizada em conformidade.  Os tipos de ficheiros não PDF serão tratados da mesma forma que se `generateNormalizedImages` estivesse definido.
| `normalizedImageMaxWidth` | Qualquer inteiro entre 50-10000 | A largura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. | 
| `normalizedImageMaxHeight` | Qualquer inteiro entre 50-10000 | A altura máxima (em pixels) para imagens normalizadas geradas. A predefinição é 2 000. |

> [!NOTE]
> O padrão de 2000 pixels para as imagens normalizadas a largura e altura máxima baseia-se nos tamanhos máximos suportados pela [habilidade de OCR](cognitive-search-skill-ocr.md) e pela habilidade de análise de [imagem](cognitive-search-skill-image-analysis.md). A [habilidade OCR](cognitive-search-skill-ocr.md) suporta uma largura e altura máximas de 4200 para línguas não inglesas, e 10000 para inglês.  Se aumentar os limites máximos, o processamento pode falhar em imagens maiores, dependendo da definição de skillset e da linguagem dos documentos. 
## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Description |
|--------------------|-------------|
| `file_data` | O ficheiro de que o conteúdo deve ser extraído. |

A entrada "file_data" deve ser um objeto definido da seguinte forma:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Este objeto de referência de ficheiro pode ser gerado de uma de 3 maneiras:

 - Definindo o `allowSkillsetToReadFileData` parâmetro na definição do indexante para "verdadeiro".  Isto criará um caminho `/document/file_data` que representa os dados originais do ficheiro descarregados a partir da sua fonte de dados blob. Este parâmetro aplica-se apenas aos dados no armazenamento blob.

 - Definir o `imageAction` parâmetro na definição do indexante para um valor diferente de `none` .  Isto cria um conjunto de imagens que seguem a convenção necessária para a entrada desta habilidade se for passada individualmente (ou `/document/normalized_images/*` seja, ).

 - Ter uma habilidade personalizada devolver um objeto json definido exatamente como acima.  O `$type` parâmetro deve ser definido exatamente e o parâmetro deve ser a base `file` `data` 64 codificada por dados de matriz do conteúdo do ficheiro.

## <a name="skill-outputs"></a>Saídas de competências

| Nome de saída    | Description |
|--------------|-------------|
| `content` | O conteúdo textual do documento. |
| `normalized_images`   | Quando o `imageAction` valor for definido para outro `none` valor, o novo campo de *normalized_images* conterá uma variedade de imagens. Consulte [a documentação para extração de imagem](cognitive-search-concept-image-scenarios.md) para obter mais detalhes sobre o formato de saída de cada imagem. |

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
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Entrada de exemplo

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


##  <a name="sample-output"></a>Saída de exemplo

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

+ [Competências incorporadas](cognitive-search-predefined-skills.md)
+ [Como definir um skillset](cognitive-search-defining-skillset.md)
+ [Como processar e extrair informação de imagens em cenários de pesquisa cognitiva](cognitive-search-concept-image-scenarios.md)