---
title: Como configurar a georreplicação para a Cache do Azure para Redis | Documentos da Microsoft
description: Aprenda a replicar a sua Cache do Azure para instâncias de Redis em regiões geográficas.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: 4254175955c3560c7bd0fdd08c6b60c318238b76
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991564"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Como configurar a georreplicação para a Cache do Azure para Redis

Replicação geográfica fornece um mecanismo para ligar duas escalão Premium da Cache do Azure para instâncias de Redis. Um cache é escolhida como o cache ligada primário e o outro como o cache secundário ligada. Torna-se a cache de ligado secundária só de leitura e escritos na cache primária de dados são replicados para o cache secundário ligada. Esta funcionalidade pode ser utilizada para replicar uma cache entre regiões do Azure. Este artigo fornece um guia para configurar a georreplicação para o escalão Premium Cache do Azure para instâncias de Redis.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de georreplicação

Para configurar a georreplicação entre dois caches, devem ser cumpridos os seguintes pré-requisitos:

- Ambos os caches são [escalão Premium](cache-premium-tier-intro.md) coloca em cache.
- Ambos os caches estão na mesma subscrição do Azure.
- O cache ligada secundário é o mesmo tamanho de cache ou um tamanho de cache maior do que o cache ligada primário.
- Ambos os caches são criadas e num Estado de execução.

Algumas funcionalidades não são suportadas com georreplicação:

- Persistência não é suportada com georreplicação.
- Clustering é suportado se ambos os caches têm o clustering ativado e tem o mesmo número de partições horizontais.
- São suportadas as caches na mesma VNET.
- Caches em VNETs diferentes são suportadas com avisos. Ver [pode utilizar a georreplicação com meu caches numa VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) para obter mais informações.

Após a configuração da replicação geográfica, as seguintes restrições aplicam-se para o par de cache ligada:

- O cache ligada secundário é só de leitura; pode lê-lo, mas não é possível escrever todos os dados. 
- Todos os dados que se encontrava na cache secundária ligado antes da ligação foi adicionada são removidos. Se os replicação geográfica é posterior no entanto, remover os dados replicados permanecem na cache secundária ligado.
- Não é possível [dimensionamento](cache-how-to-scale.md) qualquer cache enquanto as caches estão ligadas.
- Não é possível [alterar o número de partições horizontais](cache-how-to-premium-clustering.md) se a cache tiver clustering ativado.
- Não é possível ativar a persistência na cache de ambos.
- Pode [exportar](cache-how-to-import-export-data.md#export) partir de qualquer cache.
- Não é possível [importação](cache-how-to-import-export-data.md#import) no cache secundária ligado.
- Não é possível eliminar o cache ligada ou o grupo de recursos que contém os mesmos, até desvinculá os caches. Para obter mais informações, consulte [por que a operação falhar quando tentei eliminar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os caches estão em diferentes regiões, os custos da saída de rede aplicam-se aos dados movidos entre regiões. Para obter mais informações, consulte [quanto custa para replicar dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- A ativação pós-falha automática não ocorre entre o cache ligada primário e secundário. Para obter mais informações e obter informações sobre como realizar a ativação pós-falha de um aplicativo cliente, consulte [como realizar a ativação pós-falha para a cache ligada secundário funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar uma ligação de replicação geográfica

1. Para ligar dois caches em conjunto para georreplicação, fist clique **georreplicação** no menu de recursos de cache que pretende para ser o principal ligado a cache. Em seguida, clique em **adicionar ligação de replicação de cache** partir do **georreplicação** painel.

    ![Adicionar ligação](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome da sua cache secundária pretendido do **caches compatíveis** lista. Se a sua cache secundária não for apresentado na lista, certifique-se de que o [pré-requisitos de georreplicação](#geo-replication-prerequisites) para a cache secundária são cumpridos. Para filtrar os caches por região, clique na região no mapa para mostrar apenas esses caches no **caches compatíveis** lista.

    ![Caches compatíveis de georreplicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Também pode iniciar o processo de ligação ou ver detalhes sobre a cache secundária através do menu de contexto.

    ![Menu de contexto de georreplicação](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Clique em **Link** para vincular os dois caches e iniciar o processo de replicação.

    ![Caches de ligação](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Pode ver o progresso do processo de replicação na **georreplicação** painel.

    ![Estado de ligação](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Também pode ver o estado da ligação na **descrição geral** painel para caches primários e secundários.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Assim que o processo de replicação estiver concluído, o **estado de ligação** é alterado para **Succeeded**.

    ![Estado da cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    O cache ligada primário permanece disponível para utilização durante o processo de ligação. A cache de ligado secundária não está disponível enquanto o processo de ligação for concluída.

## <a name="remove-a-geo-replication-link"></a>Remover uma ligação de replicação geográfica

1. Para remover a associação entre dois caches e parar a replicação geográfica, clique em **desassociar caches** partir do **georreplicação** painel.
    
    ![Desassociar caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Quando tiver concluído o processo a desassociar, a cache secundária está disponível para leituras e gravações.

>[!NOTE]
>Quando a ligação de georreplicação é removida, os dados replicados da cache primária ligado permanecem na cache secundária.
>
>

## <a name="geo-replication-faq"></a>FAQ de georreplicação

- [Pode utilizar a georreplicação com uma cache de escalão Standard ou Basic?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Minha cache está disponível para utilização durante o processo de ligação ou a desassociar?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Posso ligar caches de mais de dois em conjunto?](#can-i-link-more-than-two-caches-together)
- [Pode ligar dois caches de diferentes subscrições do Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Pode ligar dois caches com diferentes tamanhos?](#can-i-link-two-caches-with-different-sizes)
- [Pode utilizar replicação geográfica com o clustering ativado?](#can-i-use-geo-replication-with-clustering-enabled)
- [Pode utilizar a georreplicação com meu caches numa VNET?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [O que é a agenda de replicação para georreplicação Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quanto tempo demora a replicação de georreplicação?](#how-long-does-geo-replication-replication-take)
- [O ponto de recuperação de replicação é garantido?](#is-the-replication-recovery-point-guaranteed)
- [Posso utilizar o PowerShell ou CLI do Azure para gerir os replicação geográfica?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Quanto custa para replicar dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Por que a operação falhar quando tentei eliminar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Em que região deve ser usado para minha cache ligada secundário?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Como funciona a ativação pós-falha para a cache secundária ligado?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Pode utilizar a georreplicação com uma cache de escalão Standard ou Basic?

Não, a georreplicação só está disponível para as caches de escalão Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Minha cache está disponível para utilização durante o processo de ligação ou a desassociar?

- Quando cria uma ligação, o cache ligada primário permanece disponível enquanto o processo de ligação é concluída.
- Quando cria uma ligação, o cache ligada secundário não está disponível enquanto o processo de ligação for concluída.
- Quando a desassociação, ambas as caches permanecem disponíveis enquanto o processo a desassociar é concluída.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso ligar caches de mais de dois em conjunto?

Não, pode apenas vincular dois caches.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Pode ligar dois caches de diferentes subscrições do Azure?

Não, ambas as caches tem de ser na mesma subscrição do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Pode ligar dois caches com diferentes tamanhos?

Sim, desde que o cache ligada secundário é maior do que o cache ligada primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Pode utilizar replicação geográfica com o clustering ativado?

Sim, desde que ambas as caches de tem o mesmo número de partições horizontais.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Pode utilizar a georreplicação com meu caches numa VNET?

Sim, georreplicação de caches em VNETs é suportada com advertências:

- Georreplicação entre caches na mesma VNET é suportada.
- Também é suportada a georreplicação entre caches em VNETs diferentes.
  - Se as VNETs estiverem na mesma região, pode ligá-las usando [peering de VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou uma [ligação de Gateway de VPN VNET a VNET](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V).
  - Se as VNETs estiverem em regiões diferentes, replicação geográfica utilização o VNET peering não é suportado devido a uma restrição com balanceadores de carga interno básico. Para obter mais informações sobre restrições de peering de VNET, veja [requisitos de rede Virtual - Peering - e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). A solução recomendada é utilizar uma ligação de Gateway de VPN VNET a VNET.

Usando [este modelo do Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), pode implementar rapidamente dois caches georreplicado para uma VNET ligada com uma ligação de Gateway de VPN VNET a VNET.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é a agenda de replicação para georreplicação Redis?

A replicação é assíncrona e contínuo e não acontece com base numa agenda específica. Todas as escritas feitas para o primário são instantaneamente e assíncrona replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo demora a replicação de georreplicação?

A replicação é incremental, assíncrono e contínua e o tempo necessário não é muito diferente da latência em várias regiões. Em determinadas circunstâncias, a cache secundária pode ser necessário fazer uma sincronização completa dos dados dos principais. O tempo de replicação, neste caso é dependente do número de fatores como: a carga sobre a cache primária, a largura de banda disponível e a latência de inter-regional. Descobrimos que o tempo de replicação para um par de georreplicado completa de 53 GB pode ser qualquer número entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Para as caches num modo georreplicado, persistência está desativada. Se um par de georreplicado for desassociado, por exemplo, uma ativação pós-falha iniciada pelo cliente, o cache secundária ligado mantém seus dados sincronizados até esse ponto no tempo. Nenhum ponto de recuperação é garantido em tais situações.

Para obter um ponto de recuperação [exportar](cache-how-to-import-export-data.md#export) partir de qualquer cache. Posteriormente, pode [importação](cache-how-to-import-export-data.md#import) no cache principal ligado.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso utilizar o PowerShell ou CLI do Azure para gerir os replicação geográfica?

Sim, os replicação geográfica pode ser gerida com o portal do Azure, o PowerShell ou a CLI do Azure. Para obter mais informações, consulte a [documentos do PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) ou [docs da CLI do Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar dados entre regiões do Azure?

Ao utilizar a georreplicação, os dados da cache primária ligado são replicados para o cache secundário ligada. Não existe nenhum custo associado para a transferência de dados, se os dois caches ligados estão na mesma região. Se os dois caches ligados estão em diferentes regiões, a cobrança da transferência de dados é o custo de saída de rede de mover a partir de qualquer região de dados. Para obter mais informações, consulte [detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhar quando tentei eliminar a minha cache ligada?

Não não possível eliminar o caches georreplicado e os respetivos grupos de recursos, enquanto ligado até remover a ligação de replicação geográfica. Se tentar eliminar o grupo de recursos que contém uma ou ambas as caches de ligado, os outros recursos no grupo de recursos são eliminados, mas o grupo de recursos permanece no `deleting` caches de estado e qualquer ligadas no grupo de recursos permanecem na `running`estado. Para eliminar completamente o grupo de recursos e os caches ligados dentro dele, desassociar caches, conforme descrito em [remover uma ligação de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Em que região deve ser usado para minha cache ligada secundário?

Em geral, recomenda-se para a sua cache de existir na mesma região do Azure que a aplicação que acede ao mesmo. Para aplicações com as regiões principais e de contingência separadas, recomenda-se que as caches primárias e secundárias existem nessas mesmas regiões. Para obter mais informações sobre as regiões emparelhadas, consulte [práticas recomendadas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona a ativação pós-falha para a cache secundária ligado?

A ativação pós-falha automática em regiões do Azure não é suportada para as caches georreplicado. Num cenário de recuperação após desastre, os clientes devem aparecer a pilha de todo o aplicativo de forma coordenada na sua região de cópia de segurança. Permitir que o aplicativo individual que decidir de componentes quando mudar para as suas cópias de segurança por conta própria pode afetar negativamente o desempenho. Um dos principais benefícios de Redis é o que é um arquivo de latência muito baixa. Se o aplicativo do cliente de principal está numa região diferente da respetiva cache, o tempo de ida e volta adicional seria ter um impacto considerável no desempenho. Por esse motivo, podemos evitar efetuar automaticamente a ativação pós-falha devido a problemas de disponibilidade transitório.

Para iniciar uma ativação pós-falha iniciada pelo cliente, desassocie primeiro os caches. Em seguida, altere o seu cliente de Redis para utilizar o ponto final de ligação da cache secundária (anteriormente conhecido como ligado). Quando os dois caches são desassociados, a cache secundária torna-se de uma cache de leitura / escrita regular novamente e aceita os pedidos diretamente a partir de clientes Redis.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md).
