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
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481540"
---
O Event Hubs fornece transmissão de mensagens através de um padrão de consumidor particionado em que cada consumidor só lê um subconjunto específico, ou partição, do fluxo de mensagens. Este padrão permite um dimensionamento horizontal do processamento de eventos e fornece outras funcionalidades centradas nos fluxos que não estão disponíveis nas filas e nos tópicos.

Uma partição é uma sequência ordenada de eventos mantida num hub de eventos. À medida que chegam novos eventos, eles são adicionados ao final desta sequência. Uma partição pode ser considerada como um "registo de consolidação".

![Hubs de Eventos](./media/event-hubs-partitions/partition.png)

Os Hubs de eventos retém os dados durante um período de retenção configurado aplicado a todas as partições de evento hub. Os eventos expiram em intervalos de tempo; não é possível eliminá-los explicitamente. Uma vez que as partições são independentes e contêm a sua própria sequência de dados, aumentam frequentemente com taxas diferentes.

![Hubs de Eventos](./media/event-hubs-partitions/multiple-partitions.png)

O número de partições é especificado durante a criação e deve ser entre 2 e 32. O número de partições não é alterável, pelo que deve considerar uma escala a longo prazo quando definir o número de partições. As partições são um mecanismo de organização de dados relacionado com o paralelismo a jusante necessário nas aplicações de consumo. O número de partições num hub de eventos está diretamente relacionado com o número de leitores simultâneos que espera ter. Pode aumentar o número de partições além de 32 ao contactar a equipa do Event Hubs.

Talvez você queira defini-lo para ser o maior valor possível, que é 32, no momento da criação. Lembre-se de que ter mais de uma partição resultará em eventos enviados a várias partições sem reter o pedido, a menos que você configure os remetentes para enviar somente para uma única partição fora do 32 deixando as 31 restantes de partições redundantes. No primeiro caso, você terá que ler eventos em todas as partições 32. No último caso, não há nenhum custo adicional óbvio da configuração extra que você precisa fazer no host do processador de eventos.

Enquanto as partições serem identificáveis e poderem ser enviadas diretamente, enviando diretamente para uma partição não é recomendado. Em vez disso, você pode usar construções de nível superior introduzidas na seção [editores de eventos](../articles/event-hubs/event-hubs-features.md#event-publishers) . 

As partições são preenchidas com uma seqüência de dados de eventos que contêm o corpo do evento, uma matriz de propriedades definida pelo utilizador e metadados, como o desvio na partição e o respetivo número na sequência de transmissão.

É recomendável equilibrar unidades de produtividade 1:1 e partições para obter uma escala ideal. Uma única partição tem uma entrada garantida e saída de até uma unidade de taxa de transferência. Embora você possa obter uma taxa de transferência mais alta em uma partição, o desempenho não é garantido. É por isso que é altamente recomendável que o número de partições em um hub de eventos seja maior ou igual ao número de unidades de produtividade.

Considerando a taxa de transferência total que você planeja precisar, você sabe o número de unidades de produtividade que precisa e o número mínimo de partições, mas quantas partições você deve ter? Escolha o número de partições com base no paralelismo downstream que você deseja obter, bem como suas necessidades futuras de taxa de transferência. Não há nenhum custo para o número de partições que você tem em um hub de eventos.

Para obter mais informações sobre as partições e o compromisso entre disponibilidade e fiabilidade, veja o [Guia de programação de Hubs de Eventos](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) e o artigo [Disponibilidade e consistência em Hubs de Eventos](../articles/event-hubs/event-hubs-availability-and-consistency.md).
