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
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028315"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Erros comuns e avisos do pipeline de enriquecimento de ia no Azure Search

Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante a enriquecimento do ia no Azure Search.

## <a name="errors"></a>Erros
A indexação é interrompida quando a contagem de erros excede [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se você quiser que os indexadores ignorem esses erros (e pulem sobre "documentos com falha"), considere atualizar o `maxFailedItems` e o `maxFailedItemsPerBatch`, conforme descrito [aqui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento com falha junto com sua chave de documento (quando disponível) será exibido como um erro no status de execução do indexador. Você pode utilizar a [API de índice](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos posteriormente se tiver definido o indexador para tolerar falhas.

As seções a seguir podem ajudá-lo a resolver erros, permitindo que a indexação continue.

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

### <a name="could-not-index-document"></a>Não foi possível indexar o documento
O documento foi lido e processado, mas o indexador não pôde adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Reason | Exemplo | Action |
| --- | --- | --- |
| Um campo contém um termo muito grande | Um termo no documento é maior que o [limite de 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Você pode evitar essa restrição garantindo que o campo não esteja configurado como filtrável, facetable ou classificável.
| O documento é muito grande para ser indexado | Um documento é maior que o [tamanho máximo de solicitação de API](search-limits-quotas-capacity.md#api-request-limits) | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>A entrada de habilidade ' languageCode ' tem os seguintes códigos de idioma ' X, Y, Z ', pelo menos um dos quais é inválido.
Não há suporte para um ou mais valores passados para a entrada opcional `languageCode` de uma habilidade de downstream. Isso pode ocorrer se você estiver passando a saída do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) para as habilidades subsequentes e a saída consistir em mais idiomas do que o suportado nessas habilidades de downstream.

Se você souber que o conjunto de dados está todos em um idioma, deverá remover o [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e a entrada de habilidade `languageCode` e usar o parâmetro de habilidade `defaultLanguageCode` para essa habilidade, supondo que a linguagem tenha suporte para essa habilidade.

Se você souber que o conjunto de dados contém vários idiomas e, portanto, precisar da entrada [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e `languageCode`, considere adicionar um [ConditionalSkill](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não têm suporte antes de passar no texto para a habilidade de downstream.  Aqui está um exemplo de como seria o EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Aqui estão algumas referências para os idiomas com suporte no momento para cada uma das habilidades que podem produzir essa mensagem de erro:
* [Análise de texto idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (para [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)e [SentimentSkill](cognitive-search-skill-sentiment.md))
* [Idiomas com suporte do tradutor](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (para o [texto TranslationSkill](cognitive-search-skill-text-translation.md))
* [SplitSkill de texto](cognitive-search-skill-textsplit.md) Idiomas com suporte: `da, de, en, es, fi, fr, it, ko, pt`

### <a name="skill-did-not-execute-within-the-time-limit"></a>A habilidade não foi executada dentro do limite de tempo
Há dois casos sob os quais você pode encontrar essa mensagem de erro, e cada uma delas deve ser tratada de forma diferente. Siga as instruções abaixo, dependendo de qual habilidade retornou esse erro para você.

#### <a name="built-in-cognitive-service-skills"></a>Habilidades de serviço cognitiva interna
Muitas das habilidades cognitivas internas, como detecção de idioma, reconhecimento de entidade ou OCR, são apoiadas por um ponto de extremidade de API de serviço cognitiva. Às vezes, há problemas transitórios com esses pontos de extremidade e uma solicitação atingirá o tempo limite. Para problemas transitórios, não há nenhuma solução, exceto aguardar e tentar novamente. Como uma mitigação, considere definir o indexador para ser [executado em um agendamento](search-howto-schedule-indexers.md). A indexação agendada pega de onde parou. Supondo que problemas transitórios sejam resolvidos, a indexação e o processamento de habilidades cognitivas devem ser capazes de continuar na próxima execução agendada.

#### <a name="custom-skills"></a>Competências personalizadas
Se você encontrar um erro de tempo limite com uma habilidade personalizada que você criou, há algumas coisas que você pode experimentar. Primeiro, examine sua habilidade personalizada e verifique se ela não está ficando presa em um loop infinito e se está retornando um resultado consistentemente. Depois de confirmar que é o caso, determine qual é o tempo de execução da sua habilidade. Se você não definiu explicitamente um valor `timeout` em sua definição de habilidade personalizada, o padrão `timeout` será de 30 segundos. Se 30 segundos não for longo o suficiente para que sua habilidade seja executada, você poderá especificar um valor maior `timeout` em sua definição de habilidade personalizada. Aqui está um exemplo de uma definição de habilidade personalizada em que o tempo limite é definido como 90 segundos:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

O valor máximo que você pode definir para o parâmetro `timeout` é 230 segundos.  Se sua habilidade personalizada não puder ser executada de forma consistente em 230 segundos, você pode considerar reduzir o `batchSize` de sua habilidade personalizada para que ele tenha menos documentos para ser processado em uma única execução.  Se você já tiver definido seu `batchSize` como 1, precisará reescrever a habilidade para poder executar em menos de 230 segundos ou dividi-la em várias habilidades personalizadas para que o tempo de execução para qualquer habilidade personalizada individual seja de, no máximo, 230 segundos. Examine a [documentação de habilidades personalizadas](cognitive-search-custom-skill-web-api.md) para obter mais informações.

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Não foi possível ' `MergeOrUpload` ' | documento ' `Delete` ' para o índice de pesquisa

O documento foi lido e processado, mas o indexador não pôde adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Reason | Exemplo | Action |
| --- | --- | --- |
| Um termo no documento é maior que o [limite de 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Um campo contém um termo muito grande | Você pode evitar essa restrição garantindo que o campo não esteja configurado como filtrável, facetable ou classificável.
| Um documento é maior que o [tamanho máximo de solicitação de API](search-limits-quotas-capacity.md#api-request-limits) | O documento é muito grande para ser indexado | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| Problemas de conexão com o índice de destino (que persiste após novas tentativas) porque o serviço está em outra carga, como consulta ou indexação. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está sob carga pesada. | [Escalar verticalmente seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está sendo corrigido para atualização de serviço ou está no meio de uma reconfiguração de topologia. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está inoperante no momento/o serviço de pesquisa está passando por uma transição. | Configure o serviço com pelo menos 3 réplicas para a documentação de 99,9% de disponibilidade por [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Falha ao estabelecer conexão para atualizar índice. Ocorreu uma falha desconhecida. | Configure os indexadores para serem [executados em uma agenda](search-howto-schedule-indexers.md) para captar de um estado de falha.

##  <a name="warnings"></a>:|
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