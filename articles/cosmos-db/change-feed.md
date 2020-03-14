---
title: Trabalhar com a alteração de suporte de feed no Azure Cosmos DB
description: Use o suporte de alimentação de alterações da Azure Cosmos DB para rastrear alterações nos documentos, processamento baseado em eventos como gatilhos, e manter caches e sistemas analíticos atualizados
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368133"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Change feed in Azure Cosmos DB - visão geral

O suporte do feed de alterações no Azure Cosmos DB funciona através da escuta de alterações num contentor do Azure Cosmos. Em seguida, disponibiliza a lista ordenada dos documentos que foram alterados, pela ordem pela qual foram modificados. As alterações são preservadas, podem ser processadas de forma assíncrona e incremental e o resultado pode ser distribuído por um ou mais consumidores para processamento paralelo. 

O Azure Cosmos DB é bem adequado para IoT, varejo, jogos e aplicações de Registro em log operacional. Um padrão de design comum nesses aplicativos é usar as alterações dos dados para acionar ações adicionais. Exemplos de ações adicionais incluem:

* Acionar uma notificação ou uma chamada para uma API, quando um item é inserido ou atualizado.
* Processamento de IoT ou processamento nos dados operacionais de análise em tempo real em fluxo em tempo real.
* Movimento de dados adicionais ao sincronizar com um cache ou de um mecanismo de pesquisa ou de um armazém de dados ou arquivamento de dados no armazenamento amovível.

A alteração de feed no Azure Cosmos DB permite-lhe criar soluções eficientes e dimensionáveis para cada um desses padrões, conforme mostrado na imagem seguinte:

![Usar as alterações do Azure Cosmos DB feed para análise em tempo real de energia e cenários de computação orientada para eventos](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>APIs e SDKs de cliente suportados

Esta funcionalidade é atualmente suportada pelas seguintes APIs do Azure Cosmos DB e SDKs do cliente.

| **Motoristas de clientes** | **CLI do Azure** | **SQL API** | **API da Azure Cosmos DB para Cassandra** | **API da Azure Cosmos DB para o MongoDB** | **Gremlin API**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | ND | Sim | Sim | Sim | Sim | Não |
|Java|ND|Sim|Sim|Sim|Sim|Não|
|Python|ND|Sim|Sim|Sim|Sim|Não|
|JS/nó|ND|Sim|Sim|Sim|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Feed de alterações e operações diferentes

Hoje em dia, verá todas as operações no feed de alterações. A funcionalidade de onde pode controlar alterar feed, para operações específicas, como apenas atualizações e inserções não ainda não está disponível. Pode adicionar um "marcador suave" no item para atualizações e filtro com base nisso ao processar itens no feed de alteração. Atualmente, a alteração do feed não regista apaga-se. Semelhante ao exemplo anterior, pode adicionar um marcador de forma recuperável nos itens que estão a ser eliminados, por exemplo, pode adicionar um atributo no item chamado "eliminado" e defini-lo como "true" e definir um valor de TTL no item, para que sejam eliminado automaticamente. Pode ler o feed de mudança para itens históricos (a alteração mais recente correspondente ao item, não inclui as alterações intermédias), por exemplo, itens que foram adicionados há cinco anos. Se o item não for eliminado pode ler a alteração do feed com relação às General a origem do seu contentor.

### <a name="sort-order-of-items-in-change-feed"></a>A ordem dos itens no feed de alterações de classificação

Itens de feed de alterações são fornecidos por ordem de seu tempo de modificação. Esta ordem de classificação é garantida por chave de partição lógica.

### <a name="consistency-level"></a>Nível de consistência

Ao consumir o feed de mudança num nível de consistência eventual, pode haver duplicados eventos entre as operações subsequentes de leitura de feed de mudança (o último evento de uma operação de leitura aparece como o primeiro do próximo).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed de alterações em várias regiões do Azure Cosmos contas

Uma conta do Cosmos do Azure de várias regiões, se uma região de escrita falhar, feed de alterações irão funcionar em toda a operação de ativação pós-falha manual e vai ser contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed de alterações e a hora Live (TTL)

Se uma propriedade de valor de TTL (Time TTL) é definida num item como -1, o feed de alterações serão mantidos para sempre. Se os dados não for eliminados, irá permanecer no feed de alterações.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed de alterações e _etag, _lsn ou TS

O formato de _etag é interno e não deve demorar dependência, porque ele pode mudar em qualquer altura. TS é uma modificação ou um carimbo de criação. Pode usar TS para comparação cronológica. _lsn é um id de lote que é adicionado apenas para a mudança de alimentos; representa o ID de transação. Número de itens pode ter o mesmo _lsn. ETag no FeedResponse é diferente do _etag que vir no item. _etag é um identificador interno e é utilizado para a simultaneidade informa ao controle sobre a versão do item, ao passo que o ETag é utilizado para o feed de sequenciamento.

## <a name="change-feed-use-cases-and-scenarios"></a>Cenários e casos de utilização de feed de alterações

Permite um processamento de grandes conjuntos de dados com um grande volume de escritas eficiente de feed de alterações. Feed de alterações também oferece uma alternativa ao consultar o conjunto de dados inteiro para identificar o que foi alterado.

### <a name="use-cases"></a>Casos de utilização

Por exemplo, com o feed de alterações pode efetuar as seguintes tarefas com eficiência:

* Atualizar uma cache, um índice de pesquisa de atualização ou atualizar um armazém de dados com dados armazenados no Azure Cosmos DB.

* Implementar um nível de dados de nível de aplicação e arquivo, por exemplo, armazenar "dados quentes" em Azure Cosmos DB e eliminar "dados frios" para outros sistemas de armazenamento, por exemplo, [armazenamento De Blob Azure.](../storage/common/storage-introduction.md)

* Execute zero migrações de período de indisponibilidade para outra conta do Cosmos do Azure ou outro contentor do Cosmos do Azure com uma chave de partição lógica diferente.

* Implementar [arquitetura lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) usando Azure Cosmos DB, onde o Azure Cosmos DB suporta tanto em tempo real, lote e consulta servindo camadas, permitindo assim a arquitetura lambda com tCO baixo.

* Receba e guarde dados de eventos de dispositivos, sensores, infraestruturas e aplicações, e processe estes eventos em tempo real, por exemplo, usando [a Spark](../hdinsight/spark/apache-spark-overview.md).  A imagem seguinte mostra como pode implementar com o Azure Cosmos DB através de feed de alterações de arquitetura de lambda:

![Pipeline de lambda com base no Cosmos DB do Azure para ingestão e consulta](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Cenários

Seguem-se alguns dos cenários, que pode facilmente implementar com o feed de alterações:

* Dentro das suas aplicações web ou móveis [sem servidor,](https://azure.microsoft.com/solutions/serverless/) pode acompanhar eventos como todas as alterações ao perfil, preferências do seu cliente ou à sua localização e desencadear determinadas ações, por exemplo, enviar notificações push para os seus dispositivos utilizando [funções Azure](change-feed-functions.md).

* Se estiver a utilizar o Azure Cosmos DB para criar um jogo, pode, por exemplo, utilize alterar feed para implementar as classificações em tempo real com base em classificações de jogos concluídos.


## <a name="working-with-change-feed"></a>Trabalhar com o feed de alterações

Pode trabalhar com o feed de alterações com as seguintes opções:

* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Utilização de feed de mudança com processador de feed de mudança](change-feed-processor.md) 

Feed de alterações está disponível para cada chave de partição lógica dentro do contêiner, e podem ser distribuído por um ou mais consumidores para processamento paralelo, conforme mostrado na imagem abaixo.

![Processamento distribuído de feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* Feed de alterações está ativado por predefinição para todas as contas do Cosmos do Azure.

* Pode utilizar a sua [entrada aprovisionada](request-units.md) para ler a partir do feed de mudanças, como qualquer outra operação da Azure Cosmos DB, em qualquer uma das regiões associadas à sua base de dados Azure Cosmos.

* O feed de alterações inclui inserções e operações de atualização efetuadas aos itens dentro do contentor. Pode capturar eliminações ao definir um sinalizador de "eliminação de forma recuperável" dentro de seus itens (por exemplo, documentos) em vez de eliminações. Em alternativa, pode definir um período de validade finito para os seus itens com a [capacidade TTL](time-to-live.md). Por exemplo, 24 horas e utilização elimina o valor dessa propriedade para capturar. Com esta solução, deve processar as alterações dentro de um intervalo de tempo mais curto do que o período de expiração do TTL. 

* Cada alteração a um item exatamente uma vez aparece no feed de alterações e os clientes têm de gerir a lógica de ponto de verificação. Se pretender evitar a complexidade da gestão dos pontos de verificação, o processador de alimentação de mudanças fornece um checkpoint automático e uma semântica "pelo menos uma vez". Consulte [a utilização do feed de mudança com o processador de alimentação de mudanças](change-feed-processor.md).

* A alteração mais recente para um determinado item está incluída no log de alterações. Alterações intermédias poderão não estar disponíveis.

* O feed de alterações é classificado a ordem dos modificação dentro de cada valor de chave de partição lógica. Não existe nenhuma ordem garantida entre os valores de chave de partição.

* As alterações podem ser sincronizadas a partir de qualquer ponto anterior no tempo, que é não há nenhum período de retenção de dados fixa para o qual as alterações ficam disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contentor do Cosmos do Azure. Esta capacidade permite que as alterações dos contentores grandes para serem processados em paralelo por vários consumidores.

* As aplicações podem solicitar múltiplas alterações no mesmo recipiente simultaneamente. ChangeFeedOptions.StartTime pode ser utilizado para fornecer um ponto de partida inicial. Por exemplo, para encontrar o token de continuação correspondente a uma hora de relógio especificada. ContinuationToken, se for especificado, wins sobre os valores StartTime e StartFromBeginning. A precisão da ChangeFeedOptions.StartTime é ~ 5 segundos. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Alterar feed em APIs para Cassandra e MongoDB

A funcionalidade de alimentação de mudanças é surgida como fluxo de mudança em MongoDB API e Consulta com predicado em Cassandra API. Para saber mais sobre os detalhes de implementação da MongoDB API, consulte os [streams Change no Azure Cosmos DB API para MongoDB](mongodb-change-streams.md).

A Nativa Apache Cassandra fornece a captura de dados de mudança (CDC), um mecanismo para sinalizar tabelas específicas para o arquivo, bem como rejeitar escritos para essas tabelas uma vez que um tamanho configurável no disco para o log CDC é alcançado. A funcionalidade de feed de mudança em Azure Cosmos DB API para Cassandra aumenta a capacidade de consultar as mudanças com predicado via CQL. Para saber mais sobre os detalhes da implementação, consulte [change feed no Azure Cosmos DB API para Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passos seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Opções para ler feed de mudança](read-change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)
* [Utilização do processador de feed de mudança](change-feed-processor.md)
