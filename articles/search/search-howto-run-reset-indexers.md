---
title: Executar ou repor indexantes
titleSuffix: Azure Cognitive Search
description: Redefinir um indexante, habilidades ou documentos individuais para atualizar a totalidade ou parte e indexar ou loja de conhecimento.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 06f9d336bb04aa26cea4ebcdae1cfd045e1c2a1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361252"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Como executar ou repor indexantes, competências ou documentos

A execução do indexante pode ocorrer quando cria o [indexador](search-indexer-overview.md)pela primeira vez, ao executar um indexante a pedido, ou ao definir um indexante num horário. Após a execução inicial, um indexante mantém o registo dos documentos de pesquisa que foram indexados através de uma "marca de água alta" interna. O marcador nunca é exposto na API, mas internamente o indexante sabe onde a indexação parou para que possa retomar onde parou na próxima corrida.

Pode limpar a marca de água elevada repondo o indexante se quiser reprocessar do zero. As APIs de reset estão disponíveis em níveis decrescentes na hierarquia do objeto:

+ Todo o corpus de pesquisa (utilizar [Indexadores de Reset)](#reset-indexers)
+ Um documento específico ou lista de documentos (utilizar [Documentos de Reset - pré-visualização)](#reset-docs)
+ Uma habilidade ou enriquecimento específico num documento (use [Reset Skills - pré-visualização)](#reset-skills)

As APIs de reset são usadas para atualizar o conteúdo em cache (aplicável em cenários [de enriquecimento de IA),](cognitive-search-concept-intro.md) ou para limpar a marca de água alta e reconstruir o índice.

O reset, seguido de execução, pode reprocessar documentos existentes e novos documentos, mas não remove documentos de pesquisa órfãos no índice de pesquisa que foram criados em execuções anteriores. Para obter mais informações sobre a eliminação, consulte [Adicionar, Atualizar ou Eliminar Documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Indexadores de execução

[Criar Indexer](/rest/api/searchservice/create-indexer) cria e executa o indexante a menos que o crie num estado desativado ("desativado": verdadeiro). A primeira corrida demora um pouco mais porque a sua criação de objetos de cobertura também.

[O indexante run](/rest/api/searchservice/run-indexer) detetará e processará apenas o necessário para sincronizar o índice de pesquisa com a fonte de dados. O armazenamento de blob tem deteção de alterações incorporadas. Outras fontes de dados, como a Azure SQL ou a Cosmos DB, têm de ser configuradas para deteção de alterações antes que o indexante possa ler apenas as novas e atualizadas linhas.

Pode executar um indexador utilizando qualquer uma destas abordagens:

+ Portal Azure, utilizando o comando **Run** na página indexante
+ [Indexante de Execução (REST)](/rest/api/searchservice/run-indexer)
+ [Método RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) no Azure .NET SDK (ou utilizando o método RunIndexer equivalente em outro SDK)

A execução do indexante está sujeita aos seguintes limites:

+ O número máximo de postos de trabalho indexantes é 1 por réplica Sem empregos simultâneos.

  Se a execução do indexante já estiver na capacidade, receberá esta notificação: "Não executou indexante '<indexante-nome>', erro: "Outra invocação indexante está em curso; invocações simultâneas não são permitidas.

+ O tempo máximo de funcionamento é de 2 horas se utilizar um skillset, e 24 horas sem. 

  Pode esticar o processamento colocando o indexante num horário. O nível Livre tem prazos de execução mais baixos. Para a lista completa, consulte [os limites do indexante](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Redefinir um indexador

A reposição de um indexante é abrangente. Dentro do índice de pesquisa, qualquer documento de pesquisa que tenha sido originalmente povoado pelo indexante está marcado para o processamento completo. Quaisquer novos documentos encontrados a fonte subjacente serão adicionados ao índice como documentos de pesquisa. Se o indexante estiver configurado para utilizar um skillset e [cache,](search-howto-incremental-index.md)o skillset é reformulado e a cache é renovada.

Pode redefinir um indexador utilizando qualquer uma destas abordagens, seguido de uma execução de indexante utilizando um dos métodos acima discutidos.

+ Portal Azure, utilizando o comando **Reset** na página do indexante
+ [Indexante de Reset (REST)](/rest/api/searchservice/reset-indexer)
+ [Método ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) no Azure .NET SDK (ou utilizando o método RunIndexer equivalente em outro SDK)

Uma bandeira de reset é desmarcada após o fim da corrida. Qualquer lógica regular de deteção de alterações que esteja operacional para a sua fonte de dados será retomada na próxima execução, recolhendo quaisquer outros valores novos ou atualizados no resto do conjunto de dados.

> [!NOTE]
> Um pedido de reset determina o que é reprocessado (indexante, habilidade ou documento), mas não afeta de outra forma o comportamento do indexante. Se o indexante tiver parâmetros de tempo, mapeamentos de campo, opções de caching, opções de lote, etc., essas definições estão todas em vigor quando se executa um indexante depois de o ter reposto.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Capacidades de reposição (pré-visualização)

> [!IMPORTANT] 
> [O Reset Skills](/rest/api/searchservice/preview-api/reset-skills) está em pré-visualização pública, disponível apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Para os indexantes que têm habilidades, pode redefinir habilidades específicas para forçar o processamento dessa habilidade e quaisquer habilidades a jusante que dependam da sua produção. [Os enriquecimentos em cache](search-howto-incremental-index.md) também são refrescados. As competências de reposição invalidam os resultados da habilidade em cache, o que é útil quando uma nova versão de uma habilidade é implementada e você quer que o indexante reexame essa habilidade para todos os documentos. 

[As Capacidades de Reset](/rest/api/searchservice/preview-api/reset-skills) estão disponíveis através do REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Pode especificar competências individuais, como indicado no exemplo acima, mas se alguma dessas habilidades necessitar de produção de competências não cotadas (#2 até #4), as competências não cotadas serão executadas a menos que a cache possa fornecer as informações necessárias. Para que isto seja verdade, os enriquecimentos em cache para competências #2 através de #4 não devem ter dependência de #1 (listados para reset).

Se não forem especificadas competências, toda a habilidade é executada e se o cache estiver ativado, a cache também é renovada.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Repor os docs (pré-visualização)

> [!IMPORTANT] 
> [Os Documentos de Reset](/rest/api/searchservice/preview-api/reset-documents) estão em pré-visualização pública, disponíveis apenas através da pré-visualização REST API. As funcionalidades de pré-visualização são oferecidas como está, nos [Termos Complementares de Utilização.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

A [API de documentos reset](https://docs.microsoft.com/rest/api/searchservice/preview-api/reset-documents) aceita uma lista de chaves de documento para que possa atualizar documentos específicos. Se especificados, os parâmetros de reset tornam-se o único determinante do que é processado, independentemente de outras alterações nos dados subjacentes. Por exemplo, se 20 blobs foram adicionados ou atualizados desde a última execução do indexante, mas apenas repôs um documento, apenas um documento será processado.

Numa base documental, todos os campos nesse documento de pesquisa são atualizados com valores da fonte de dados. Não pode escolher quais os campos para refrescar. 

Se o documento for enriquecido através de um skillset e tiver dados em cache, o skillset é invocado apenas para os documentos especificados, e o cached é atualizado para os documentos reprocessados.

Ao testar esta API pela primeira vez, as seguintes APIs irão ajudá-lo a validar e testar os comportamentos:

+ [Obtenha o Estado do Indexer](/rest/api/searchservice/get-indexer-status) com a versão API, `2020-06-30-Preview` para verificar o estado de reposição e o estado de execução. Pode encontrar informações sobre o pedido de reset no final da resposta ao estado.
+ [Redefinir documentos](/rest/api/searchservice/preview-api/reset-documents) com a versão `2020-06-30-Preview` API, para especificar quais os documentos a processar.
+ [Executar Indexer](/rest/api/searchservice/run-indexer) para executar o indexante (qualquer versão API).
+ [Procurar Documentos](/rest/api/searchservice/search-documents) para verificar se há valores atualizados e também para devolver as chaves do documento se não tiver a certeza do valor. Utilize `"select": "<field names>"` se quiser limitar quais os campos que aparecem na resposta.

### <a name="formulate-and-send-the-reset-request"></a>Formular e enviar o pedido de reset

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

As chaves de documento fornecidas no pedido são valores do índice de pesquisa, que podem ser diferentes dos campos correspondentes na fonte de dados. Se não tiver a certeza do valor da chave, [envie uma consulta](search-query-create.md) para devolver o valor. Pode utilizar `select` para devolver apenas o campo de chaves de documento.

Para as bolhas que são analisadas em vários documentos de pesquisa (por exemplo, se usou [jsonLines ou jsonArrays](search-howto-index-json-blobs.md), [outexto delimitado](search-howto-index-csv-blobs.md)) como um modo de análise, a chave de documento é gerada pelo indexante e pode ser desconhecida para si. Nesta situação, uma consulta para a chave do documento será determinante para fornecer o valor correto.

Ligar várias vezes para a API com teclas diferentes anexa as novas chaves da lista de chaves de documentos reiniciadas. Ligar para a API com o **`overwrite`** parâmetro definido para verdadeiro substituirá a lista atual de chaves de documento a reiniciar com a carga útil do pedido:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Verifique o estado do reset

Para verificar o estado de um reset e para ver quais as chaves do documento que estão na fila para o processamento, utilize [o Estado do Indexante](/rest/api/searchservice/get-indexer-status) com **`api-version=06-30-2020-Preview`** . A API de pré-visualização devolverá a **`currentState`** secção, que pode encontrar no final da resposta do Estado do Indexante.

O "modo" será **`indexingAllDocs`** para Capacidades de Reset (porque potencialmente todos os documentos são afetados, para os campos que são povoados através do enriquecimento de IA).

Para documentos de reset, o modo está definido para **`indexingResetDocs`** . O indexante mantém este estatuto até que todas as chaves de documento fornecidas na chamada de documentos de reset sejam processadas e nenhum outro emprego indexante será executado enquanto a operação está a progredir. Encontrar todos os documentos na lista de chaves do documento requer decifrar cada documento para localizar e corresponder na chave, o que pode demorar algum tempo se o conjunto de dados for grande. Se um recipiente de bolhas contiver centenas de bolhas, e os documentos que pretende repor estão no final, o indexante não encontrará as bolhas correspondentes até que todas as outras tenham sido verificadas primeiro.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Após o reprocessamento dos documentos, o indexante volta ao **`indexingAllDocs`** modo e processará quaisquer outros documentos novos ou atualizados na próxima execução.

## <a name="next-steps"></a>Passos seguintes

As APIs de reset são utilizadas para informar o âmbito da próxima execução do indexante. Para o processamento real, você precisará invocar uma corrida de indexante a pedido ou permitir um trabalho programado para completar o trabalho. Após o funcionamento, o indexante volta ao processamento normal, seja num horário ou num processamento a pedido.

Depois de reiniciar e reenexerir os trabalhos indexantes, pode monitorizar o estado do serviço de pesquisa ou obter informações detalhadas através de registos de diagnóstico.

+ [Operações indexantes (REST)](/rest/api/searchservice/indexer-operations)
+ [Monitorizar o estado do indexante de pesquisa](search-howto-monitor-indexers.md)
+ [Recolher e analisar dados de registo](search-monitor-logs.md)
+ [Agende um indexador](search-howto-schedule-indexers.md)