---
title: Alterar padrões de design de feed em Azure Cosmos DB
description: Visão geral dos padrões comuns de design de alimentos para mudanças
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 7e6981fb57421846b491693bb6195ecef31a3773
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986307"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Alterar padrões de design de feed em Azure Cosmos DB

O feed de mudança de DB Azure Cosmos permite um processamento eficiente de grandes conjuntos de dados com um elevado volume de escritos. O feed de mudança também oferece uma alternativa para consultar todo um conjunto de dados para identificar o que mudou. Este documento centra-se em padrões comuns de design de feed de mudança, trocas de design e limitações de alimentação.

A Azure Cosmos DB é adequada para aplicações ioT, gaming, retalho e exploração madeireira. Um padrão de design comum nestas aplicações é usar alterações nos dados para desencadear ações adicionais. Exemplos de ações adicionais incluem:

* Desencadeando uma notificação ou uma chamada para uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para processamento de ioT ou análise em tempo real em dados operacionais.
* Movimento de dados como sincronização com cache, motor de busca, armazém de dados ou armazenamento a frio.

O feed de mudança no Azure Cosmos DB permite-lhe construir soluções eficientes e escaláveis para cada um destes padrões, como mostra a seguinte imagem:

![Usando o feed de mudança de DB azure Cosmos para alimentar cenários de análise em tempo real e cenários de computação orientados por eventos](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Computação e notificações de eventos

O feed de mudança de DB Azure Cosmos pode simplificar cenários que precisam desencadear uma notificação ou uma chamada para uma API com base num determinado evento. Pode utilizar a Biblioteca de [Processos](change-feed-processor.md) de Feed de Mudança para fazer uma pesquisa automática no seu contentor para alterações e chamar uma API externa sempre que houver uma escrita ou atualização.

Também pode desencadear uma notificação seletiva ou enviar uma chamada para uma API com base em critérios específicos. Por exemplo, se estiver a ler o feed de mudança utilizando [funções Azure,](change-feed-functions.md)pode colocar lógica na função apenas para enviar uma notificação se tiver sido cumprido um critério específico. Embora o código função Azure seja executado durante cada escrita e atualização, a notificação só seria enviada se tivessem sido cumpridos critérios específicos.

## <a name="real-time-stream-processing"></a>Processamento de fluxo em tempo real

O feed de mudança de DB Azure Cosmos pode ser usado para processamento de fluxo em tempo real para ioT ou processamento de análise em tempo real em dados operacionais.
Por exemplo, poderá receber e armazenar dados de eventos de dispositivos, sensores, infraestruturas e aplicações, e processar estes eventos em tempo real, utilizando [o Spark](../hdinsight/spark/apache-spark-overview.md). A imagem que se segue mostra como pode implementar uma arquitetura lambda usando o Azure Cosmos DB através de change feed:

![Oleoduto de lambda baseado em Azure Cosmos db para ingestão e consulta](./media/change-feed/lambda.png)

Em muitos casos, as implementações de processamento de fluxo recebem primeiro um grande volume de dados recebidos numa fila temporária de mensagens, como o Azure Event Hub ou o Apache Kafka. O feed de mudança é uma ótima alternativa devido à capacidade da Azure Cosmos DB de suportar uma elevada taxa sustentada de ingestão de dados com baixa leitura garantida e latência de escrever. As vantagens do feed de mudança de DB Azure Cosmos sobre uma fila de mensagens incluem:

### <a name="data-persistence"></a>Persistência de dados

Os dados escritos para o Azure Cosmos DB aparecerão no feed de mudança e serão mantidos até serem eliminados. As filas de mensagens têm normalmente um período máximo de retenção. Por exemplo, [o Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) oferece uma retenção máxima de dados de 90 dias.

### <a name="querying-ability"></a>Capacidade de consulta

Além de ler a partir de um feed de mudança de recipiente Cosmos, você também pode executar consultas SQL sobre os dados armazenados em Azure Cosmos DB. O feed de mudança não é uma duplicação de dados já no recipiente, mas sim apenas um mecanismo diferente de leitura dos dados. Portanto, se ler dados do feed de mudança, será sempre consistente com consultas do mesmo recipiente Azure Cosmos DB.

### <a name="high-availability"></a>Elevada disponibilidade

A Azure Cosmos DB oferece até 99,999% de disponibilidade de leitura e escrita. Ao contrário de muitas filas de mensagens, os dados do Azure Cosmos DB podem ser facilmente distribuídos e configurados globalmente com um [RTO (Objetivo](consistency-levels-tradeoffs.md#rto) do Tempo de Recuperação) de zero.

Após o processamento de itens no feed de mudança, você pode construir uma vista materializada e persistir valores agregados de volta em Azure Cosmos DB. Se estiver a usar o Azure Cosmos DB para construir um jogo, pode, por exemplo, usar o feed de mudança para implementar tabelas de líderes em tempo real com base em pontuações de jogos concluídos.

## <a name="data-movement"></a>Movimento de dados

Também pode ler a partir do feed de mudança para o movimento de dados em tempo real.

Por exemplo, o feed de alteração ajuda-o a executar as seguintes tarefas de forma eficiente:

* Atualize um cache, índice de pesquisa ou armazém de dados com dados armazenados em Azure Cosmos DB.

* Execute zero migrações em tempo de baixa para outra conta Azure Cosmos ou outro recipiente Azure Cosmos com uma chave de partição lógica diferente.

* Implementar um nível de nível de aplicação de nível de dados e arquivo. Por exemplo, pode armazenar "dados quentes" em Azure Cosmos DB e eliminar "dados frios" a outros sistemas de armazenamento, como [o Armazenamento De Blob Azure.](../storage/common/storage-introduction.md)

Quando tiver de [desnormalizar os dados através de divisórias e contentores,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)pode ler a partir do feed de mudança do seu recipiente como fonte para esta replicação de dados. A replicação de dados em tempo real com o feed de mudança só pode garantir uma eventual consistência. Pode [monitorizar até que ponto o Processador change feed fica para trás](how-to-use-change-feed-estimator.md) nas alterações de processamento no seu recipiente Cosmos.

## <a name="event-sourcing"></a>Sourcing de eventos

O padrão de fornecimento de [eventos](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) envolve a utilização de uma loja apenas para o apêndice para registar toda a série de ações sobre esses dados. O feed de mudança da Azure Cosmos DB é uma ótima escolha como uma loja central de dados em eventos de sourcing arquiteturas onde toda a ingestão de dados é modelada como escreve (sem atualizações ou exclusões). Neste caso, cada escreva para o Azure Cosmos DB é um "evento" e você terá um registo completo de eventos passados no feed de mudança. As utilizações típicas dos eventos publicados pela loja central de eventos são para manter vistas materializadas ou para integração com sistemas externos. Como não há um limite de tempo para a retenção no feed de mudança, você pode reproduzir todos os eventos passados lendo desde o início do feed de mudança do seu recipiente Cosmos.

Pode ter [várias alterações que os consumidores subscrevam no feed de mudança do mesmo recipiente](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Além da entrada do contentor de [aluguer,](change-feed-processor.md#components-of-the-change-feed-processor) não há qualquer custo para utilizar o feed de mudança. O feed de alteração está disponível em todos os recipientes, independentemente de ser utilizado.

O Azure Cosmos DB é uma grande loja de dados persistente sita-apenas no padrão de abastecimento de eventos devido aos seus pontos fortes na escalabilidade horizontal e elevada disponibilidade. Além disso, a biblioteca change Feed Processor oferece uma garantia ["pelo menos uma vez",](change-feed-processor.md#error-handling) garantindo que não perderá o processamento de quaisquer eventos.

## <a name="current-limitations"></a>Limitações atuais

O feed de mudança tem limitações importantes que deve entender. Enquanto os itens num recipiente Cosmos permanecerão sempre no feed de mudança, o feed de mudança não é um registo de funcionamento completo. Existem áreas importantes a considerar na conceção de uma aplicação que utiliza o feed de mudança.

### <a name="intermediate-updates"></a>Atualizações intermédias

Apenas a alteração mais recente para um determinado item está incluída no feed de mudança. Ao processar alterações, irá ler a versão mais recente do item disponível. Se houver várias atualizações para o mesmo item num curto espaço de tempo, é possível perder o processamento de atualizações intermédias. Se quiser rastrear atualizações e ser capaz de reproduzir atualizações passadas num item, recomendamos que modele estas atualizações como uma série de escritos.

### <a name="deletes"></a>Elimina

O feed de alteração não captura apaga. Se apagar um item do seu recipiente, também é removido do alimento para alterações. O método mais comum de manuseamento disto é adicionar um marcador suave nos itens que estão a ser eliminados. Você pode adicionar uma propriedade chamada "eliminado" e defini-lo como "verdadeiro" no momento da eliminação. Esta atualização de documentos aparecerá no feed de alteração. Pode definir um TTL neste item para que possa ser eliminado automaticamente mais tarde.

### <a name="guaranteed-order"></a>Ordem garantida

Existe uma ordem garantida no feed de mudança dentro de um valor-chave de divisória, mas não através de valores-chave de divisória. Deve selecionar uma chave de partição que lhe dê uma garantia de encomenda significativa.

Por exemplo, considere uma aplicação de retalho utilizando o padrão de design de fornecimento de eventos. Nesta aplicação, diferentes ações de utilizador são cada um "eventos" que são modelados como escreve para o Azure Cosmos DB. Imagine se alguns eventos de exemplo ocorreram na seguinte sequência:

1. Cliente adiciona item A ao seu carrinho de compras
2. Cliente adiciona item B ao seu carrinho de compras
3. Cliente adiciona remove item A do seu carrinho de compras
4. Clientes verificam e o conteúdo do carrinho de compras é enviado

Mantém-se uma vista materializada dos conteúdos atuais do carrinho de compras para cada cliente. Esta aplicação deve assegurar que estes eventos sejam processados na ordem em que ocorrem. Se, por exemplo, o check-out do carrinho fosse processado antes da remoção do item A, é provável que o cliente tivesse enviado o item A, em oposição ao item B desejado. A fim de garantir que estes quatro eventos são processados por ordem da sua ocorrência, devem ser enquadrados no mesmo valor-chave de partilha. Se selecionar **o nome** de utilizador (cada cliente tem um nome de utilizador único) como chave de partição, pode garantir que estes eventos aparecem no feed de alteração na mesma ordem em que são escritos para o Azure Cosmos DB.

## <a name="examples"></a>Exemplos

Aqui estão alguns exemplos de código de mudança no mundo real que se estendem para além do âmbito das amostras fornecidas nos docs da Microsoft:

- [Introdução ao feed de mudança](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT usar caso centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso de uso retalhista centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do feed de alterações](change-feed.md)
* [Opções para ler feed de mudança](read-change-feed.md)
* [Utilização de feed de mudança com funções azure](change-feed-functions.md)