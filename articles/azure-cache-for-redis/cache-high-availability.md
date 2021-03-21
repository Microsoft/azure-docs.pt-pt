---
title: Alta disponibilidade para Azure Cache para Redis
description: Saiba mais sobre a Azure Cache para funcionalidades e opções de alta disponibilidade do Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: ad6696fc4fe2af7047c25a3a9c260d3b12588ee2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203305"
---
# <a name="high-availability-for-azure-cache-for-redis"></a>Alta disponibilidade para Azure Cache para Redis

Azure Cache para Redis tem alta disponibilidade incorporada. O objetivo da sua arquitetura de alta disponibilidade é garantir que a sua instância de Redis gerida esteja a funcionar mesmo quando as suas máquinas virtuais subjacentes (VMs) são impactadas por interrupções planeadas ou não planeadas. Oferece taxas percentuais muito maiores do que as que são alcançáveis ao hospedar redis num único VM.

Azure Cache para Redis implementa alta disponibilidade usando vários VMs, *chamados nós,* para uma cache. Configura estes nós de modo a que a replicação de dados e o fracasso ocorram de forma coordenada. Também orquestra operações de manutenção como patching de software Redis. Várias opções de alta disponibilidade estão disponíveis nos níveis Standard, Premium e Enterprise:

| Opção | Descrição | Disponibilidade | Standard | Premium | Grandes Empresas |
| ------------------- | ------- | ------- | :------: | :---: | :---: |
| [Replicação padrão](#standard-replication)| Configuração replicada de duplo nó num único datacenter com falha automática | 99,9% |✔|✔|-|
| [Redundância entre zonas](#zone-redundancy) | Configuração replicada em vários nóns em AZs, com falha automática | 99,95% (Nível Premium), 99,99% (Níveis empresariais) |-|Pré-visualizar|Pré-visualizar|
| [Georreplicação](#geo-replication) | Casos de cache ligados em duas regiões, com falha controlada pelo utilizador | 99,999% (nível de empresa) |-|✔|Pré-visualizar|

## <a name="standard-replication"></a>Replicação padrão

Um Cache Azure para Redis no nível Standard ou Premium funciona por defeito num par de servidores Redis. Os dois servidores estão hospedados em VMs dedicados. O Redis de código aberto permite que apenas um servidor lide com pedidos de escrita de dados. Este servidor é o nó *primário,* enquanto o outro *replica*. Depois de providenciar os nós do servidor, a Cache Azure para Redis atribui-lhes funções primárias e réplicas. O nó primário geralmente é responsável pela manutenção da escrita, bem como por pedidos de leitura de clientes Redis. Numa operação de escrita, compromete uma nova chave e uma atualização chave para a sua memória interna e responde imediatamente ao cliente. Remete a operação para a réplica assíncronea.

:::image type="content" source="media/cache-high-availability/replication.png" alt-text="Configuração da replicação de dados":::
   
>[!NOTE]
>Normalmente, um cliente Redis comunica com o nó primário numa cache Redis para todos os pedidos de leitura e escrita. Certos clientes redis podem ser configurados para ler a partir do nó de réplica.
>
>

Se o nó primário numa cache Redis não estiver disponível, a réplica promover-se-á para se tornar o novo primário automaticamente. Este processo é chamado de *failover*. A réplica vai esperar muito tempo antes de assumir o caso de o nó primário recuperar rapidamente. Quando um failover acontece, Azure Cache for Redis fornece um novo VM e junta-o à cache como o nó de réplica. A réplica executa uma sincronização completa de dados com a primária de modo a ter outra cópia dos dados da cache.

Um nó primário pode sair de serviço como parte de uma atividade de manutenção planeada, como software Redis ou atualização do sistema operativo. Também pode deixar de funcionar devido a eventos não planeados, como falhas em hardware, software ou rede subjacentes. [Failover e patching para Azure Cache para Redis](cache-failover.md) fornece uma explicação detalhada sobre tipos de failovers Redis. Um Azure Cache para Redis passará por muitas falhas durante a sua vida. A arquitetura de alta disponibilidade é projetada para tornar estas mudanças dentro de uma cache o mais transparente possível para os seus clientes.

>[!NOTE]
>O seguinte está disponível como pré-visualização.
>
>

Além disso, a Azure Cache para Redis permite nómada de réplica adicional no nível Premium. Uma [cache multi-réplica](cache-how-to-multi-replicas.md) pode ser configurada com até três nós de réplica. Ter mais réplicas geralmente melhora a resiliência devido aos nós adicionais que apoiam as primárias. Mesmo com mais réplicas, uma cache Azure para o caso Redis ainda pode ser severamente impactada por uma paragem de nível de datacenter ou AZ. Pode aumentar a disponibilidade de cache utilizando várias réplicas em conjunto com [a redundância de zona.](#zone-redundancy)

## <a name="zone-redundancy"></a>Redundância entre zonas

A Azure Cache para Redis suporta configurações redundantes de zona nos níveis Premium e Enterprise. Uma [cache redundante de zona](cache-how-to-zone-redundancy.md) pode colocar os seus nós em [diferentes Zonas de Disponibilidade de Azure](../availability-zones/az-overview.md) na mesma região. Elimina o datacenter ou a paragem de AZ como um único ponto de falha e aumenta a disponibilidade global do seu cache.

### <a name="premium-tier"></a>Escalão Premium

>[!NOTE]
>Isto está disponível como pré-visualização.
>
>

O diagrama a seguir ilustra a configuração redundante da zona para o nível Premium:

:::image type="content" source="media/cache-high-availability/zone-redundancy.png" alt-text="Configuração de redundância de zona":::
   
Azure Cache para Redis distribui nós numa área de cache redundante de forma redonda sobre as AZs que selecionou. Também determina qual o nó que servirá como principal inicialmente.

Uma cache redundante de zona proporciona falha automática. Quando o nó primário atual não estiver disponível, uma das réplicas assumirá. A sua aplicação pode experimentar um tempo de resposta de cache mais elevado se o novo nó primário estiver localizado num AZ diferente. As AZs estão geograficamente separadas. Mudar de um AZ para outro altera a distância física entre onde a sua aplicação e cache estão hospedados. Esta alteração tem impacto nas latências da rede de ida e volta da sua aplicação para a cache. Espera-se que a latência extra se encava dentro de um intervalo aceitável para a maioria das aplicações. Recomendamos que teste a sua aplicação para garantir que pode funcionar bem com uma cache redundante de zona.

### <a name="enterprise-tiers"></a>Escalões Enterprise

Uma cache em cada nível da Enterprise funciona num cluster da Redis Enterprise. Requer um número ímpar de nós de servidor em todos os momentos para formar um quórum. Por padrão, é composto por três nós, cada um hospedado num VM dedicado. Uma cache Enterprise tem dois nós de *dados* do mesmo tamanho e um *nó de quórum* menor. Uma cache Enterprise Flash tem três nós de dados do mesmo tamanho. O cluster enterprise divide os dados do Redis em divisórias internamente. Cada divisória tem uma *réplica primária* e pelo menos uma *réplica.* Cada nó de dados contém uma ou mais divisórias. O cluster Enterprise garante que as réplicas primárias e réplicas de qualquer partição nunca são aparadas no mesmo nó de dados. As partições replicam os dados assíncronosamente das primárias às réplicas correspondentes.

Quando um nó de dados fica indisponível ou uma divisão de rede acontece, ocorre uma falha semelhante à descrita na [replicação Standard.](#standard-replication) O cluster Enterprise usa um modelo baseado em quórum para determinar quais os nós sobreviventes que participarão num novo quórum. Também promove divisórias réplicas dentro destes nós para as primárias, conforme necessário.

## <a name="geo-replication"></a>Georreplicação

[A geo-replicação](cache-how-to-geo-replication.md) é um mecanismo para ligar duas ou mais caches Azure para instâncias Redis, tipicamente abrangendo duas regiões de Azure. 

### <a name="premium-tier"></a>Escalão Premium

>[!NOTE]
>A geo-replicação no nível Premium é projetada principalmente para a recuperação de desastres.
>
>

Duas instâncias de cache de nível Premium podem ser ligadas através [de geo-replicação](cache-how-to-geo-replication.md) para que possa fazer o back up dos seus dados de cache para uma região diferente. Uma vez ligados entre si, um caso é designado como cache principal ligado e o outro como a cache ligada secundária. Apenas a cache primária aceita ler e escrever pedidos. Os dados escritos na cache primária são replicados na cache secundária. Uma aplicação acede à cache através de pontos finais separados para as caches primárias e secundárias. A aplicação deve enviar todos os pedidos de escrita para a cache primária quando é implantado em várias regiões do Azure. Pode ler a partir da cache primária ou secundária. Em geral, pretende que as instâncias de cálculo da sua aplicação leiam a partir dos caches mais próximos para reduzir a latência. A transferência de dados entre as duas instâncias de cache é assegurada pela TLS.

A geo-replicação não fornece falhas automáticas devido a preocupações sobre o tempo de ida e volta de rede adicionado entre regiões se o resto da sua aplicação permanecer na região primária. Terá de gerir e iniciar a falha desvinculando a cache secundária. Isto irá promovê-lo para ser o novo exemplo primário.

### <a name="enterprise-tiers"></a>Escalões Enterprise

>[!NOTE]
>Isto está disponível como pré-visualização.
>
>

Os níveis enterprise suportam uma forma mais avançada de geo-replicação, chamada [geo-replicação ativa.](cache-how-to-active-geo-replication.md) Aproveitando tipos de dados replicados sem conflitos, o software Redis Enterprise suporta escrever em múltiplas instâncias de cache e cuida da fusão de alterações e na resolução de conflitos, se necessário. Duas ou mais instâncias de cache de nível empresarial em diferentes regiões de Azure podem ser unidas para formar uma cache geo-replicada ativa. Uma aplicação utilizando tal cache pode ler e escrever para as instâncias de cache geo-distribuídas através dos pontos finais correspondentes. Deve usar o que é mais próximo de cada instância computacional, o que dá a menor latência. A aplicação também precisa de monitorizar as instâncias de cache e mudar para outra região se uma das instâncias ficar indisponível. Para obter mais informações sobre o funcionamento da geo-replicação ativa, consulte [Geo-Distriubtion Active-Active (Baseado em CRDTs)](https://redislabs.com/redis-enterprise/technology/active-active-geo-distribution/).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como configurar a Cache Azure para opções de alta disponibilidade do Redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)
* [Adicione réplicas ao Cache Azure para Redis](cache-how-to-multi-replicas.md)
* [Permitir redundância de zona para Azure Cache para Redis](cache-how-to-zone-redundancy.md)
* [Configurar a geo-replicação para Azure Cache para Redis](cache-how-to-geo-replication.md)
