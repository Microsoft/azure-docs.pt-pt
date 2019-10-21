---
title: Failover e aplicação de patches – cache do Azure para Redis | Microsoft Docs
description: Saiba mais sobre failover, aplicação de patch e o processo de atualização para o cache do Azure para Redis.
services: cache
author: asasine
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: adsasine
ms.openlocfilehash: 305511efe86d2b241ef5014d9c3f0501cfd3fbdc
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675905"
---
# <a name="failover-and-patching-for-azure-cache-for-redis"></a>Failover e aplicação de patch para o cache do Azure para Redis

Entender o que é um failover no contexto com o cache do Azure para o serviço Redis é essencial para a criação de aplicativos cliente resilientes e bem-sucedidos. Uma causa comum para um failover de cache é proveniente do serviço de gerenciamento que corre os binários Redis. Este artigo aborda o que é um failover, como eles ocorrem durante a aplicação de patches e como criar um aplicativo cliente resiliente.

## <a name="what-is-a-failover"></a>O que é um failover?

### <a name="a-quick-summary-of-our-architecture"></a>Um resumo rápido de nossa arquitetura

Um cache é construído de várias máquinas virtuais com IPs privados separados. Cada máquina virtual, também conhecida como um nó, é conectada a um balanceador de carga compartilhado com um único IP virtual. Cada nó executa o processo do servidor Redis e pode ser acessado por meio do nome do host e das portas Redis. Cada nó é considerado um nó mestre ou de réplica. Quando um aplicativo cliente se conecta a um cache, seu tráfego passa por esse balanceador de carga e é automaticamente roteado para o nó mestre.

Em um cache básico, o único nó é sempre um mestre. Em um cache Standard ou Premium, há dois nós em que um é escolhido o mestre e o outro é a réplica. Como os caches padrão e Premium têm vários nós, um nó pode estar indisponível enquanto o outro continua a processar solicitações. Os caches clusterizados são compostos de muitos fragmentos, cada um com nós mestre e de réplica distintos. Um fragmento pode estar inoperante enquanto os outros permanecem disponíveis.

> [!NOTE]
> Um cache básico não tem vários nós e não oferece um SLA na disponibilidade. Os caches básicos são recomendados apenas para fins de desenvolvimento e teste. Use um cache Standard ou Premium para uma implantação de vários nós para aumentar a disponibilidade.

### <a name="a-failover-explained"></a>Um failover explicado

Um failover ocorre quando um nó de réplica se promove a se tornar um nó mestre e o nó mestre antigo fecha as conexões existentes. Depois que o nó mestre for revertido, ele notará a alteração nas funções e rebaixará a si mesmo para se tornar uma réplica. Em seguida, ele se conectará ao novo mestre e sincronizará os dados. Um failover pode ser planejado ou não planejado.

Um failover planejado ocorre durante atualizações do sistema, como aplicação de patch de Redis ou atualizações de so e operações de gerenciamento, como dimensionamento e reinicialização. Como os nós recebem um aviso avançado da atualização, eles podem trocar funções de maneira cooperativa e atualizar rapidamente o balanceador de carga da alteração. Um failover planejado deve ser concluído em menos de 1 segundo.

Um failover não planejado pode ocorrer devido a falha de hardware, falha de rede ou outras interrupções inesperadas no nó mestre. O nó de réplica se promoverá para o mestre, mas o processo levará mais tempo. Um nó de réplica deve primeiro detectar que o nó mestre não está disponível antes de poder iniciar o processo de failover. O nó de réplica também deve verificar se essa falha não planejada não é transitória ou local para evitar um failover muito adiantado. Esse atraso na detecção significa que um failover não planejado normalmente é concluído em 10 a 15 segundos.

## <a name="how-does-patching-occur"></a>Como ocorre a aplicação de patch?

Regularmente, o cache do Azure para o serviço Redis faz a manutenção para atualizar seu cache com os recursos e as correções mais recentes da plataforma. Para corrigir um cache, o serviço segue as seguintes etapas:

1. O serviço de gerenciamento seleciona um nó para ser corrigido.
1. Se o nó selecionado for um nó mestre, seu nó de réplica se promoverá em si mesmo. Essa promoção é considerada um failover planejado.
1. O nó selecionado é reinicializado para realizar as novas alterações e é retornado como um nó de réplica. Os nós de réplica se conectam ao nó mestre e sincronizam os dados.
1. Quando a sincronização de dados é concluída, o processo de aplicação de patch se repete para os nós restantes.

Como a aplicação de patch é um failover planejado, o nó de réplica se promove rapidamente a se tornar um mestre e começa a atender solicitações e novas conexões. Os caches básicos não têm um nó de réplica e ficam indisponíveis até que a atualização seja concluída. Cada fragmento de um cache clusterizado é corrigido separadamente e não fecha as conexões com outro fragmento.

> [!IMPORTANT]
> Os nós são corrigidos um de cada vez para evitar a perda de dados. Os caches básicos terão perda de dados. Os caches clusterizados são corrigidos em um fragmento por vez.

Vários caches no mesmo grupo de recursos e região também são corrigidos um de cada vez.  Os caches que estão em diferentes grupos de recursos ou regiões diferentes podem ser corrigidos simultaneamente.

Como a sincronização de dados completa ocorre antes da repetição do processo, a perda de dados provavelmente ocorrerá ao usar um cache Standard ou Premium. Você pode proteger ainda mais contra a perda de dados usando a [exportação](cache-how-to-import-export-data.md#export) de dados e a habilitação da [persistência](cache-how-to-premium-persistence.md).

### <a name="additional-cache-load"></a>Carregamento de cache adicional

Sempre que ocorre um failover, os caches padrão e Premium precisam replicar dados de um nó para o outro. Essa replicação causa um aumento de carga na memória do servidor e na CPU. Se a instância de cache já estiver muito carregada, os aplicativos cliente poderão enfrentar maior latência. Em casos extremos, os aplicativos cliente podem receber exceções de tempo limite. [Defina](cache-configure.md#memory-policies) a configuração de `maxmemory-reserved` do cache para ajudar a reduzir o impacto dessa carga adicional.

## <a name="how-does-a-failover-impact-my-client-application"></a>Como um failover afeta meu aplicativo cliente?

O número de erros vistos pelo aplicativo cliente dependerá de quantas operações estavam pendentes na conexão no momento do failover. Qualquer conexão que é roteada pelo nó que as conexões fechadas verá erros. Muitas bibliotecas de cliente podem gerar diferentes tipos de erros, incluindo exceções de tempo limite, exceções de conexão ou exceções de soquete quando as conexões são interrompidas. O número e o tipo de exceções dependem de onde no caminho do código a solicitação é quando o cache fecha suas conexões. Por exemplo, uma operação que envia uma solicitação, mas não recebeu uma resposta quando o failover ocorre, pode receber uma exceção de tempo limite. Novas solicitações no objeto de conexão fechado receberão exceções de conexão até que a reconexão ocorra com êxito.

A maioria das bibliotecas de cliente tentará se reconectar ao cache se estiver configurado para fazer isso, mas os bugs imprevistos podem ocasionalmente colocar os objetos de biblioteca em um estado irrecuperável. Se os erros persistirem por mais tempo que um período pré-configurado, o objeto de conexão deverá ser recriado. No .NET e em outras linguagens orientadas a objeto, a recriação da conexão sem reiniciar o aplicativo pode ser realizada usando [um padrão de \> de \<T lento](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#reconnecting-with-lazyt-pattern).

### <a name="what-should-i-do-in-my-application"></a>O que devo fazer em meu aplicativo?

Como um failover não pode ser completamente evitado, os aplicativos cliente devem ser gravados para resiliência em interrupções de conexão e solicitações com falha. Apesar da maioria das bibliotecas de cliente reconectarem-se automaticamente ao ponto de extremidade do cache, poucas bibliotecas de cliente tentam repetir solicitações com falha. Dependendo do cenário do aplicativo, a lógica de repetição com retirada pode fazer sentido.

Para testar a resiliência de um aplicativo cliente, use uma [reinicialização](cache-administration.md#reboot) como um gatilho manual para quebras de conexão. Além disso, é recomendável [agendar atualizações](cache-administration.md#schedule-updates) em um cache para informar o serviço de gerenciamento para que os patches de tempo de execução do Redis sejam aplicados durante as janelas semanais especificadas. Essas janelas são normalmente escolhidas para períodos em que o tráfego do aplicativo cliente é menor para evitar possíveis incidentes.

### <a name="client-network-configuration-changes"></a>Alterações de configuração de rede do cliente

Determinadas alterações de configuração de rede do lado do cliente podem disparar erros "nenhuma conexão disponível".  Alternar o endereço IP virtual de um aplicativo cliente entre os slots de preparo e de produção ou dimensionar o tamanho/número de instâncias de seu aplicativo pode causar um problema de conectividade que dura menos de um minuto. Seu aplicativo cliente provavelmente perderá a conexão com outros recursos de rede externa, além de Redis.

## <a name="next-steps"></a>Passos seguintes

- [Agendar atualizações](cache-administration.md#schedule-updates) para seu cache.
- Teste a resiliência do aplicativo usando uma [reinicialização](cache-administration.md#reboot).
- [Configure](cache-configure.md#memory-policies) as reservas e as políticas de memória.
