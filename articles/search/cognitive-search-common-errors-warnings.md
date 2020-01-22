---
title: Erros e avisos do indexador
titleSuffix: Azure Cognitive Search
description: Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante o enriquecimento do ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9cf3bcc514118c7f8052981c39023d6cac361d22
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314730"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Solucionando problemas de erros e avisos comuns do indexador no Azure Pesquisa Cognitiva

Este artigo fornece informações e soluções para erros comuns e avisos que você pode encontrar durante a indexação e o enriquecimento de ia no Azure Pesquisa Cognitiva.

A indexação é interrompida quando a contagem de erros excede [' maxFailedItems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Se você quiser que os indexadores ignorem esses erros (e pulem sobre "documentos com falha"), considere atualizar o `maxFailedItems` e `maxFailedItemsPerBatch` conforme descrito [aqui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento com falha junto com sua chave de documento (quando disponível) será exibido como um erro no status de execução do indexador. Você pode utilizar a [API de índice](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos posteriormente se tiver definido o indexador para tolerar falhas.

As informações de erro neste artigo podem ajudá-lo a resolver erros, permitindo que a indexação continue.

Os avisos não param de indexação, mas indicam condições que podem resultar em resultados inesperados. Se você tomar uma ação ou não depende dos dados e do seu cenário.

A partir da versão de API `2019-05-06`, os erros e avisos do indexador em nível de item são estruturados para fornecer maior clareza em relação às causas e às próximas etapas. Elas contêm as seguintes propriedades:

| Propriedade | Descrição | Exemplo |
| --- | --- | --- |
| key | A ID do documento do documento impactado pelo erro ou aviso. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| nome | O nome da operação que descreve onde ocorreu o erro ou o aviso. Isso é gerado pela seguinte estrutura: [Category]. [subcategoria]. [resourceType]. Source | DocumentExtraction. azureblob. myBlobContainerName enriquecetion. WebApiSkill. myskillname Projetion. SearchIndex. OutputFieldMapping. myOutputFieldName Projetion. SearchIndex. MergeOrUpload. myindexname Projeção. KnowledgeStore. Table. mytablename |
| message | Uma descrição de alto nível do erro ou aviso. | Não foi possível executar a habilidade porque a solicitação da API Web falhou. |
| details | Todos os detalhes adicionais que podem ser úteis para diagnosticar o problema, como a resposta WebApi, se a execução de uma habilidade personalizada falharem. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 origem, Func`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... restante do rastreamento de pilha... |
| documentationLink | Um link para a documentação relevante com informações detalhadas para depurar e resolver o problema. Esse link geralmente apontará para uma das seções abaixo nesta página. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Erro: não foi possível ler o documento

O indexador não pôde ler o documento a partir da fonte de dados. Isso pode acontecer devido a:

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| tipos de campo inconsistentes em diferentes documentos | O tipo de valor tem uma incompatibilidade com o tipo de coluna. Não foi possível armazenar `'{47.6,-122.1}'` na coluna autores.  O tipo esperado é JArray. | Verifique se o tipo de cada campo é o mesmo em documentos diferentes. Por exemplo, se o primeiro documento `'startTime'` campo for um DateTime e, no segundo documento, for uma cadeia de caracteres, esse erro será atingido. |
| erros do serviço subjacente da fonte de dados | (de Cosmos DB) `{"Errors":["Request rate is large"]}` | Verifique sua instância de armazenamento para garantir que ela esteja íntegra. Talvez seja necessário ajustar o dimensionamento/particionamento. |
| problemas transitórios | Ocorreu um erro no nível de transporte ao receber os resultados do servidor. (provedor: provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Erro: não foi possível extrair o conteúdo ou os metadados do documento
O indexador com uma fonte de dados de BLOB não pôde extrair o conteúdo ou os metadados do documento (por exemplo, um arquivo PDF). Isso pode acontecer devido a:

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| o blob está acima do limite de tamanho | O documento tem `'150441598'` bytes, o que excede o tamanho máximo `'134217728'` bytes para extração de documentos para sua camada de serviço atual. | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob tem um tipo de conteúdo sem suporte | O documento tem um tipo de conteúdo sem suporte `'image/png'` | [erros de indexação de BLOB](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| o blob está criptografado | Não foi possível processar o documento-ele pode estar criptografado ou protegido por senha. | Você pode ignorar o blob com [as configurações de blob](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed). |
| problemas transitórios | "Erro ao processar o blob: a solicitação foi anulada: a solicitação foi cancelada". "O documento atingiu o tempo limite durante o processamento." | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Erro: não foi possível analisar o documento
O indexador lê o documento da fonte de dados, mas houve um problema ao converter o conteúdo do documento no esquema de mapeamento de campo especificado. Isso pode acontecer devido a:

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| A chave do documento está ausente | A chave do documento não pode estar ausente ou vazia | Garantir que todos os documentos tenham chaves de documento válidas |
| A chave do documento é inválida | A chave do documento não pode ter mais de 1024 caracteres | Modifique a chave do documento para atender aos requisitos de validação. |
| Não foi possível aplicar o mapeamento de campo a um campo | Não foi possível aplicar a função de mapeamento `'functionName'` ao `'fieldName'`de campo. A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique os [mapeamentos de campo](search-indexer-field-mappings.md) definidos no indexador e compare com os dados do campo especificado do documento com falha. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não foi possível ler o valor do campo | Não foi possível ler o valor da coluna `'fieldName'` no índice `'fieldIndex'`. Ocorreu um erro no nível de transporte ao receber os resultados do servidor. (provedor: provedor TCP, erro: 0-uma conexão existente foi fechada forçosamente pelo host remoto.) | Esses erros normalmente são devido a problemas de conectividade inesperados com o serviço subjacente da fonte de dados. Tente executar o documento por meio do indexador novamente mais tarde. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Erro: não foi possível executar a habilidade
O indexador não pôde executar uma habilidade no conseqüência de habilidades.

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| Problemas de conectividade transitórios | Ocorreu um erro transitório. Tente novamente mais tarde. | Ocasionalmente, há problemas de conectividade inesperados. Tente executar o documento por meio do indexador novamente mais tarde. |
| Possível bug do produto | Ocorreu um erro inesperado. | Isso indica uma classe desconhecida de falha e pode significar que há um bug do produto. Registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter ajuda. |
| Uma habilidade encontrou um erro durante a execução | (Da habilidade de mesclagem) Um ou mais valores de deslocamento eram inválidos e não puderam ser analisados. Os itens foram inseridos no final do texto | Use as informações na mensagem de erro para corrigir o problema. Esse tipo de falha exigirá ação a ser resolvida. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Erro: não foi possível executar a habilidade porque a solicitação da API Web falhou
Falha na execução da habilidade porque a chamada para a API da Web falhou. Normalmente, essa classe de falha ocorre quando habilidades personalizadas são usadas; nesse caso, você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade interna, consulte a mensagem de erro para obter ajuda para corrigir o problema.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Erro: não foi possível executar a habilidade porque a resposta de habilidade da API Web é inválida
Falha na execução da habilidade porque a chamada para a API da Web retornou uma resposta inválida. Normalmente, essa classe de falha ocorre quando habilidades personalizadas são usadas; nesse caso, você precisará depurar seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade interna, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Erro: a habilidade não foi executada dentro do limite de tempo
Há dois casos sob os quais você pode encontrar essa mensagem de erro, e cada uma delas deve ser tratada de forma diferente. Siga as instruções abaixo, dependendo de qual habilidade retornou esse erro para você.

### <a name="built-in-cognitive-service-skills"></a>Habilidades de serviço cognitiva interna
Muitas das habilidades cognitivas internas, como detecção de idioma, reconhecimento de entidade ou OCR, são apoiadas por um ponto de extremidade de API de serviço cognitiva. Às vezes, há problemas transitórios com esses pontos de extremidade e uma solicitação atingirá o tempo limite. Para problemas transitórios, não há nenhuma solução, exceto aguardar e tentar novamente. Como uma mitigação, considere definir o indexador para ser [executado em um agendamento](search-howto-schedule-indexers.md). A indexação agendada pega de onde parou. Supondo que problemas transitórios sejam resolvidos, a indexação e o processamento de habilidades cognitivas devem ser capazes de continuar na próxima execução agendada.

Se você continuar a ver esse erro no mesmo documento para uma habilidade cognitiva interna, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência, pois isso não é esperado.

### <a name="custom-skills"></a>Competências personalizadas
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

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Erro: não foi possível '`MergeOrUpload`' | documento '`Delete`' para o índice de pesquisa

O documento foi lido e processado, mas o indexador não pôde adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| Um campo contém um termo muito grande | Um termo no documento é maior que o [limite de 32 KB](search-limits-quotas-capacity.md#api-request-limits) | Você pode evitar essa restrição garantindo que o campo não esteja configurado como filtrável, facetable ou classificável.
| O documento é muito grande para ser indexado | Um documento é maior que o [tamanho máximo de solicitação de API](search-limits-quotas-capacity.md#api-request-limits) | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| O documento contém muitos objetos na coleção | Uma coleção em seu documento excede o [máximo de elementos em todas as coleções complexas limite](search-limits-quotas-capacity.md#index-limits) "o documento com a chave `'1000052'` tem `'4303'` objetos em coleções (matrizes JSON). No máximo `'3000'` objetos podem estar em coleções em todo o documento. Remova objetos de coleções e tente indexar o documento novamente. " | É recomendável reduzir o tamanho da coleção complexa no documento para abaixo do limite e evitar a alta utilização do armazenamento.
| Problemas de conexão com o índice de destino (que persiste após novas tentativas) porque o serviço está em outra carga, como consulta ou indexação. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está sob carga pesada. | [Escalar verticalmente seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está sendo corrigido para atualização de serviço ou está no meio de uma reconfiguração de topologia. | Falha ao estabelecer conexão para atualizar índice. O serviço de pesquisa está inoperante no momento/o serviço de pesquisa está passando por uma transição. | Configure o serviço com pelo menos 3 réplicas para a documentação de 99,9% de disponibilidade por [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Falha ao estabelecer conexão para atualizar índice. Ocorreu uma falha desconhecida. | Configure os indexadores para serem [executados em uma agenda](search-howto-schedule-indexers.md) para captar de um estado de falha.
| Uma solicitação de indexação feita ao índice de destino não foi confirmada dentro de um período de tempo limite devido a problemas de rede. | Não foi possível estabelecer a conexão com o índice de pesquisa em tempo hábil. | Configure os indexadores para serem [executados em uma agenda](search-howto-schedule-indexers.md) para captar de um estado de falha. Além disso, tente diminuir o tamanho do [lote](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) do indexador se essa condição de erro persistir.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Erro: não foi possível indexar o documento porque os dados do indexador para o índice eram inválidos

O documento foi lido e processado, mas devido a uma incompatibilidade na configuração dos campos de índice e a natureza dos dados extraídos pelo indexador, não foi possível adicioná-lo ao índice de pesquisa. Isso pode acontecer devido a:

| Razão | Detalhes/exemplo
| --- | ---
| O tipo de dados dos campos extraídos pelo indexador é incompatível com o modelo de dados do campo de índice de destino correspondente. | O campo de dados '_Data_' no documento com a chave ' 888 ' tem um valor inválido ' do tipo ' EDM. String ' '. O tipo esperado era ' Collection (EDM. String) '. |
| Falha ao extrair qualquer entidade JSON de um valor de cadeia de caracteres. | Não foi possível analisar o valor ' do tipo ' EDM. String ' ' do campo '_Data_' como um objeto JSON. Erro: ' depois de analisar um valor, um caractere inesperado foi encontrado: ' '. Caminho '_caminho_', linha 1, posição 3162. ' |
| Falha ao extrair uma coleção de entidades JSON de um valor de cadeia de caracteres.  | Não foi possível analisar o valor ' do tipo ' EDM. String ' ' do campo '_Data_' como uma matriz JSON. Erro: ' depois de analisar um valor, um caractere inesperado foi encontrado: ' '. Caminho ' [0] ', linha 1, posição 27. ' |
| Um tipo desconhecido foi descoberto no documento de origem. | Tipo desconhecido '_desconhecido_' não pode ser indexado |
| Uma notação incompatível para pontos de Geografia foi usada no documento de origem. | Não há suporte para literais de cadeia de caracteres de ponto WKT. Em vez disso, use literais de ponto geojson |

Em todos esses casos, consulte [tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e [mapa de tipo de dados para indexadores](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) para se certificar de que você criou o esquema de índice corretamente e configurou os [mapeamentos de campo de indexador](search-indexer-field-mappings.md)apropriados. A mensagem de erro incluirá detalhes que podem ajudar a rastrear a origem da incompatibilidade.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Erro: a política de controle de alterações integrada não pode ser usada porque a tabela tem uma chave primária composta

Isso se aplica a tabelas SQL e geralmente acontece quando a chave é definida como uma chave composta ou, quando a tabela define um índice clusterizado exclusivo (como em um índice SQL, não um índice Azure Search). O principal motivo é que o atributo de chave é modificado para ser uma chave primária composta no caso de um [índice clusterizado exclusivo](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). Nesse caso, certifique-se de que a tabela SQL não tenha um índice clusterizado exclusivo ou que você mapeie o campo de chave para um campo que tenha a garantia de não ter valores duplicados.


## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Erro: não foi possível processar o documento dentro do tempo de execução máximo do indexador

Esse erro ocorre quando o indexador não pode concluir o processamento de um único documento da fonte de dados dentro do tempo de execução permitido. O [tempo máximo de execução](search-limits-quotas-capacity.md#indexer-limits) é menor quando habilidades são usados. Quando esse erro ocorrer, se você tiver maxFailedItems definido com um valor diferente de 0, o indexador ignorará o documento em execuções futuras para que a indexação possa progredir. Se você não puder ignorar nenhum documento, ou se estiver vendo esse erro de forma consistente, considere a possibilidade de dividir documentos em documentos menores para que o progresso parcial possa ser feito dentro de uma única execução do indexador.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Erro: não foi possível projetar o documento

Esse erro ocorre quando o indexador está tentando [projetar dados em um repositório de conhecimento](knowledge-store-projection-overview.md) e houve uma falha em nossa tentativa de fazer isso.  Essa falha pode ser consistente e corrigível ou pode ser uma falha transitória com o coletor de saída de projeção que talvez seja necessário aguardar e tentar novamente para resolver.  Aqui estão um conjunto de Estados de falha conhecidos e possíveis resoluções.

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| Não foi possível atualizar o blob de projeção `'blobUri'` no contêiner `'containerName'` |O contêiner especificado não existe. | O indexador verificará se o contêiner especificado foi criado anteriormente e o criará, se necessário, mas ele só executará essa verificação uma vez por execução de indexer. Esse erro significa que algo excluiu o contêiner após esta etapa.  Para resolver esse erro, tente: Deixe as informações da conta de armazenamento sozinhas, aguarde até que o indexador seja concluído e execute novamente o indexador. |
| Não foi possível atualizar o blob de projeção `'blobUri'` no contêiner `'containerName'` |Não é possível gravar dados na conexão de transporte: uma conexão existente foi fechada forçosamente pelo host remoto. | Espera-se que essa seja uma falha transitória com o armazenamento do Azure e, portanto, deve ser resolvida executando novamente o indexador. Se você encontrar esse erro consistentemente, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que ele possa ser investigado mais detalhadamente.  |
| Não foi possível atualizar `'projectionRow'` de linha na tabela `'tableName'` | O servidor está ocupado. | Espera-se que essa seja uma falha transitória com o armazenamento do Azure e, portanto, deve ser resolvida executando novamente o indexador. Se você encontrar esse erro consistentemente, registre um [tíquete de suporte](https://ms.portal.azure.com/#create/Microsoft.Support) para que ele possa ser investigado mais detalhadamente.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Aviso: a entrada de habilidade era inválida
Uma entrada para a habilidade estava ausente, o tipo errado ou é inválido. A mensagem de aviso indicará o impacto:
1) Não foi possível executar a habilidade
2) Habilidade executada, mas pode ter resultados inesperados

As habilidades cognitivas têm entradas e entradas opcionais necessárias. Por exemplo, a [habilidade de extração de frases-chave](cognitive-search-skill-keyphrases.md) tem duas entradas necessárias `text`, `languageCode`e nenhuma entrada opcional. As entradas de habilidades personalizadas são consideradas entradas opcionais.

Se quaisquer entradas necessárias estiverem ausentes ou se qualquer entrada não for do tipo correto, a habilidade será ignorada e gerará um aviso. As habilidades ignoradas não geram saídas, portanto, se outras habilidades usarem saídas da habilidade ignorada, elas poderão gerar avisos adicionais.

Se uma entrada opcional estiver ausente, a habilidade ainda será executada, mas poderá produzir uma saída inesperada devido à entrada ausente.

Em ambos os casos, esse aviso pode ser esperado devido à forma de seus dados. Por exemplo, se você tiver um documento que contém informações sobre pessoas com os campos `firstName`, `middleName`e `lastName`, você poderá ter alguns documentos que não têm uma entrada para `middleName`. Se você passar `middleName` como uma entrada para uma habilidade no pipeline, espera-se que essa entrada de habilidade esteja sem algum tempo. Você precisará avaliar seus dados e cenário para determinar se alguma ação é necessária ou não como resultado desse aviso.

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

| Razão | Detalhes/exemplo | Resolução |
| --- | --- | --- |
| A entrada de habilidade é do tipo errado | "A entrada de habilidade necessária não era do tipo esperado `String`. Nome: `text`, origem: `/document/merged_content`. "  "A entrada de habilidade necessária não estava no formato esperado. Nome: `text`, origem: `/document/merged_content`. "  "Não é possível iterar em `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`de não matriz."  "Não é possível selecionar `0` em `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`não matrizes" | Determinadas habilidades esperam entradas de tipos específicos, por exemplo, a [habilidade de sentimentos](cognitive-search-skill-sentiment.md) espera que `text` seja uma cadeia de caracteres. Se a entrada especificar um valor que não seja de cadeia de caracteres, a habilidade não será executada e não gerará nenhuma saída. Verifique se o conjunto de dados tem valores de entrada uniformes no tipo ou use uma [habilidade de API Web personalizada](cognitive-search-custom-skill-web-api.md) para pré-processar a entrada. Se você estiver Iterando a habilidade em uma matriz, verifique o contexto de habilidade e a entrada tem `*` nas posições corretas. Geralmente, o contexto e a fonte de entrada devem terminar com `*` para matrizes. |
| A entrada da habilidade está ausente | "A entrada de habilidade necessária está ausente. Nome: `text`, fonte: `/document/merged_content`"" valor `/document/normalized_images/0/imageTags`ausente ".  "Não é possível selecionar `0` na matriz `/document/pages` de comprimento `0`". | Se todos os documentos obtiverem esse aviso, provavelmente haverá um erro de digitação nos caminhos de entrada e você deverá verificar a maiúsculas e minúsculas do nome da propriedade, `*` extra ou ausente no caminho e certificar-se de que os documentos da fonte de dados forneçam as entradas necessárias. |
| A entrada do código do idioma da habilidade é inválida | `languageCode` de entrada de habilidades tem os códigos de idioma a seguir `X,Y,Z`, pelo menos um deles é inválido. | Veja mais detalhes [abaixo](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Aviso: a entrada de habilidade ' languageCode ' tem os seguintes códigos de idioma ' X, Y, Z ', pelo menos um dos quais é inválido.
Não há suporte para um ou mais valores passados para a entrada de `languageCode` opcional de uma habilidade de downstream. Isso pode ocorrer se você estiver passando a saída do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) para as habilidades subsequentes e a saída consistir em mais idiomas do que o suportado nessas habilidades de downstream.

Se você souber que o conjunto de dados está todos em um idioma, deverá remover o [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e a entrada de habilidade de `languageCode` e usar o parâmetro de habilidade `defaultLanguageCode` para essa habilidade, supondo que o idioma tenha suporte para essa habilidade.

Se você souber que o conjunto de dados contém vários idiomas e, portanto, precisar do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e da entrada de `languageCode`, considere adicionar um [ConditionalSkill](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não têm suporte antes de passar o texto para a habilidade de downstream.  Aqui está um exemplo de como seria o EntityRecognitionSkill:

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

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Aviso: a entrada da habilidade foi truncada
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

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Aviso: resposta de habilidade da API Web contém avisos
O indexador foi capaz de executar uma habilidade no configurador de habilidades, mas a resposta da solicitação da API Web indicou que havia avisos durante a execução. Examine os avisos para entender como os dados são afetados e se a ação é necessária ou não.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Aviso: a configuração atual do indexador não dá suporte ao progresso incremental

Esse aviso ocorre apenas para Cosmos DB fontes de dados.

O progresso incremental durante a indexação garante que, se a execução do indexador for interrompida por falhas transitórias ou por um limite de tempo de execução, o indexador poderá escolher onde parou na próxima vez em que for executado, em vez de ter que reindexar toda a coleção do zero. Isso é especialmente importante ao indexar grandes coleções.

A capacidade de retomar um trabalho de indexação não concluído é o predicado em ter documentos ordenados pela coluna `_ts`. O indexador usa o carimbo de data/hora para determinar qual documento deve ser selecionado em seguida. Se a coluna `_ts` estiver ausente ou se o indexador não puder determinar se uma consulta personalizada foi ordenada por ela, o indexador será iniciado no início e você verá esse aviso.

É possível substituir esse comportamento, permitindo o progresso incremental e suprimindo esse aviso usando a propriedade de configuração `assumeOrderByHighWatermarkColumn`.

Para obter mais informações, consulte [progresso incremental e consultas personalizadas](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Aviso: alguns dados foram perdidos durante a projeção. A linha ' X ' na tabela ' Y ' tem a propriedade de cadeia de caracteres ' Z ', que era muito longa.

O [serviço de armazenamento de tabela](https://azure.microsoft.com/services/storage/tables) tem limites de como [as propriedades de entidade](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) grande podem ser. As cadeias de caracteres podem ter de 32.000 a ou menos. Se uma linha com uma propriedade de cadeia de caracteres com mais de 32.000 caracteres estiver sendo projetada, somente os primeiros 32.000 caracteres serão preservados. Para contornar esse problema, evite projetar linhas com propriedades de cadeia de caracteres com mais de 32.000 caracteres.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Aviso: texto extraído truncado para caracteres X
Os indexadores limitam a quantidade de texto que pode ser extraída de um documento. Esse limite depende do tipo de preço: 32.000 caracteres para a camada gratuita, 64.000 para básico e 4 milhões para as camadas Standard, Standard S2 e Standard S3. O texto que estava truncado não será indexado. Para evitar esse aviso, tente separar documentos com grandes quantidades de texto em vários documentos menores. 

Para obter mais informações, consulte [limites do indexador](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Aviso: não foi possível mapear o campo de saída ' X ' para o índice de pesquisa
Os mapeamentos de campo de saída que referenciam dados não existentes/nulos produzirão avisos para cada documento e resultarão em um campo de índice vazio. Para solucionar esse problema, verifique novamente os caminhos de origem de mapeamento de campo de saída para possíveis erros de digitação ou defina um valor padrão usando a [habilidade condicional](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Aviso: a política de detecção de alteração de dados está configurada para usar a coluna de chave ' X '
[As políticas de detecção de alteração de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) têm requisitos específicos para as colunas que usam para detectar alterações. Um desses requisitos é que essa coluna é atualizada toda vez que o item de origem é alterado. Outro requisito é que o novo valor dessa coluna seja maior que o valor anterior. As colunas de chave não atendem a esse requisito porque não mudam em cada atualização. Para contornar esse problema, selecione uma coluna diferente para a política de detecção de alteração.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Aviso: o texto do documento parece ser codificado em UTF-16, mas não tem uma marca de ordem de byte

Os [modos de análise do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) precisam saber como o texto é codificado antes de analisá-lo. As duas maneiras mais comuns de codificar texto são UTF-16 e UTF-8. UTF-8 é uma codificação de comprimento variável em que cada caractere está entre 1 byte e 4 bytes de comprimento. UTF-16 é uma codificação de tamanho fixo em que cada caractere tem 2 bytes de comprimento. O UTF-16 tem duas variantes diferentes, "big endian" e "little endian". A codificação de texto é determinada por uma "marca de ordem de byte", uma série de bytes antes do texto.

| Encoding | Marca de ordem de byte |
| --- | --- |
| UTF-16 big endian | 0xFE 0xFF |
| UTF-16 little endian | 0xFE 0xFF |
| UTF-8 | 0xEF 0xBB 0xBF |

Se nenhuma marca de ordem de byte estiver presente, presume-se que o texto seja codificado como UTF-8.

Para contornar esse aviso, determine qual é a codificação de texto para esse BLOB e adicione a marca de ordem de byte apropriada.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Aviso: a coleção de Cosmos DB ' X ' tem uma política de indexação lenta. Alguns dados podem ser perdidos

As coleções com políticas de indexação [lentas](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) não podem ser consultadas de forma consistente, resultando na falta de dados no indexador. Para contornar esse aviso, altere sua política de indexação para consistente.
