---
title: Configure a geo-replicação para a Cache Premium Azure para instâncias Redis
description: Saiba como replicar o seu Azure Cache para instâncias Redis Premium em regiões do Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 130cb1d63da27010012c22dc2cdb40c3d8f03273
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178563"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Configure a geo-replicação para a Cache Premium Azure para instâncias Redis

Neste artigo, você vai aprender a configurar um Cache Azure geo-replicado usando o portal Azure.

A geo-replicação liga dois Premium Azure Cache para instâncias Redis e cria uma relação de replicação de dados. Estes casos de cache estão geralmente localizados em diferentes regiões de Azure, embora não sejam necessários. Um caso funciona como o primário, e o outro como secundário. As pegas primárias lêem e escrevem pedidos e propagam alterações para o secundário. Este processo continua até que a ligação entre as duas instâncias seja removida.

> [!NOTE]
> A geo-replicação é projetada como uma solução de recuperação de desastres.
>
>

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de geo-replicação

Para configurar a geo-replicação entre duas caches, devem ser cumpridos os seguintes pré-requisitos:

- Ambos os caches são caches [de nível Premium.](cache-overview.md#service-tiers)
- Ambos os caches estão na mesma subscrição do Azure.
- A cache ligada secundária é do mesmo tamanho de cache ou de um tamanho de cache maior do que a cache ligada primária.
- Ambos os caches são criados e em estado de execução.

Algumas funcionalidades não são suportadas com geo-replicação:

- A persistência não é suportada com geo-replicação.
- O agrupamento é suportado se ambos os caches tiverem clustering ativado e tiverem o mesmo número de fragmentos.
- As caches no mesmo VNET são suportadas.
- Caches em VNETs diferentes são suportados com ressalvas. Posso [usar a geo-replicação com os meus caches num VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Após a configuração da geo-replicação, aplicam-se as seguintes restrições ao seu par de cache ligado:

- A cache ligada secundária é apenas de leitura; pode ler, mas não pode escrever nenhum dado. 
- Todos os dados que estavam na cache ligada secundária antes da ligação ser adicionada são removidos. No entanto, se a geo-replicação for removida posteriormente, os dados replicados permanecem na cache ligada secundária.
- Não se pode [escalar](cache-how-to-scale.md) qualquer uma das caches enquanto os caches estão ligados.
- Não é [possível alterar o número de fragmentos](cache-how-to-premium-clustering.md) se a cache tiver o agrupamento ativado.
- Não se pode permitir a persistência em nenhuma das caches.
- Pode [exportar](cache-how-to-import-export-data.md#export) de qualquer cache.
- Não se pode [importar](cache-how-to-import-export-data.md#import) para a cache ligada secundária.
- Não é possível eliminar cache ligado ou o grupo de recursos que os contém, até desvincular os caches. Para mais informações, veja [por que a operação falhou quando tentei apagar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estiverem em diferentes regiões, os custos de saída da rede aplicam-se aos dados transportados através das regiões. Para mais informações, veja [quanto custa replicar os meus dados em todas as regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- A falha automática não ocorre entre a cache primária e secundária ligada. Para obter mais informações e informações sobre como falhar com uma aplicação de cliente, veja [como é que a falha no trabalho de cache ligado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicione uma ligação de geo-replicação

1. Para ligar dois caches para geo-replicação, clique em **Geo-replicação** a partir do menu de Recursos da cache que pretende ser a cache principal ligada. Em seguida, clique em Adicionar link de **replicação** de cache a partir da lâmina **de geo-replicação.**

    ![Adicionar link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome da sua cache secundária pretendida a partir da lista de **caches compatíveis.** Se a sua cache secundária não estiver apresentada na lista, verifique se os [pré-requisitos de geoprovação](#geo-replication-prerequisites) para a cache secundária são cumpridos. Para filtrar as caches por região, clique na região no mapa para exibir apenas as caches na lista **de caches compatíveis.**

    ![Caches compatíveis com geo-replicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Também pode iniciar o processo de ligação ou ver detalhes sobre a cache secundária utilizando o menu de contexto.

    ![Menu de contexto de geo-replicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique **em Link** para ligar as duas caches juntas e inicie o processo de replicação.

    ![Caches de ligação](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Pode ver o progresso do processo de replicação na lâmina **de geo-replicação.**

    ![Estado de ligação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Também pode ver o estado de ligação na lâmina **de visão geral** para as caches primária e secundária.

    ![Screenshot que realça como ver o estado de ligação para as caches primárias e secundárias.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Uma vez concluído o processo de replicação, **o estado de Ligação** muda para **Sucesso**.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    A cache principal ligada permanece disponível para utilização durante o processo de ligação. A cache ligada secundária não está disponível até que o processo de ligação esteja concluído.

## <a name="remove-a-geo-replication-link"></a>Remover uma ligação de geo-replicação

1. Para remover a ligação entre duas caches e parar a geo-replicação, clique em **Caches Desconectar** da lâmina **de geo-replicação.**
    
    ![Caches de desvinculação](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação estiver concluído, a cache secundária está disponível para leituras e escritas.

>[!NOTE]
>Quando a ligação de geo-replicação é removida, os dados replicados da cache ligada primária permanecem na cache secundária.
>
>

## <a name="geo-replication-faq"></a>FAQ de geo-replicação

- [Posso usar a geo-replicação com uma cache standard ou basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [O meu cache está disponível para ser utilizado durante o processo de ligação ou desvinculação?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso ligar mais de dois caches juntos?](#can-i-link-more-than-two-caches-together)
- [Posso ligar dois caches de diferentes assinaturas Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Posso ligar dois caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Posso utilizar a geo-replicação com agrupamento ativado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Posso usar a geo-replicação com os meus caches num VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Qual é o calendário de replicação da geo-replicação do Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo demora a replicação da geo-replicação?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação da replicação está garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso usar o PowerShell ou o Azure CLI para gerir a geo-replicação?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa replicar os meus dados nas regiões de Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Porque é que a operação falhou quando tentei apagar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Que região devo usar para a minha cache secundária ligada?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como é que o fracasso no cache secundário ligado funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Posso configurar firewall com geo-replicação?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a geo-replicação com uma cache standard ou basic?

Não, a geo-replicação só está disponível para caches de nível Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>O meu cache está disponível para ser utilizado durante o processo de ligação ou desvinculação?

- Ao ligar, a cache ligada primária permanece disponível enquanto o processo de ligação termina.
- Ao ligar, a cache ligada secundária não está disponível até que o processo de ligação esteja concluído.
- Ao desvincular, ambos os caches permanecem disponíveis enquanto o processo de desvinculação termina.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso ligar mais de dois caches juntos?

Não, só se pode ligar dois caches.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso ligar dois caches de diferentes assinaturas Azure?

Não, ambos os caches devem estar na mesma assinatura Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso ligar dois caches com tamanhos diferentes?

Sim, desde que a cache ligada secundária seja maior do que a cache ligada primária.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso utilizar a geo-replicação com agrupamento ativado?

Sim, desde que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a geo-replicação com os meus caches num VNET?

Sim, a geo-replicação de caches em VNETs é suportada com ressalvas:

- A geo-replicação entre caches no mesmo VNET é suportada.
- A geo-replicação entre caches em diferentes VNETs também é suportada.
  - Se os VNETs estiverem na mesma região, pode ligá-los utilizando [o ntenet VNET](../virtual-network/virtual-network-peering-overview.md) ou uma [ligação VPN Gateway VNET-to-VNET](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
  - Se os VNETs estiverem em diferentes regiões, a geo-replicação utilizando o peering VNET é suportada, mas um cliente VM em VNET 1 (região 1) não será capaz de aceder à cache em VNET 2 (região 2) através do seu nome DNS devido a uma restrição com os equilibradores internos básicos. Para obter mais informações sobre as restrições de observação do VNET, consulte [Rede Virtual - Peering - Requisitos e restrições](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). A solução recomendada é utilizar uma ligação VPN Gateway VNET-to-VNET.
  
Utilizando [este modelo Azure,](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)pode rapidamente implantar duas caches geo-replicadas num VNET ligado a uma ligação VNET-vNET do Gateway VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Qual é o calendário de replicação da geo-replicação do Redis?

A replicação é contínua e assíncronea e não acontece num horário específico. Todos os escritos feitos às primárias são instantaneamente e assíncronosamente replicados no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo demora a replicação da geo-replicação?

A replicação é incremental, assíncrona e contínua e o tempo tomado não é muito diferente da latência entre regiões. Em certas circunstâncias, a cache secundária pode ser necessária para fazer uma sincronização completa dos dados a partir da primária. O tempo de replicação neste caso depende do número de fatores como: carga na cache primária, largura de banda disponível e latência inter-região. Encontramos tempo de replicação para um par geo-replicado completo de 53 GB pode estar em qualquer lugar entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação da replicação está garantido?

Para caches num modo geo-replicado, a persistência é desativada. Se um par geo-replicado não estiver ligado, como um failover iniciado pelo cliente, a cache ligada secundária mantém os seus dados sincronizados até esse ponto de tempo. Não é garantido qualquer ponto de recuperação nestas situações.

Para obter um ponto de recuperação, [exporte](cache-how-to-import-export-data.md#export) de qualquer uma das caches. Pode [importar](cache-how-to-import-export-data.md#import) mais tarde para a cache principal ligada.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou o Azure CLI para gerir a geo-replicação?

Sim, a geo-replicação pode ser gerida usando o portal Azure, PowerShell ou Azure CLI. Para obter mais informações, consulte os [docs PowerShell](/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [Azure CLI](/cli/azure/redis/server-link).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa replicar os meus dados nas regiões de Azure?

Ao utilizar a geo-replicação, os dados da cache ligada primária são replicados para a cache ligada secundária. Não há nenhuma taxa para a transferência de dados se os dois caches ligados estiverem na mesma região. Se as duas caches ligadas estiverem em diferentes regiões, a taxa de transferência de dados é o custo de saída da rede de dados que se deslocam em qualquer uma das regiões. Para obter mais informações, consulte [detalhes de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Porque é que a operação falhou quando tentei apagar a minha cache ligada?

Caches geo-replicados e seus grupos de recursos não podem ser eliminados enquanto estiver ligados até remover a ligação de geo-replicação. Se tentar eliminar o grupo de recursos que contém uma ou ambas as caches ligadas, os outros recursos do grupo de recursos são eliminados, mas o grupo de recursos permanece no `deleting` estado e quaisquer caches ligados no grupo de recursos permanecem no `running` estado. Para eliminar completamente o grupo de recursos e as caches ligadas no seu interior, desvincular as caches tal como descrito no [Remove uma ligação de geo-replicação](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Que região devo usar para a minha cache secundária ligada?

Em geral, recomenda-se que o seu cache exista na mesma região do Azure que a aplicação que a acessa. Para aplicações com regiões primárias e de recuo separadas, recomenda-se que existam caches primários e secundários nessas mesmas regiões. Para obter mais informações sobre as regiões emparelhadas, consulte [as Melhores Práticas – Regiões emparelhadas Azure.](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como é que o fracasso no cache secundário ligado funciona?

A falha automática nas regiões de Azure não é suportada por caches geo-replicados. Num cenário de recuperação de desastres, os clientes devem apresentar toda a pilha de aplicações de forma coordenada na sua região de reserva. Permitir que os componentes individuais da aplicação decidam quando devem mudar para as suas cópias de segurança por si só pode ter um impacto negativo no desempenho. Um dos principais benefícios do Redis é que é uma loja de baixa latência. Se a aplicação principal do cliente estiver numa região diferente da sua cache, o tempo de ida e volta adicionado teria um impacto notável no desempenho. Por esta razão, evitamos falhar automaticamente devido a problemas de disponibilidade transitórios.

Para iniciar uma falha iniciada pelo cliente, primeiro desvincula as caches. Em seguida, mude o seu cliente Redis para utilizar o ponto final de ligação da cache secundária (anteriormente ligada). Quando os dois caches são desvinculados, a cache secundária torna-se novamente uma cache de leitura-escrita regular e aceita pedidos diretamente dos clientes Redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Posso configurar uma firewall com geo-replicação?

Sim, pode configurar uma [firewall](./cache-configure.md#firewall) com geo-replicação. Para que a geo-replicação funcione ao lado de uma firewall, certifique-se de que o endereço IP da cache secundária é adicionado às regras de firewall da cache primária.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço redis](cache-overview.md#service-tiers)
* [Alta disponibilidade para Azure Cache para Redis](cache-high-availability.md)
