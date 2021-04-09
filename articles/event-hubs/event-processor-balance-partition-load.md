---
title: Equilibrar a carga de partição em vários casos - Azure Event Hubs | Microsoft Docs
description: Descreve como equilibrar a carga de partição em várias instâncias da sua aplicação usando um processador de eventos e o Azure Event Hubs SDK.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025049"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Equilibrar a carga de partição em vários casos da sua aplicação
Para escalar a sua aplicação de processamento de eventos, pode executar várias instâncias da aplicação e fazê-la equilibrar a carga entre si. Nas versões mais antigas, o [EventProcessorHost](event-hubs-event-processor-host.md) permitiu equilibrar a carga entre várias instâncias do seu programa e eventos de checkpoint ao receber. Nas versões mais recentes (5.0 em diante), **EventProcessorClient** (.NET e Java) ou **EventHubConsumerClient** (Python e JavaScript) permite-lhe fazer o mesmo. O modelo de desenvolvimento é simplificado utilizando eventos. Subscreve os eventos que lhe interessam ao registar um manipulador de eventos. Se estiver a utilizar a versão antiga da biblioteca do cliente, consulte os seguintes guias de migração: [.NET,](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) [Java,](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md) [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)e [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

Este artigo descreve um cenário de amostra para usar várias instâncias para ler eventos a partir de um centro de eventos e, em seguida, dar-lhe detalhes sobre as funcionalidades do cliente processador de eventos, que lhe permite receber eventos de várias divisórias ao mesmo tempo e carregar o equilíbrio com outros consumidores que usam o mesmo centro de eventos e grupo de consumidores.

> [!NOTE]
> A chave para a escala para os Centros de Eventos é a ideia dos consumidores divididos. Em contraste com o padrão [dos consumidores concorrentes,](/previous-versions/msp-n-p/dn568101(v=pandp.10)) o padrão de consumidor dividido permite uma escala elevada, removendo o estrangulamento da contenção e facilitando o fim do paralelismo.

## <a name="example-scenario"></a>Cenário de exemplo

Como cenário, considere uma empresa de segurança doméstica que monitoriza 100.000 casas. A cada minuto, obtém dados de vários sensores, tais como um detetor de movimento, sensor aberto porta/janela, detetor de rutura de vidro, e assim por diante, instalado em cada casa. A empresa fornece um site para os residentes monitorizarem a atividade da sua casa em tempo real.

Cada sensor empurra os dados para um centro de eventos. O centro de eventos está configurado com 16 divisórias. Na parte final, você precisa de um mecanismo que possa ler estes eventos, consolidá-los (filtrar, agregar, e assim por diante) e despejar o agregado para uma bolha de armazenamento, que é então projetada para uma página web amigável.

## <a name="write-the-consumer-application"></a>Escreva a aplicação do consumidor

Ao conceber o consumidor num ambiente distribuído, o cenário deve tratar dos seguintes requisitos:

1. **Escala:** Crie vários consumidores, com cada consumidor a apropriar-se da leitura de algumas divisórias do Event Hubs.
2. **Equilíbrio de carga:** Aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detetor de monóxido de carbono) é adicionado a cada casa, o número de eventos aumenta. Nesse caso, o operador (um ser humano) aumenta o número de casos de consumidores. Depois, o conjunto de consumidores pode reequilibrar o número de divisórias que possuem, para partilhar a carga com os novos consumidores.
3. **Retoma sem emenda sobre falhas:** Se um consumidor (**consumidor A)** falhar (por exemplo, a máquina virtual que acolhe o consumidor despenha-se subitamente), então outros consumidores podem pegar nas divisórias detidas pelo **consumidor A** e continuar. Além disso, o ponto de continuação, chamado *de ponto de verificação* ou *compensação,* deve estar no ponto exato em que o **consumidor A** falhou, ou ligeiramente antes disso.
4. **Consumir eventos:** Embora os três pontos anteriores tratem da gestão do consumidor, tem de haver um código para consumir os acontecimentos e fazer algo de útil com ele. Por exemplo, agrega-o e envia-o para o armazenamento de bolhas.

## <a name="event-processor-or-consumer-client"></a>Processador de eventos ou cliente de consumo

Não precisa de construir a sua própria solução para satisfazer estes requisitos. Os Azure Event Hubs SDKs fornecem esta funcionalidade. Em SDKs .NET ou Java, utiliza um cliente de processador de eventos (EventProcessorClient), e em SDKs Python e JavaScript, utiliza o EventHubConsumerClient. Na antiga versão da SDK, foi o anfitrião do processador de eventos (EventProcessorHost) que apoiou estas funcionalidades.

Para a maioria dos cenários de produção, recomendamos que utilize o cliente processador do evento para eventos de leitura e processamento. O cliente do processador destina-se a proporcionar uma experiência robusta para o processamento de eventos em todas as divisórias de um centro de eventos de forma performante e tolerante a falhas, ao mesmo tempo que fornece um meio para checkpoint o seu progresso. Os clientes de processadores de eventos podem trabalhar em cooperação no contexto de um grupo de consumidores para um determinado centro de eventos. Os clientes irão gerir automaticamente a distribuição e o equilíbrio do trabalho à medida que as instâncias se tornarem disponíveis ou indisponíveis para o grupo.

## <a name="partition-ownership-tracking"></a>Rastreio da propriedade da partição

Uma instância de processador de eventos normalmente possui e processa eventos de uma ou mais divisórias. A propriedade das divisórias é distribuída uniformemente entre todas as instâncias ativas do processador de eventos associadas a um centro de eventos e combinação de grupos de consumidores. 

Cada processador de eventos recebe um identificador único e reclama a propriedade das divisórias adicionando ou atualizando uma entrada numa loja de ponto de verificação. Todas as instâncias do processador de eventos comunicam periodicamente com esta loja para atualizar o seu próprio estado de processamento, bem como para aprender sobre outros casos ativos. Estes dados são então utilizados para equilibrar a carga entre os processadores ativos. Novas instâncias podem juntar-se à piscina de processamento para escalar. Quando as ocorrências se abatem, seja por falhas ou para reduzir, a propriedade da partição é transferida graciosamente para outros processadores ativos.

Os registos de propriedade de partição na loja de checkpoint mantêm o registo do espaço de nome do Event Hubs, nome do centro de eventos, grupo de consumidores, identificador de processador de eventos (também conhecido como proprietário), ID de partição e o último tempo modificado.



| Espaço de nomes dos Event Hubs               | Nome do Hub de Eventos | **Grupo de consumidores** | Proprietário                                | ID de Partição | Última hora modificada  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Cada instância do processador de eventos adquire a propriedade de uma partição e começa a processar a partição do último ponto de [verificação](# Checkpointing)conhecido . Se um processador falhar (VM desliga-se), outras instâncias detetam-no olhando para o último tempo modificado. Outros casos tentam obter a propriedade das divisórias anteriormente detidas pela instância inativa, e a loja de pontos de controlo garante que apenas uma das instâncias consegue reclamar a propriedade de uma partição. Assim, em qualquer momento, há no máximo um processador que recebe eventos de uma partição.

## <a name="receive-messages"></a>Receber mensagens

Quando cria um processador de eventos, especifica as funções que irão processar eventos e erros. Cada chamada para a função que processa eventos proporciona um único evento a partir de uma partição específica. É sua responsabilidade lidar com este evento. Se quiser certificar-se de que o consumidor processa todas as mensagens pelo menos uma vez, tem de escrever o seu próprio código com lógica de repetição. Mas tenha cuidado com as mensagens envenenadas.

Recomendamos que faça as coisas relativamente rápido. Ou seja, faça o mínimo de processamento possível. Se você precisa escrever para armazenamento e fazer algum encaminhamento, é melhor usar dois grupos de consumidores e ter dois processadores de eventos.

## <a name="checkpointing"></a>Pontos de verificação

*O checkpointing* é um processo pelo qual um processador de eventos marca ou compromete a posição do último evento processado com sucesso dentro de uma partição. A marcação de um ponto de verificação é normalmente feita dentro da função que processa os eventos e ocorre numa base por partição dentro de um grupo de consumidores. 

Se um processador de eventos se desligar de uma partição, outra instância pode retomar o processamento da partição no ponto de verificação que foi previamente cometido pelo último processador dessa partição nesse grupo de consumidores. Quando o processador se conecta, passa a offset para o centro do evento para especificar a localização para começar a ler. Desta forma, pode utilizar o checkpointing para marcar eventos como "completo" por aplicações a jusante e para fornecer resiliência quando um processador de eventos se desatar. É possível retornar a dados mais antigos especificando uma compensação mais baixa deste processo de verificação. 

Quando o ponto de verificação é realizado para marcar um evento como processado, uma entrada na loja de pontos de verificação é adicionada ou atualizada com o número de compensação e sequência do evento. Os utilizadores devem decidir a frequência da atualização do ponto de verificação. A atualização após cada evento processado com sucesso pode ter implicações de desempenho e custos, uma vez que desencadeia uma operação de escrita para a loja de pontos de verificação subjacente. Além disso, a verificação de cada evento é indicativo de um padrão de mensagens em fila para o qual uma fila de Autocarros de Serviço pode ser uma opção melhor do que um centro de eventos. A ideia por trás do Event Hubs é que você recebe entrega "pelo menos uma vez" em grande escala. Ao tornar os seus sistemas a jusante idempotentes, é fácil recuperar de falhas ou recomeços que resultam em que os mesmos eventos sejam recebidos várias vezes.

> [!NOTE]
> Se estiver a utilizar o Azure Blob Storage como loja de checkpoint num ambiente que suporta uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure, terá de utilizar código para alterar a versão API do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se estiver a executar [Os Centros de Eventos numa versão Azure Stack Hub 2002](/azure-stack/user/event-hubs-overview), a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, é necessário utilizar o código para direcionar a versão API do serviço de armazenamento para 2017-11-09. Para um exemplo sobre como direcionar uma versão específica da API de armazenamento, consulte estas amostras no GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) ou  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Casos de segurança e de processador de roscas

Por predefinição, a função que processa os eventos é chamada sequencialmente para uma determinada partição. Eventos subsequentes e chamadas para esta função a partir da mesma fila de partição nos bastidores, à medida que a bomba de evento continua a funcionar em segundo plano em outros fios. Eventos de diferentes divisórias podem ser processados simultaneamente e qualquer estado partilhado que seja acedido através de divisórias tem de ser sincronizado.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes arranques rápidos:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
