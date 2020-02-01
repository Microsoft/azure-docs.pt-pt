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
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907659"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Equilibre a carga de partição em vários casos da sua aplicação
Para escalar a sua aplicação de processamento de eventos, pode executar várias instâncias da aplicação e fazê-la equilibrar a carga entre si. Nas versões mais antigas, o [EventProcessorHost](event-hubs-event-processor-host.md) permitiu-lhe equilibrar a carga entre várias instâncias do seu programa e eventos de checkpoint ao receber. Nas versões mais recentes (5.0 em diante), **o EventProcessorClient** (.NET e Java) ou o **EventHubConsumerClient** (Python e JavaScript) permite-lhe fazer o mesmo. O modelo de desenvolvimento é simplificado utilizando eventos. Subscreva os eventos em que está interessado ao registar um manipulador de eventos.

Este artigo descreve um cenário de amostra para usar vários casos para ler eventos a partir de um centro de eventos e, em seguida, dar-lhe detalhes sobre funcionalidades do cliente do processador de eventos, que lhe permite receber eventos de várias divisórias ao mesmo tempo e carregar o equilíbrio com outros consumidores que usam o mesmo centro de eventos e grupo de consumidores.

> [!NOTE]
> A chave ser dimensionada para os Hubs de eventos é a idéia de consumidores particionadas. Em comparação com o [consumidores concorrentes](https://msdn.microsoft.com/library/dn568101.aspx) padrão, o padrão de consumidor particionado permite criar uma escala elevada, removendo o afunilamento de contenção e promovendo o paralelismo de ponto a ponto.

## <a name="example-scenario"></a>Cenário de exemplo

Como um cenário de exemplo, considere uma empresa de segurança inicial que monitoriza a 100.000 casas. A cada minuto, obtém dados de vários sensores, tais como um detetor de movimento, sensor aberto porta/janela, detetor de rutura de vidro, e assim por diante, instalado em cada casa. A empresa fornece um web site a residentes monitorizar a atividade de sua casa em tempo real.

Cada sensor envia por push dados para um hub de eventos. O hub de eventos é configurado com 16 partições. No lado consumista, é necessário um mecanismo que possa ler estes eventos, consolidá-los (filtro, agregado, e assim por diante) e despejar o agregado numa bolha de armazenamento, que é então projetada para uma página web amigável.

## <a name="write-the-consumer-application"></a>Escreva a aplicação de consumidor

Ao projetar o consumidor num ambiente distribuído, o cenário tem de lidar com os seguintes requisitos:

1. **Escala:** criar vários consumidores, com cada consumidor a propriedade de leitura de algumas partições de Hubs de eventos.
2. **O balanceamento de carga:** aumentar ou reduzir os consumidores dinamicamente. Por exemplo, quando um novo tipo de sensor (por exemplo, um detector de monóxido de carbono) é adicionado a cada página inicial, aumenta o número de eventos. Nesse caso, o operador (um ser humano) aumenta o número de instâncias de consumidor. Em seguida, o conjunto de consumidores pode reequilibrar o número de partições que ele possui, para partilhar a carga com os consumidores recém-adicionada.
3. **Currículo sem emenda sobre falhas:** Se um consumidor (**consumidor A**) falhar (por exemplo, a máquina virtual que alberga o consumidor de repente se despenha), então outros consumidores podem pegar nas divisórias detidas pelo **consumidor A** e continuar. Além disso, o continuação ponto, chamado um *ponto de verificação* ou *deslocamento*, deve estar no momento exato em que **consumidor A** com falha, ou um pouco antes disso.
4. **Consumir eventos:** Embora os três pontos anteriores se desloquem à gestão do consumidor, deve haver código para consumir os eventos e fazer algo de útil com ele. Por exemplo, agrega-o e envia-o para o armazenamento de bolhas.

## <a name="event-processor-or-consumer-client"></a>Processador de eventos ou cliente de consumo

Não precisa de construir a sua própria solução para satisfazer estes requisitos. Os SDKs do Azure Event Hubs fornecem esta funcionalidade. Em .NET ou Java SDKs, utiliza um cliente de processador de eventos (EventProcessorClient), e em SDKs de Script Python e Java, utiliza o EventHubConsumerClient. Na versão antiga do SDK, foi o anfitrião do processador de eventos (EventProcessorHost) que apoiou estas funcionalidades.

Para a maioria dos cenários de produção, recomendamos que utilize o cliente do processador de eventos para eventos de leitura e processamento. O cliente do processador destina-se a proporcionar uma experiência robusta para processar eventos em todas as divisórias de um hub de eventos de forma performativa e tolerante a falhas, fornecendo um meio para fazer um checkpoint do seu progresso. Os clientes de processadores de eventos também são capazes de trabalhar cooperativamente no contexto de um grupo de consumidores para um determinado hub de eventos. Os clientes gerirão automaticamente a distribuição e o equilíbrio do trabalho à medida que os casos ficam disponíveis ou indisponíveis para o grupo.

## <a name="partition-ownership-tracking"></a>Propriedade de partição de controlo

Um caso de processador de eventos normalmente possui e processa eventos de uma ou mais divisórias. A propriedade das divisórias é distribuída uniformemente entre todas as instâncias de processadores de eventos ativos associadas a um hub de eventos e combinação de grupo de consumidores. 

Cada processador de eventos recebe um identificador único e reclama a propriedade das divisórias adicionando ou atualizando uma entrada numa loja de controlo. Todas as instâncias de processadorde eventos comunicam periodicamente com esta loja para atualizar o seu próprio estado de processamento, bem como para aprender sobre outras instâncias ativas. Estes dados são então utilizados para equilibrar a carga entre os processadores ativos. Novas instâncias podem juntar-se à piscina de processamento para escalar. Quando os casos descem, seja devido a falhas ou à redução da escala, a propriedade da partição é graciosamente transferida para outros processadores ativos.

Os registos de propriedade da partilha na loja de controlo registam o espaço de nome do Event Hubs, o nome do hub de eventos, o grupo de consumidores, o identificador de processador de eventos (também conhecido como proprietário), o id de partição e o último tempo modificado.



| Espaço de nomes dos Event Hubs               | O nome do hub de eventos | **Grupo de consumidores** | Proprietário                                | ID da partição | Última vez modificada  |
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

Quando o ponto de verificação é realizado para marcar um evento como processado, uma entrada na loja de controlo é adicionada ou atualizada com o número de compensação e sequência do evento. Os utilizadores devem decidir a frequência de atualização do ponto de verificação. A atualização após cada evento processado com sucesso pode ter implicações de desempenho e custos, uma vez que desencadeia uma operação de escrita para a loja de controlo subjacente. Além disso, o checkpoint de cada evento é indicativo de um padrão de mensagens em fila para o qual uma fila de ônibus de serviço pode ser uma opção melhor do que um centro de eventos. A idéia por trás dos Hubs de eventos é que ", pelo menos, uma vez" entrega em grande escala. Ao fazer sua idempotentes downstream sistemas, é fácil de recuperar de falhas ou reiniciar esse resultado nos mesmos eventos a ser recebidos várias vezes.

## <a name="thread-safety-and-processor-instances"></a>Instâncias de processador e de segurança do thread

Por predefinição, o processador de eventos ou o consumidor são seguros e comportam-se de forma sincronizada. Quando os eventos chegam para uma partição, a função que processa os eventos é chamada. Mensagens e chamadas subsequentes para esta função fazem fila nos bastidores, à medida que a bomba de mensagem continua a correr em segundo plano em outros fios. A segurança do thread remove a necessidade de coleções thread-safe e aumenta significativamente o desempenho.

## <a name="next-steps"></a>Passos seguintes
Veja as seguintes partidas rápidas:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
