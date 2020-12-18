---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/24/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce906ad62b51956cb85f854846740fa09e06895d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97664988"
---
Event Hub organiza sequências de eventos em uma ou mais divisórias. À medida que os eventos mais recentes chegam, são adicionados ao fim desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

As partições contêm dados de eventos que contenham o corpo do evento, um saco de propriedade definido pelo utilizador que descreve o evento, e metadados como a sua compensação na partição, o seu número na sequência de fluxo e o horário do lado do serviço no qual foi aceite.

![Diagrama que mostra a mais antiga a mais recente sequência de eventos.](./media/event-hubs-partitions/partition.png)

O Event Hubs é projetado para ajudar no processamento de grandes volumes de eventos, e a partição ajuda com isso de duas maneiras:

Em primeiro lugar, embora o Event Hubs seja um serviço PaaS, há uma realidade física por baixo, e manter um registo que preserva a ordem dos eventos requer que estes eventos sejam mantidos juntos no armazenamento subjacente e nas suas réplicas e isso resulta num teto de produção para tal log. A partição permite que vários troncos paralelos sejam usados para o mesmo Centro de Eventos e, portanto, multiplicando a capacidade de produção bruta disponível.

Em segundo lugar, as suas próprias aplicações devem ser capazes de acompanhar o processamento do volume de eventos que estão sendo enviados para um Centro de Eventos. Isso pode ser bastante complexo e requer uma capacidade de processamento paralela substancial, escalonada e paralela. A razão para as divisórias é a mesma que acima: A capacidade de um único processo para lidar com eventos é limitada, e por isso você precisa de vários processos, e divisórias são como a sua solução alimenta esses processos e ainda garante que cada evento tem um proprietário de processamento claro. 

O Event Hubs retém eventos para um tempo de retenção configurado que se aplica em todas as divisórias. Os eventos são automaticamente removidos quando o período de retenção foi atingido. Se especificar um período de retenção de um dia, o evento ficará indisponível exatamente 24 horas após a sua aceitação. Não é possível eliminar explicitamente os acontecimentos. 

O tempo de retenção permitido é de até 7 dias para o Event Hubs Standard e até 90 dias para os Centros de Eventos Dedicados. Se precisar de arquivar eventos para além do período de retenção permitido, pode [armazená-los automaticamente no Azure Storage ou no Azure Data Lake, ligando a funcionalidade de Captura de Centros de Eventos](../articles/event-hubs/event-hubs-capture-overview.md), e se precisar de pesquisar ou analisar esses arquivos profundos, pode [facilmente importá-los para a Azure Synapse ou outras lojas](../articles/event-hubs/store-captured-data-data-warehouse.md) e plataformas de análise similares. 

A razão para o limite do Event Hubs na retenção de dados com base no tempo é evitar que grandes volumes de dados históricos dos clientes sejam presos numa loja profunda que é apenas indexada por um timetamp e só permite o acesso sequencial. A filosofia arquitetónica aqui é que os dados históricos precisam de uma indexação mais rica e de um acesso mais direto do que a interface de eventos em tempo real que os Event Hubs ou Kafka fornecem. Os motores de fluxo de eventos não são adequados para desempenhar o papel de lagos de dados ou arquivos de longo prazo para o fornecimento de eventos. 

Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes. No Event Hubs, isso não é preocupação que exija uma intervenção administrativa como seria, por exemplo, em Apache Kafka, mas a distribuição desigual levará a uma carga desigual nos seus processadores de eventos a jusante.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de divisórias é especificado na criação e deve estar entre 1 e 32 no Event Hubs Standard. A contagem de divisórias pode ser de até 2000 divisórias por Unidade de Capacidade em Centros de Eventos Dedicados. 

Recomendamos que escolha pelo menos tantas divisões quanto espera necessitar em unidades de [produção sustentadas (TU)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) durante a carga máxima da sua aplicação para esse Centro de Eventos em particular. Deve calcular com uma única partição com uma capacidade de produção de 1 TU (1 MByte in, 2 MByte out). Pode escalar as TUs no seu espaço de nome ou nas unidades de capacidade do seu cluster independentemente da contagem de divisórias. Um Centro de Eventos com 32 divisórias ou um Centro de Eventos com 1 partição incorrem exatamente no mesmo custo quando o espaço de nome está definido para 1 capacidade TU. 

As aplicações controlam o mapeamento de eventos a divisórias de uma de três maneiras:

- Especificando a chave de partição, que é constantemente mapeada (utilizando uma função de haxixe) para uma das divisórias disponíveis. 
- Ao não especificar uma chave de partição, que permite ao corretor escolher aleatoriamente uma partição para um determinado evento.
- Enviando explicitamente eventos para uma partição específica.

Especificar uma chave de partição permite manter os eventos relacionados juntos na mesma partição e na ordem exata em que foram enviados. A chave de partição é uma corda que deriva do seu contexto de aplicação e identifica a inter-relação dos eventos.

Uma sequência de eventos identificados por uma chave de partição é um *fluxo*. Uma divisória é uma loja de troncos multiplexed para muitos desses fluxos. 

A contagem de divisórias de um Centro de Eventos pode ser aumentada após a criação do Event Hub, mas a distribuição de fluxos através de divisórias mudará quando for feita como o mapeamento das chaves de partição para as divisórias muda, pelo que deve esforçar-se por evitar tais alterações se a ordem relativa dos eventos for importante na sua aplicação.

Definir o número de divisórias ao valor máximo permitido é tentador, mas tenha sempre em mente que os fluxos do seu evento precisam de ser estruturados de modo a que possa realmente tirar partido de múltiplas divisórias. Se você precisa de preservação absoluta da ordem em todos os eventos ou apenas um punhado de substreams, você pode não ser capaz de aproveitar muitas divisórias. Além disso, muitas divisórias tornam o lado do processamento mais complexo. 

Embora as divisórias possam ser enviadas diretamente, não é recomendado. Em vez disso, pode utilizar construções de nível superior introduzidas na secção [de editores](../articles/event-hubs/event-hubs-features.md#event-publishers) do Evento. 

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
