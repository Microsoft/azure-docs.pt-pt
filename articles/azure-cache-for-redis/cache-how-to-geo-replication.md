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
ms.date: 09/15/2017
ms.author: yegu
ms.openlocfilehash: 383ea07005d7dae47cd0ef1da8a4a57d8b20d613
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435818"
---
# <a name="how-to-configure-geo-replication-for-azure-cache-for-redis"></a>Como configurar a georreplicação para a Cache do Azure para Redis

Replicação geográfica fornece um mecanismo para ligar duas escalão Premium da Cache do Azure para instâncias de Redis. Um cache é designado como o cache ligada primário e o outro como o cache secundário ligada. Torna-se a cache de ligado secundária só de leitura e escritos na cache primária de dados são replicados para o cache secundário ligada. Esta funcionalidade pode ser utilizada para replicar uma cache entre regiões do Azure. Este artigo fornece um guia para configurar a georreplicação para o escalão Premium Cache do Azure para instâncias de Redis.

## <a name="geo-replication-prerequisites"></a>Pré-requisitos de georreplicação

Para configurar a georreplicação entre dois caches, devem ser cumpridos os seguintes pré-requisitos:

- Tem de ser ambos os caches [escalão Premium](cache-premium-tier-intro.md) coloca em cache.
- Ambos os caches tem de ser na mesma subscrição do Azure.
- O cache ligada secundário tem de ser o mesmo escalão de preços ou um escalão de preço maior do que o cache ligada primário.
- Se o cache ligada principal tem o clustering ativado, o cache ligada secundário tem de ter clustering ativado com o mesmo número de partições horizontais que o cache ligada primário.
- Ambos os caches têm de ser criadas e num Estado de execução.
- Persistência não tem de estar ativada em qualquer um dos cache.
- Georreplicação entre caches na mesma VNET é suportada. 
- Georreplicação entre caches em VNETs em modo de peering na mesma região é atualmente uma funcionalidade de pré-visualização. As duas VNETs devem ser configurados de forma a que recursos as VNETs são capazes de alcançar entre si através de ligações de TCP.
- Georreplicação entre caches em VNETs em modo de peering em diferentes regiões ainda não é suportada, mas estarão em pré-visualização em breve.

Após a configuração da replicação geográfica, as seguintes restrições aplicam-se para o par de cache ligada:

- O cache ligada secundário é só de leitura; pode lê-lo, mas não é possível escrever todos os dados. 
- Todos os dados que se encontrava na cache secundária ligado antes da ligação foi adicionada são removidos. Se os replicação geográfica, em seguida, é removida no entanto, os dados replicados permanecem na cache secundária ligado.
- Não pode iniciar uma [operação de dimensionamento](cache-how-to-scale.md) em qualquer um dos cache ou [alterar o número de partições horizontais](cache-how-to-premium-clustering.md) se a cache tiver clustering ativado.
- Não é possível ativar a persistência na cache de ambos.
- Pode usar [exportar](cache-how-to-import-export-data.md#export) com o cache, mas só é possível [importação](cache-how-to-import-export-data.md#import) no cache principal ligado.
- Não é possível eliminar o cache ligada ou o grupo de recursos que contém os mesmos, até remover a ligação de replicação geográfica. Para obter mais informações, consulte [por que a operação falhar quando tentei eliminar a minha cache ligada?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Se os dois caches em regiões diferentes, os custos da saída de rede aplicam-se aos dados replicados em várias regiões para o cache secundário ligada. Para obter mais informações, consulte [quanto custa para replicar dados entre regiões do Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Não existe nenhuma ativação pós-falha automática para o secundário cache ligada se cache primária (e sua réplica) ficam inativos. Por ordem para as aplicações de cliente de ativação pós-falha, terá de remover a ligação de replicação geográfica e apontar os aplicativos de cliente para a cache do que era anteriormente conhecido como o cache secundário ligada manualmente. Para obter mais informações, consulte [como realizar a ativação pós-falha para a cache ligada secundário funciona?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Adicionar uma ligação de replicação geográfica

1. Para vincular dois caches de premium para georreplicação, clique em **georreplicação** no menu de recursos da cache se destina a ser o principal ligado em cache e, em seguida, clique em **adicionar ligação de replicação de cache** do o **georreplicação** painel.

    ![Adicionar ligação](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Clique no nome da cache secundária pretendido do **caches compatíveis** lista. Se a cache pretendida não for apresentado na lista, certifique-se de que o [pré-requisitos de georreplicação](#geo-replication-prerequisites) para a cache secundária pretendida são cumpridos. Para filtrar os caches por região, clique em região pretendida no mapa para mostrar apenas esses caches no **caches compatíveis** lista.

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

    Durante o processo de ligação, o cache ligada primário permanece disponível para utilização, mas a cache de ligado secundária não está disponível enquanto o processo de ligação for concluída.

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

- Quando ligar caches de dois em conjunto para georreplicação, o cache ligada primário permanece disponível para utilização, mas a cache de ligado secundária não está disponível enquanto o processo de ligação for concluída.
- Quando remover a ligação de georreplicação entre dois caches, ambas as caches permanecem disponíveis para utilização.

### <a name="can-i-link-more-than-two-caches-together"></a>Posso ligar caches de mais de dois em conjunto?

Não, ao utilizar a georreplicação pode apenas ligar caches de dois em conjunto.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Pode ligar dois caches de diferentes subscrições do Azure?

Não, ambas as caches tem de ser na mesma subscrição do Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Pode ligar dois caches com diferentes tamanhos?

Sim, desde que o cache ligada secundário é maior do que o cache ligada primário.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Pode utilizar replicação geográfica com o clustering ativado?

Sim, desde que ambas as caches de tem o mesmo número de partições horizontais.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Pode utilizar a georreplicação com meu caches numa VNET?

Sim, são suportados os replicação geográfica das caches em VNETs. 

- Georreplicação entre caches na mesma VNET é suportada.
- Georreplicação entre caches em VNETs diferentes também é suportada, desde que as duas VNETs estão configuradas de forma a que recursos as VNETs são capazes de alcançar entre si através de ligações de TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>O que é a agenda de replicação para georreplicação Redis?

A replicação não acontece com base numa agenda específica, é como contínua e assíncronas todas as escritas feitas para o primário instantaneamente assincronamente são replicadas no secundário.

### <a name="how-long-does-geo-replication-replication-take"></a>Quanto tempo demora a replicação de georreplicação?

A replicação é incremental, assíncrono e contínua e o tempo que demora, normalmente, não é muito diferente do que a latência em várias regiões. Em determinadas circunstâncias, em determinadas alturas secundário poderá ser necessário fazer uma sincronização completa dos dados dos principais. O tempo de replicação, neste caso é dependente do número de fatores como: inter de carga na cache principal, de largura de banda disponível na máquina de cache, a latência de região etc. Por exemplo, com base em alguns testes Achamos esse tempo de replicação para um completo 53 GB georreplicado emparelhe na região Leste-nos e regiões do Oeste dos E.U.A. podem ser qualquer número entre 5 a 10 minutos.

### <a name="is-the-replication-recovery-point-guaranteed"></a>O ponto de recuperação de replicação é garantido?

Atualmente, para as caches num modo georreplicado, funcionalidade de persistência e importar/exportar está desativada. Portanto, no caso de um cliente iniciada a ativação pós-falha ou em casos em que uma ligação de replicação tiver sido interrompida entre o par georreplicado, secundário irá reter a memória em dados que ele tenha sincronizado dos principais até esse ponto no tempo. Não há nenhuma garantia de ponto de recuperação fornecida no tipo de situação.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Posso utilizar o PowerShell ou CLI do Azure para gerir os replicação geográfica?

Neste momento só pode gerir com o portal do Azure de georreplicação.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Quanto custa para replicar dados entre regiões do Azure?

Ao utilizar a georreplicação, os dados da cache primária ligado são replicados para o cache secundário ligada. Se os dois caches ligados estiverem na mesma região do Azure, não existe nenhum custo associado a transferência de dados. Se os dois caches ligados estiverem em diferentes regiões do Azure, a cobrança de transferência de dados de Georreplicação é o custo de largura de banda da replicação desses dados para outras regiões do Azure. Para obter mais informações, consulte [detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Por que a operação falhar quando tentei eliminar a minha cache ligada?

Quando dois caches estão ligadas em conjunto, não é possível eliminar a cache ou o grupo de recursos que contém-los até remover a ligação de replicação geográfica. Se tentar eliminar o grupo de recursos que contém uma ou ambas as caches de ligado, os outros recursos no grupo de recursos são eliminados, mas o grupo de recursos permanece no `deleting` caches de estado e qualquer ligadas no grupo de recursos permanecem na `running`estado. Para concluir a eliminação do grupo de recursos e os caches ligados dentro da mesma, quebrar a ligação de replicação geográfica conforme descrito em [remover uma ligação de replicação geográfica](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Em que região deve ser usado para minha cache ligada secundário?

Em geral, recomenda-se para a sua cache de existir na mesma região do Azure que a aplicação que acede ao mesmo. Se seu aplicativo tiver uma região principal e de contingência, em seguida, as caches primárias e secundárias devem existir nessas mesmas regiões. Para obter mais informações sobre as regiões emparelhadas, consulte [práticas recomendadas – regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Como funciona a ativação pós-falha para a cache secundária ligado?

Na versão inicial da georreplicação, a Cache de Redis do Azure não suporta a ativação pós-falha automática em regiões do Azure. Replicação geográfica é utilizada principalmente num cenário de recuperação após desastre. Num cenário de recuperação após desastre, os clientes devem apresentada a pilha de todo o aplicativo numa região de cópia de segurança na forma coordenada, em vez de deixar os componentes de aplicações individuais decidir quando mudar para as suas cópias de segurança por conta própria. Isto é especialmente relevante ao Redis. Um dos principais benefícios de Redis é o que é um arquivo de latência muito baixa. Se Redis utilizada por uma aplicação realiza a ativação pós-falha para uma região do Azure diferente, mas a escala de computação não, o tempo de ida adicionado seria ter um impacto considerável no desempenho. Por esse motivo, gostaríamos de evitar a falha de Redis através de automaticamente devido a problemas de disponibilidade transitório.

Atualmente, para iniciar a ativação pós-falha, terá de remover a ligação de replicação geográfica no portal do Azure e, em seguida, alterar o ponto final de ligação no cliente do Redis da cache primária ligado para a cache secundária (anteriormente conhecido como ligada). Quando os dois caches são desassociar, a réplica se tornar uma cache de leitura / escrita regular novamente e aceita os pedidos diretamente a partir de clientes Redis.


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md).

