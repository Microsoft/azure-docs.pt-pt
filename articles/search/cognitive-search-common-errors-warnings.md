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
ms.openlocfilehash: 6455ac9dbe0933f6d46d1137e0a19dcc388d8c80
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243050"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Erros comuns e avisos do pipeline de enriquecimento de ia no Azure Search

Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante a enriquecimento do ia no Azure Search.

## <a name="errors"></a>Erros
A indexação é interrompida quando a contagem de erros excede [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se você quiser que os indexadores ignorem esses erros (e pulem sobre "documentos com falha"), considere atualizar o `maxFailedItems` e `maxFailedItemsPerBatch` conforme descrito [aqui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento com falha junto com sua chave de documento (quando disponível) será exibido como um erro no status de execução do indexador. Você pode utilizar a [API de índice](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos posteriormente se tiver definido o indexador para tolerar falhas.

As seções a seguir podem ajudá-lo a resolver erros, permitindo que a indexação continue.

### <a name="could-not-read-document"></a>Não foi possível ler o documento
O indexador não pôde ler o documento a partir da fonte de dados. Isso pode acontecer devido a:

| Razão | Exemplo | Ação |
| --- | --- | --- |
| tipos de campo inconsistentes em diferentes documentos | O tipo de valor tem uma incompatibilidade com o tipo de coluna. Não foi possível armazenar `'{47.6,-122.1}'` na coluna autores.  O tipo esperado é JArray. | Verifique se o tipo de cada campo é o mesmo em documentos diferentes. Por exemplo, se o primeiro documento `'startTime'` campo for um DateTime e, no segundo documento, for uma cadeia de caracteres, esse erro será atingido. |
| erros do serviço subjacente da fonte de dados | (de Cosmos DB) `{"Errors":["Request rate is large"]}` | Verifique sua instância de armazenamento para garantir que ela esteja íntegra. Talvez seja necessário ajustar o dimensionamento/particionamento. |
| problemas transitórios | Ocorreu um erro no nível de transporte ao receber os resultados do servidor. (provedor: provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

### <a name="could-not-extract-document-content"></a>Não foi possível extrair o conteúdo do documento
O indexador com uma fonte de dados de BLOB não pôde extrair o conteúdo do documento (por exemplo, um arquivo PDF). Isso pode acontecer devido a:

| Razão | Exemplo | Ação |
| --- | --- | --- |
| o blob está acima do limite de tamanho | O documento tem `'150441598'` bytes, o que excede o tamanho máximo `'134217728'` bytes para extração de documentos para sua camada de serviço atual. | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob tem um tipo de conteúdo sem suporte | O documento tem um tipo de conteúdo sem suporte `'image/png'` | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob está criptografado | Não foi possível processar o documento-ele pode estar criptografado ou protegido por senha. | [configurações de BLOB](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| problemas transitórios | Erro ao processar o blob: a solicitação foi anulada: a solicitação foi cancelada. | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

### <a name="could-not-parse-document"></a>Não foi possível analisar o documento
O indexador lê o documento da fonte de dados, mas houve um problema ao converter o conteúdo do documento no esquema de mapeamento de campo especificado. Isso pode acontecer devido a:

| Razão | Exemplo | Ação |
| --- | --- | --- |
| A chave do documento está ausente | A chave do documento não pode estar ausente ou vazia | Garantir que todos os documentos tenham chaves de documento válidas |
| A chave do documento é inválida | A chave do documento não pode ter mais de 1024 caracteres | Modifique a chave do documento para atender aos requisitos de validação. |
| Não foi possível aplicar o mapeamento de campo a um campo | Não foi possível aplicar a função de mapeamento `'functionName'` ao `'fieldName'` de campo. A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique os [mapeamentos de campo](search-indexer-field-mappings.md) definidos no indexador e compare com os dados do campo especificado do documento com falha. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não foi possível ler o valor do campo | Não foi possível ler o valor da coluna `'fieldName'` no índice `'fieldIndex'`. Ocorreu um erro no nível de transporte ao receber os resultados do servidor. (provedor: provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto.) | Esses erros normalmente são devido a problemas de conectividade inesperados com o serviço subjacente da fonte de dados. Tente executar o documento por meio do indexador novamente mais tarde. |

### <a name="could-not-execute-skill"></a>Não foi possível executar a habilidade
O indexador não pôde executar uma habilidade no conseqüência de habilidades.

| Razão | Exemplo | Ação |
| --- | --- | --- |
| Problemas de conectividade transitórios | Ocorreu um erro transitório. Tente novamente mais tarde. | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |
| Possível bug do produto | Ocorreu um erro inesperado. | Isso indica uma classe desconhecida de falha e pode significar que há um bug do produto. Registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter ajuda. |
| Uma habilidade encontrou um erro durante a execução | (Da habilidade de mesclagem) Um ou mais valores de deslocamento eram inválidos e não puderam ser analisados. Os itens foram inseridos no final do texto | Use as informações na mensagem de erro para corrigir o problema. Esse tipo de falha exigirá ação a ser resolvida. |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>Não foi possível executar a habilidade porque a solicitação da API Web falhou
Falha na execução da habilidade porque a chamada para a API da Web falhou. Normalmente, essa classe de falha ocorre quando habilidades personalizadas são usadas; nesse caso, você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade interna, consulte a mensagem de erro para obter ajuda para corrigir o problema.

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Não foi possível executar a habilidade porque a resposta de habilidade da API Web é inválida
Falha na execução da habilidade porque a chamada para a API da Web retornou uma resposta inválida. Normalmente, essa classe de falha ocorre quando habilidades personalizadas são usadas; nesse caso, você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade interna, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência.

### <a name="skill-did-not-execute-within-the-time-limit"></a>A habilidade não foi executada dentro do limite de tempo
Há dois casos sob os quais você pode encontrar essa mensagem de erro, e cada uma delas deve ser tratada de forma diferente. Siga as instruções abaixo, dependendo de qual habilidade retornou esse erro para você.

#### <a name="built-in-cognitive-service-skills"></a>Habilidades de serviço cognitiva interna
Muitas das habilidades cognitivas internas, como detecção de idioma, reconhecimento de entidade ou OCR, são apoiadas por um ponto de extremidade de API de serviço cognitiva. Às vezes, há problemas transitórios com esses pontos de extremidade e uma solicitação atingirá o tempo limite. Para problemas transitórios, não há nenhuma solução, exceto aguardar e tentar novamente. Como uma mitigação, considere definir o indexador para ser [executado em um agendamento](search-howto-schedule-indexers.md). A indexação agendada pega de onde parou. Supondo que problemas transitórios sejam resolvidos, a indexação e o processamento de habilidades cognitivas devem ser capazes de continuar na próxima execução agendada.

#### <a name="custom-skills"></a>Competências personalizadas
Se você encontrar um erro de tempo limite com uma habilidade personalizada que você criou, há algumas coisas que você pode experimentar. Primeiro, examine sua habilidade personalizada e verifique se ela não está ficando presa em um loop infinito e se está retornando um resultado consistentemente. Depois de confirmar que é o caso, determine qual é o tempo de execução da sua habilidade. Se você não definiu explicitamente um valor de `timeout` em sua definição de habilidade personalizada, o `timeout` padrão será de 30 segundos. Se 30 segundos não for longo o suficiente para que sua habilidade seja executada, você poderá especificar um valor de `timeout` maior em sua definição de habilidade personalizada. Aqui está um exemplo de uma definição de habilidade personalizada em que o tempo limite é definido como 90 segundos:

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

O valor máximo que você pode definir para o parâmetro `timeout` é de 230 segundos.  Se sua habilidade personalizada não puder ser executada de forma consistente em 230 segundos, você poderá considerar a redução da `batchSize` de sua habilidade personalizada para que ela tenha menos documentos para ser processada em uma única execução.  Se você já definiu sua `batchSize` como 1, precisará reescrever a habilidade para poder executar em menos de 230 segundos ou dividi-la em várias habilidades personalizadas para que o tempo de execução para qualquer habilidade personalizada individual seja de, no máximo, 230 segundos. Examine a [documentação de habilidades personalizadas](cognitive-search-custom-skill-web-api.md) para obter mais informações.

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Não foi possível ' `MergeOrUpload` ' | documento ' `Delete` ' para o índice de pesquisa

O documento foi lido e processado, mas o indexador não pôde adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Razão | Exemplo | Ação |
| --- | --- | --- |
| Um termo no documento é maior que o [limite de 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Um campo contém um termo muito grande | Você pode evitar essa restrição garantindo que o campo não esteja configurado como filtrável, facetable ou classificável.
| Um documento é maior que o [tamanho máximo de solicitação de API](search-limits-quotas-capacity.md#api-request-limits) | O documento é muito grande para ser indexado | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| Problemas de conexão com o índice de destino (que persiste após novas tentativas) porque o serviço está em outra carga, como consulta ou indexação. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está sob carga pesada. | [Escalar verticalmente seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está sendo corrigido para atualização de serviço ou está no meio de uma reconfiguração de topologia. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está inoperante no momento/o serviço de pesquisa está passando por uma transição. | Configure o serviço com pelo menos 3 réplicas para a documentação de 99,9% de disponibilidade por [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Falha ao estabelecer conexão para atualizar índice. Ocorreu uma falha desconhecida. | Configure os indexadores para serem [executados em uma agenda](search-howto-schedule-indexers.md) para captar de um estado de falha.

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Não foi possível indexar o documento porque os dados do indexador para o índice eram inválidos

O documento foi lido e processado, mas devido a uma incompatibilidade na configuração dos campos de índice e a natureza dos dados extraídos pelo indexador, não foi possível adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Razão | Exemplo
| --- | ---
| O tipo de dados dos campos extraídos pelo indexador é incompatível com o modelo de dados do campo de índice de destino correspondente. | O campo de dados '_Data_' no documento com a chave '_Data_' tem um valor inválido ' do tipo ' EDM. String ' '. O tipo esperado era ' Collection (EDM. String) '. |
| Falha ao extrair qualquer entidade JSON de um valor de cadeia de caracteres. | Não foi possível analisar o valor ' do tipo ' EDM. String ' ' do campo '_Data_' como um objeto JSON. Erro: ' depois de analisar um valor, um caractere inesperado foi encontrado: ' '. Caminho '_caminho_', linha 1, posição 3162. ' |
| Falha ao extrair uma coleção de entidades JSON de um valor de cadeia de caracteres.  | Não foi possível analisar o valor ' do tipo ' EDM. String ' ' do campo '_Data_' como uma matriz JSON. Erro: ' depois de analisar um valor, um caractere inesperado foi encontrado: ' '. Caminho ' [0] ', linha 1, posição 27. ' |
| Um tipo desconhecido foi descoberto no documento de origem. | Tipo desconhecido '_desconhecido_' não pode ser indexado |
| Uma notação incompatível para pontos de Geografia foi usada no documento de origem. | Não há suporte para literais de cadeia de caracteres de ponto WKT. Em vez disso, use literais de ponto geojson |

Em todos esses casos, consulte os [tipos de dados com suporte (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e o [mapa de tipo de dados para indexadores no Azure Search](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) para se certificar de que você criou o esquema de índice corretamente e configurou os [mapeamentos de campo do indexador](search-indexer-field-mappings.md)apropriados. A mensagem de erro incluirá detalhes que podem ajudar a rastrear a origem da incompatibilidade.

### <a name="could-not-process-document-within-indexer-max-run-time"></a>Não foi possível processar o documento dentro do tempo de execução máximo do indexador

Esse erro ocorre quando o indexador não pode concluir o processamento de um único documento da fonte de dados dentro do tempo de execução permitido. O [tempo máximo de execução](search-limits-quotas-capacity.md#indexer-limits) é menor quando habilidades são usados. Quando esse erro ocorrer, se você tiver maxFailedItems definido com um valor diferente de 0, o indexador ignorará o documento em execuções futuras para que a indexação possa progredir. Se você não puder ignorar nenhum documento, ou se estiver vendo esse erro de forma consistente, considere a possibilidade de dividir documentos em documentos menores para que o progresso parcial possa ser feito dentro de uma única execução do indexador.

##  <a name="warnings"></a>:|
Os avisos não param de indexação, mas indicam condições que podem resultar em resultados inesperados. Se você tomar uma ação ou não depende dos dados e do seu cenário.

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>Não foi possível executar a habilidade porque uma entrada de habilidade era inválida
O indexador não pôde executar uma habilidade no conteriante porque uma entrada para a habilidade estava ausente, o tipo errado ou é inválido.

As habilidades cognitivas têm entradas e entradas opcionais necessárias. Por exemplo, a [habilidade de extração de frases-chave](cognitive-search-skill-keyphrases.md) tem duas entradas necessárias `text`, `languageCode` e nenhuma entrada opcional. Se as entradas necessárias forem inválidas, a habilidade será ignorada e gerará um aviso. As habilidades ignoradas não geram saídas, portanto, se outras habilidades usarem saídas da habilidade ignorada, elas poderão gerar avisos adicionais.

Se você quiser fornecer um valor padrão no caso de entrada ausente, poderá usar a [habilidade condicional](cognitive-search-skill-conditional.md) para gerar um valor padrão e, em seguida, usar a saída da [habilidade condicional](cognitive-search-skill-conditional.md) como a entrada de habilidade.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Razão | Exemplo | Ação |
| --- | --- | --- |
| A entrada de habilidade é do tipo errado | A entrada de habilidade necessária `X` não era do tipo esperado `String`. A entrada de habilidade necessária `X` não estava no formato esperado. | Determinadas habilidades esperam entradas de tipos específicos, por exemplo, a [habilidade de sentimentos](cognitive-search-skill-sentiment.md) espera que `text` seja uma cadeia de caracteres. Se a entrada especificar um valor que não seja de cadeia de caracteres, a habilidade não será executada e não gerará nenhuma saída. Verifique se o conjunto de dados tem valores de entrada uniformes no tipo ou use uma [habilidade de API Web personalizada](cognitive-search-custom-skill-web-api.md) para pré-processar a entrada. Se você estiver Iterando a habilidade em uma matriz, verifique o contexto de habilidade e a entrada tem `*` nas posições corretas. Geralmente, o contexto e a fonte de entrada devem terminar com `*` para matrizes. |
| A entrada da habilidade está ausente | A entrada de habilidade necessária `X` está ausente. | Se todos os documentos obtiverem esse aviso, provavelmente haverá um erro de digitação nos caminhos de entrada e você deverá verificar a maiúsculas e minúsculas do nome da propriedade, `*` extra ou ausente no caminho, e os documentos da fonte de dados definem as entradas necessárias. |
| A entrada do código do idioma da habilidade é inválida | `languageCode` de entrada de habilidades tem os códigos de idioma a seguir `X,Y,Z`, pelo menos um deles é inválido. | Veja mais detalhes [abaixo](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>A entrada de habilidade ' languageCode ' tem os seguintes códigos de idioma ' X, Y, Z ', pelo menos um dos quais é inválido.
Não há suporte para um ou mais valores passados para a entrada de `languageCode` opcional de uma habilidade de downstream. Isso pode ocorrer se você estiver passando a saída do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) para as habilidades subsequentes e a saída consistir em mais idiomas do que o suportado nessas habilidades de downstream.

Se você souber que o conjunto de dados está todos em um idioma, deverá remover o [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e a entrada de habilidade de `languageCode` e usar o parâmetro de habilidade `defaultLanguageCode` para essa habilidade, supondo que o idioma tenha suporte para essa habilidade.

Se você souber que o conjunto de dados contém vários idiomas e, portanto, precisar do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e da entrada de `languageCode`, considere adicionar um [ConditionalSkill](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não têm suporte antes de passar no texto para a habilidade de downstream.  Aqui está um exemplo de como seria o EntityRecognitionSkill:

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

### <a name="web-api-skill-response-contains-warnings"></a>Resposta de habilidade da API Web contém avisos
O indexador foi capaz de executar uma habilidade no configurador de habilidades, mas a resposta da solicitação da API Web indicou que havia avisos durante a execução. Examine os avisos para entender como os dados são afetados e se a ação é necessária ou não.

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>A configuração atual do indexador não dá suporte ao progresso incremental
Esse aviso ocorre apenas para Cosmos DB fontes de dados.

O progresso incremental durante a indexação garante que, se a execução do indexador for interrompida por falhas transitórias ou por um limite de tempo de execução, o indexador poderá escolher onde parou na próxima vez em que for executado, em vez de ter que reindexar toda a coleção do zero. Isso é especialmente importante ao indexar grandes coleções.

A capacidade de retomar um trabalho de indexação não concluído é o predicado em ter documentos ordenados pela coluna `_ts`. O indexador usa o carimbo de data/hora para determinar qual documento deve ser selecionado em seguida. Se a coluna `_ts` estiver ausente ou se o indexador não puder determinar se uma consulta personalizada foi ordenada por ela, o indexador será iniciado no início e você verá esse aviso.

É possível substituir esse comportamento, permitindo o progresso incremental e suprimindo esse aviso usando a propriedade de configuração `assumeOrderByHighWatermarkColumn`.

[Mais informações sobre Cosmos DB progresso incremental e consultas personalizadas.](https://go.microsoft.com/fwlink/?linkid=2099593)