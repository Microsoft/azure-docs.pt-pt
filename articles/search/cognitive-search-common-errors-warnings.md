---
title: Erros e avisos do indexador
titleSuffix: Azure Cognitive Search
description: Este artigo fornece informações e soluções para erros e avisos comuns que você pode encontrar durante o enriquecimento de IA em Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 3ba0abe8510291351c10ba085ba7e42b8197d886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553243"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Resolução de problemas erros e avisos comuns do indexante na Pesquisa Cognitiva do Azure

Este artigo fornece informações e soluções para erros e avisos comuns que você pode encontrar durante a indexação e enriquecimento de IA na Pesquisa Cognitiva Azure.

A indexação para quando a contagem de erros excede ['maxFailedItems'.](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 

Se pretender que os indexantes ignorem estes erros (e ignorem os "documentos falhados"), considere atualizar o `maxFailedItems` e como descrito `maxFailedItemsPerBatch` [aqui](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento falhado juntamente com a sua chave de documento (quando disponível) aparecerá como um erro no estado de execução do indexante. Pode utilizar o [índice api](/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos num ponto posterior se tiver definido o indexante para tolerar falhas.

As informações de erro neste artigo podem ajudá-lo a resolver erros, permitindo que a indexação continue.

Os avisos não param de indexar, mas indicam condições que podem resultar em resultados inesperados. Se toma medidas ou não depende dos dados e do seu cenário.

Começando pela versão `2019-05-06` API, os erros e avisos indexantes de nível de item são estruturados para proporcionar uma maior clareza em torno das causas e dos próximos passos. Contêm as seguintes propriedades:

| Propriedade | Descrição | Exemplo |
| --- | --- | --- |
| key | O documento identificação do documento impactado pelo erro ou aviso. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | O nome da operação descrevendo onde ocorreu o erro ou o aviso. Isto é gerado pela seguinte estrutura: [categoria]. [subcategoria]. [recursosType]. [nome de recurso] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projeção.SearchIndex.OutputFieldMapping.myOutputFieldName Projeção.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Uma descrição de alto nível do erro ou aviso. | Não foi possível executar habilidade porque o pedido da Web Api falhou. |
| detalhes | Quaisquer detalhes adicionais que possam ser úteis para diagnosticar o problema, como a resposta WebApi se executar uma habilidade personalizada falhou. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 fonte, Func `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` ... resto de traços de pilha... |
| documentaçãoLink | Um link para documentação relevante com informações detalhadas para depurar e resolver o problema. Este link irá muitas vezes apontar para uma das secções abaixo nesta página. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>Erro: Não consegui ler documento

Indexer não conseguiu ler o documento a partir da fonte de dados. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Tipos de campo inconsistentes em diferentes documentos | "O tipo de valor tem um desfasamento com o tipo de coluna. Não podia armazenar `'{47.6,-122.1}'` na coluna de autores.  O tipo esperado é JArray."  "Erro de conversão de dados nvarchar para flutuar."  "A conversão falhou na conversão do valor nvarchar '12 meses' para o tipo de dados int."  "Erro de transbordo aritmético convertendo a expressão para o tipo de dados int." | Certifique-se de que o tipo de cada campo é o mesmo em diferentes documentos. Por exemplo, se o primeiro campo de documento `'startTime'` for um DateTime, e no segundo documento for uma corda, este erro será atingido. |
| erros do serviço subjacente da fonte de dados | (da Cosmos DB) `{"Errors":["Request rate is large"]}` | Verifique a sua instância de armazenamento para se certificar de que está saudável. Pode ser necessário ajustar a sua escala/divisória. |
| questões transitórias | Ocorreu um erro de nível de transporte ao receber resultados do servidor. (fornecedor: Fornecedor TCP, erro: 0 - Uma ligação existente foi fechada à força pelo anfitrião remoto | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento através do seu indexante novamente mais tarde. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Erro: Não foi possível extrair conteúdo ou metadados do seu documento
Indexer com uma fonte de dados Blob não foi capaz de extrair o conteúdo ou metadados do documento (por exemplo, um ficheiro PDF). Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| blob está acima do limite de tamanho | O documento é `'150441598'` bytes, que excede o tamanho máximo `'134217728'` de bytes para a extração de documentos para o seu nível de serviço atual. | [erros de indexação blob](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob tem tipo de conteúdo não suportado | O documento tem o tipo de conteúdo não suportado `'image/png'` | [erros de indexação blob](search-howto-indexing-azure-blob-storage.md#DealingWithErrors) |
| blob é encriptado | O documento não pôde ser processado - pode ser encriptado ou protegido por palavra-passe. | Pode saltar a bolha com [as definições de bolhas](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex). |
| questões transitórias | "Bolha de processamento de erro: O pedido foi abortado: O pedido foi cancelado." "Documento cronometrado durante o processamento." | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento através do seu indexante novamente mais tarde. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>Erro: Não foi possível analisar o documento
Indexer leu o documento a partir da fonte de dados, mas houve um problema de conversão do conteúdo do documento no esquema de mapeamento de campo especificado. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Falta a chave do documento. | A chave do documento não pode faltar ou esvaziar | Certifique-se de que todos os documentos têm chaves de documento válidas. A chave do documento é determinada através da definição da propriedade 'chave' como parte da [definição](/rest/api/searchservice/create-index#request-body)de índice . Os indexantes emitirão este erro quando a propriedade sinalizada como 'chave' não pode ser encontrada num determinado documento. |
| A chave do documento é inválida | A chave do documento não pode ter mais de 1024 caracteres | Modifique a chave do documento para satisfazer os requisitos de validação. |
| Não poderia aplicar mapeamento de campo a um campo | Não foi possível aplicar a função de mapeamento `'functionName'` no campo `'fieldName'` . A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique [duas vezes os mapeamentos](search-indexer-field-mappings.md) de campo definidos no indexante e compare com os dados do campo especificado do documento falhado. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não conseguia ler o valor do campo | Não consegui ler o valor da coluna `'fieldName'` no `'fieldIndex'` índice. Ocorreu um erro de nível de transporte ao receber resultados do servidor. (fornecedor: Fornecedor TCP, erro: 0 - Uma ligação existente foi fechada à força pelo hospedeiro remoto.) | Estes erros são normalmente devido a problemas de conectividade inesperados com o serviço subjacente da fonte de dados. Tente passar o documento através do seu indexante novamente mais tarde. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>Erro: Não foi possível mapear o campo de saída `xyz` ' '' para o índice de pesquisa devido a problema de deserialização enquanto aplicava a função de mapeamento `abc` ' '
O mapeamento de saída pode ter falhado porque os dados de saída estão no formato errado para a função de mapeamento que está a usar. Por exemplo, a aplicação da função de mapeamento Base64Encode em dados binários geraria este erro. Para resolver o problema, repercuta o indexante sem especificar a função de mapeamento ou certifique-se de que a função de mapeamento é compatível com o tipo de dados do campo de saída. Consulte [o mapeamento do campo de saída](cognitive-search-output-field-mapping.md) para obter mais detalhes.

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>Erro: Não foi possível executar habilidade
Indexer não foi capaz de executar uma habilidade no skillset.

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Problemas de conectividade transitória | Ocorreu um erro transitório. Tente novamente mais tarde. | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento através do seu indexante novamente mais tarde. |
| Bug potencial do produto | Ocorreu um erro inesperado. | Isto indica uma classe de falha desconhecida e pode significar que há um bug do produto. Por favor, preencha um [bilhete de apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter ajuda. |
| Uma habilidade encontrou um erro durante a execução | (Da Capacidade de Fusão) Um ou mais valores compensados eram inválidos e não podiam ser analisados. Os itens foram inseridos no final do texto | Utilize as informações na mensagem de erro para corrigir o problema. Este tipo de falha exigirá medidas para resolver. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Erro: Não foi possível executar habilidade porque o pedido da API web falhou
A execução de habilidades falhou porque a chamada para a API web falhou. Normalmente, esta classe de falha ocorre quando são utilizadas habilidades personalizadas, caso em que terá de desordiá-lo para resolver o problema. Se em vez disso a falha for de uma habilidade incorporada, consulte a mensagem de erro para ajudar a corrigir o problema.

Ao depurar este problema, não se esqueça de prestar atenção a quaisquer [advertências de entrada de habilidades](#warning-skill-input-was-invalid) para esta habilidade. O seu ponto final da API Web pode estar a falhar porque o indexante está a passar-lhe uma entrada inesperada.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Erro: Não foi possível executar habilidade porque a resposta de habilidade da Web API é inválida
A execução de habilidades falhou porque a chamada para a API web devolveu uma resposta inválida. Normalmente, esta classe de falha ocorre quando são utilizadas habilidades personalizadas, caso em que terá de desordiá-lo para resolver o problema. Se em vez disso a falha for de uma habilidade incorporada, por favor, preencha um [bilhete de apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Erro: Habilidade não executou dentro do prazo
Existem dois casos em que poderá encontrar esta mensagem de erro, cada uma das quais deve ser tratada de forma diferente. Siga as instruções abaixo, dependendo da habilidade que devolveu este erro para si.

### <a name="built-in-cognitive-service-skills"></a>Competências de Serviço Cognitivo Incorporado
Muitas das habilidades cognitivas incorporadas, tais como deteção de linguagens, reconhecimento de entidades ou OCR, são apoiadas por um ponto final da API do Serviço Cognitivo. Por vezes, há questões transitórias com estes pontos finais e um pedido vai esgotar-se. Para questões transitórias, não há remédio a não ser esperar e tentar novamente. Como mitigação, considere definir o seu indexante para [executar um horário](search-howto-schedule-indexers.md). A indexação programada retoma onde deixou de lado. Assumindo que as questões transitórias são resolvidas, a indexação e o processamento de habilidades cognitivas devem poder continuar na próxima corrida programada.

Se continuar a ver este erro no mesmo documento para uma habilidade cognitiva incorporada, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência, uma vez que tal não é esperado.

### <a name="custom-skills"></a>Competências personalizadas
Se encontrar um erro de tempo limite com uma habilidade personalizada que criou, há algumas coisas que pode tentar. Em primeiro lugar, reveja a sua habilidade personalizada e certifique-se de que não está preso num ciclo infinito e que está a devolver um resultado de forma consistente. Assim que confirmares que é esse o caso, determina qual é o tempo de execução da tua habilidade. Se não definiu explicitamente um `timeout` valor na definição de habilidade personalizada, então o padrão `timeout` é de 30 segundos. Se 30 segundos não for suficiente para a sua habilidade ser executada, poderá especificar um valor mais elevado `timeout` na definição de habilidade personalizada. Aqui está um exemplo de uma definição de habilidade personalizada onde o tempo limite é definido para 90 segundos:

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

O valor máximo que pode definir para o `timeout` parâmetro é de 230 segundos.  Se a sua habilidade personalizada não for capaz de executar de forma consistente dentro de 230 segundos, poderá considerar reduzir a `batchSize` sua habilidade personalizada para que tenha menos documentos para processar dentro de uma única execução.  Se já definiu o seu `batchSize` 1, terá de reescrever a habilidade para poder executar em menos de 230 segundos ou dividi-la em múltiplas habilidades personalizadas para que o tempo de execução para qualquer habilidade personalizada seja de um máximo de 230 segundos. Reveja a [documentação de habilidade personalizada](cognitive-search-custom-skill-web-api.md) para mais informações.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Erro: Não podia ' `MergeOrUpload` | '' `Delete` documento para o índice de pesquisa

O documento foi lido e processado, mas o indexante não pôde adicioná-lo ao índice de pesquisa. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Um campo contém um termo que é muito grande | Um termo no seu documento é maior do que o limite de [32 KB](search-limits-quotas-capacity.md#api-request-limits) | Pode evitar esta restrição garantindo que o campo não está configurado como filtrado, facial ou ordenado.
| O documento é demasiado grande para ser indexado | Um documento é maior do que o [tamanho máximo de pedido de api](search-limits-quotas-capacity.md#api-request-limits) | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| Documento contém muitos objetos na coleção | Uma coleção no seu documento excede os [elementos máximos em todas as coleções complexas limite](search-limits-quotas-capacity.md#index-limits) "O documento com chave `'1000052'` tem `'4303'` objetos em coleções (matrizes JSON). Na maioria dos `'3000'` objetos são permitidos estar em coleções em todo o documento. Por favor, remova objetos das coleções e tente indexar o documento novamente." | Recomendamos reduzir o tamanho da coleção complexa no documento para abaixo do limite e evitar uma elevada utilização do armazenamento.
| Problemas de ligação ao índice-alvo (que persiste após as retrações) porque o serviço está sob outra carga, como consulta ou indexação. | Não conseguiu estabelecer a ligação ao índice de atualização. O serviço de pesquisa está sob carga pesada. | [Dimensione o seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está a ser remendado para atualização de serviço, ou está no meio de uma reconfiguração de topologia. | Não conseguiu estabelecer a ligação ao índice de atualização. O serviço de pesquisa está atualmente em baixo/O serviço de pesquisa está a passar por uma transição. | Serviço de configuração com pelo menos 3 réplicas para 99,9% de disponibilidade por [documentação SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Não conseguiu estabelecer a ligação ao índice de atualização. Ocorreu uma falha desconhecida. | Configure os indexadores para [executar em um horário](search-howto-schedule-indexers.md) para pegar de um estado falhado.
| Um pedido de indexação feito ao índice-alvo não foi reconhecido num prazo de tempo devido a problemas de rede. | Não foi possível estabelecer a ligação ao índice de pesquisa em tempo útil. | Configure os indexadores para [executar em um horário](search-howto-schedule-indexers.md) para pegar de um estado falhado. Além disso, tente baixar o tamanho do [lote](/rest/api/searchservice/create-indexer#parameters) do indexante se esta condição de erro persistir.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Erro: Não foi possível indexar o documento porque alguns dos dados do documento não eram válidos

O documento foi lido e processado pelo indexante, mas devido a uma incompatibilidade na configuração dos campos de índice e nos dados extraídos e tratados pelo indexante, não pôde ser adicionado ao índice de pesquisa. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo
| --- | ---
| O tipo de dados do campo extraído pelo indexante é incompatível com o modelo de dados do respetivo campo de índice-alvo. | O campo de dados '_dados_' no documento com a chave '888' tem um valor inválido 'do tipo 'Edm.String'. O tipo esperado era 'Collection(Edm.String)'. |
| Não conseguiu extrair qualquer entidade JSON de um valor de corda. | Não podia analisar o valor 'do tipo 'Edm.String' dos _dados_ do campo ' como um objeto JSON. Erro:'Depois de analisar um valor, um personagem inesperado foi encontrado: ''. Caminho '_caminho_', linha 1, posição 3162.' |
| Não conseguiu extrair uma coleção de entidades JSON de um valor de corda.  | Não podia analisar o valor 'do tipo 'Edm.String' dos _dados_ do campo ' como uma matriz JSON. Erro:'Depois de analisar um valor, um personagem inesperado foi encontrado: ''. Caminho '[0]', linha 1, posição 27.» |
| Um tipo desconhecido foi descoberto no documento de origem. | Tipo desconhecido '_desconhecido_' não pode ser indexado |
| No documento-origem foi utilizada uma notação incompatível para pontos de geografia. | As cordas de WKT POINT não são suportadas. Por favor, use geoJson ponto literal em vez |

Em todos estes casos, consulte [os tipos de dados suportados](/rest/api/searchservice/supported-data-types) e [o mapa do tipo de dados para indexadores](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) para se certificar de que constrói corretamente o esquema de índice e que configura [mapeamentos de campo indexantes apropriados](search-indexer-field-mappings.md). A mensagem de erro incluirá detalhes que podem ajudar a localizar a origem da incompatibilidade.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Erro: A política integrada de rastreio de alterações não pode ser utilizada porque a tabela tem uma chave primária composta

Isto aplica-se às tabelas SQL, e geralmente acontece quando a chave é definida como uma chave composta ou, quando a tabela definiu um índice agrupado único (como num índice SQL, e não num índice de Pesquisa Azure). A razão principal é que o atributo chave é modificado para ser uma chave primária composta no caso de um [índice único agrupado](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described). Nesse caso, certifique-se de que a sua tabela SQL não tem um índice agrupado único, ou que mapeia o campo chave para um campo que é garantido não ter valores duplicados.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Erro: Não foi possível processar o documento dentro do tempo de funcionamento do indexante

Este erro ocorre quando o indexante não consegue terminar o processamento de um único documento a partir da fonte de dados dentro do tempo de execução permitido. [O tempo máximo de funcionamento](search-limits-quotas-capacity.md#indexer-limits) é mais curto quando as habilidades são utilizadas. Quando este erro ocorre, se tiver maxFailedItems definido para um valor diferente de 0, o indexante contorna o documento em execuções futuras para que a indexação possa progredir. Se não puder ignorar qualquer documento, ou se estiver a ver este erro de forma consistente, considere dividir documentos em documentos menores para que os progressos parciais possam ser feitos dentro de uma única execução indexante.

<um nome="não podia-projecto-documento></a>

## <a name="error-could-not-project-document"></a>Erro: Não foi possível projetar documento

Este erro ocorre quando o indexante está a tentar [projetar dados numa loja de conhecimento](knowledge-store-projection-overview.md) e houve uma falha na nossa tentativa de o fazer.  Esta falha pode ser consistente e reparável ou pode ser uma falha transitória com o lavatório de projeção que poderá ter de esperar e voltar a tentar para resolver.  Aqui estão um conjunto de estados de fracasso conhecidos e possíveis resoluções.

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Não foi possível atualizar a bolha de projeção `'blobUri'` no recipiente `'containerName'` |O recipiente especificado não existe. | O indexante verificará se o recipiente especificado foi previamente criado e irá criá-lo se necessário, mas só efetua esta verificação uma vez que o indexante seja executado. Este erro significa que algo apagou o recipiente após este passo.  Para resolver este erro, tente isto: deixe as informações da sua conta de armazenamento em paz, aguarde que o indexante termine e, em seguida, reexecute o indexante. |
| Não foi possível atualizar a bolha de projeção `'blobUri'` no recipiente `'containerName'` |Não foi possível escrever dados para a ligação de transporte: Uma ligação existente foi fechada à força pelo hospedeiro remoto. | Espera-se que esta seja uma falha transitória com o Azure Storage e, portanto, deve ser resolvida através da re-tação do indexante. Se encontrar este erro de forma consistente, por favor preencha um [bilhete de apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possa ser investigado mais adiante.  |
| Não foi possível atualizar a fila `'projectionRow'` na tabela `'tableName'` | O servidor está ocupado. | Espera-se que esta seja uma falha transitória com o Azure Storage e, portanto, deve ser resolvida através da re-tação do indexante. Se encontrar este erro de forma consistente, por favor preencha um [bilhete de apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possa ser investigado mais adiante.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>Aviso: A entrada de habilidades foi inválida
Faltava uma entrada para a habilidade, o tipo errado, ou de outra forma inválido. A mensagem de aviso indicará o impacto:
1) Não podia executar habilidade
2) Habilidade executada mas pode ter resultados inesperados

As competências cognitivas requerem entradas e entradas opcionais. Por exemplo, a [habilidade de extração da frase chave](cognitive-search-skill-keyphrases.md) tem duas entradas necessárias , e nenhuma entrada `text` `languageCode` opcional. As entradas de habilidades personalizadas são consideradas entradas opcionais.

Se alguma entrada necessária estiver em falta ou se alguma entrada não for o tipo certo, a habilidade é ignorada e gera um aviso. As habilidades ignoradas não geram saídas, por isso, se outras habilidades utilizarem saídas da habilidade ignorada, poderão gerar avisos adicionais.

Se faltar uma entrada opcional, a habilidade continuará a funcionar, mas poderá produzir uma saída inesperada devido à entrada em falta.

Em ambos os casos, este aviso pode ser esperado devido à forma dos seus dados. Por exemplo, se tiver um documento que contenha informações sobre pessoas com os `firstName` `middleName` campos, `lastName` e, pode ter alguns documentos que não têm `middleName` entrada. Se passar `middleName` como entrada para uma habilidade no oleoduto, então espera-se que esta entrada de habilidades possa estar a perder algum tempo. Será necessário avaliar os seus dados e cenários para determinar se é ou não necessária alguma ação como resultado deste aviso.

Se pretender fornecer um valor predefinido em caso de falta de entrada, pode utilizar a [habilidade condicional](cognitive-search-skill-conditional.md) para gerar um valor predefinido e, em seguida, utilizar a saída da [habilidade condicional](cognitive-search-skill-conditional.md) como entrada de habilidade.


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

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| A entrada de habilidade é o tipo errado | "A entrada de habilidades necessárias não era do tipo `String` esperado. Nome: `text` , Fonte: `/document/merged_content` ."  "A entrada de habilidades necessárias não era do formato esperado. Nome: `text` , Fonte: `/document/merged_content` ."  "Não pode iterar sobre `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` não-matriz."  "Incapaz de selecionar `0` em não `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` matriz" | Certas habilidades esperam entradas de tipos específicos, por exemplo, [a habilidade de sentimento](cognitive-search-skill-sentiment.md) espera ser uma `text` corda. Se a entrada especificar um valor não-string, então a habilidade não executa e não gera saídas. Certifique-se de que o seu conjunto de dados tem valores de entrada uniformes em tipo, ou utilize uma [habilidade de API web personalizada](cognitive-search-custom-skill-web-api.md) para pré-processar a entrada. Se estiver a imtar a habilidade sobre uma matriz, verifique o contexto de habilidade e a entrada `*` nas posições corretas. Normalmente, tanto o contexto como a fonte de entrada devem terminar com `*` matrizes. |
| Falta a entrada de habilidades | "Falta a entrada de habilidades necessárias. Nome: `text` , Fonte: `/document/merged_content` " "Valor em `/document/normalized_images/0/imageTags` falta."  "Incapaz de selecionar `0` em matriz `/document/pages` de `0` comprimento." | Se todos os seus documentos recebem este aviso, muito provavelmente há um erro nos caminhos de entrada e você deve verificar duas vezes o invólucro do nome da propriedade, extra ou `*` faltando no caminho, e certifique-se de que os documentos da fonte de dados fornecem as entradas necessárias. |
| A entrada do código de linguagem de habilidade é inválida | A entrada de `languageCode` habilidades tem os seguintes códigos `X,Y,Z` linguísticos, pelo menos um dos quais é inválido. | Veja mais detalhes [abaixo](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Aviso: A entrada de habilidades 'languageCode' tem os seguintes códigos linguísticos 'X,Y,Z', pelo menos um dos quais é inválido.
Um ou mais dos valores passados para a entrada opcional `languageCode` de uma habilidade a jusante não é suportado. Isto pode ocorrer se estiver a passar a saída do [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) para competências subsequentes, e a saída consiste em mais idiomas do que são suportados nessas competências a jusante.

Note que também pode receber um aviso semelhante a este se uma entrada inválida `countryHint` for passada para o LanguageDetectionSkill. Se isso acontecer, por favor valide que o campo que está a utilizar a partir da sua fonte de dados para essa entrada contém códigos de país ISO 3166-1 alfa-2 de letra válidos. Se alguns forem válidos e alguns forem inválidos, continue com as seguintes orientações, mas `languageCode` substitua-os com `countryHint` e com o caso de `defaultLanguageCode` `defaultCountryHint` utilização.

Se sabe que o seu conjunto de dados está todo num só idioma, deve remover o [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) e a `languageCode` entrada de habilidades e utilizar o `defaultLanguageCode` parâmetro de habilidade para essa habilidade, assumindo que a linguagem é suportada para essa habilidade.

Se sabe que o seu conjunto de dados contém vários idiomas e, portanto, precisa da [introdução languageDetectionSkill,](cognitive-search-skill-language-detection.md) `languageCode` considere adicionar um [ConditionalSkill](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não são suportados antes de passar o texto para a habilidade a jusante.  Aqui está um exemplo de como isto pode parecer para a EntityRecognitionSkill:

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

Aqui estão algumas referências para as línguas atualmente suportadas para cada uma das habilidades que podem produzir esta mensagem de erro:
* [Texto Analytics Suportado idiomas](../cognitive-services/text-analytics/language-support.md) (para o [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill,](cognitive-search-skill-entity-recognition.md) [SentimentSkill](cognitive-search-skill-sentiment.md)e [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md)
* [Línguas apoiadas pelo tradutor](../cognitive-services/translator/language-support.md) (para o [texto TranslationSkill)](cognitive-search-skill-text-translation.md)
* [Texto SplitSkill](cognitive-search-skill-textsplit.md) Línguas suportadas: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>Aviso: Entrada de habilidade foi truncada
As capacidades cognitivas têm limites ao comprimento do texto que pode ser analisado ao mesmo tempo. Se a entrada de texto destas competências ultrapassar esse limite, truncaremos o texto para cumprir o limite e, em seguida, realizaremos o enriquecimento nesse texto truncado. Isto significa que a habilidade é executada, mas não em todos os seus dados.

No exemplo LanguageDetectionSkill abaixo, o `'text'` campo de entrada pode desencadear este aviso se estiver acima do limite de caracteres. Pode encontrar os limites de entrada de competências na documentação de [competências.](cognitive-search-predefined-skills.md)

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

Se quiser certificar-se de que todo o texto é analisado, considere utilizar a [habilidade Split](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Aviso: A resposta de habilidade da API web contém avisos
Indexer foi capaz de executar uma habilidade no skillset, mas a resposta do pedido da API web indicava que havia avisos durante a execução. Reveja os avisos para entender como os seus dados são impactados e se é ou não necessária ação.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Aviso: A configuração atual do indexante não suporta o progresso incremental

Este aviso ocorre apenas para fontes de dados da Cosmos DB.

O progresso incremental durante a indexação garante que se a execução do indexante for interrompida por falhas transitórias ou por um prazo de execução, o indexante pode retomar onde ficou de fora da próxima vez que for executado, em vez de ter que re-indexar toda a coleção do zero. Isto é especialmente importante na indexação de grandes coleções.

A capacidade de retomar um trabalho de indexação inacabado baseia-se em ter documentos encomendados pela `_ts` coluna. O indexante usa a hora para determinar qual o documento a recolher a seguir. Se a `_ts` coluna estiver em falta ou se o indexante não conseguir determinar se uma consulta personalizada é ordenada por ela, o indexante começa no início e verá este aviso.

É possível anular este comportamento, permitindo o progresso incremental e suprimindo este aviso utilizando a `assumeOrderByHighWatermarkColumn` propriedade de configuração.

Para mais informações, consulte [progresso incremental e consultas personalizadas.](search-howto-index-cosmosdb.md#IncrementalProgress)

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Aviso: Alguns dados foram perdidos durante a projeção. Linha 'X' na tabela 'Y' tem propriedade de cordas 'Z' que foi muito longa.

O [serviço de Armazenamento de Mesa](https://azure.microsoft.com/services/storage/tables) tem limites para a grande quantidade de propriedades da [entidade.](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) As cordas podem ter 32.000 caracteres ou menos. Se uma linha com uma propriedade de cordas superior a 32.000 caracteres está sendo projetada, apenas os primeiros 32.000 caracteres são preservados. Para contornar esta questão, evite projetar linhas com propriedades de cordas superiores a 32.000 caracteres.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Aviso: Texto extraído truncado para caracteres X
Os indexantes limitam a quantidade de texto que pode ser extraído de qualquer documento. Este limite depende do nível de preços: 32.000 caracteres para o free tier, 64.000 para Basic, 4 milhões para Standard, 8 milhões para Standard S2 e 16 milhões para o Standard S3. O texto truncado não será indexado. Para evitar este aviso, tente separar documentos com grandes quantidades de texto em documentos múltiplos e menores. 

Para obter mais informações, consulte [os limites do Indexer.](search-limits-quotas-capacity.md#indexer-limits)

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Aviso: Não foi possível mapear o campo de saída 'X' para o índice de pesquisa
Os mapeamentos do campo de saída que referenciam dados inexistentes/nulos produzirão avisos para cada documento e resultarão num campo de índice vazio. Para resolver este problema, verifique duas vezes os caminhos de origem de mapeamento de campo de saída para possíveis tipografias ou desaperte um valor padrão utilizando a [habilidade condicional](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist). Consulte [o mapeamento do campo de saída](cognitive-search-output-field-mapping.md) para obter mais detalhes.

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Não pode iterar sobre não-matriz | "Não pode iterar sobre `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` não-matriz." | Este erro ocorre quando a saída não é uma matriz. Se acha que a saída deve ser uma matriz, verifique se há erros no campo de origem de saída indicado. Por exemplo, pode ter um desaparecido ou extra `*` no nome do campo de origem. Também é possível que a entrada para esta habilidade seja nula, resultando numa matriz vazia. Encontrar detalhes semelhantes na [Secção DeSumana foi inválido.](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)    |
| Incapaz de selecionar `0` em não matriz | "Incapaz de selecionar `0` em não `/document/pages` matriz." | Isto pode acontecer se a produção de competências não produzir uma matriz e o nome do campo de origem de saída tiver índice de matriz ou `*` no seu caminho. Verifique duas vezes os caminhos fornecidos nos nomes do campo de origem de saída e o valor de campo para o nome de campo indicado. Encontrar detalhes semelhantes na [Secção DeSumana foi inválido.](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid)  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Aviso: A política de deteção de alterações de dados está configurada para utilizar a coluna-chave 'X'
[As políticas de deteção de alterações](/rest/api/searchservice/create-data-source#data-change-detection-policies) de dados têm requisitos específicos para as colunas que utilizam para detetar alterações. Um desses requisitos é que esta coluna é atualizada sempre que o item de origem é alterado. Outro requisito é que o novo valor para esta coluna é maior do que o valor anterior. As colunas-chave não cumprem este requisito porque não mudam em todas as atualizações. Para contornar este problema, selecione uma coluna diferente para a política de deteção de alterações.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Aviso: O texto do documento parece estar codificado UTF-16, mas falta uma marca de ordem byte

Os [modos de análise do indexante](/rest/api/searchservice/create-indexer#blob-configuration-parameters) precisam de saber como o texto é codificado antes de o analisar. As duas formas mais comuns de codificar textos são UTF-16 e UTF-8. UTF-8 é uma codificação de comprimento variável onde cada personagem tem entre 1 byte e 4 bytes de comprimento. UTF-16 é uma codificação de comprimento fixo onde cada personagem tem 2 bytes de comprimento. UTF-16 tem duas variantes diferentes, "big endian" e "pequeno endian". A codificação de texto é determinada por uma "marca de ordem byte", uma série de bytes antes do texto.

| Encoding | Marca de Ordem Byte |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Pequeno Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Se não houver marca de ordem byte, presume-se que o texto está codificado como UTF-8.

Para contornar este aviso, determine qual é o texto codificado para esta bolha e adicione a marca de ordem byte apropriada.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Aviso: A coleção 'X' da Cosmos DB tem uma política de indexação preguiçosa. Alguns dados podem ser perdidos

As coleções com políticas de indexação [preguiçosas](../cosmos-db/index-policy.md#indexing-mode) não podem ser consultadas de forma consistente, resultando em dados em falta do seu indexante. Para contornar este aviso, altere a sua política de indexação para Consistente.

## <a name="warning-the-document-contains-very-long-words-longer-than-64-characters-these-words-may-result-in-truncated-andor-unreliable-model-predictions"></a>Aviso: O documento contém palavras muito longas (mais de 64 caracteres). Estas palavras podem resultar em previsões de modelos truncadas e/ou pouco fiáveis.

Este aviso é transmitido pelo serviço Text Analytics.  Em alguns casos, é seguro ignorar este aviso, como quando o seu documento contém um URL longo (o que provavelmente não é uma frase chave ou sentimento de condução, etc.).  Esteja ciente de que quando uma palavra é maior que 64 caracteres, será truncado para 64 caracteres que podem afetar as previsões do modelo.