---
title: Erros comuns e avisos-Azure Search
description: Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante a enriquecimento do ia no Azure Search.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329386"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Erros comuns e avisos do pipeline de enriquecimento de ia no Azure Search

Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante a enriquecimento do ia no Azure Search.

## <a name="errors"></a>Erros
A indexação é interrompida quando a contagem de erros excede [' maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). As seções a seguir podem ajudá-lo a resolver erros, permitindo que a indexação continue.

### <a name="could-not-read-document"></a>Não foi possível ler o documento
O indexador não pôde ler o documento a partir da fonte de dados. Isso pode acontecer devido a:

| Reason | Exemplo | Action |
| --- | --- | --- |
| tipos de campo inconsistentes em diferentes documentos | O tipo de valor tem uma incompatibilidade com o tipo de coluna. Não foi possível armazenar `'{47.6,-122.1}'` na coluna autores.  O tipo esperado é JArray. | Verifique se o tipo de cada campo é o mesmo em documentos diferentes. Por exemplo, se o primeiro documento @no__t campo-0 for um DateTime e, no segundo documento, for uma cadeia de caracteres, esse erro será atingido. |
| erros do serviço subjacente da fonte de dados | (de Cosmos DB) `{"Errors":["Request rate is large"]}` | Verifique sua instância de armazenamento para garantir que ela esteja íntegra. Talvez seja necessário ajustar o dimensionamento/particionamento. |
| problemas transitórios | Ocorreu um erro no nível de transporte ao receber os resultados do servidor. operador Provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

### <a name="could-not-extract-document-content"></a>Não foi possível extrair o conteúdo do documento
O indexador com uma fonte de dados de BLOB não pôde extrair o conteúdo do documento (por exemplo, um arquivo PDF). Isso pode acontecer devido a:

| Reason | Exemplo | Action |
| --- | --- | --- |
| o blob está acima do limite de tamanho | O documento é `'150441598'` bytes, o que excede o tamanho máximo `'134217728'` bytes para extração de documentos para a camada de serviço atual. | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob tem um tipo de conteúdo sem suporte | O documento tem um tipo de conteúdo sem suporte `'image/png'` | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob está criptografado | Não foi possível processar o documento-ele pode estar criptografado ou protegido por senha. | [configurações de BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemas transitórios | Erro ao processar o blob: A solicitação foi anulada: A solicitação foi cancelada. | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

### <a name="could-not-parse-document"></a>Não foi possível analisar o documento
O indexador lê o documento da fonte de dados, mas houve um problema ao converter o conteúdo do documento no esquema de mapeamento de campo especificado. Isso pode acontecer devido a:

| Reason | Exemplo | Action |
| --- | --- | --- |
| A chave do documento está ausente | A chave do documento não pode estar ausente ou vazia | Garantir que todos os documentos tenham chaves de documento válidas |
| A chave do documento é inválida | A chave do documento não pode ter mais de 1024 caracteres | Modifique a chave do documento para atender aos requisitos de validação. |
| Não foi possível aplicar o mapeamento de campo a um campo | Não foi possível aplicar a função de mapeamento `'functionName'` ao campo `'fieldName'`. A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique os [mapeamentos de campo](search-indexer-field-mappings.md) definidos no indexador e compare com os dados do campo especificado do documento com falha. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não foi possível ler o valor do campo | Não foi possível ler o valor da coluna `'fieldName'` no índice `'fieldIndex'`. Ocorreu um erro no nível de transporte ao receber os resultados do servidor. operador Provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto.) | Esses erros normalmente são devido a problemas de conectividade inesperados com o serviço subjacente da fonte de dados. Tente executar o documento por meio do indexador novamente mais tarde. |

##  <a name="warnings"></a>Avisos
Os avisos não param de indexação, mas indicam condições que podem resultar em resultados inesperados. Se você tomar uma ação ou não depende dos dados e do seu cenário.

### <a name="skill-input-was-truncated"></a>A entrada da habilidade foi truncada
Habilidades cognitivas têm limites para o comprimento do texto que pode ser analisado de uma só vez. Se a entrada de texto dessas habilidades estiver acima desse limite, truncaremos o texto para atender ao limite e, em seguida, executaremos o enriquecimento nesse texto truncado. Isso significa que a habilidade é executada, mas não sobre todos os seus dados.

No exemplo LanguageDetectionSkill abaixo, o campo de entrada `'text'` pode disparar esse aviso se estiver acima do limite de caracteres. Você pode encontrar os limites de entrada de habilidades na [documentação de habilidades](cognitive-search-predefined-skills.md).

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Se você quiser garantir que todo o texto seja analisado, considere usar a [habilidade de divisão](cognitive-search-skill-textsplit.md).