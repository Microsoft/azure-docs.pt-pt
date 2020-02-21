---
title: Troubleshoot Azure Cache para problemas do lado do cliente redis
description: Saiba como resolver problemas comuns do lado do cliente com o Azure Cache para redis, tais como a pressão de memória do cliente Redis, explosão de tráfego, alta CPU, largura de banda limitada, grandes pedidos ou tamanho de resposta grande.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523344"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Troubleshoot Azure Cache para problemas do lado do cliente redis

Esta secção discute problemas de resolução de problemas que ocorrem devido a uma condição no cliente Redis que a sua aplicação utiliza.

- [Pressão de memória sobre o cliente Redis](#memory-pressure-on-redis-client)
- [Explosão de trânsito](#traffic-burst)
- [Alta utilização do CPU do cliente](#high-client-cpu-usage)
- [Limitação da largura de banda do lado do cliente](#client-side-bandwidth-limitation)
- [Grande tamanho de pedido ou resposta](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressão de memória sobre o cliente Redis

A pressão de memória na máquina cliente leva a todo o tipo de problemas de desempenho que podem atrasar o processamento de respostas a partir da cache. Quando a pressão da memória atinge, o sistema pode páginar dados para o disco. Esta falha na _página_ faz com que o sistema abrande significativamente.

Para detetar pressão de memória sobre o cliente:

- Monitorize o uso da memória na máquina para se certificar de que não excede a memória disponível.
- Monitorize o balcão de desempenho `Page Faults/Sec` do cliente. Durante o funcionamento normal, a maioria dos sistemas tem algumas falhas de página. Os picos nas falhas de página correspondentes aos prazos de pedido podem indicar pressão de memória.

A alta pressão de memória sobre o cliente pode ser atenuada de várias formas:

- Investigue os padrões de utilização da sua memória para reduzir o consumo de memória no cliente.
- Atualize o vM do seu cliente para um tamanho maior com mais memória.

## <a name="traffic-burst"></a>Explosão de trânsito

Explosões de tráfego combinadas com configurações de `ThreadPool` deficientes podem resultar em atrasos no processamento de dados já enviados pelo Redis Server, mas ainda não consumidos do lado do cliente.

Monitorize como as suas estatísticas `ThreadPool` mudam ao longo do tempo usando [um exemplo `ThreadPoolLogger`](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Pode utilizar `TimeoutException` mensagens do StackExchange.Redis como abaixo para investigar mais aprofundadamente:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Na exceção anterior, existem várias questões interessantes:

- Note que na secção `IOCP` e na secção `WORKER` tem um valor `Busy` superior ao valor `Min`. Esta diferença significa que as definições de `ThreadPool` precisam de ser ajustadas.
- Também pode ver `in: 64221`. Este valor indica que 64.211 bytes foram recebidos na camada de tomada de núcleo do cliente, mas não foram lidos pela aplicação. Esta diferença normalmente significa que a sua aplicação (por exemplo, StackExchange.Redis) não está a ler dados da rede tão rapidamente quanto o servidor está a enviá-lo para si.

Pode [configurar as definições de `ThreadPool`](cache-faq.md#important-details-about-threadpool-growth) para se certificar de que a sua piscina de rosca se escala rapidamente em cenários de explosão.

## <a name="high-client-cpu-usage"></a>Alta utilização do CPU do cliente

O alto uso do CPU do cliente indica que o sistema não consegue acompanhar o trabalho que lhe foi pedido. Mesmo que a cache tenha enviado a resposta rapidamente, o cliente pode não conseguir processar a resposta em tempo útil.

Monitorize o uso de CPU em todo o sistema do cliente utilizando métricas disponíveis no portal Azure ou através de contadores de desempenho na máquina. Tenha cuidado para não monitorizar o CPU do *processo* porque um único processo pode ter uma utilização baixa do CPU, mas o CPU em todo o sistema pode ser elevado. Tenha em atenção os picos no uso do CPU que correspondam aos intervalos. O ALTO CPU também pode causar elevados valores `in: XXX` em mensagens de erro `TimeoutException`, conforme descrito na secção [de explosão](#traffic-burst) de tráfego.

> [!NOTE]
> StackExchange.Redis 1.1.603 e mais tarde inclui a métrica `local-cpu` em `TimeoutException` mensagens de erro. Certifique-se de que utiliza a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem bugs constantemente a ser corrigidos no código para torná-lo mais robusto para os intervalos de tempo, pelo que ter a versão mais recente é importante.
>

Para mitigar o elevado uso de CPU de um cliente:

- Investigue o que está a causar picos de CPU.
- Atualize o seu cliente para um tamanho VM maior com mais capacidade de CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitação da largura de banda do lado do cliente

Dependendo da arquitetura das máquinas clientes, podem ter limitações na largura de banda da rede que têm disponível. Se o cliente exceder a largura de banda disponível sobrecarregando a capacidade da rede, os dados não são processados no lado do cliente tão rapidamente quanto o servidor está a enviá-lo. Esta situação pode levar a intervalos.

Monitorize como a utilização da largura de banda muda ao longo do tempo utilizando [um exemplo `BandwidthLogger`](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Este código pode não funcionar com sucesso em alguns ambientes com permissões restritas (como web sites do Azure).

Para mitigar, reduza o consumo de largura de banda da rede ou aumente o tamanho do VM do cliente para um com mais capacidade de rede.

## <a name="large-request-or-response-size"></a>Grande pedido ou tamanho de resposta

Um grande pedido/resposta pode causar intervalos. Como exemplo, suponha que o seu valor de tempo configurado no seu cliente seja de 1 segundo. A sua aplicação solicita duas teclas (por exemplo, 'A' e 'B') ao mesmo tempo (utilizando a mesma ligação física à rede). A maioria dos clientes apoia o pedido de "pipelining", onde ambos os pedidos 'A' e 'B' são enviados um após o outro sem esperar pelas suas respostas. O servidor envia as respostas de volta na mesma ordem. Se a resposta 'A' for grande, pode comer a maior parte do tempo para pedidos posteriores.

No exemplo seguinte, os pedidos 'A' e 'B' são enviados rapidamente para o servidor. O servidor começa a enviar respostas 'A' e 'B' rapidamente. Devido aos tempos de transferência de dados, a resposta 'B' deve esperar atrás da resposta 'A' vezes fora, mesmo que o servidor tenha respondido rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Este pedido/resposta é difícil de medir. Pode instrumentalar o seu código de cliente para rastrear grandes pedidos e respostas.

As resoluções para grandes dimensões de resposta são variadas, mas incluem:

1. Otimize a sua aplicação para um grande número de pequenos valores, em vez de alguns grandes valores.
    - A solução preferida é dividir os seus dados em valores menores relacionados.
    - Ver o post [Qual é a gama de tamanho ideal para redis? 100 KB é muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para detalhes sobre por que os valores menores são recomendados.
1. Aumente o tamanho do seu VM para obter maiores capacidades de largura de banda
    - Mais largura de banda no seu cliente ou vM do servidor pode reduzir os tempos de transferência de dados para respostas maiores.
    - Compare o uso atual da rede em ambas as máquinas com os limites do tamanho atual do VM. Mais largura de banda apenas no servidor ou apenas no cliente pode não ser suficiente.
1. Aumente o número de objetos de ligação que a sua aplicação utiliza.
    - Utilize uma abordagem de robin redondo para fazer pedidos sobre diferentes objetos de ligação.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como posso comparar e testar o desempenho da minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
