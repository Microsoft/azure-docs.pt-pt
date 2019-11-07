---
title: Habilidade de pesquisa cognitiva de extração de documentos (versão prévia)
titleSuffix: Azure Cognitive Search
description: Extrai o conteúdo de um arquivo dentro do pipeline de enriquecimento. Esta habilidade está atualmente em visualização pública.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: e4274f1cb2eacaf78ab83bfb9d637d044d2290bd
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720121"
---
# <a name="document-extraction-cognitive-skill"></a>Habilidade cognitiva de extração de documentos

> [!IMPORTANT] 
> Esta habilidade está atualmente em visualização pública. A funcionalidade de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-Preview](search-api-preview.md) fornece recursos de visualização. No momento, não há suporte para Portal ou SDK do .NET.

A habilidade de **extração de documentos** extrai o conteúdo de um arquivo dentro do pipeline de enriquecimento. Isso permite que você aproveite a etapa de extração de documentos que normalmente ocorre antes da execução do Configurador de habilidades com arquivos que podem ser gerados por outras habilidades.

> [!NOTE]
> Ao expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de ia, você precisará [anexar um recurso de serviços cognitivas cobráveis](cognitive-search-attach-cognitive-services.md). As cobranças são acumuladas ao chamar APIs em serviços cognitivas e para extração de imagem como parte do estágio de quebra de documento na indexação. Não há encargos para a extração de texto de documentos.
>
> A execução de habilidades internas é cobrada pelo [preço pago pelo uso dos serviços cognitivas](https://azure.microsoft.com/pricing/details/cognitive-services/)existentes. O preço de extração de imagem é descrito na [página de preços](https://go.microsoft.com/fwlink/?linkid=2042400).
## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. util. DocumentExtractionSkill

## <a name="skill-parameters"></a>Parâmetros de habilidade

Os parâmetros diferenciam maiúsculas de minúsculas.

| Entradas            | Valores Permitidos | Descrição |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Defina como `default` para extração de documentos de arquivos que não são texto puro ou JSON. Defina como `text` para melhorar o desempenho em arquivos de texto sem formatação. Defina como `json` para extrair conteúdo estruturado de arquivos JSON. Se `parsingMode` não for definido explicitamente, ele será definido como `default`. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Defina como `contentAndMetadata` para extrair todos os metadados e conteúdo textual de cada arquivo. Defina como `allMetadata` para extrair somente os [metadados específicos do tipo de conteúdo](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) (por exemplo, metadados exclusivos para arquivos apenas. png). Se `dataToExtract` não for definido explicitamente, ele será definido como `contentAndMetadata`. |
| `configuration` | Veja abaixo. | Um dicionário de parâmetros opcionais que ajustam como a extração de documento é executada. Consulte a tabela abaixo para obter descrições das propriedades de configuração com suporte. |

| Parâmetro de configuração   | Valores Permitidos | Descrição |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Defina como `none` para ignorar imagens inseridas ou arquivos de imagem no conjunto de dados. Esse é o padrão. <br/>Para [análise de imagem usando habilidades cognitivas](cognitive-search-concept-image-scenarios.md), defina como `generateNormalizedImages` para que a habilidade crie uma matriz de imagens normalizadas como parte da quebra de documento. Essa ação requer que `parsingMode` seja definida como `default` e `dataToExtract` seja definido como `contentAndMetadata`. Uma imagem normalizada refere-se ao processamento adicional, resultando em saída de imagem uniforme, dimensionada e girada para promover a renderização consistente quando você inclui imagens nos resultados da pesquisa visual (por exemplo, fotografias de mesmo tamanho em um controle de grafo, como visto no [JFK demonstração](https://github.com/Microsoft/AzureSearch_JFK_Files)). Essas informações são geradas para cada imagem quando você usa essa opção.  <br/>Se você definir como `generateNormalizedImagePerPage`, os arquivos PDF serão tratados de forma diferente, em vez de extrair imagens inseridas, cada página será renderizada como uma imagem e normalizada adequadamente.  Os tipos de arquivo não PDF serão tratados da mesma forma como se `generateNormalizedImages` foi definido.
| `normalizedImageMaxWidth` | Qualquer inteiro entre 50-10000 | A largura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. | 
| `normalizedImageMaxHeight` | Qualquer inteiro entre 50-10000 | A altura máxima (em pixels) para as imagens normalizadas geradas. O padrão é 2000. |

> [!NOTE]
> O padrão de 2000 pixels para a largura e a altura máximas das imagens normalizadas baseia-se nos tamanhos máximos com suporte da [habilidade de OCR](cognitive-search-skill-ocr.md) e da [habilidade de análise de imagem](cognitive-search-skill-image-analysis.md). A [habilidade de OCR](cognitive-search-skill-ocr.md) dá suporte a uma largura e altura máxima de 4200 para idiomas que não estão em inglês e 10000 para inglês.  Se você aumentar os limites máximos, o processamento poderá falhar em imagens maiores, dependendo da sua definição de Skills e do idioma dos documentos. 
## <a name="skill-inputs"></a>Entradas de habilidades

| Nome de entrada     | Descrição |
|--------------------|-------------|
| file_data | O arquivo do qual o conteúdo deve ser extraído. |

A entrada "file_data" deve ser um objeto definido da seguinte maneira:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Esse objeto de referência de arquivo pode ser gerado de uma das três maneiras:

 - Definindo o parâmetro `allowSkillsetToReadFileData` na definição do indexador como "true".  Isso criará um caminho `/document/file_data` que é um objeto que representa os dados do arquivo original baixados da fonte de dados do blob. Esse parâmetro se aplica somente aos dados no armazenamento de BLOBs.

 - Definindo o parâmetro `imageAction` na definição do indexador com um valor diferente de `none`.  Isso cria uma matriz de imagens que segue a Convenção necessária para a entrada para essa habilidade se for aprovada individualmente (ou seja, `/document/normalized_images/*`).

 - Ter uma habilidade personalizada retorna um objeto JSON definido exatamente como acima.  O parâmetro `$type` deve ser definido como exatamente `file` e o parâmetro `data` deve ser os dados da matriz de bytes codificados 64 base do conteúdo do arquivo.

## <a name="skill-outputs"></a>Saídas de habilidades

| Nome da saída    | Descrição |
|--------------|-------------|
| conteúdo | O conteúdo textual do documento. |
| normalized_images | Quando a `imageAction` é definida com um valor diferente `none`, o novo campo *normalized_images* conterá uma matriz de imagens. Consulte [a documentação para extração de imagem](cognitive-search-concept-image-scenarios.md) para obter mais detalhes sobre o formato de saída de cada imagem. |

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

## <a name="see-also"></a>Consultar também

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um congrau de habilidade](cognitive-search-defining-skillset.md)
+ [Como processar e extrair informações de imagens em cenários de pesquisa cognitiva](cognitive-search-concept-image-scenarios.md)