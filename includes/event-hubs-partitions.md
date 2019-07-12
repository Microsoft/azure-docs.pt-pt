---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b23f9532aa1ca6f7bae914ff8cb9d7566a0fec86
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841578"
---
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Hubs de Eventos](./media/event-hubs-partitions/partition.png)

Os Hubs de eventos retém os dados durante um período de retenção configurado aplicado a todas as partições de evento hub. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado durante a criação e deve ser entre 2 e 32. O número de partições não é alterável, pelo que deve considerar uma escala a longo prazo quando definir o número de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Pode aumentar o número de partições além de 32 ao contactar a equipa do Event Hubs.

Enquanto as partições serem identificáveis e poderem ser enviadas diretamente, enviando diretamente para uma partição não é recomendado. Em vez disso, pode utilizar construções de nível superior, apresentadas na [os publicadores de eventos](../articles/event-hubs/event-hubs-features.md#event-publishers) secção. 

As partições são preenchidas com uma seqüência de dados de eventos que contêm o corpo do evento, uma matriz de propriedades definida pelo utilizador e metadados, como o desvio na partição e o respetivo número na sequência de transmissão.

Recomendamos que equilibre as unidades de débito de 1:1 e as partições para alcançar a escala ideal. Uma única partição tem uma entrada garantida e a saída de até uma unidade de débito. Embora possa ser capaz de alcançar um débito mais elevado numa partição, o desempenho não é garantido. É por isso é altamente recomendável que o número de partições num hub de eventos de ser maior que ou igual ao número de unidades de débito.

Tendo em conta o débito total que tencione necessidade, sabe o número de unidades de débito que necessita e o número mínimo de partições, mas o número de partições que deve ter? Escolha o número de partições com base em quer atingir o paralelismo a jusante, bem como as suas necessidades de débito futuras. Não existe nenhum custo associado para o número de partições que tem dentro de um Hub de eventos.

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
