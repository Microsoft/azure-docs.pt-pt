---
title: Como configurar a geo-replicação para o Azure Cache para redis ! Microsoft Docs
description: Aprenda a replicar o seu Azure Cache para os casos Redis em regiões geográficas.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129426"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Como configurar a geo-replicação para Azure Cache para Redis

A geo-replicação fornece um mecanismo para ligar dois acabamentos Premium Azure Cache para instâncias Redis. Uma cache é escolhida como a cache ligada primária, e a outra como a cache secundária ligada. A cache ligada secundária torna-se apenas de leitura, e os dados escritos para a cache primária são replicados para a cache ligada secundária. Esta funcionalidade pode ser usada para replicar um cache em todas as regiões de Azure. Este artigo fornece um guia para configurar a geo-replicação para os seus casos De Acabamento Azure de nível Premium para os casos Redis.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de geo-replicação

Para configurar a geo-replicação entre dois caches, devem ser cumpridos os seguintes pré-requisitos:

- Ambos os caches são caches [de nível Premium.](cache-premium-tier-intro.md)
- Ambos os caches estão na mesma subscrição do Azure.
- A cache ligada secundária é do mesmo tamanho de cache ou de um tamanho de cache maior do que a cache ligada primária.
- Ambos os caches são criados e em estado de funcionamento.

Algumas funcionalidades não são suportadas com geo-replicação:

- A persistência não é suportada com geo-replicação.
- O agrupamento é suportado se ambos os caches tiverem clustering ativado e tiverem o mesmo número de fragmentos.
- Os caches no mesmo VNET são suportados.
- Caches em diferentes VNETs são suportados com ressalvas. [Posso usar a geo-replicação com os meus caches num VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Após a configuração da geo-replicação, as seguintes restrições aplicam-se ao seu par de cache ligado:

- A cache secundária ligada é apenas leitura; pode ler a partir dele, mas não pode escrever nenhum dado. 
- Todos os dados que estavam na cache ligada secundária antes da adição do link são removidos. No entanto, se a geo-replicação for posteriormente removida, os dados replicados permanecem na cache ligada secundária.
- Não se pode [escalar](cache-how-to-scale.md) nenhum dos caches enquanto os caches estão ligados.
- Não pode [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se a cache tiver o agrupamento ativado.
- Não se pode permitir a persistência em nenhum dos caches.
- Pode [exportar](cache-how-to-import-export-data.md#export) de qualquer cache.
- Não se pode [importar](cache-how-to-import-export-data.md#import) para a cache secundária ligada.
- Não é possível eliminar nem a cache ligada, nem o grupo de recursos que os contém, até desligar os caches. Para mais informações, veja [por que a operação falhou quando tentei apagar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estiverem em diferentes regiões, os custos de egress da rede aplicam-se aos dados deslocados entre regiões. Para mais informações, veja [quanto custa replicar os meus dados nas regiões de Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- A falha automática não ocorre entre a cache ligada primária e secundária. Para obter mais informações e informações sobre como falhar com uma aplicação de cliente, veja [como é que falhar com o cache ligado secundário funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicione uma ligação de geo-replicação

1. Para ligar dois caches para geo-replicação, clique no punho **Geo-replicação** a partir do menu de Recursos da cache que pretende ser a cache principal ligada. Em seguida, clique em Adicionar o link de **replicação** cache a partir da lâmina **de geo-replicação.**

    ![Adicionar link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome da sua cache secundária pretendida a partir da lista de **caches compatíveis.** Se a sua cache secundária não estiver exposta na lista, verifique se os [pré-requisitos de geo-replicação](#geo-replication-prerequisites) para a cache secundária são cumpridos. Para filtrar as caches por região, clique na região no mapa para exibir apenas esses caches na lista de **caches compatíveis.**

    ![Caches compatíveis com geo-replicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Também pode iniciar o processo de ligação ou visualizar detalhes sobre a cache secundária utilizando o menu de contexto.

    ![Menu de contexto de geo-replicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **Link** para ligar os dois caches e iniciar o processo de replicação.

    ![Caches de ligação](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Pode ver o progresso do processo de replicação na lâmina **de geo-replicação.**

    ![Estado de ligação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Também pode visualizar o estado de ligação na lâmina **de visão geral** para os caches primários e secundários.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Uma vez concluído o processo de replicação, o estado de **Ligação** muda para **Succeed .**

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    A cache ligada primária permanece disponível para utilização durante o processo de ligação. A cache secundária ligada não está disponível até que o processo de ligação esteja concluído.

## <a name="remove-a-geo-replication-link"></a>Remover uma ligação de geo-replicação

1. Para remover a ligação entre dois caches e parar a geo-replicação, clique em **caches Unlink** da lâmina **de geo-replicação.**
    
    ![Caches de desvinculação](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação estiver concluído, a cache secundária está disponível tanto para leituras como para escritos.

>[!NOTE]
>Quando a ligação de geo-replicação é removida, os dados replicados da cache ligada primária permanecem na cache secundária.
>
>

## <a name="geo-replication-faq"></a>FaQ de geo-replicação

- [Posso usar a geo-replicação com uma cache de nível Standard ou Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [A minha cache está disponível para utilização durante o processo de ligação ou desvinculação?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso ligar mais de dois caches juntos?](#can-i-link-more-than-two-caches-together)
- [Posso ligar dois caches de diferentes subscrições do Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Posso ligar dois caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Posso usar a geo-replicação com agrupamento habilitado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Posso usar a geo-replicação com os meus caches num VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Qual é o calendário de replicação para a geo-replicação redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo demora a replicação da geo-replicação?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação da replicação está garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso usar powerShell ou Azure CLI para gerir a geo-replicação?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa replicar os meus dados nas regiões de Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Porque é que a operação falhou quando tentei apagar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Que região devo usar para o meu cache secundário ligado?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como é que falhar com o cache ligado secundário funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a geo-replicação com uma cache de nível Standard ou Basic?

Não, a geo-replicação só está disponível para caches de nível Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>A minha cache está disponível para utilização durante o processo de ligação ou desvinculação?

- Ao ligar, a cache ligada primária permanece disponível enquanto o processo de ligação termina.
- Ao ligar, a cache ligada secundária não está disponível até que o processo de ligação esteja concluído.
- Ao desligar, ambos os caches permanecem disponíveis enquanto o processo de desvinculação termina.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso ligar mais de dois caches juntos?

Não, só podes ligar dois caches.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso ligar dois caches de diferentes subscrições do Azure?

Não, ambos os caches devem estar na mesma assinatura Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso ligar dois caches com tamanhos diferentes?

Sim, desde que a cache secundária ligada seja maior do que a cache ligada primária.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a geo-replicação com agrupamento habilitado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a geo-replicação com os meus caches num VNET?

Sim, a geo-replicação de caches em VNETs é suportada com ressalvas:

- A geo-replicação entre caches no mesmo VNET é suportada.
- A geo-replicação entre caches em diferentes VNETs também é suportada.
  - Se os VNETs estiverem na mesma região, pode ligá-los utilizando [o peering VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou uma [ligação VPN Gateway VNET-to-VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se os VNETs estiverem em diferentes regiões, a geo-replicação usando o peering VNET não é suportada devido a uma restrição com os equilibradores de carga interna básicos. Para obter mais informações sobre os constrangimentos de peering VNET, consulte [rede virtual - Peering - Requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). A solução recomendada é utilizar uma ligação VPN Gateway VNET-to-VNET.

Utilizando [este modelo Azure,](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)pode rapidamente implantar dois caches geo-replicados num VNET ligado a uma ligação VPN Gateway VNET-to-VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Qual é o calendário de replicação para a geo-replicação redis?

A replicação é contínua e assíncrona e não acontece num horário específico. Todas as escritas feitas às primárias são instantaneamente e assincronicamente replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo demora a replicação da geo-replicação?

A replicação é incremental, assíncrona e contínua e o tempo demorou não muito diferente da latência entre as regiões. Em determinadas circunstâncias, a cache secundária pode ser necessária para fazer uma sincronização completa dos dados a partir do primário. O tempo de replicação neste caso depende do número de fatores como: carga na cache primária, largura de banda disponível e latência inter-região. Encontramos tempo de replicação para um par geo-replicado completo de 53 GB pode estar em qualquer lugar entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação da replicação está garantido?

Para caches em modo geo-replicado, a persistência é desativada. Se um par geo-replicado não estiver ligado, como uma falha iniciada pelo cliente, a cache ligada secundária mantém os seus dados sincronizados até esse ponto de tempo. Não é garantido nenhum ponto de recuperação nestas situações.

Para obter um ponto de recuperação, [exportar](cache-how-to-import-export-data.md#export) de qualquer cache. Mais tarde, pode [importar](cache-how-to-import-export-data.md#import) para a cache principal ligada.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar powerShell ou Azure CLI para gerir a geo-replicação?

Sim, a geo-replicação pode ser gerida usando o portal Azure, PowerShell ou Azure CLI. Para mais informações, consulte os [docs powerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [os docs Azure CLI](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa replicar os meus dados nas regiões de Azure?

Ao utilizar a geo-replicação, os dados da cache ligada primária são replicados para a cache ligada secundária. Não há nenhum custo para a transferência de dados se os dois caches ligados estiverem na mesma região. Se os dois caches ligados estiverem em diferentes regiões, a taxa de transferência de dados é a rede que ultrapassa o custo dos dados que se deslocam em qualquer uma das regiões. Para mais informações, consulte os detalhes de preços da [largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Porque é que a operação falhou quando tentei apagar a minha cache ligada?

Os caches geo-replicados e os seus grupos de recursos não podem ser eliminados enquanto estiverem ligados até removerem a ligação de geo-replicação. Se tentar eliminar o grupo de recursos que contém um ou ambos os caches ligados, os outros `deleting` recursos do grupo de recursos são eliminados, mas o grupo de recursos permanece no estado e quaisquer caches ligados no grupo de recursos permanecem no `running` estado. Para eliminar completamente o grupo de recursos e as caches ligadas no seu interior, descoinede os caches descritos em [Remover uma ligação de geo-replicação](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Que região devo usar para o meu cache secundário ligado?

Em geral, recomenda-se que o seu cache exista na mesma região de Azure que a aplicação que lhe acede. Para aplicações com regiões primárias e recuadas separadas, recomenda-se que existam caches primários e secundários nessas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [As Melhores Práticas – Regiões Emparelhadas de Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como é que falhar com o cache ligado secundário funciona?

A falha automática em todas as regiões de Azure não é suportada para caches geo-replicados. Num cenário de recuperação de desastres, os clientes devem trazer toda a pilha de aplicações de forma coordenada na sua região de backup. Deixar os componentes individuais decidirem quando mudar para as suas cópias de segurança por si só pode ter um impacto negativo no desempenho. Um dos principais benefícios do Redis é que é uma loja de baixa latência. Se a aplicação principal do cliente estiver numa região diferente da sua cache, o tempo adicional de ida e volta teria um impacto notável no desempenho. Por esta razão, evitamos falhar automaticamente devido a problemas de disponibilidade transitórios.

Para iniciar uma falha iniciada pelo cliente, primeiro desligue os caches. Em seguida, mude o seu cliente Redis para utilizar o ponto final de ligação da cache secundária (anteriormente ligada). Quando os dois caches não estão ligados, a cache secundária torna-se novamente uma cache de leitura regular e aceita pedidos diretamente de clientes Redis.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [Azure Cache para o nível Redis Premium.](cache-premium-tier-intro.md)
