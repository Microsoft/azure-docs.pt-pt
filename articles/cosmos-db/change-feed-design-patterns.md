---
title: Alterar padrões de design de feed em Azure Cosmos DB
description: Visão geral dos padrões comuns de design de feed de mudança
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: ebd1c4f71d71ca70f6d10763d538b1877b0c3539
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489358"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Alterar padrões de design de feed em Azure Cosmos DB

O feed de alteração DB Azure Cosmos permite um processamento eficiente de grandes conjuntos de dados com um elevado volume de escritos. O feed de alteração também oferece uma alternativa à consulta de um conjunto de dados inteiro para identificar o que mudou. Este documento centra-se em padrões comuns de design de feed de mudança, trocas de design e limitações de feed.

O Azure Cosmos DB é adequado para aplicações de IoT, jogos, retalho e exploração de madeira operacional. Um padrão de design comum nestas aplicações é usar alterações nos dados para desencadear ações adicionais. Exemplos de ações adicionais incluem:

* Desencadear uma notificação ou uma chamada para uma API, quando um item é inserido ou atualizado.
* Processamento de fluxo em tempo real para processamento de IoT ou análise em tempo real em dados operacionais.
* Movimento de dados como sincronização com uma cache, um motor de busca, um armazém de dados ou armazenamento frio.

O feed de mudança em Azure Cosmos DB permite-lhe construir soluções eficientes e escaláveis para cada um destes padrões, como mostra a seguinte imagem:

:::image type="content" source="./media/change-feed/changefeedoverview.png" alt-text="Utilizando o DB de mudança de Azure Cosmos para alimentar a análise em tempo real e cenários de computação orientados para eventos" border="false":::

## <a name="event-computing-and-notifications"></a>Computação de eventos e notificações

O feed de alteração DB do Azure Cosmos pode simplificar cenários que precisam de desencadear uma notificação ou enviar uma chamada para uma API com base num determinado evento. Pode utilizar a Biblioteca de [Processos de Alimentação de Alteração](change-feed-processor.md) para sondar automaticamente o seu recipiente para alterações e ligar para uma API externa sempre que houver uma escrita ou atualização.

Também pode desencadear seletivamente uma notificação ou enviar uma chamada para uma API com base em critérios específicos. Por exemplo, se estiver a ler a partir do feed de alteração utilizando [funções Azure,](change-feed-functions.md)pode colocar lógica na função apenas para enviar uma notificação se tiver sido cumprido um critério específico. Embora o código da Função Azure fosse executado durante cada escrita e atualização, a notificação só seria enviada se tivessem sido cumpridos critérios específicos.

## <a name="real-time-stream-processing"></a>Processamento de fluxo em tempo real

O feed de alteração DB Azure Cosmos pode ser utilizado para o processamento de fluxo em tempo real para o processamento de IoT ou análise em tempo real em dados operacionais.
Por exemplo, pode receber e armazenar dados de eventos a partir de dispositivos, sensores, infraestruturas e aplicações, e processar estes eventos em tempo real, utilizando [o Spark](../hdinsight/spark/apache-spark-overview.md). A imagem a seguir mostra como você pode implementar uma arquitetura lambda usando o Azure Cosmos DB através de change feed:

:::image type="content" source="./media/change-feed/lambda.png" alt-text="Utilizando o DB de mudança de Azure Cosmos para alimentar a análise em tempo real e cenários de computação orientados para eventos" border="false":::

Em muitos casos, as implementações de processamento de fluxo recebem primeiro um grande volume de dados de entrada numa fila de mensagens temporárias como O Azure Event Hub ou Apache Kafka. O feed de mudança é uma ótima alternativa devido à capacidade da Azure Cosmos DB de suportar uma taxa sustentada de ingestão de dados com baixa leitura garantida e escrever latência. As vantagens do feed de mudança de DB do Azure Cosmos através de uma fila de mensagens incluem:

### <a name="data-persistence"></a>Persistência de dados

Os dados escritos para Azure Cosmos DB aparecerão no feed de alteração e serão mantidos até serem eliminados. As filas de mensagens normalmente têm um período máximo de retenção. Por exemplo, [o Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) oferece uma retenção máxima de dados de 90 dias.

### <a name="querying-ability"></a>Capacidade de consulta

Além de ler a partir do feed de mudança de um recipiente Cosmos, você também pode executar consultas SQL sobre os dados armazenados em Azure Cosmos DB. O feed de alteração não é uma duplicação de dados já no recipiente, mas sim apenas um mecanismo diferente de leitura dos dados. Portanto, se ler os dados do feed de alteração, será sempre consistente com consultas do mesmo recipiente Azure Cosmos DB.

### <a name="high-availability"></a>Elevada disponibilidade

A Azure Cosmos DB oferece até 99,999% de leitura e disponibilidade de escrita. Ao contrário de muitas filas de mensagens, os dados DB do Azure Cosmos podem ser facilmente distribuídos globalmente e configurados com um [RTO (Objetivo](./consistency-levels.md#rto) de Tempo de Recuperação) de zero.

Após o processamento de itens no feed de alteração, pode construir uma visão materializada e persistir valores agregados no Azure Cosmos DB. Se estiveres a usar o Azure Cosmos DB para construir um jogo, podes, por exemplo, usar o feed de alteração para implementar leaderboards em tempo real com base em pontuações de jogos concluídos.

## <a name="data-movement"></a>Movimento de dados

Também pode ler a partir do feed de alteração para movimento de dados em tempo real.

Por exemplo, o feed de alteração ajuda-o a executar as seguintes tarefas de forma eficiente:

* Atualize um cache, índice de pesquisa ou armazém de dados com dados armazenados em Azure Cosmos DB.

* Execute migrações zero para outra conta Azure Cosmos ou outro recipiente Azure Cosmos com uma chave de partição lógica diferente.

* Implementar um tiering de dados de nível de aplicação e arquivo. Por exemplo, pode armazenar "dados quentes" no Azure Cosmos DB e envelhecer "dados frios" para outros sistemas de armazenamento, como [o Azure Blob Storage](../storage/common/storage-introduction.md).

Quando tiver de [desnormalizar os dados através de divisórias e contentores,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)pode ler-se a partir do feed de alteração do seu contentor como fonte para esta replicação de dados. A replicação de dados em tempo real com o feed de alteração só pode garantir uma eventual consistência. Pode [monitorizar até que ponto o processador Change Feed fica para trás](how-to-use-change-feed-estimator.md) nas alterações de processamento no seu contentor Cosmos.

## <a name="event-sourcing"></a>Sourcing de eventos

O [padrão de sourcing](/azure/architecture/patterns/event-sourcing) do evento envolve usar uma loja apenas de apêndice para registar toda a série de ações nesses dados. O feed de mudança da Azure Cosmos DB é uma ótima escolha como uma loja de dados central em arquiteturas de sourcing em casos em que toda a ingestão de dados é modelada como escreve (sem atualizações ou eliminações). Neste caso, cada um escreve para Azure Cosmos DB é um "evento" e terá um registo completo de eventos passados no feed de mudança. Os usos típicos dos eventos publicados pela loja central de eventos são para manter vistas materializadas ou para integração com sistemas externos. Como não há limite de tempo para a retenção no feed de mudança, pode reproduzir todos os eventos passados lendo desde o início do feed de mudança do seu contentor Cosmos.

Pode ter [vários consumidores de feed de alteração subscreverem o feed de alteração do mesmo recipiente](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Além da produção provisitada [do contentor de arrendamento,](change-feed-processor.md#components-of-the-change-feed-processor) não há qualquer custo para utilizar o alimento para a mudança. O feed de alteração está disponível em todos os recipientes, independentemente de ser utilizado.

A azure Cosmos DB é uma grande loja de dados persistente apenas de apêndice central no padrão de fornecimento de eventos devido aos seus pontos fortes na escalabilidade horizontal e alta disponibilidade. Além disso, a biblioteca do processador de feed de alteração oferece uma garantia ["pelo menos uma vez",](change-feed-processor.md#error-handling) garantindo que não perderá o processamento de quaisquer eventos.

## <a name="current-limitations"></a>Limitações atuais

O feed de mudança tem limitações importantes que deve entender. Enquanto os itens num recipiente Cosmos permanecerão sempre no feed de alteração, o feed de alteração não é um registo de funcionamento completo. Existem áreas importantes a ter em conta na conceção de uma aplicação que utiliza o feed de mudança.

### <a name="intermediate-updates"></a>Atualizações intermédias

Apenas a alteração mais recente para um determinado item está incluída no feed de alteração. Ao processar alterações, irá ler a versão mais recente disponível do item. Se houver várias atualizações para o mesmo item num curto espaço de tempo, é possível não processar atualizações intermédias. Se pretender rastrear as atualizações e ser capaz de reproduzir atualizações passadas para um item, recomendamos modelar estas atualizações como uma série de escritas.

### <a name="deletes"></a>Elimina

O feed de alteração não captura exclusões. Se eliminar um item do seu recipiente, este também é removido do feed de alteração. O método mais comum de manuseamento é adicionar um marcador suave nos itens que estão a ser eliminados. Pode adicionar uma propriedade chamada "eliminada" e defini-la como "verdadeira" no momento da eliminação. Esta atualização de documento aparecerá no feed de alteração. Pode definir um TTL neste item para que possa ser apagado automaticamente mais tarde.

### <a name="guaranteed-order"></a>Ordem garantida

Existe ordem garantida no feed de mudança dentro de um valor chave de partição, mas não em valores-chave de partição. Deve selecionar uma chave de partição que lhe dê uma garantia de ordem significativa.

Por exemplo, considere uma aplicação de retalho usando o padrão de design de fornecimento de eventos. Nesta aplicação, diferentes ações do utilizador são cada "evento" que são modelados como escreve para Azure Cosmos DB. Imagine se alguns eventos de exemplo ocorrerem na seguinte sequência:

1. Cliente adiciona o item A ao seu carrinho de compras
2. Cliente adiciona o item B ao seu carrinho de compras
3. Cliente remove o item A do carrinho de compras
4. O cliente verifica e o conteúdo do carrinho de compras é enviado

Mantém-se uma visão materializada do conteúdo atual do carrinho de compras para cada cliente. Esta aplicação deve garantir que estes eventos sejam processados na ordem em que ocorrem. Se, por exemplo, o check-out do carrinho fosse processado antes da remoção do ponto A, é provável que o cliente tivesse enviado o Item A, em oposição ao item B pretendido. A fim de garantir que estes quatro eventos são processados por ordem da sua ocorrência, devem enquadrar-se no mesmo valor-chave de partição. Se selecionar o **nome de utilizador** (cada cliente tem um nome de utilizador único) como chave de partição, pode garantir que estes eventos aparecem no feed de alteração na mesma ordem em que são escritos para Azure Cosmos DB.

## <a name="examples"></a>Exemplos

Aqui estão alguns exemplos de códigos de feed de mudança de mudança no mundo real que se estendem para além do âmbito das amostras fornecidas nos docs da Microsoft:

- [Introdução ao feed de alteração](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [Caso de uso de IoT centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Caso de uso de retalho centrado em torno do feed de mudança](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do feed de alterações](change-feed.md)
* [Opções para ler alterar feed](read-change-feed.md)
* [Utilização de feed de alteração com Funções Azure](change-feed-functions.md)