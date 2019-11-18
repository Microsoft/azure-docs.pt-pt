---
title: Como configurar a replicação geográfica para o cache do Azure para Redis | Microsoft Docs
description: Saiba como replicar o cache do Azure para instâncias de Redis em regiões geográficas.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129426"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Como configurar a replicação geográfica para o cache do Azure para Redis

A replicação geográfica fornece um mecanismo para vincular duas camadas do cache do Azure de camada Premium para instâncias de Redis. Um cache é escolhido como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário torna-se somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache em regiões do Azure. Este artigo fornece um guia para configurar a replicação geográfica para o cache do Azure da camada Premium para instâncias Redis.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de replicação geográfica

Para configurar a replicação geográfica entre dois caches, os seguintes pré-requisitos devem ser atendidos:

- Os dois caches são caches da [camada Premium](cache-premium-tier-intro.md) .
- Os dois caches estão na mesma assinatura do Azure.
- O cache vinculado secundário tem o mesmo tamanho de cache ou um tamanho de cache maior do que o cache vinculado primário.
- Ambos os caches são criados e em estado de execução.

Alguns recursos não têm suporte com a replicação geográfica:

- A persistência não tem suporte com a replicação geográfica.
- O clustering terá suporte se ambos os caches tiverem o clustering habilitado e tiverem o mesmo número de fragmentos.
- Há suporte para caches na mesma VNET.
- Os caches em diferentes VNETs têm suporte com advertências. Veja posso [usar a replicação geográfica com meus caches em uma VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) para obter mais informações.

Após a configuração da replicação geográfica, as seguintes restrições se aplicam ao seu par de cache vinculado:

- O cache vinculado secundário é somente leitura; Você pode ler a partir dele, mas não pode gravar dados nele. 
- Todos os dados que estavam no cache vinculado secundário antes do link ser adicionado são removidos. No entanto, se a replicação geográfica for removida posteriormente, os dados replicados permanecerão no cache vinculado secundário.
- Não é possível [dimensionar](cache-how-to-scale.md) o cache enquanto os caches estão vinculados.
- Você não poderá [alterar o número de fragmentos](cache-how-to-premium-clustering.md) se o cache tiver o clustering habilitado.
- Não é possível habilitar a persistência em um dos caches.
- Você pode [Exportar](cache-how-to-import-export-data.md#export) de um desses caches.
- Não é possível [importar](cache-how-to-import-export-data.md#import) para o cache vinculado secundário.
- Você não pode excluir o cache vinculado ou o grupo de recursos que os contém, até desvincular os caches. Para obter mais informações, consulte [por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estiverem em regiões diferentes, os custos de egresso de rede se aplicarão aos dados movidos entre regiões. Para obter mais informações, consulte [quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- O failover automático não ocorre entre o cache vinculado primário e o secundário. Para obter mais informações e informações sobre como fazer failover de um aplicativo cliente, confira [como funciona a falha no cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar um link de replicação geográfica

1. Para vincular dois caches para replicação geográfica, clique em **replicação geográfica** no menu de recursos do cache no qual você pretende ser o cache vinculado primário. Em seguida, clique em **Adicionar link de replicação de cache** na folha **replicação geográfica** .

    ![Adicionar link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome do seu cache secundário pretendido na lista **caches compatíveis** . Se o seu cache secundário não for exibido na lista, verifique se os [pré-requisitos de replicação geográfica](#geo-replication-prerequisites) para o cache secundário foram atendidos. Para filtrar os caches por região, clique na região no mapa para exibir apenas os caches na lista de **caches compatíveis** .

    ![Caches compatíveis com a replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Você também pode iniciar o processo de vinculação ou exibir detalhes sobre o cache secundário usando o menu de contexto.

    ![Menu de contexto de replicação geográfica](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **vincular** para vincular os dois caches juntos e iniciar o processo de replicação.

    ![Caches de link](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Você pode exibir o progresso do processo de replicação na folha de **replicação geográfica** .

    ![Status de vinculação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Você também pode exibir o status de vinculação na folha de **visão geral** para os caches primário e secundário.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Depois que o processo de replicação for concluído, o **status do link** será alterado para **êxito**.

    ![Status do cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    O cache vinculado primário permanece disponível para uso durante o processo de vinculação. O cache vinculado secundário não estará disponível até que o processo de vinculação seja concluído.

## <a name="remove-a-geo-replication-link"></a>Remover um link de replicação geográfica

1. Para remover o vínculo entre dois caches e parar a replicação geográfica, clique em **desvincular caches** da folha de **replicação geográfica** .
    
    ![Desvincular caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando o processo de desvinculação é concluído, o cache secundário está disponível para leituras e gravações.

>[!NOTE]
>Quando o link de replicação geográfica é removido, os dados replicados do cache vinculado primário permanecem no cache secundário.
>
>

## <a name="geo-replication-faq"></a>Perguntas frequentes sobre replicação geográfica

- [Posso usar a replicação geográfica com um cache de camada Standard ou Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [O cache está disponível para uso durante o processo de vinculação ou desvinculação?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso vincular mais de dois caches juntos?](#can-i-link-more-than-two-caches-together)
- [Posso vincular dois caches de diferentes assinaturas do Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Posso vincular dois caches com tamanhos diferentes?](#can-i-link-two-caches-with-different-sizes)
- [Posso usar a replicação geográfica com o clustering habilitado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Posso usar a replicação geográfica com meus caches em uma VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Qual é o agendamento de replicação para a replicação geográfica Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo leva a replicação de replicação geográfica?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação de replicação é garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso usar o PowerShell ou o CLI do Azure para gerenciar a replicação geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa para replicar meus dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que a operação falhou quando tentei excluir meu cache vinculado?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Qual região devo usar para meu cache vinculado secundário?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona o failover para o cache vinculado secundário?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Posso usar a replicação geográfica com um cache de camada Standard ou Basic?

Não, a replicação geográfica só está disponível para caches da camada Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>O cache está disponível para uso durante o processo de vinculação ou desvinculação?

- Ao vincular, o cache vinculado primário permanece disponível enquanto o processo de vinculação é concluído.
- Ao vincular, o cache vinculado secundário não estará disponível até que o processo de vinculação seja concluído.
- Ao desvincular, os dois caches permanecem disponíveis enquanto o processo de desvinculação é concluído.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso vincular mais de dois caches juntos?

Não, você só pode vincular dois caches juntos.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Posso vincular dois caches de diferentes assinaturas do Azure?

Não, ambos os caches devem estar na mesma assinatura do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Posso vincular dois caches com tamanhos diferentes?

Sim, desde que o cache vinculado secundário seja maior do que o cache vinculado primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Posso usar a replicação geográfica com o clustering habilitado?

Sim, contanto que ambos os caches tenham o mesmo número de fragmentos.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Posso usar a replicação geográfica com meus caches em uma VNET?

Sim, há suporte para a replicação geográfica de caches em VNETs com as advertências:

- Há suporte para a replicação geográfica entre caches na mesma VNET.
- Também há suporte para a replicação geográfica entre caches em diferentes VNETs.
  - Se os VNETs estiverem na mesma região, você poderá conectá-los usando o [emparelhamento vnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou uma [conexão vnet a vnet do gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se os VNETs estiverem em regiões diferentes, a replicação geográfica usando o emparelhamento VNET não terá suporte devido a uma restrição com balanceadores de carga internos básicos. Para obter mais informações sobre restrições de emparelhamento VNET, consulte [rede virtual – emparelhamento-requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). A solução recomendada é usar uma conexão VNET a VNET do gateway de VPN.

Usando [este modelo do Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), você pode implantar rapidamente dois caches replicados geograficamente em uma VNET conectada a uma conexão VNET a vnet do gateway de VPN.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Qual é o agendamento de replicação para a replicação geográfica Redis?

A replicação é contínua e assíncrona e não ocorre em um agendamento específico. Todas as gravações feitas no primário são instantâneas e replicadas de forma assíncrona no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo leva a replicação de replicação geográfica?

A replicação é incremental, assíncrona e contínua e o tempo gasto não é muito diferente da latência entre regiões. Em determinadas circunstâncias, o cache secundário pode ser necessário para fazer uma sincronização completa dos dados a partir do primário. O tempo de replicação, nesse caso, depende do número de fatores como: carga no cache primário, largura de banda de rede disponível e latência entre regiões. Encontramos o tempo de replicação para um par completo de 53 GB com replicação geográfica pode estar entre 5 e 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Para caches em um modo replicado geograficamente, a persistência está desabilitada. Se um par com replicação geográfica estiver desvinculado, como um failover iniciado pelo cliente, o cache vinculado secundário manterá seus dados sincronizados até esse ponto de tempo. Nenhum ponto de recuperação é garantido nessas situações.

Para obter um ponto de recuperação, [exporte](cache-how-to-import-export-data.md#export) de um dos caches. Posteriormente, você poderá [importar](cache-how-to-import-export-data.md#import) para o cache vinculado primário.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso usar o PowerShell ou o CLI do Azure para gerenciar a replicação geográfica?

Sim, a replicação geográfica pode ser gerenciada usando o portal do Azure, o PowerShell ou o CLI do Azure. Para obter mais informações, consulte os documentos do [PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [CLI do Azure docs](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar meus dados entre regiões do Azure?

Ao usar a replicação geográfica, os dados do cache vinculado primário são replicados para o cache vinculado secundário. Não haverá cobrança para a transferência de dados se os dois caches vinculados estiverem na mesma região. Se os dois caches vinculados estiverem em regiões diferentes, o encargo de transferência de dados será o custo de saída de rede da movimentação de dados em qualquer região. Para obter mais informações, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhou quando tentei excluir meu cache vinculado?

Os caches replicados geograficamente e seus grupos de recursos não podem ser excluídos enquanto estiverem vinculados até que você remova o link de replicação geográfica. Se você tentar excluir o grupo de recursos que contém um ou ambos os caches vinculados, os outros recursos no grupo de recursos serão excluídos, mas o grupo de recursos permanecerá no estado de `deleting` e todos os caches vinculados no grupo de recursos permanecerão no estado de `running`. Para excluir completamente o grupo de recursos e os caches vinculados dentro dele, desvincule os caches conforme descrito em [remover um link de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Qual região devo usar para meu cache vinculado secundário?

Em geral, é recomendável que o cache exista na mesma região do Azure que o aplicativo que o acessa. Para aplicativos com regiões primárias e de fallback separadas, é recomendável que os seus caches primário e secundário existam nas mesmas regiões. Para obter mais informações sobre regiões emparelhadas, consulte [práticas recomendadas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona o failover para o cache vinculado secundário?

O failover automático nas regiões do Azure não tem suporte para caches com replicação geográfica. Em um cenário de recuperação de desastres, os clientes devem abrir toda a pilha de aplicativos de maneira coordenada em sua região de backup. Deixar componentes de aplicativos individuais decidir quando alternar para seus backups por conta própria pode afetar negativamente o desempenho. Um dos principais benefícios do Redis é que ele é um armazenamento de baixa latência. Se o aplicativo principal do cliente estiver em uma região diferente de seu cache, o tempo de viagem de ida e volta terá um impacto perceptível no desempenho. Por esse motivo, evitamos o failover automaticamente devido a problemas de disponibilidade transitórios.

Para iniciar um failover iniciado pelo cliente, primeiro desvincule os caches. Em seguida, altere o cliente Redis para usar o ponto de extremidade de conexão do cache secundário (anteriormente vinculado). Quando os dois caches são desvinculados, o cache secundário se torna um cache de leitura-gravação regular e aceita solicitações diretamente de clientes Redis.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md).
