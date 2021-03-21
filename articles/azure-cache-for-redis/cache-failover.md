---
title: Ativação pós-falha e aplicação de patches – Cache de Redis do Azure
description: Saiba mais sobre failover, patching e o processo de atualização para Azure Cache para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: cc7c70fa2e7131f09f621e992d537e0b120061ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210738"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e patching para Azure Cache para Redis

Para construir aplicações resilientes e bem sucedidas de clientes, é fundamental compreender o failover no contexto do serviço Azure Cache para o serviço Redis. Uma falha pode fazer parte de operações de gestão planeadas, ou pode ser causada por falhas não planeadas de hardware ou rede. Um uso comum de falha de cache vem quando o serviço de gestão corrige a Cache Azure para binários Redis. Este artigo cobre o que é um failover, como ocorre durante o patching, e como construir uma aplicação resiliente do cliente.

## <a name="what-is-a-failover"></a>O que é um fracasso?

Vamos começar com uma visão geral do failover para Azure Cache para Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Um resumo rápido da arquitetura cache

Uma cache é construída de várias máquinas virtuais com endereços IP separados e privados. Cada máquina virtual, também conhecida como nó, está ligada a um equilibrador de carga partilhado com um único endereço IP virtual. Cada nó executa o processo do servidor Redis e é acessível através do nome de anfitrião e das portas Redis. Cada nó é considerado um nó primário ou um nó de réplica. Quando uma aplicação do cliente se liga a uma cache, o seu tráfego passa por este equilibrador de carga e é automaticamente encaminhado para o nó primário.

Numa cache básica, o nó único é sempre primário. Numa cache Standard ou Premium, existem dois nós: um é escolhido como o principal e o outro é a réplica. Como os caches Standard e Premium têm múltiplos nós, um nó pode estar indisponível enquanto o outro continua a processar pedidos. Caches agrupados são feitos de muitos fragmentos, cada um com nódoas primárias e réplicas distintas. Um fragmento pode estar em baixo enquanto os outros permanecem disponíveis.

> [!NOTE]
> Uma cache Basic não tem múltiplos nós e não oferece um acordo de nível de serviço (SLA) para a sua disponibilidade. As caches básicas são recomendadas apenas para fins de desenvolvimento e teste. Utilize uma cache Standard ou Premium para uma implementação multi-nós, para aumentar a disponibilidade.

### <a name="explanation-of-a-failover"></a>Explicação de um fracasso

Um failover ocorre quando um nó réplica se promove para se tornar um nó primário, e o antigo nó primário fecha as ligações existentes. Depois que o nó primário volta a subir, nota a mudança de papéis e despromo-se para se tornar uma réplica. Em seguida, liga-se ao novo primário e sincroniza os dados. Um fracasso pode ser planeado ou não planeado.

Uma *falha planeada* ocorre durante as atualizações do sistema, tais como patching Redis ou atualizações de SO, e operações de gestão, tais como dimensionamento e reboot. Como os nós recebem aviso prévio da atualização, podem trocar cooperativamente funções e atualizar rapidamente o equilibrador de carga da alteração. Um failover planeado normalmente termina em menos de 1 segundo.

Uma *falha não planeada* pode acontecer devido a falha de hardware, falha de rede ou outras interrupções inesperadas no nó primário. O nó réplica promove-se ao primário, mas o processo demora mais tempo. Um nó de réplica deve primeiro detetar que o nó primário não está disponível antes de poder iniciar o processo de failover. O nó de réplica também deve verificar se esta falha não planeada não é transitória ou local, para evitar uma falha desnecessária. Este atraso na deteção significa que uma falha não planeada normalmente termina dentro de 10 a 15 segundos.

## <a name="how-does-patching-occur"></a>Como é que o remendos ocorre?

O serviço Azure Cache for Redis atualiza regularmente o seu cache com as mais recentes funcionalidades e correções da plataforma. Para remendar uma cache, o serviço segue estes passos:

1. O serviço de gestão seleciona um nó a ser remendado.
1. Se o nó selecionado for um nó primário, o nó de réplica correspondente promove-se cooperativamente. Esta promoção é considerada uma falha planeada.
1. O nó selecionado reinicia para receber as novas alterações e volta a ser um nó de réplica.
1. O nó de réplica liga-se ao nó primário e sincroniza os dados.
1. Quando a sincronização de dados estiver concluída, o processo de remendação repete-se para os restantes nós.

Como o patching é um fracasso planeado, o nó de réplica rapidamente se promove para se tornar um primário e começa a servir pedidos e novas ligações. Caches básicos não têm um nó de réplica e estão indisponíveis até que a atualização esteja completa. Cada fragmento de uma cache agrupada é remendado separadamente e não fecha as ligações a outro fragmento.

> [!IMPORTANT]
> Os nós são remendados um de cada vez para evitar a perda de dados. Caches básicos terão perda de dados. Caches agrupados são remendados um fragmento de cada vez.

Várias caches no mesmo grupo de recursos e região também são remendados um de cada vez.  Caches que se encontram em diferentes grupos de recursos ou diferentes regiões podem ser remendados simultaneamente.

Como a sincronização total de dados ocorre antes da repetição do processo, é improvável que a perda de dados ocorra quando se utiliza uma cache Standard ou Premium. Pode ainda proteger-se contra a perda de dados [exportando](cache-how-to-import-export-data.md#export) dados e permitindo a [persistência](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Carga de cache adicional

Sempre que ocorre uma falha, os caches Standard e Premium precisam de replicar dados de um nó para o outro. Esta replicação causa algum aumento de carga tanto na memória do servidor como no CPU. Se a instância de cache já estiver fortemente carregada, as aplicações do cliente poderão sofrer um aumento da latência. Em casos extremos, as aplicações dos clientes podem receber exceções de tempo. Para ajudar a atenuar o impacto desta carga adicional, [configufique](cache-configure.md#memory-policies) a regulação da `maxmemory-reserved` cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>Como é que uma falha afeta a minha aplicação de cliente?

O número de erros verificados pela aplicação do cliente depende de quantas operações estavam pendentes nessa ligação no momento da falha. Qualquer ligação que seja encaminhada através do nó que fechou as suas ligações verá erros. Muitas bibliotecas de clientes podem lançar diferentes tipos de erros quando as ligações quebram, incluindo exceções de tempo, exceções de ligação ou exceções à tomada. O número e tipo de exceções depende de onde na via de código o pedido é quando a cache fecha as suas ligações. Por exemplo, uma operação que envia um pedido mas não recebeu uma resposta quando o resultado ocorre pode obter uma exceção de tempo de paragem. Novos pedidos no objeto de ligação fechado recebem exceções de ligação até que a reconexão aconteça com sucesso.

A maioria das bibliotecas de clientes tentam reconectar-se com a cache se estiverem configuradas para o fazer. No entanto, os insetos imprevistos podem ocasionalmente colocar os objetos da biblioteca num estado irrecuperável. Se os erros persistirem por mais tempo do que uma quantidade pré-configurada, o objeto de ligação deve ser recriado. Em Microsoft.NET e outras línguas orientadas a objetos, recriar a ligação sem reiniciar a aplicação pode ser realizado utilizando [um \<T\> padrão preguiçoso](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Como faço a minha candidatura resiliente?

Como não pode evitar falhas completamente, escreva os pedidos do seu cliente para resiliência a quebras de ligação e pedidos falhados. Embora a maioria das bibliotecas de clientes se reconecte automaticamente ao ponto final da cache, poucas delas tentam reeferição de pedidos falhados. Dependendo do cenário de aplicação, pode fazer sentido usar a lógica de re-tentar com o backoff.

Para testar a resiliência de uma aplicação do cliente, utilize um [reboot](cache-administration.md#reboot) como gatilho manual para quebras de ligação. Além disso, recomendamos que [marque atualizações](cache-administration.md#schedule-updates) numa cache. Informe o serviço de gestão para aplicar patches de tempo de execução Redis durante as janelas semanais especificadas. Estas janelas são normalmente períodos em que o tráfego de aplicações do cliente é baixo, para evitar potenciais incidentes.

### <a name="can-i-be-notified-in-advance-of-a-planned-maintenance"></a>Posso ser notificado antes de uma manutenção planeada?

A Azure Cache para Redis publica agora notificações num canal de publicação/subscrição chamado [AzureRedisEvents](https://github.com/Azure/AzureCacheForRedis/blob/main/AzureRedisEvents.md) cerca de 30 segundos antes das atualizações planeadas. Estas são notificações de tempo de execução, e são construídas especialmente para aplicações que podem usar disjuntores para contornar os comandos de cache ou tampão, por exemplo, durante as atualizações planeadas. Não é um mecanismo que possa notificá-lo com dias ou horas de antecedência.

### <a name="client-network-configuration-changes"></a>Alterações na configuração da rede do cliente

Certas alterações na configuração da rede do lado do cliente podem desencadear erros de "Nenhuma ligação disponível". Tais alterações podem incluir:

- Trocando o endereço IP virtual de uma aplicação de cliente entre slots de produção e encenação.
- Dimensionando o tamanho ou número de casos da sua aplicação.

Tais alterações podem causar um problema de conectividade que dura menos de um minuto. A sua aplicação ao cliente provavelmente perderá a sua ligação a outros recursos de rede externos, além do serviço Azure Cache para o redis.

## <a name="next-steps"></a>Passos seguintes

- [Agendar atualizações](cache-administration.md#schedule-updates) para o seu cache.
- Resiliência da aplicação de teste utilizando um [reboot](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) reservas e políticas de memória.
