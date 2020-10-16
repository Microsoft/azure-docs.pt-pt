---
title: Resolver problemas do lado do servidor da Cache do Azure para Redis
description: Aprenda a resolver problemas comuns do lado do servidor com a Cache Azure para Redis, tais como pressão de memória, CPU alto, comandos de longa duração ou limitações de largura de banda.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008921"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Resolver problemas do lado do servidor da Cache do Azure para Redis

Esta secção discute problemas de resolução de problemas que ocorrem devido a uma condição de cache Azure para Redis ou a máquina virtual que o hospeda.

- [Pressão de memória no servidor Redis](#memory-pressure-on-redis-server)
- [Alta carga de utilização do CPU ou servidor](#high-cpu-usage-or-server-load)
- [Comandos de execução longa](#long-running-commands)
- [Limitação da largura de banda do lado do servidor](#server-side-bandwidth-limitation)

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na secção [informações adicionais.](#additional-information)
>

## <a name="memory-pressure-on-redis-server"></a>Pressão de memória no servidor Redis

A pressão de memória no lado do servidor leva a todo o tipo de problemas de desempenho que podem atrasar o processamento de pedidos. Quando a pressão da memória atinge, o sistema pode páginar dados para o disco. Esta _falha de página_ faz com que o sistema abrande significativamente. Existem várias causas possíveis desta pressão de memória:

- A cache é preenchida com dados perto da sua capacidade máxima.
- O Redis está a ver uma grande fragmentação de memória. Esta fragmentação é mais frequentemente causada pelo armazenamento de objetos grandes, uma vez que o Redis é otimizado para pequenos objetos.

Redis expõe duas estatísticas através do comando [INFO](https://redis.io/commands/info) que podem ajudá-lo a identificar este problema: "used_memory" e "used_memory_rss". Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando o portal.

Há várias alterações possíveis que pode fazer para ajudar a manter um bom estado de funcionamento da utilização da memória:

- [Configure uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e desfaça os prazos de validade nas suas chaves. Esta política pode não ser suficiente se tiver fragmentação.
- [Configure um valor maxmemory reservado](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o suficiente para compensar a fragmentação da memória.
- Divida os objetos em cache grandes em objetos relacionados mais pequenos.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas, por exemplo, sobre a memória utilizada, para ser notificado antecipadamente sobre potenciais impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de memória.

## <a name="high-cpu-usage-or-server-load"></a>Alta carga de utilização do CPU ou servidor

Uma carga elevada de servidor ou utilização de CPU significa que o servidor não pode processar os pedidos em tempo útil. O servidor pode ser lento para responder e incapaz de acompanhar as taxas de pedido.

[Monitorize métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) como CPU ou carga do servidor. Tenha cuidado com picos no uso do CPU que correspondam a intervalos de tempo.

Existem várias alterações que pode fazer para mitigar a elevada carga do servidor:

- Investigue o que está a causar picos de CPU, tais como [comandos de longa duração anotados](#long-running-commands) abaixo ou falhas na página devido à alta pressão de memória.
- [Crie alertas](cache-how-to-monitor.md#alerts) em métricas como CPU ou carga de servidor para ser notificado precocemente sobre potenciais impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de CPU.

## <a name="long-running-commands"></a>Comandos de execução longa

Alguns comandos Redis são mais caros para executar do que outros. A [documentação de comandos Redis](https://redis.io/commands) mostra a complexidade do tempo de cada comando. Como o processamento do comando redis é de linha única, um comando que leva tempo a executar bloqueará todos os outros que vierem atrás dele. Deve rever os comandos que está a emitir ao seu servidor Redis para compreender os seus impactos de desempenho. Por exemplo, o comando [KEYS](https://redis.io/commands/keys) é frequentemente utilizado sem saber que é uma operação O(N). Pode evitar CHAVES utilizando [SCAN](https://redis.io/commands/scan) para reduzir os picos de CPU.

Utilizando o comando [SLOWLOG,](https://redis.io/commands/slowlog) pode medir comandos dispendiosos que estão a ser executados contra o servidor.

## <a name="server-side-bandwidth-limitation"></a>Limitação da largura de banda do lado do servidor

Diferentes tamanhos de cache têm diferentes capacidades de largura de banda de rede. Se o servidor exceder a largura de banda disponível, os dados não serão enviados para o cliente tão rapidamente. Os pedidos dos clientes podem ser cronometrar porque o servidor não consegue empurrar os dados para o cliente com rapidez suficiente.

As métricas "Cache Read" e "Cache Write" podem ser usadas para ver a quantidade de largura de banda do lado do servidor que está a ser utilizada. Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no portal.

Para mitigar situações em que a utilização da largura de banda da rede está próxima da capacidade máxima:

- Alterar o comportamento de chamada do cliente para reduzir a procura de rede.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas, por exemplo, leitura de cache ou escrita de cache para ser notificado antecipadamente sobre potenciais impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de largura de banda de rede.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do cliente da Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Escolher o escalão certo](cache-overview.md#choosing-the-right-tier)
- [Como posso comparar e testar o desempenho da minha cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso comandar o Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
