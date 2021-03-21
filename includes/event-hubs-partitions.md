---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 03/15/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2cb203a00bb00767126f95e1fdc2f5aff8990f01
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601286"
---
O Event Hubs organiza sequências de eventos enviados para um centro de eventos em uma ou mais divisórias. À medida que os eventos mais recentes chegam, são adicionados ao fim desta sequência. 

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

Uma partição pode ser considerada como um "log compromete-se". As partições contêm dados de eventos que contêm corpo do evento, um saco de propriedade definido pelo utilizador que descreve o evento, metadados como o seu offset na partição, o seu número na sequência de fluxo e o horário de serviço no qual foi aceite.

![Diagrama que mostra a mais antiga a mais recente sequência de eventos.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Vantagens da utilização de divisórias
O Event Hubs é projetado para ajudar no processamento de grandes volumes de eventos, e a partilha ajuda com isso de duas maneiras:

- Embora o Event Hubs seja um serviço PaaS, há uma realidade física por baixo, e manter um registo que preserva a ordem dos eventos requer que estes eventos sejam mantidos juntos no armazenamento subjacente e nas suas réplicas e isso resulta num teto de produção para tal registo. A partição permite que vários troncos paralelos sejam usados para o mesmo centro de eventos e, portanto, multiplicando a capacidade de produção bruta disponível.
- As suas próprias aplicações devem ser capazes de acompanhar o processamento do volume de eventos que estão a ser enviados para um centro de eventos. Pode ser complexo e requer uma capacidade de processamento paralela substancial, escalonada e paralela. A capacidade de um único processo para lidar com eventos é limitada, por isso precisa de vários processos. As divisórias são como a sua solução alimenta esses processos e ainda garante que cada evento tem um claro proprietário de processamento. 

### <a name="number-of-partitions"></a>Número de divisórias
O número de divisórias é especificado na criação e deve estar entre 1 e 32 no Event Hubs Standard. A contagem de divisórias pode ser de até 2000 divisórias por Unidade de Capacidade em Centros de Eventos Dedicados. 

Recomendamos que escolha pelo menos tantas divisões quanto espera necessitar em unidades de [produção sustentadas (TU)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) durante a carga máxima da sua aplicação para esse Centro de Eventos em particular. Deve calcular com uma única partição com uma capacidade de produção de 1 TU (1 MByte in, 2 MByte out). Pode escalar as TUs no seu espaço de nome ou nas unidades de capacidade do seu cluster independentemente da contagem de divisórias. Um Centro de Eventos com 32 divisórias ou um Centro de Eventos com 1 partição incorrem exatamente no mesmo custo quando o espaço de nome está definido para 1 capacidade TU. 

A contagem de divisórias para um centro de eventos num [cluster dedicado de Clusters de Eventos](../articles/event-hubs/event-hubs-dedicated-overview.md) pode ser [aumentada](../articles/event-hubs/dynamically-add-partitions.md) após a criação do centro de eventos, mas a distribuição de fluxos através de divisórias mudará quando for feita como o mapeamento das chaves de partição para as divisórias muda, por isso deve esforçar-se para evitar tais alterações se a ordem relativa dos eventos for importante na sua aplicação.

Definir o número de divisórias ao valor máximo permitido é tentador, mas tenha sempre em mente que os fluxos do seu evento precisam de ser estruturados de modo a que possa realmente tirar partido de múltiplas divisórias. Se você precisa de preservação absoluta da ordem em todos os eventos ou apenas um punhado de substreams, você pode não ser capaz de aproveitar muitas divisórias. Além disso, muitas divisórias tornam o lado do processamento mais complexo. 


### <a name="mapping-of-events-to-partitions"></a>Mapeamento de eventos para divisórias
Pode utilizar uma chave de partição para mapear dados de eventos recebidos em partições específicas para a finalidade de organização de dados. A chave de partição é um valor fornecido pelo remetente transmitido para um hub de eventos. É processada através de uma função hash estática que cria a atribuição de partições. Se não especificar uma chave de partição ao publicar um evento, é utilizada uma atribuição round robin.

O publicador de eventos apenas tem conhecimento da respetiva chave de partição, não da partição onde os eventos são publicados. Este desacoplamento da chave e da partição faz com que o remetente não tenha necessidade de saber muito sobre o processamento a jusante. Uma identidade por dispositivo ou utilizador exclusivo faz com que uma chave de partição seja segura, mas outros atributos como a geografia também podem ser utilizados para agrupar os eventos relacionados numa única partição.

Especificar uma chave de partição permite manter os eventos relacionados juntos na mesma partição e na ordem exata em que foram enviados. A chave de partição é uma corda que deriva do seu contexto de aplicação e identifica a inter-relação dos eventos. Uma sequência de eventos identificados por uma chave de partição é um *fluxo*. Uma divisória é uma loja de troncos multiplexed para muitos desses fluxos. 

> [!NOTE]
> Embora possa enviar eventos diretamente para divisórias, não o recomendamos, especialmente quando a alta disponibilidade é importante para si. Diminui a disponibilidade de um centro de eventos para o nível de partição. Para mais informações, consulte [Disponibilidade e Consistência.](../articles/event-hubs/event-hubs-availability-and-consistency.md)

