---
title: Troubleshoot Azure Cache para problemas do lado do servidor Redis
description: Aprenda a resolver problemas comuns do lado do servidor com o Azure Cache para Redis, tais como pressão de memória, CPU alta, comandos de longa duração ou limitações de largura de banda.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277937"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Troubleshoot Azure Cache para problemas do lado do servidor Redis

Esta secção discute problemas de resolução de problemas que ocorrem devido a uma condição num Azure Cache para Redis ou nas máquinas virtuais que o hospedam.

- [Pressão de memória no servidor Redis](#memory-pressure-on-redis-server)
- [Alta utilização de CPU ou carga do servidor](#high-cpu-usage-or-server-load)
- [Comandos de execução longa](#long-running-commands)
- [Limitação da largura de banda do lado do servidor](#server-side-bandwidth-limitation)

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para mais informações e instruções, consulte os artigos na secção [informação adicional.](#additional-information)
>

## <a name="memory-pressure-on-redis-server"></a>Pressão de memória no servidor Redis

A pressão de memória no lado do servidor leva a todo o tipo de problemas de desempenho que podem atrasar o processamento de pedidos. Quando a pressão da memória atinge, o sistema pode páginar dados para o disco. Esta falha na _página_ faz com que o sistema abrande significativamente. Existem várias possíveis causas desta pressão de memória:

- A cache está cheia de dados próximos da sua capacidade máxima.
- Redis está a ver uma grande fragmentação de memória. Esta fragmentação é mais frequentemente causada por armazenar objetos grandes, uma vez que redis é otimizado para pequenos objetos.

Redis expõe duas estatísticas através do comando [INFO](https://redis.io/commands/info) que podem ajudá-lo a identificar este problema: "used_memory" e "used_memory_rss". Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando o portal.

Existem várias alterações possíveis que pode fazer para ajudar a manter o uso da memória saudável:

- [Configure uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e detete os prazos de validade nas suas teclas. Esta política pode não ser suficiente se tiver fragmentação.
- [Configure um valor reservado](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) à memória máxima que seja grande o suficiente para compensar a fragmentação da memória.
- Separem os seus grandes objetos em cache em objetos relacionados menores.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas como a memória usada para ser notificada precocemente sobre potenciais impactos.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de memória.

## <a name="high-cpu-usage-or-server-load"></a>Alta utilização de CPU ou carga do servidor

Uma carga de servidor elevada ou utilização de CPU significa que o servidor não pode processar pedidos em tempo útil. O servidor pode ser lento para responder e incapaz de acompanhar as taxas de pedido.

[Monitorize métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) como CPU ou carga do servidor. Tenha em atenção os picos no uso do CPU que correspondam aos intervalos.

Existem várias alterações que pode fazer para mitigar a alta carga do servidor:

- Investigue o que está a causar picos de CPU, tais como [comandos de longa duração notados](#long-running-commands) abaixo ou falhas de página devido à alta pressão de memória.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas como CPU ou carga do servidor para ser notificado precocemente sobre potenciais impactos.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de CPU.

## <a name="long-running-commands"></a>Comandos de longa duração

Alguns comandos Redis são mais caros de executar do que outros. A [documentação dos redis](https://redis.io/commands) mostra a complexidade do tempo de cada comando. Como o processamento de comando redis é de roscar, um comando que leva tempo a executar bloqueará todos os outros que o perseguem. Deve rever os comandos que está a emitir para o seu servidor Redis para compreender os seus impactos de desempenho. Por exemplo, o comando [KEYS](https://redis.io/commands/keys) é frequentemente usado sem saber que é uma operação O(N). Pode evitar chaves utilizando o [SCAN](https://redis.io/commands/scan) para reduzir os picos de CPU.

Utilizando o comando [SLOWLOG,](https://redis.io/commands/slowlog) pode medir comandos caros que estão a ser executados contra o servidor.

## <a name="server-side-bandwidth-limitation"></a>Limitação da largura de banda do lado do servidor

Diferentes tamanhos de cache têm diferentes capacidades de largura de banda de rede. Se o servidor exceder a largura de banda disponível, os dados não serão enviados para o cliente tão rapidamente. Os pedidos dos clientes podem sair porque o servidor não consegue empurrar dados para o cliente com rapidez suficiente.

As métricas "Cache Read" e "Cache Write" podem ser usadas para ver a largura de banda do lado do servidor. Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no portal.

Para mitigar situações em que a utilização da largura de banda da rede esteja próxima da capacidade máxima:

- Mude o comportamento da chamada do cliente para reduzir a procura da rede.
- [Crie alertas](cache-how-to-monitor.md#alerts) sobre métricas como leitura de cache ou cache escrevam para serem notificados precocemente sobre potenciais impactos.
- [Escala](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de largura de banda de rede.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do cliente da Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Que Azure Cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como posso comparar e testar o desempenho da minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorizar o Azure Cache para redis](cache-how-to-monitor.md)
- [Como posso executar os comandos redis?](cache-faq.md#how-can-i-run-redis-commands)
