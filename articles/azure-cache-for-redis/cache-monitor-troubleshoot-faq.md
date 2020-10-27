---
title: Cache Azure para monitorização e resolução de problemas de FAQs
description: Aprenda as respostas a perguntas comuns que o ajudam a monitorizar e resolver problemas Azure Cache para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537529"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Cache Azure para monitorização e resolução de problemas de FAQs
Este artigo fornece respostas a perguntas comuns sobre como monitorizar e resolver problemas Azure Cache para Redis.

## <a name="common-questions-and-answers"></a>Perguntas comuns e respostas
Esta secção abrange as seguintes PERGUNTAS Frequentes:

* [Como monitorizo a saúde e o desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que estou a ver intervalos?](#why-am-i-seeing-timeouts)
* [Porque é que o meu cliente foi desligado da cache?](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorizo a saúde e o desempenho da minha cache?
O Microsoft Azure Cache para instâncias Redis pode ser monitorizado no [portal Azure](https://portal.azure.com). Pode ver métricas, gráficos de métricas de pin para o Startboard, personalizar a data e a gama de horários dos gráficos de monitorização, adicionar e remover métricas dos gráficos e definir alertas quando certas condições são satisfeitas. Para obter mais informações, consulte [o Monitor Azure Cache para Redis](cache-how-to-monitor.md).

O menu Azure Cache for Redis **Resource** também contém várias ferramentas para monitorizar e resolver problemas nas suas caches.

* **Diagnosticar e resolver problemas** fornece informações sobre questões e estratégias comuns para resolvê-los.
* **A saúde dos recursos** observa o seu recurso e diz-lhe se está a funcionar como esperado. Para obter mais informações sobre o serviço de saúde Azure Resource, consulte [a visão geral da saúde do Recurso Azure.](../service-health/resource-health-overview.md)
* **O novo pedido de apoio** oferece opções para abrir um pedido de apoio para a sua cache.

Estas ferramentas permitem-lhe monitorizar a saúde do seu Azure Cache para instâncias Redis e ajudá-lo a gerir as suas aplicações de cache. Para obter mais informações, consulte a secção "Apoiar & configurações de resolução de problemas" de [Como configurar a Cache Azure para Redis](cache-configure.md).

### <a name="why-am-i-seeing-timeouts"></a>Por que estou a ver intervalos?
Os intervalos acontecem no cliente que usas para falar com o Redis. Quando um comando é enviado para o servidor Redis, o comando é feito na fila e o servidor Redis eventualmente pega no comando e executa-o. No entanto, o cliente pode sair do tempo durante este processo e se o fizer uma exceção é levantada no lado da chamada. Para obter mais informações sobre problemas de tempo de resolução de problemas, consulte [a resolução de problemas do lado do cliente](cache-troubleshoot-client.md) e as [exceções de tempo limite de stackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Porque é que o meu cliente foi desligado da cache?
Os seguintes são uma razão comum para uma desconexão de cache.

* Causas do lado do cliente
  * O pedido do cliente foi redistribuído.
  * A aplicação do cliente realizou uma operação de escala.
    * No caso dos Serviços cloud ou web Apps, isso pode ser devido a autoscalagem.
  * A camada de rede do lado do cliente mudou.
  * Ocorreram erros transitórios no cliente ou nos nós de rede entre o cliente e o servidor.
  * Os limites do limiar da largura de banda foram atingidos.
  * As operações ligadas à CPU demoraram muito tempo a ser concluídas.
* Causas do lado do servidor
  * Na oferta de cache padrão, o serviço Azure Cache for Redis iniciou uma falha do nó primário para o nó de réplica.
  * Azure estava remendando o caso onde a cache foi implantada
    * Isto pode ser para atualizações do servidor Redis ou manutenção geral de VM.


## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização e resolução de problemas da sua Cache Azure para instâncias Redis, consulte [Como monitorizar a Cache Azure para Redis](cache-how-to-monitor.md) e os vários guias de resolução de problemas.

Saiba mais sobre [outros Azure Cache para Redis FAQs](cache-faq.md).