---
title: Ativação pós-falha e aplicação de patches – Cache de Redis do Azure
description: Aprenda sobre o failover, patching e o processo de atualização para Azure Cache para Redis.
author: asasine
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 6ff33bd594181aabc4fd7d55ce33f780a0d06086
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74122199"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e remendar para Azure Cache para Redis

Para construir aplicações resilientes e bem-sucedidas para clientes, é fundamental compreender a falha no contexto do serviço Azure Cache for Redis. Uma falha pode fazer parte de operações de gestão planeadas, ou pode ser causada por falhas de hardware ou rede não planeadas. Um uso comum de cache failover vem quando o serviço de gestão remende o Cache Azure para binários Redis. Este artigo cobre o que é uma falha, como ocorre durante o patching, e como construir uma aplicação resiliente do cliente.

## <a name="what-is-a-failover"></a>O que é uma falha?

Vamos começar com uma visão geral do fracasso para Azure Cache para Redis.

### <a name="a-quick-summary-of-cache-architecture"></a>Um resumo rápido da arquitetura cache

Uma cache é construída de múltiplas máquinas virtuais com endereços IP separados e privados. Cada máquina virtual, também conhecida como nó, está ligada a um equilibrista de carga partilhado com um único endereço IP virtual. Cada nó executa o processo do servidor Redis e é acessível através do nome anfitrião e das portas Redis. Cada nó é considerado um mestre ou um nó de réplica. Quando uma aplicação do cliente se conecta a uma cache, o seu tráfego passa por este equilibrista de carga e é automaticamente encaminhado para o nó principal.

Numa cache básica, o nó único é sempre um mestre. Numa cache Standard ou Premium, existem dois nós: um é escolhido como mestre e o outro é a réplica. Como os caches Standard e Premium têm vários nós, um nó pode estar indisponível enquanto o outro continua a processar pedidos. Caches agrupados são feitos de muitos fragmentos, cada um com nós de mestre e réplica distintos. Um fragmento pode estar em baixo enquanto os outros permanecem disponíveis.

> [!NOTE]
> Uma cache Básica não tem vários nós e não oferece um acordo de nível de serviço (SLA) para a sua disponibilidade. Os caches básicos são recomendados apenas para fins de desenvolvimento e teste. Utilize uma cache Standard ou Premium para uma implantação de vários nódosos, para aumentar a disponibilidade.

### <a name="explanation-of-a-failover"></a>Explicação de uma falha

Uma falha ocorre quando um nó de réplica se promove a tornar-se um nó mestre, e o velho nó mestre fecha as ligações existentes. Depois do nó mestre voltar, nota a mudança de papéis e despromova-se para se tornar uma réplica. Em seguida, liga-se ao novo mestre e sincroniza os dados. Uma falha pode ser planeada ou não planeada.

Uma *falha planeada* ocorre durante as atualizações do sistema, tais como remendos Redis ou atualizações de SO, e operações de gestão, tais como escala e reinicialização. Como os nós recebem aviso prévio da atualização, podem trocar de funções cooperativamente e atualizar rapidamente o equilibrador de carga da alteração. Uma falha planeada normalmente termina em menos de 1 segundo.

Uma *falha não planeada* pode acontecer devido a falha de hardware, falha de rede ou outras falhas inesperadas no nó principal. O nó de réplica promove-se a dominar, mas o processo demora mais tempo. Um nó de réplica deve primeiro detetar que o seu nó principal não está disponível antes de poder iniciar o processo de failover. O nó de réplica também deve verificar se esta falha não planeada não é transitória ou local, para evitar uma falha desnecessária. Este atraso na deteção significa que uma falha não planeada normalmente termina dentro de 10 a 15 segundos.

## <a name="how-does-patching-occur"></a>Como é que o remendo ocorre?

O serviço Azure Cache for Redis atualiza regularmente o seu cache com as mais recentes funcionalidades e correções da plataforma. Para remendar uma cache, o serviço segue estes passos:

1. O serviço de gestão seleciona um nó a ser remendado.
1. Se o nó selecionado for um nó mestre, o nó de réplica correspondente promove-se cooperativamente. Esta promoção é considerada uma falha planeada.
1. O nó selecionado reinicia para tomar as novas alterações e volta a ser um nó de réplica.
1. O nó de réplica liga-se ao nó principal e sincroniza os dados.
1. Quando a sincronização de dados estiver completa, o processo de remendo repete-se para os restantes nós.

Como o patching é uma falha planeada, o nó de réplica rapidamente se promove para se tornar um mestre e começa a servir pedidos e novas conexões. Os caches básicos não têm um nó de réplica e estão indisponíveis até que a atualização esteja completa. Cada fragmento de uma cache agrupada é remendado separadamente e não fecha as ligações a outro fragmento.

> [!IMPORTANT]
> Os nódosos são remendados um de cada vez para evitar a perda de dados. Caches básicos terão perda de dados. Caches agrupados são remendados um fragmento de cada vez.

Vários caches no mesmo grupo de recursos e região também são remendados um de cada vez.  Caches que se encontram em diferentes grupos de recursos ou regiões diferentes podem ser remendados simultaneamente.

Uma vez que a sincronização completa de dados ocorre antes da repetição do processo, é improvável que a perda de dados ocorra quando se utiliza uma cache Standard ou Premium. Pode ainda prevenir-se contra a perda de dados [exportando](cache-how-to-import-export-data.md#export) dados e permitindo a [persistência](cache-how-to-premium-persistence.md).

## <a name="additional-cache-load"></a>Carga adicional de cache

Sempre que ocorre uma falha, os caches Standard e Premium precisam de replicar dados de um nó para o outro. Esta replicação causa algum aumento de carga tanto na memória do servidor como no CPU. Se a instância de cache já estiver fortemente carregada, as aplicações do cliente podem experimentar um aumento da latência. Em casos extremos, as aplicações dos clientes podem receber exceções no prazo. Para ajudar a mitigar o impacto desta carga `maxmemory-reserved` adicional, [configure](cache-configure.md#memory-policies) a definição da cache.

## <a name="how-does-a-failover-affect-my-client-application"></a>Como é que uma falha afeta a aplicação do meu cliente?

O número de erros observados pela aplicação do cliente depende de quantas operações estavam pendentes nessa ligação no momento da falha. Qualquer ligação que seja encaminhada através do nó que fechou as suas ligações verá erros. Muitas bibliotecas de clientes podem lançar diferentes tipos de erros quando as ligações se rompem, incluindo exceções de tempo, exceções à ligação ou exceções à tomada. O número e o tipo de exceções dependem de onde, no caminho do código, o pedido é quando a cache fecha as suas ligações. Por exemplo, uma operação que envia um pedido mas não recebeu uma resposta quando ocorre a falha pode ter uma exceção de tempo- fora. Novos pedidos no objeto de ligação fechado recebem exceções de ligação até que a reconexão aconteça com sucesso.

A maioria das bibliotecas de clientes tenta reconectar-se com a cache se estiverem configuradas para o fazer. No entanto, insetos imprevistos podem ocasionalmente colocar os objetos da biblioteca em um estado irrecuperável. Se persistirem erros por mais tempo do que um período de tempo pré-configurado, o objeto de ligação deve ser recriado. Em Microsoft.NET e outras línguas orientadas para o objeto, recriar a ligação sem reiniciar a aplicação pode ser realizada utilizando [um padrão Lazy\<T\> ](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="how-do-i-make-my-application-resilient"></a>Como faço a minha aplicação resiliente?

Como não pode evitar falhas completamente, escreva os seus pedidos de resiliência ao seu cliente a quebras de ligação e pedidos falhados. Embora a maioria das bibliotecas de clientes reconecte-se automaticamente ao ponto final do cache, poucos deles tentam rejulgar pedidos falhados. Dependendo do cenário de aplicação, pode fazer sentido usar a lógica de retry com backoff.

Para testar a resiliência de uma aplicação de cliente, utilize um [reboot](cache-administration.md#reboot) como gatilho manual para quebras de ligação. Além disso, recomendamos que [marque atualizações](cache-administration.md#schedule-updates) numa cache. Informe o serviço de gestão para aplicar patches de tempo de execução Redis durante janelas semanais especificadas. Estas janelas são normalmente períodos em que o tráfego de aplicação do cliente é baixo, para evitar potenciais incidentes.

### <a name="client-network-configuration-changes"></a>Alterações na configuração da rede do cliente

Certas alterações de configuração de rede do lado do cliente podem desencadear erros de "Nenhuma ligação disponível". Tais alterações podem incluir:

- Trocar o endereço IP virtual de uma aplicação de cliente entre as ranhuras de preparação e de produção.
- Dimensionar o tamanho ou número de instâncias da sua aplicação.

Tais alterações podem causar um problema de conectividade que dura menos de um minuto. A sua aplicação de cliente provavelmente perderá a sua ligação a outros recursos de rede externos, além do serviço Azure Cache para redis.

## <a name="next-steps"></a>Passos seguintes

- [Agende atualizações](cache-administration.md#schedule-updates) para a sua cache.
- Teste a resiliência da aplicação utilizando um [reboot](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) reservas e políticas de memória.
