---
title: Erros e avisos do indexador
titleSuffix: Azure Cognitive Search
description: Este artigo fornece informações e soluções para erros e avisos comuns que pode encontrar durante o enriquecimento de IA em Pesquisa Cognitiva Azure.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed10e998ea05b6687190b1f87095f8bc28265905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086620"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Resolução de problemas comum de erros e avisos de indexantes em Pesquisa Cognitiva Azure

Este artigo fornece informações e soluções para erros e avisos comuns que pode encontrar durante a indexação e enriquecimento de IA na Pesquisa Cognitiva Azure.

A indexação para quando a contagem de erros excede ['maxFailedItems'.](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 

Se quiser que os indexadores ignorem estes erros (e `maxFailedItems` saltem por cima de "documentos falhados"), considere atualizar o e `maxFailedItemsPerBatch` o descrito [aqui](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers).

> [!NOTE]
> Cada documento falhado juntamente com a sua chave de documento (quando disponível) aparecerão como um erro no estado de execução do indexante. Pode utilizar o [índice api](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) para carregar manualmente os documentos num ponto posterior se tiver definido o indexante para tolerar falhas.

As informações de erro neste artigo podem ajudá-lo a resolver erros, permitindo que a indexação continue.

Os avisos não param de indexar, mas indicam condições que podem resultar em resultados inesperados. Quer tome medidas ou não depende dos dados e do seu cenário.

Começando com `2019-05-06`a versão API, os erros e avisos indexantes de nível de item são estruturados para proporcionar maior clareza em torno de causas e próximos passos. Contêm as seguintes propriedades:

| Propriedade | Descrição | Exemplo |
| --- | --- | --- |
| key | A identificação do documento do documento impactada pelo erro ou aviso. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| nome | O nome de operação descreve onde ocorreu o erro ou o aviso. Isto é gerado pela seguinte estrutura: [categoria]. [subcategoria]. [resourceType]. [nome dos recursos] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | Uma descrição de alto nível do erro ou aviso. | Não conseguiu executar a habilidade porque o pedido da Web Api falhou. |
| detalhes | Quaisquer detalhes adicionais que possam ser úteis para diagnosticar o problema, como a resposta WebApi se executar uma habilidade personalizada falhou. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 fonte,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... resto do vestígio da pilha... |
| documentaçãoLink | Um link para documentação relevante com informações detalhadas para depurar e resolver o problema. Este link irá muitas vezes apontar para uma das secções abaixo nesta página. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Erro: Não consegui ler documento

O indexante não conseguiu ler o documento a partir da fonte de dados. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Tipos de campo inconsistentes em diferentes documentos | "O tipo de valor tem um desfasamento com o tipo de coluna. Não podia `'{47.6,-122.1}'` armazenar na coluna de autores.  O tipo esperado é JArray."  "Erro de conversão de nvarchar tipo de dados para flutuar."  "A conversão falhou na conversão do valor nvarchar '12 meses' para o tipo de dados int."  "Erro de transbordo aritmético convertendo expressão em int tipo de dados." | Certifique-se de que o tipo de cada campo é o mesmo em diferentes documentos. Por exemplo, se `'startTime'` o primeiro campo de documentofor um DateTime, e no segundo documento for uma sequência, este erro será atingido. |
| erros do serviço subjacente da fonte de dados | (de Cosmos DB)`{"Errors":["Request rate is large"]}` | Verifique a sua instância de armazenamento para se certificar de que está saudável. Pode ser necessário ajustar a sua escala/partição. |
| questões transitórias | Ocorreu um erro ao nível de transporte ao receber resultados do servidor. (fornecedor: Fornecedor tCP, erro: 0 - Uma ligação existente foi fechada à força pelo hospedeiro remoto | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento pelo seu indexante novamente mais tarde. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Erro: Não conseguiu extrair conteúdo ou metadados do seu documento
O indexante com uma fonte de dados blob não foi capaz de extrair o conteúdo ou metadados do documento (por exemplo, um ficheiro PDF). Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| blob é sobre o limite de tamanho | O `'150441598'` documento é bytes, que `'134217728'` excede o tamanho máximo para a extração de documentos para o seu atual nível de serviço. | [erros de indexação blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob tem tipo de conteúdo não suportado | Documento tem tipo de conteúdo não suportado`'image/png'` | [erros de indexação blob](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob é encriptado | O documento não podia ser processado - pode ser encriptado ou protegido por palavra-passe. | Pode saltar a bolha com [as definições](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)de bolhas . |
| questões transitórias | "Erro de processamento blob: O pedido foi abortado: O pedido foi cancelado." "Documento cronometrado durante o processamento." | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento pelo seu indexante novamente mais tarde. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Erro: Não podia analisar documento
O indexante leu o documento a partir da fonte de dados, mas houve um problema de conversão do conteúdo do documento no esquema de mapeamento de campo especificado. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| A chave do documento está em falta. | A chave do documento não pode faltar ou esvaziar | Certifique-se de que todos os documentos têm chaves de documento válidas |
| A chave do documento é inválida | A chave do documento não pode ser superior a 1024 caracteres | Modifique a chave do documento para satisfazer os requisitos de validação. |
| Não poderia aplicar mapeamento de campo em um campo | Não podia aplicar `'functionName'` a `'fieldName'`função de mapeamento no campo . A matriz não pode ser nula. Nome do parâmetro: bytes | Verifique duas vezes os [mapeamentos](search-indexer-field-mappings.md) de campo definidos no indexador e compare com os dados do campo especificado do documento falhado. Pode ser necessário modificar os mapeamentos de campo ou os dados do documento. |
| Não podia ler o valor do campo | Não consegui ler o `'fieldName'` valor `'fieldIndex'`da coluna no índice . Ocorreu um erro ao nível de transporte ao receber resultados do servidor. (fornecedor: Fornecedor TCP, erro: 0 - Uma ligação existente foi fechada à força pelo hospedeiro remoto.) | Estes erros devem-se tipicamente a problemas de conectividade inesperados com o serviço subjacente da fonte de dados. Tente passar o documento pelo seu indexante novamente mais tarde. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Erro: Não podia executar habilidade
O indexante não foi capaz de executar uma habilidade no skillset.

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Problemas transitórios de conectividade | Ocorreu um erro transitório. Tente novamente mais tarde. | Ocasionalmente, há problemas de conectividade inesperados. Tente passar o documento pelo seu indexante novamente mais tarde. |
| Bug potencial do produto | Ocorreu um erro inesperado. | Isto indica uma classe de falha desconhecida e pode significar que há um bug do produto. Por favor, preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter ajuda. |
| Uma habilidade encontrou um erro durante a execução | (De Função De Habilidade de Fusão) Um ou mais valores compensados eram inválidos e não podiam ser analisados. Itens foram inseridos no final do texto | Utilize a informação na mensagem de erro para corrigir o problema. Este tipo de falha exigirá medidas para resolver. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Erro: Não conseguiu executar a habilidade porque o pedido da Web API falhou
A execução de habilidades falhou porque a chamada para a Web API falhou. Normalmente, esta classe de falha ocorre quando são utilizadas habilidades personalizadas, caso em que terá de desinpurar o seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade incorporada, consulte a mensagem de erro para ajudar na resolução do problema.

Ao depurar esta questão, certifique-se de prestar atenção a quaisquer avisos de entrada de [habilidadepara](#warning-skill-input-was-invalid) esta habilidade. O ponto final da Web API pode estar a falhar porque o indexante está a passar-lhe uma entrada inesperada.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Erro: Não podia executar habilidade porque a resposta à habilidade da Web API é inválida
A execução de habilidades falhou porque a chamada para a Web API devolveu uma resposta inválida. Normalmente, esta classe de falha ocorre quando são utilizadas habilidades personalizadas, caso em que terá de desinpurar o seu código personalizado para resolver o problema. Se, em vez disso, a falha for de uma habilidade incorporada, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Erro: A habilidade não executou dentro do prazo
Há dois casos em que pode encontrar esta mensagem de erro, cada um dos quais deve ser tratado de forma diferente. Por favor, siga as instruções abaixo, dependendo da habilidade que lhe devolveu este erro.

### <a name="built-in-cognitive-service-skills"></a>Competências do Serviço Cognitivo Incorporado
Muitas das habilidades cognitivas incorporadas, tais como deteção de linguagem, reconhecimento de entidades ou OCR, são apoiadas por um ponto final da API do Serviço Cognitivo. Por vezes, há questões transitórias com estes pontos finais e um pedido vai esgotar-se. Para questões transitórias, não há remédio a não ser esperar e tentar novamente. Como mitigação, considere definir o seu indexante para [funcionar num horário](search-howto-schedule-indexers.md). A indexação programada retoma onde ficou parado. Assumindo que as questões transitórias são resolvidas, a indexação e o processamento de habilidades cognitivas devem poder continuar na próxima execução programada.

Se continuar a ver este erro no mesmo documento para uma habilidade cognitiva incorporada, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para obter assistência, como não é esperado.

### <a name="custom-skills"></a>Competências personalizadas
Se encontrar um erro de tempo com uma habilidade personalizada que criou, há algumas coisas que pode tentar. Primeiro, reveja a sua habilidade personalizada e certifique-se de que não está preso num ciclo infinito e que está a devolver um resultado de forma consistente. Assim que confirmares que é esse o caso, determina qual é o tempo de execução da tua habilidade. Se não definiu explicitamente um `timeout` valor na sua `timeout` definição de habilidade personalizada, então o padrão é de 30 segundos. Se 30 segundos não forem suficientes para a `timeout` sua habilidade executar, poderá especificar um valor mais elevado na sua definição de habilidade personalizada. Aqui está um exemplo de uma definição de habilidade personalizada onde o tempo de tempo é definido para 90 segundos:

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

O valor máximo que pode `timeout` definir para o parâmetro é de 230 segundos.  Se a sua habilidade personalizada não for capaz de executar de forma `batchSize` consistente dentro de 230 segundos, poderá considerar reduzir a sua habilidade personalizada para que tenha menos documentos para processar dentro de uma única execução.  Se já definiu `batchSize` o seu para 1, terá de reescrever a habilidade para poder executar em menos de 230 segundos ou dividi-la em múltiplas habilidades personalizadas para que o tempo de execução de qualquer habilidade personalizada seja no máximo 230 segundos. Reveja a [documentação de habilidades personalizadas](cognitive-search-custom-skill-web-api.md) para mais informações.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Erro: Não`MergeOrUpload`podia ' '`Delete`' documento para o índice de pesquisa

O documento foi lido e processado, mas o indexante não conseguiu adicioná-lo ao índice de pesquisa. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Um campo contém um termo que é muito grande | Um termo no seu documento é maior que o limite de [32 KB](search-limits-quotas-capacity.md#api-request-limits) | Pode evitar esta restrição garantindo que o campo não está configurado como filtravel, facetável ou classificado.
| O documento é demasiado grande para ser indexado | Um documento é maior do que o tamanho máximo do [pedido api](search-limits-quotas-capacity.md#api-request-limits) | [Como indexar grandes conjuntos de dados](search-howto-large-index.md)
| Documento contém demasiados objetos na coleção | Uma coleção no seu documento excede os [elementos máximos em todas as coleções complexas limite](search-limits-quotas-capacity.md#index-limits) "O documento com chave `'1000052'` tem `'4303'` objetos em coleções (matrizes JSON). Na `'3000'` maioria dos objetos são permitidos estar em coleções em todo o documento. Por favor, remova os objetos das coleções e tente indexar o documento novamente." | Recomendamos reduzir o tamanho da coleção complexa no documento para abaixo do limite e evitar uma utilização elevada de armazenamento.
| Problemas de ligação ao índice-alvo (que persiste após repetições) porque o serviço está sob outra carga, como consulta ou indexação. | Não conseguiu estabelecer a ligação ao índice de atualização. O serviço de busca está sob carga pesada. | [Aumentar o seu serviço de pesquisa](search-capacity-planning.md)
| O serviço de pesquisa está a ser corrigido para atualização de serviço, ou está no meio de uma reconfiguração de topologia. | Não conseguiu estabelecer a ligação ao índice de atualização. O serviço de pesquisa está atualmente em baixa/O serviço de pesquisa está em transição. | Configure o serviço com pelo menos 3 réplicas para 99,9% de disponibilidade por [documentação SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Falha no recurso de computação/rede subjacente (raro) | Não conseguiu estabelecer a ligação ao índice de atualização. Ocorreu uma falha desconhecida. | Configure os indexantes para [executar em um horário](search-howto-schedule-indexers.md) para pegar em um estado falhado.
| Um pedido de indexação feito ao índice-alvo não foi reconhecido dentro de um período de tempo limite devido a problemas de rede. | Não foi possível estabelecer a ligação ao índice de pesquisa atempadamente. | Configure os indexantes para [executar em um horário](search-howto-schedule-indexers.md) para pegar em um estado falhado. Além disso, tente baixar o tamanho do [lote](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) do indexante se esta condição de erro persistir.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Erro: Não podia indexar documento porque alguns dos dados do documento não eram válidos

O documento foi lido e tratado pelo indexante, mas devido a uma incompatibilidade na configuração dos campos indexados e dos dados extraídos e tratados pelo indexante, não foi possível adicionar ao índice de pesquisa. Isto pode acontecer devido a:

| Razão | Detalhes/Exemplo
| --- | ---
| O tipo de dados do campo(s) extraído pelo indexante é incompatível com o modelo de dados do campo de índice-alvo correspondente. | Os_dados_do campo de dados ' no documento com a chave '888' têm um valor inválido 'de tipo 'Edm.String'. O tipo esperado era 'Collection(Edm.String)'. |
| Não conseguiu extrair qualquer entidade JSON de um valor de cadeia. | Não podia analisar o valor 'do tipo 'Edm.String'' dos_dados_do campo como um objeto JSON. Erro:'Depois de analisar um valor foi encontrado um personagem inesperado: ''. Caminho '_caminho_', linha 1, posição 3162.' |
| Não conseguiu extrair uma coleção de entidades JSON de um valor de cadeia.  | Não podia analisar o valor 'do tipo 'Edm.String'' dos_dados_do campo como uma matriz JSON. Erro:'Depois de analisar um valor foi encontrado um personagem inesperado: ''. Caminho '[0]', linha 1, posição 27. |
| Um tipo desconhecido foi descoberto no documento de origem. | O tipo desconhecido '_desconhecido_' não pode ser indexado |
| No documento de origem foi utilizada uma notação incompatível para pontos de geografia. | Os literais de cordas WKT POINT não são suportados. Por favor, use geoJson ponto literals em vez |

Em todos estes casos, consulte os tipos de [dados suportados](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) e o mapa do [tipo de dados para os indexadores](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) para se certificar de que você constrói o esquema de índice corretamente e criou [mapeamentos de campo indexantes apropriados](search-indexer-field-mappings.md). A mensagem de erro incluirá detalhes que podem ajudar a localizar a origem do desfasamento.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Erro: A política integrada de rastreio de alterações não pode ser utilizada porque a tabela tem uma chave primária composta

Isto aplica-se às tabelas SQL, e geralmente acontece quando a chave é definida como uma chave composta ou, quando a tabela definiu um índice clustered único (como num índice SQL, e não um índice de Pesquisa Azure). A razão principal é que o atributo-chave é modificado para ser uma chave primária composta no caso de um [índice agrupado único](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15). Nesse caso, certifique-se de que a sua tabela SQL não tem um índice agrupado único, ou que mapeie o campo-chave para um campo que é garantido não ter valores duplicados.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Erro: Não conseguiu processar o documento dentro do tempo de execução máximo do indexante

Este erro ocorre quando o indexante não consegue terminar o processamento de um único documento a partir da fonte de dados dentro do tempo de execução permitido. [O tempo máximo](search-limits-quotas-capacity.md#indexer-limits) de funcionamento é mais curto quando são utilizadas habilidades. Quando este erro ocorre, se tiver maxFailedItems definidos para um valor diferente de 0, o indexante contorna o documento em futuras execuções para que a indexação possa progredir. Se não se pode dar ao luxo de ignorar qualquer documento, ou se estiver a ver este erro de forma consistente, considere quebrar documentos em documentos menores para que o progresso parcial possa ser feito dentro de uma execução de um único indexador.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Erro: Não podia projetar documento

Este erro ocorre quando o indexante está a tentar [projetar dados numa loja](knowledge-store-projection-overview.md) de conhecimento e houve uma falha na nossa tentativa de o fazer.  Esta falha pode ser consistente e reparável ou pode ser uma falha transitória com o afundor de saída de projeção que poderá ter de esperar e retentar para resolver.  Aqui estão um conjunto de estados de fracasso conhecidos e possíveis resoluções.

| Razão | Detalhes/Exemplo | Resolução |
| --- | --- | --- |
| Não conseguiatualizar a `'blobUri'` bolha de projeção em recipiente`'containerName'` |O recipiente especificado não existe. | O indexante verificará se o recipiente especificado foi previamente criado e irá criá-lo se necessário, mas só executa este controlo uma vez por execução do indexador. Este erro significa que algo apagou o recipiente após este passo.  Para resolver este erro, tente isto: deixe as informações da sua conta de armazenamento em paz, aguarde que o indexante termine e, em seguida, reecorra o indexante. |
| Não conseguiatualizar a `'blobUri'` bolha de projeção em recipiente`'containerName'` |Incapaz de escrever dados para a ligação de transporte: Uma ligação existente foi fechada à força pelo hospedeiro remoto. | Espera-se que esta seja uma falha transitória com o Armazenamento Azure e, portanto, deve ser resolvida refuncionando o indexante. Se encontrar este erro de forma consistente, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possa ser investigado mais aprofundadamente.  |
| Não poderia `'projectionRow'` atualizar linha na tabela`'tableName'` | O servidor está ocupado. | Espera-se que esta seja uma falha transitória com o Armazenamento Azure e, portanto, deve ser resolvida refuncionando o indexante. Se encontrar este erro de forma consistente, por favor preencha um bilhete de [apoio](https://ms.portal.azure.com/#create/Microsoft.Support) para que possa ser investigado mais aprofundadamente.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Aviso: A entrada de habilidade supunha inválida
Faltava uma entrada para a habilidade, o tipo errado, ou de outra forma inválida. A mensagem de aviso indicará o impacto:
1) Não podia executar habilidade
2) Habilidade executada mas pode ter resultados inesperados

As capacidades cognitivas exigiram inputs e inputs opcionais. Por exemplo, a habilidade de [extração](cognitive-search-skill-keyphrases.md) `text`da `languageCode`frase chave tem duas inputs necessárias, e nenhuma entrada opcional. As inputs de habilidade personalizadasão consideradas inputs opcionais.

Se faltarem as inputs necessárias ou se alguma entrada não for do tipo certo, a habilidade é ignorada e gera um aviso. As habilidades ignoradas não geram saídas, por isso, se outras habilidades utilizarem saídas da habilidade ignorada, podem gerar avisos adicionais.

Se faltar uma entrada opcional, a habilidade continuará a funcionar, mas poderá produzir uma saída inesperada devido à entrada em falta.

Em ambos os casos, este aviso pode ser esperado devido à forma dos seus dados. Por exemplo, se tiver um documento que `firstName`contenha informações sobre pessoas com os campos, `middleName`e, `lastName`pode ter alguns documentos que não têm entrada para `middleName`. Se passar `middleName` como uma entrada para uma habilidade no oleoduto, então espera-se que esta entrada de habilidade possa estar a perder parte do tempo. Terá de avaliar os seus dados e cenário para determinar se é ou não necessária qualquer ação em resultado deste aviso.

Se pretender fornecer um valor predefinido em caso de falta de entrada, pode utilizar a [habilidade Condicional](cognitive-search-skill-conditional.md) para gerar um valor predefinido e, em seguida, utilizar a saída da [habilidade Condicional](cognitive-search-skill-conditional.md) como entrada de habilidade.


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
| A entrada de habilidade é o tipo errado | "A entrada necessária de competências `String`não era do tipo esperado . Nome: `text`, `/document/merged_content`Fonte: ."  "A entrada necessária de competências não era do formato esperado. Nome: `text`, `/document/merged_content`Fonte: ."  "Não pode iterar `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`sobre a não-matriz."  "Incapaz de `0` selecionar em `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`não-matriz" | Certas habilidades esperam inputs de tipos `text` específicos, por [exemplo,](cognitive-search-skill-sentiment.md) a habilidade de sentimento espera ser uma corda. Se a entrada especificar um valor não-string, então a habilidade não executa e não gera saídas. Certifique-se de que o seu conjunto de dados tem valores de entrada uniformes em tipo ou utilize uma [habilidade API Web personalizada](cognitive-search-custom-skill-web-api.md) para pré-processar a entrada. Se está a iterar a habilidade sobre uma matriz, `*` verifique o contexto de habilidade e a entrada têm nas posições corretas. Normalmente, tanto o contexto como `*` a fonte de entrada devem terminar com as matrizes. |
| Falta a entrada de habilidades | "Falta uma entrada de habilidade necessária. Nome: `text`, `/document/merged_content`Fonte: " `/document/normalized_images/0/imageTags`Valor em falta."  "Incapaz de `0` selecionar `/document/pages` em `0`conjunto de comprimento." | Se todos os seus documentos recebem este aviso, o mais provável é que haja uma tipografia nos caminhos de entrada e verifique duas vezes o invólucro do nome da propriedade, extra ou em falta `*` no caminho, e certifique-se de que os documentos da fonte de dados fornecem as entradas necessárias. |
| A entrada do código de linguagem de habilidade é inválida | A entrada `languageCode` de habilidades `X,Y,Z`tem os seguintes códigos linguísticos, pelo menos um dos quais é inválido. | Veja mais detalhes [abaixo](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Aviso: A inserção de habilidade 'languageCode' tem os seguintes códigos linguísticos 'X,Y,Z', pelo menos um dos quais é inválido.
Um ou mais dos valores `languageCode` passados para a entrada opcional de uma habilidade a jusante não são suportados. Isto pode ocorrer se estiver a passar a saída do [IdiomDetectionSkill](cognitive-search-skill-language-detection.md) para competências subsequentes, e a saída consiste em mais idiomas do que são suportados nessas habilidades a jusante.

Se souber que o seu conjunto de dados está num só `languageCode` idioma, deve `defaultLanguageCode` remover o [IdiomDetectionSkill](cognitive-search-skill-language-detection.md) e a entrada de habilidades e utilizar o parâmetro de habilidade para essa habilidade, assumindo que a linguagem é suportada para essa habilidade.

Se souber que o seu conjunto de dados contém vários `languageCode` idiomas e, portanto, precisa do [IdiomDetectionSkill](cognitive-search-skill-language-detection.md) e da entrada, considere adicionar um [ConditionalSkill](cognitive-search-skill-conditional.md) para filtrar o texto com idiomas que não são suportados antes de passar o texto para a habilidade a jusante.  Aqui está um exemplo do que isto pode parecer para o EntityRecognitionSkill:

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

Aqui ficam algumas referências para as línguas atualmente suportadas para cada uma das competências que podem produzir esta mensagem de erro:
* [Idiomas suportados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) por Análise de Texto (para a [KeyPhraseExtractionSkill,](cognitive-search-skill-keyphrases.md) [EntityRecognitionSkill,](cognitive-search-skill-entity-recognition.md) [SentimentSkill](cognitive-search-skill-sentiment.md), e [PIIDetectionSkill)](cognitive-search-skill-pii-detection.md)
* [Línguas Apoiadas por Tradutores](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (para a [Capacidade de Tradução](cognitive-search-skill-text-translation.md)de Texto)
* [Text SplitSkill](cognitive-search-skill-textsplit.md) Línguas suportadas:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Aviso: A entrada de habilidade foi truncada
As habilidades cognitivas têm limites ao comprimento do texto que pode ser analisado ao mesmo tempo. Se a entrada de texto destas competências for superior a esse limite, truncaremos o texto para cumprir o limite e, em seguida, realizaremos o enriquecimento nesse texto truncado. Isto significa que a habilidade é executada, mas não sobre todos os seus dados.

No exemplo , LanguageDetectionSkill `'text'` abaixo, o campo de entrada pode desencadear este aviso se estiver acima do limite de caracteres. Pode encontrar os limites de entrada de competências na documentação de [competências.](cognitive-search-predefined-skills.md)

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

Se quiser garantir que todo o texto é analisado, considere utilizar a [habilidade Split](cognitive-search-skill-textsplit.md).

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Aviso: A resposta à habilidade da Web API contém avisos
O Indexer foi capaz de executar uma habilidade no skillset, mas a resposta do pedido da API web indicou que havia avisos durante a execução. Reveja os avisos para entender como os seus dados são impactados e se é ou não necessária uma ação.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Aviso: A configuração atual do indexante não suporta o progresso incremental

Este aviso só ocorre para fontes de dados da Cosmos DB.

O progresso incremental durante a indexação garante que, se a execução do indexante for interrompida por falhas transitórias ou limite de tempo de execução, o indexante pode retomar onde deixou de funcionar da próxima vez que funcionar, em vez de ter de reindexar toda a coleção do zero. Isto é especialmente importante quando se indexam grandes coleções.

A capacidade de retomar um trabalho de indexação inacabado `_ts` baseia-se em ter documentos encomendados pela coluna. O indexante usa a marca de tempo para determinar qual o documento a recolher a seguir. Se `_ts` a coluna estiver em falta ou se o indexante não conseguir determinar se uma consulta personalizada é ordenada por ela, o indexante começa a começar e verá este aviso.

É possível anular este comportamento, permitindo o progresso incremental `assumeOrderByHighWatermarkColumn` e suprimindo este aviso utilizando a propriedade de configuração.

Para mais informações, consulte [O progresso incremental e as consultas personalizadas.](search-howto-index-cosmosdb.md#IncrementalProgress)

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Aviso: Alguns dados foram perdidos durante a projeção. Linha 'X' na tabela 'Y' tem propriedade de cordas 'Z' que era muito longa.

O [serviço de Armazenamento](https://azure.microsoft.com/services/storage/tables) de Mesa tem limites para a grande dimensão das propriedades da [entidade.](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) As cordas podem ter 32.000 caracteres ou menos. Se uma linha com uma propriedade de cordas superior a 32.000 caracteres está sendo projetada, apenas os primeiros 32.000 caracteres são preservados. Para contornar esta questão, evite projetar filas com propriedades de cordas com mais de 32.000 caracteres.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Aviso: Texto extraído truncado para caracteres X
Os indexadores limitam a quantidade de texto que pode ser extraído de qualquer documento. Este limite depende do nível de preços: 32.000 caracteres para o free tier, 64.000 para Basic, 4 milhões para standard, 8 milhões para standard S2 e 16 milhões para standard S3. O texto truncado não será indexado. Para evitar este aviso, tente separar documentos com grandes quantidades de texto em documentos múltiplos e menores. 

Para obter mais informações, consulte [os limites do Indexer](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Aviso: Não podia mapear o campo de saída 'X' para o índice de pesquisa
Os mapeamentos do campo de saída que referem dados inexistentes/nulos produzirão avisos para cada documento e resultarão num campo de índice vazio. Para contornar este problema, verifique duas vezes os seus caminhos de origem de mapeamento de campo de saída para eventuais tipografias ou detetete um valor predefinido utilizando a [habilidade Condicional](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist).

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Aviso: A política de deteção de alterações de dados está configurada para utilizar a coluna chave 'X'
As políticas de deteção de [alterações de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) têm requisitos específicos para as colunas que utilizam para detetar alterações. Um destes requisitos é que esta coluna seja atualizada sempre que o item de origem é alterado. Outra exigência é que o novo valor para esta coluna seja maior do que o valor anterior. As colunas-chave não cumprem este requisito porque não mudam em todas as atualizações. Para contornar este problema, selecione uma coluna diferente para a política de deteção de alterações.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Aviso: O texto do documento parece estar codificado uTF-16, mas falta-lhe uma marca de encomenda byte

Os modos de [análise do indexante](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) precisam de saber como o texto é codificado antes de o analisar. As duas formas mais comuns de codificar texto são UTF-16 e UTF-8. UTF-8 é uma codificação de comprimento variável onde cada personagem tem entre 1 byte e 4 bytes de comprimento. UTF-16 é uma codificação de comprimento fixo onde cada personagem tem 2 bytes de comprimento. A UTF-16 tem duas variantes diferentes, "grande endian" e "pequeno endian". A codificação de texto é determinada por uma "marca de byte order", uma série de bytes antes do texto.

| Codificação | Marca de ordem byte |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 Pequeno Endiano | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Se não houver marca de encomenda byte, presume-se que o texto está codificado como UTF-8.

Para contornar este aviso, determine qual é a codificação de texto para esta bolha e adicione a marca de byte apropriada.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Aviso: A coleção Cosmos DB 'X' tem uma política de indexação preguiçosa. Alguns dados podem ser perdidos

As coleções com políticas de indexação [preguiçosas](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) não podem ser consultadas de forma consistente, resultando na falta de dados do seu indexante. Para contornar este aviso, altere a sua política de indexação para Consistente.
