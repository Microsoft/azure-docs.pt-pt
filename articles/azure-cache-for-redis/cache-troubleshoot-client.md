---
title: Resolver problemas do lado do cliente da Cache do Azure para Redis
description: Aprenda a resolver problemas comuns do lado do cliente com a Azure Cache para Redis, como a pressão de memória do cliente Redis, a explosão de tráfego, o ALTO CPU, a largura de banda limitada, os pedidos grandes ou o grande tamanho de resposta.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008972"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Resolver problemas do lado do cliente da Cache do Azure para Redis

Esta secção discute problemas de resolução de problemas que ocorrem devido a uma condição no cliente Redis que a sua aplicação utiliza.

- [Pressão de memória no cliente Redis](#memory-pressure-on-redis-client)
- [Explosão de trânsito](#traffic-burst)
- [Uso de CPU de alto cliente](#high-client-cpu-usage)
- [Limitação da largura de banda do lado do cliente](#client-side-bandwidth-limitation)
- [Grande pedido ou tamanho de resposta](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressão de memória no cliente Redis

A pressão de memória na máquina do cliente leva a todo o tipo de problemas de desempenho que podem atrasar o processamento de respostas a partir da cache. Quando a pressão da memória atinge, o sistema pode páginar dados para o disco. Esta _falha de página_ faz com que o sistema abrande significativamente.

Para detetar a pressão da memória no cliente:

- Monitorize a utilização da memória na máquina para se certificar de que não excede a memória disponível.
- Monitorize o balcão de desempenho do `Page Faults/Sec` cliente. Durante o funcionamento normal, a maioria dos sistemas tem algumas falhas de página. Os picos nas falhas de paginação correspondentes a tempos limite de pedidos podem indicar a pressão da memória.

A alta pressão de memória sobre o cliente pode ser atenuada de várias maneiras:

- Procure nos seus padrões de utilização da memória para reduzir o consumo de memória no cliente.
- Atualize o VM do seu cliente para um tamanho maior com mais memória.

## <a name="traffic-burst"></a>Explosão de trânsito

Explosões de tráfego `ThreadPool` combinadas com configurações deficientes podem resultar em atrasos no processamento de dados já enviados pelo Redis Server, mas ainda não consumidos do lado do cliente.

Monitorize como as suas `ThreadPool` estatísticas mudam ao longo do tempo usando um [exemplo `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Pode utilizar  `TimeoutException` mensagens de StackExchange.Redis como abaixo para investigar mais aprofundadamente:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

Na exceção anterior, existem várias questões interessantes:

- Note que na `IOCP` secção e na secção tem um valor superior ao `WORKER` `Busy` `Min` valor. Esta diferença significa que as suas `ThreadPool` definições precisam de ser ajustadas.
- Também pode `in: 64221` ver. Este valor indica que 64.211 bytes foram recebidos na camada de tomada de núcleo do cliente, mas não foram lidos pela aplicação. Esta diferença normalmente significa que a sua aplicação (por exemplo, StackExchange.Redis) não está a ler dados da rede tão rapidamente quanto o servidor está a enviá-los para si.

Pode [configurar as suas `ThreadPool` Definições](cache-management-faq.md#important-details-about-threadpool-growth) para se certificar de que a sua piscina de rosca se escala rapidamente em cenários de explosão.

## <a name="high-client-cpu-usage"></a>Uso de CPU de alto cliente

O uso de CPU de alto cliente indica que o sistema não consegue acompanhar o trabalho que lhe foi pedido. Mesmo que a cache tenha enviado a resposta rapidamente, o cliente pode não processar a resposta em tempo útil.

Monitorize a utilização do CPU em todo o sistema do cliente utilizando métricas disponíveis no portal Azure ou através de contadores de desempenho na máquina. Tenha cuidado para não monitorizar o *processo* cpu porque um único processo pode ter uma utilização baixa do CPU, mas o CPU em todo o sistema pode ser elevado. Tenha cuidado com picos no uso do CPU que correspondam a intervalos de tempo. O CPU elevado também pode causar `in: XXX` valores elevados em `TimeoutException` mensagens de erro, conforme descrito na secção [de rutura de tráfego.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 e mais tarde inclui a `local-cpu` métrica em `TimeoutException` mensagens de erro. Certifique-se de que utiliza a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem bugs constantemente a ser corrigidos no código para torná-lo mais robusto para os intervalos de tempo, pelo que ter a versão mais recente é importante.
>

Para mitigar o elevado uso do CPU de um cliente:

- Investigue o que está a causar picos de CPU.
- Atualize o seu cliente para um tamanho VM maior com mais capacidade de CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitação da largura de banda do lado do cliente

Dependendo da arquitetura das máquinas cliente, podem ter limitações na largura de banda de rede que têm disponíveis. Se o cliente exceder a largura de banda disponível sobrecarregando a capacidade da rede, então os dados não são processados do lado do cliente tão rapidamente quanto o servidor está a enviá-lo. Esta situação pode levar a intervalos.

Monitorize como a sua utilização de largura de banda muda ao longo do tempo usando [um exemplo `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Este código pode não funcionar com sucesso em alguns ambientes com permissões restritas (como web sites Azure).

Para mitigar, reduza o consumo de largura de banda de rede ou aumente o tamanho vm do cliente para um com mais capacidade de rede.

## <a name="large-request-or-response-size"></a>Grande pedido ou tamanho de resposta

Um grande pedido/resposta pode causar intervalos de tempo. Como exemplo, suponha que o seu valor de tempo limite configurado no seu cliente é de 1 segundo. A sua aplicação solicita duas teclas (por exemplo, 'A' e 'B') ao mesmo tempo (utilizando a mesma ligação de rede física). A maioria dos clientes apoia o pedido de "pipelining", onde ambos os pedidos 'A' e 'B' são enviados um após o outro sem esperar pelas suas respostas. O servidor envia as respostas de volta na mesma ordem. Se a resposta 'A' for grande, pode comer a maior parte do tempo limite para pedidos posteriores.

No exemplo seguinte, o pedido 'A' e 'B' são enviados rapidamente para o servidor. O servidor começa a enviar as respostas 'A' e 'B' rapidamente. Devido aos tempos de transferência de dados, a resposta 'B' deve aguardar a resposta 'A' vezes fora, mesmo que o servidor tenha respondido rapidamente.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Este pedido/resposta é difícil de medir. Pode instrumentar o seu código de cliente para rastrear grandes pedidos e respostas.

As resoluções para grandes tamanhos de resposta são variadas, mas incluem:

1. Otimize a sua aplicação para um grande número de pequenos valores, em vez de alguns valores grandes.
    - A solução preferida é dividir os seus dados em valores menores relacionados.
    - Ver o post [Qual é a gama de tamanho de valor ideal para redis? 100 KB é muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para mais detalhes sobre o porquê de valores menores serem recomendados.
1. Aumente o tamanho do seu VM para obter maiores capacidades de largura de banda
    - Mais largura de banda no seu cliente ou servidor VM pode reduzir os tempos de transferência de dados para respostas maiores.
    - Compare a sua utilização atual da rede em ambas as máquinas com os limites do seu tamanho VM atual. Mais largura de banda apenas no servidor ou apenas no cliente pode não ser suficiente.
1. Aumente o número de objetos de ligação que a sua aplicação utiliza.
    - Utilize uma abordagem de rodapé para fazer pedidos sobre diferentes objetos de ligação.

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como posso comparar e testar o desempenho da minha cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
