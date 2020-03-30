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
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481540"
---
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Event Hubs](./media/event-hubs-partitions/partition.png)

O Event Hubs retém dados para um tempo de retenção configurado que se aplica a todas as divisórias no centro do evento. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado durante a criação e deve ser entre 2 e 32. O número de partições não é alterável, pelo que deve considerar uma escala a longo prazo quando definir o número de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Pode aumentar o número de partições além de 32 ao contactar a equipa do Event Hubs.

Talvez queira defini-lo como o valor mais alto possível, que é 32, no momento da criação. Lembre-se que ter mais do que uma partição resultará em eventos enviados para várias divisórias sem reter a ordem, a menos que configure os remetentes para enviar apenas para uma única divisória das 32 deixando as restantes 31 divisórias redundantes. No primeiro caso, terá de ler eventos em todas as 32 divisórias. Neste último caso, não existe um custo adicional óbvio para além da configuração extra que você tem que fazer no Anfitrião do Processador de Eventos.

Embora as divisórias sejam identificáveis e possam ser enviadas diretamente, o envio diretamente para uma divisória não é recomendado. Em vez disso, pode utilizar construções de nível superior introduzidas na secção [de editores](../articles/event-hubs/event-hubs-features.md#event-publishers) do Evento. 

As divisórias são preenchidas com uma sequência de dados de eventos que contém o corpo do evento, um saco de propriedade definido pelo utilizador, e metadados como a sua compensação na divisória e o seu número na sequência de fluxo.

Recomendamos que equilibre 1:1 unidades de entrada e divisórias para alcançar uma escala ideal. Uma única partição tem uma entrada garantida e uma saída de até uma unidade de entrada. Embora possa obter uma maior entrada numa partição, o desempenho não é garantido. É por isso que recomendamos vivamente que o número de divisórias num centro de eventos seja maior ou igual ao número de unidades de produção.

Dado o total de produção que planeia necessitar, sabe o número de unidades de produção que necessita e o número mínimo de divisórias, mas quantas divisórias deve ter? Escolha o número de divisórias com base no paralelismo a jusante que pretende alcançar, bem como as necessidades de entrada futuras. Não há nenhum custo para o número de divisórias que você tem dentro de um Centro de Eventos.

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
