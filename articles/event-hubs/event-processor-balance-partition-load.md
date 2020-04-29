---
title: Balance a carga de divisória em várias instâncias - Azure Event Hubs / Microsoft Docs
description: Descreve como equilibrar a carga de partição em várias instâncias da sua aplicação utilizando um processador de eventos e o Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398298"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Equilibre a carga de partição em vários casos da sua aplicação
Para escalar a sua aplicação de processamento de eventos, pode executar várias instâncias da aplicação e fazê-la equilibrar a carga entre si. Nas versões mais antigas, o [EventProcessorHost](event-hubs-event-processor-host.md) permitiu-lhe equilibrar a carga entre várias instâncias do seu programa e eventos de checkpoint ao receber. Nas versões mais recentes (5.0 em diante), **o EventProcessorClient** (.NET e Java) ou o **EventHubConsumerClient** (Python e JavaScript) permite-lhe fazer o mesmo. O modelo de desenvolvimento é simplificado utilizando eventos. Subscreva os eventos em que está interessado ao registar um manipulador de eventos.

Este artigo descreve um cenário de amostra para usar vários casos para ler eventos a partir de um hub de eventos e, em seguida, dar-lhe detalhes sobre funcionalidades do cliente do processador de eventos, que lhe permite receber eventos de várias divisórias ao mesmo tempo e carregar o equilíbrio com outros consumidores que usam o mesmo hub de eventos e grupo de consumidores.

> [!NOTE]
> A chave para escalar para os Centros de Eventos é a ideia de consumidores divididos. Em contraste com o padrão dos [consumidores concorrentes,](https://msdn.microsoft.com/library/dn568101.aspx) o padrão de consumo dividido permite uma escala elevada, removendo o estrangulamento da contenção e facilitando o fim do paralelismo.

## <a name="example-scenario"></a>Cenário de exemplo

Como cenário de exemplo, considere uma empresa de segurança doméstica que monitorize 100.000 casas. A cada minuto, obtém dados de vários sensores, tais como um detetor de movimento, sensor aberto porta/janela, detetor de rutura de vidro, e assim por diante, instalado em cada casa. A empresa fornece um site para os residentes monitorizarem a atividade da sua casa em tempo real.

Cada sensor empurra dados para um centro de eventos. O centro do evento está configurado com 16 divisórias. No lado consumista, é necessário um mecanismo que possa ler estes eventos, consolidá-los (filtro, agregado, e assim por diante) e despejar o agregado numa bolha de armazenamento, que é então projetada para uma página web amigável.

## <a name="write-the-consumer-application"></a>Escreva a aplicação do consumidor

Ao conceber o consumidor num ambiente distribuído, o cenário deve tratar dos seguintes requisitos:

1. **Escala:** Crie vários consumidores, com cada consumidor a apropriar-se da leitura de algumas divisórias do Event Hubs.
2. **Balanço de carga:** Aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detetor de monóxido de carbono) é adicionado a cada casa, o número de eventos aumenta. Nesse caso, o operador (um ser humano) aumenta o número de casos de consumo. Em seguida, o conjunto de consumidores pode reequilibrar o número de divisórias que possuem, para partilhar a carga com os consumidores recém-adicionados.
3. **Currículo sem emenda sobre falhas:** Se um consumidor (**consumidor A**) falhar (por exemplo, a máquina virtual que alberga o consumidor de repente se despenha), então outros consumidores podem pegar nas divisórias detidas pelo **consumidor A** e continuar. Além disso, o ponto de continuação, denominado ponto de *verificação* ou *compensação,* deve estar no ponto exato em que o **consumidor A** falhou, ou ligeiramente antes disso.
4. **Consumir eventos:** Embora os três pontos anteriores se desloquem à gestão do consumidor, deve haver código para consumir os eventos e fazer algo de útil com ele. Por exemplo, agrega-o e envia-o para o armazenamento de bolhas.

## <a name="event-processor-or-consumer-client"></a>Processador de eventos ou cliente de consumo

Não precisa de construir a sua própria solução para satisfazer estes requisitos. Os SDKs do Azure Event Hubs fornecem esta funcionalidade. Em .NET ou Java SDKs, utiliza um cliente de processador de eventos (EventProcessorClient), e em SDKs de Script Python e Java, utiliza o EventHubConsumerClient. Na versão antiga do SDK, foi o anfitrião do processador de eventos (EventProcessorHost) que apoiou estas funcionalidades.

Para a maioria dos cenários de produção, recomendamos que utilize o cliente do processador de eventos para eventos de leitura e processamento. O cliente do processador destina-se a proporcionar uma experiência robusta para processar eventos em todas as divisórias de um hub de eventos de forma performativa e tolerante a falhas, fornecendo um meio para fazer um checkpoint do seu progresso. Os clientes de processadores de eventos também são capazes de trabalhar cooperativamente no contexto de um grupo de consumidores para um determinado hub de eventos. Os clientes gerirão automaticamente a distribuição e o equilíbrio do trabalho à medida que os casos ficam disponíveis ou indisponíveis para o grupo.

## <a name="partition-ownership-tracking"></a>Rastreio de propriedade de partição

Um caso de processador de eventos normalmente possui e processa eventos de uma ou mais divisórias. A propriedade das divisórias é distribuída uniformemente entre todas as instâncias de processadores de eventos ativos associadas a um hub de eventos e combinação de grupo de consumidores. 

Cada processador de eventos recebe um identificador único e reclama a propriedade das divisórias adicionando ou atualizando uma entrada numa loja de controlo. Todas as instâncias de processadorde eventos comunicam periodicamente com esta loja para atualizar o seu próprio estado de processamento, bem como para aprender sobre outras instâncias ativas. Estes dados são então utilizados para equilibrar a carga entre os processadores ativos. Novas instâncias podem juntar-se à piscina de processamento para escalar. Quando os casos descem, seja devido a falhas ou à redução da escala, a propriedade da partição é graciosamente transferida para outros processadores ativos.

Os registos de propriedade da partilha na loja de controlo registam o espaço de nome do Event Hubs, o nome do hub de eventos, o grupo de consumidores, o identificador de processador de eventos (também conhecido como proprietário), o id de partição e o último tempo modificado.



| Espaço de nomes dos Event Hubs               | O nome do hub de eventos | **Grupo de consumidores** | Proprietário                                | ID de Partição | Última vez modificada  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15t01:22:00 |

Cada instância do processador de eventos adquire a propriedade de uma partição e começa a processar a partição a partir do último ponto de [verificação](# Checkpointing)conhecido . Se um processador falhar (o VM desliga), então outras instâncias detetam-no olhando para o último tempo modificado. Outros casos tentam obter a propriedade das divisórias anteriormente detidas pela instância inativa, e a loja de controlo garante que apenas uma das instâncias consegue reclamar a propriedade de uma partição. Assim, em qualquer momento, há no máximo um processador recebendo eventos de uma partição.

## <a name="receive-messages"></a>Receber mensagens

Quando cria um processador de eventos, especifica as funções que processam eventos e erros. Cada chamada para a função que processa eventos proporciona um único evento a partir de uma partição específica. É da sua responsabilidade lidar com este evento. Se quiser certificar-se de que o consumidor processa todas as mensagens pelo menos uma vez, precisa de escrever o seu próprio código com lógica de retry. Mas tenha cuidado com mensagens envenenadas.

Recomendamos que faça as coisas relativamente rápido. Ou seja, faça o mínimo de processamento possível. Se precisa de escrever para armazenar e fazer algum encaminhamento, é melhor usar dois grupos de consumidores e ter dois processadores de eventos.

## <a name="checkpointing"></a>Ponto de verificação

*O checkpoint é* um processo pelo qual um processador de eventomarca ou compromete a posição do último evento processado com sucesso dentro de uma divisória. A marcação de um ponto de verificação é normalmente feita dentro da função que processa os eventos e ocorre por partilha dentro de um grupo de consumidores. 

Se um processador de evento se desligar de uma divisória, outra instância pode retomar o processamento da partição no posto de controlo que foi previamente cometido pelo último processador dessa partição nesse grupo de consumidores. Quando o processador se conecta, passa a contrapartida para o centro do evento para especificar a localização para começar a ler. Desta forma, pode utilizar o checkpoint para marcar eventos como "completos" através de aplicações a jusante e para fornecer resiliência quando um processador de eventos cai. É possível devolver dados mais antigos ao especificar um desvio inferior a partir deste processo de ponto de verificação. 

Quando o ponto de verificação é realizado para marcar um evento como processado, uma entrada na loja de controlo é adicionada ou atualizada com o número de compensação e sequência do evento. Os utilizadores devem decidir a frequência de atualização do ponto de verificação. A atualização após cada evento processado com sucesso pode ter implicações de desempenho e custos, uma vez que desencadeia uma operação de escrita para a loja de controlo subjacente. Além disso, o checkpoint de cada evento é indicativo de um padrão de mensagens em fila para o qual uma fila de ônibus de serviço pode ser uma opção melhor do que um centro de eventos. A ideia por trás do Event Hubs é que recebes "pelo menos uma vez" a entrega em grande escala. Ao tornar os seus sistemas a jusante idempotentes, é fácil recuperar de falhas ou recomeços que resultam em que os mesmos eventos sejam recebidos várias vezes.

> [!NOTE]
> Se estiver a utilizar o Armazenamento Azure Blob como loja de controlo num ambiente que suporta uma versão diferente do Storage Blob SDK do que os normalmente disponíveis no Azure, terá de utilizar o código para alterar a versão API do serviço de armazenamento para a versão específica suportada por esse ambiente. Por exemplo, se estiver a executar Hubs de [Eventos numa versão Azure Stack Hub 2002,](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)a versão mais alta disponível para o serviço de Armazenamento é a versão 2017-11-09. Neste caso, é necessário utilizar o código para direcionar a versão API do serviço de armazenamento para 2017-11-09. Para um exemplo sobre como direcionar uma versão Específica da API de Armazenamento, consulte estas amostras no GitHub: 
> - [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) ou [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>Instâncias de segurança e processador de fios

Por predefinição, o processador de eventos ou o consumidor são seguros e comportam-se de forma sincronizada. Quando os eventos chegam para uma partição, a função que processa os eventos é chamada. Mensagens e chamadas subsequentes para esta função fazem fila nos bastidores, à medida que a bomba de mensagem continua a correr em segundo plano em outros fios. Esta segurança de rosca elimina a necessidade de coleções seguras e aumenta drasticamente o desempenho.

## <a name="next-steps"></a>Passos seguintes
Veja as seguintes partidas rápidas:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
