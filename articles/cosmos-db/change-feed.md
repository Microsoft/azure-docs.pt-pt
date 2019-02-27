---
title: Trabalhar com a alteração de suporte de feed no Azure Cosmos DB
description: Utilize o suporte de feed de alterações do Azure Cosmos DB para controlar as alterações em documentos e executar o processamento baseado em evento, como acionadores e manter atualizados os sistemas de caches e análise.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e21058d47f554ca4a057ab90433895800fb17dd9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886728"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Alterar feed no Azure Cosmos DB - descrição geral

Altere feed de suporte no Azure Cosmos DB funciona através da escuta de um contentor do Azure Cosmos DB para efetuar quaisquer alterações. Em seguida, gera a saída de lista classificada de documentos que foram alteradas pela ordem em que foram modificadas. As alterações são preservadas, podem ser processadas de forma assíncrona e incremental, e o resultado pode ser distribuído por um ou mais consumidores para processamento paralelo. 

O Azure Cosmos DB é bem adequado para IoT, varejo, jogos e aplicações de Registro em log operacional. Um padrão de design comum nesses aplicativos é usar as alterações dos dados para acionar ações adicionais. Exemplos de ações adicionais incluem:

* Acionar uma notificação ou uma chamada para uma API, quando um item é inserido ou atualizado.
* Processamento de IoT ou processamento nos dados operacionais de análise em tempo real em fluxo em tempo real.
* Movimento de dados adicionais ao sincronizar com um cache ou de um mecanismo de pesquisa ou de um armazém de dados ou arquivamento de dados no armazenamento amovível.

A alteração de feed no Azure Cosmos DB permite-lhe criar soluções eficientes e dimensionáveis para cada um desses padrões, conforme mostrado na imagem seguinte:

![Usar as alterações do Azure Cosmos DB feed para análise em tempo real de energia e cenários de computação orientada para eventos](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>APIs e SDKs de cliente suportados

Esta funcionalidade é atualmente suportada pelas seguintes APIs do Azure Cosmos DB e SDKs do cliente.

| **Controladores do cliente** | **CLI do Azure** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin**|**API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | ND | Sim | Não | Não | Sim | Não |
|Java|ND|Sim|Não|Não|Sim|Não|
|Python|ND|Sim|Não|Não|Sim|Não|
|JS/nó|ND|Sim|Não|Não|Sim|Não|

## <a name="change-feed-and-different-operations"></a>Feed de alterações e operações diferentes

Hoje em dia, verá todas as operações no feed de alterações. A funcionalidade de onde pode controlar alterar feed, para operações específicas, como apenas atualizações e inserções não ainda não está disponível. Pode adicionar um marcador"soft" no item para atualizações e filtrar com base no que, durante o processamento de itens no feed de alterações. Atualmente o feed de alterações não registar eliminações. Semelhante ao exemplo anterior, pode adicionar um marcador de forma recuperável nos itens que estão a ser eliminados, por exemplo, pode adicionar um atributo no item chamado "eliminado" e defini-lo como "true" e definir um valor de TTL no item, para que sejam eliminado automaticamente. É possível ler que a alteração de itens de feed para itens de históricos, por exemplo, que foram adicionados cinco anos atrás. Se o item não for eliminado pode ler a alteração do feed com relação às General a origem do seu contentor.

### <a name="sort-order-of-items-in-change-feed"></a>A ordem dos itens no feed de alterações de classificação

Itens de feed de alterações são fornecidos por ordem de seu tempo de modificação. Esta sequência de ordenação é garantida por chave de partição lógica.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed de alterações em várias regiões do Azure Cosmos contas

Uma conta do Cosmos do Azure de várias regiões, se uma região de escrita falhar, feed de alterações irão funcionar em toda a operação de ativação pós-falha manual e vai ser contíguo.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed de alterações e a hora Live (TTL)

Se uma propriedade de valor de TTL (Time TTL) é definida num item como -1, o feed de alterações serão mantidos para sempre. Se os dados não for eliminados, irá permanecer no feed de alterações.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>Feed de alterações e _etag, _lsn ou TS

O formato de _etag é interno e não deve demorar dependência, porque ele pode mudar em qualquer altura. TS é uma modificação ou um carimbo de criação. Pode usar TS para comparação cronológica. _lsn é um id de lote que é adicionado para apenas; feed de alterações ele representa o id de transação. Número de itens pode ter o mesmo _lsn. ETag no FeedResponse é diferente do _etag que vir no item. _etag é um identificador interno e é utilizado para a simultaneidade informa ao controle sobre a versão do item, ao passo que o ETag é utilizado para o feed de sequenciamento.

## <a name="change-feed-use-cases-and-scenarios"></a>Cenários e casos de utilização de feed de alterações

Permite um processamento de grandes conjuntos de dados com um grande volume de escritas eficiente de feed de alterações. Feed de alterações também oferece uma alternativa ao consultar o conjunto de dados inteiro para identificar o que foi alterado.

### <a name="use-cases"></a>Casos de utilização

Por exemplo, com o feed de alterações pode efetuar as seguintes tarefas com eficiência:

* Atualizar uma cache, um índice de pesquisa de atualização ou atualizar um armazém de dados com dados armazenados no Azure Cosmos DB.

* Implementar um nível de aplicativo de dados e o arquivo de camadas, por exemplo, para armazenar os "dados" no Azure Cosmos DB e expira "dados amovíveis" para outros sistemas de armazenamento, por exemplo, [armazenamento de Blobs do Azure](../storage/common/storage-introduction.md).

* Execute zero migrações de período de indisponibilidade para outra conta do Cosmos do Azure ou outro contentor do Cosmos do Azure com uma chave de partição lógica diferente.

* Implemente [arquitetura de lambda](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) , com o Azure Cosmos DB, em que o Azure Cosmos DB suporta em tempo real, batch e a servir de camadas de consulta, permitindo a arquitetura de lambda com o custo total de posse baixo.

* Receber, armazenar dados de eventos de dispositivos, sensores, infraestrutura e aplicações e processar esses eventos em tempo real, por exemplo, usando [Spark](../hdinsight/spark/apache-spark-overview.md).  A imagem seguinte mostra como pode implementar com o Azure Cosmos DB através de feed de alterações de arquitetura de lambda:

![Pipeline de lambda com base no Cosmos DB do Azure para ingestão e consulta](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Cenários

Seguem-se alguns dos cenários, que pode facilmente implementar com o feed de alterações:

* Dentro de sua [sem servidor](https://azure.microsoft.com/en-us/solutions/serverless/) web ou de aplicações móveis, pode controlar eventos, como todas as alterações para o perfil do seu cliente, as preferências ou a respetiva localização e disparar certas ações, por exemplo, enviar notificações push para os dispositivos usando [as funções do Azure](change-feed-functions.md).

* Se estiver a utilizar o Azure Cosmos DB para criar um jogo, pode, por exemplo, utilize alterar feed para implementar as classificações em tempo real com base em classificações de jogos concluídos.


## <a name="working-with-change-feed"></a>Trabalhar com o feed de alterações

Pode trabalhar com o feed de alterações com as seguintes opções:

* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
* [Usando a alteração do feed com biblioteca processador do feed de alterações](change-feed-processor.md) 

Feed de alterações está disponível para cada chave de partição lógica dentro do contêiner, e podem ser distribuído por um ou mais consumidores para processamento paralelo, conforme mostrado na imagem abaixo.

![Processamento distribuído de feed de alterações do Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Recursos do feed de alterações

* Feed de alterações está ativado por predefinição para todas as contas do Cosmos do Azure.

* Pode utilizar o seu [débito aprovisionado](request-units.md) para ler a partir do feed de alterações, assim como qualquer outra operação do Azure Cosmos DB, em qualquer uma das regiões à sua base de dados do Cosmos do Azure.

* O feed de alterações inclui inserções e operações de atualização efetuadas aos itens dentro do contentor. Pode capturar eliminações ao definir um sinalizador de "eliminação de forma recuperável" dentro de seus itens (por exemplo, documentos) em vez de eliminações. Em alternativa, pode definir um período de expiração finita para seus itens com o [capacidade TTL](time-to-live.md). Por exemplo, 24 horas e utilização elimina o valor dessa propriedade para capturar. Com esta solução, deve processar as alterações dentro de um intervalo de tempo mais curto do que o período de expiração do TTL. 

* Cada alteração a um item exatamente uma vez aparece no feed de alterações e os clientes têm de gerir a lógica de ponto de verificação. Se quiser evitar a complexidade de gerir pontos de verificação, a biblioteca de processador do feed de alterações fornece pontos de verificação automática e ", pelo menos, uma vez" semântica. Ver [usar alterar feed com biblioteca processador do feed de alterações](change-feed-processor.md).

* A alteração mais recente para um determinado item está incluída no log de alterações. Alterações intermédias poderão não estar disponíveis.

* O feed de alterações é classificado a ordem dos modificação dentro de cada valor de chave de partição lógica. Não existe nenhuma ordem garantida entre os valores de chave de partição.

* As alterações podem ser sincronizadas a partir de qualquer ponto anterior no tempo, que é não há nenhum período de retenção de dados fixa para o qual as alterações ficam disponíveis.

* As alterações estão disponíveis em paralelo para todas as chaves de partição lógica de um contentor do Cosmos do Azure. Esta capacidade permite que as alterações dos contentores grandes para serem processados em paralelo por vários consumidores.

* As aplicações podem solicitar a vários feeds de mudança no mesmo contentor em simultâneo. ChangeFeedOptions.StartTime pode ser utilizado para fornecer um ponto de partida inicial. Por exemplo, para encontrar o token de continuação correspondente a uma hora de relógio especificada. ContinuationToken, se for especificado, wins sobre os valores StartTime e StartFromBeginning. A precisão da ChangeFeedOptions.StartTime é ~ 5 segundos. 

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Feed de alterações de opções para ler](read-change-feed.md)
* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
* [Usando a alteração de biblioteca processador do feed](change-feed-processor.md)
