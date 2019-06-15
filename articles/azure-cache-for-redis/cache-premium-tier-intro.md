---
title: Introdução à Cache do Azure para o escalão Premium de Redis | Documentos da Microsoft
description: Saiba como criar e gerir a persistência de Redis, clustering de Redis e suporte VNET para o escalão Premium Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 6960c21091e0bc01c198e713c0c276984566ac41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786087"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introdução à Cache do Azure para o escalão Premium da Redis
A Cache de Redis do Azure é uma cache distribuída e gerida que ajuda a criar aplicações altamente escaláveis e reativas ao fornecer um acesso extremamente rápido aos seus dados. 

O novo escalão de Premium é um escalão preparado para empresas, que inclui todas as funcionalidades do escalão Standard e muito mais, como um melhor desempenho, maiores cargas de trabalho, recuperação após desastre, importar/exportar e maior segurança. Continue a ler para saber mais sobre as funcionalidades adicionais do escalão de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Um melhor desempenho em comparação comparado o escalão Standard ou Basic
**Melhor desempenho ao longo do Standard ou Basic escalão.** Caches no escalão Premium são implementadas no hardware que tem processadores mais rápidos e fornece um melhor desempenho em comparação comparado para o escalão básico ou padrão. As Caches de escalão Premium têm um débito superior e latências mais baixas. 

**Taxa de transferência para a Cache do mesmo tamanho é superior no Premium em comparação com o escalão Standard.** Por exemplo, o débito de uma 53 GB P4 cache (Premium) é 250 mil pedidos por segundo em comparação com a 150 K para C6 (Standard).

Para obter mais informações sobre o tamanho, débito e largura de banda com premium caches, consulte [do Azure na Cache de Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados de Redis
O escalão Premium permite-lhe manter os dados de cache numa conta de armazenamento do Azure. Numa cache básico/Standard todos os dados são armazenados apenas na memória. Em caso de infraestrutura subjacente problemas lá podem ser a potencial perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resiliência contra a perda de dados. A Cache de Redis do Azure oferece RDB e AOF (brevemente), opções no [persistência de Redis](https://redis.io/topics/persistence). 

Para obter instruções sobre como configurar a persistência, veja [como configurar a persistência para uma Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster de Redis
Se quiser criar caches superiores a 53 GB ou queira dividir os dados em vários nós de Redis, pode utilizar o que está disponível no escalão Premium de clustering de Redis. Cada nó é composta por um par de cache primário/réplica gerenciado pelo Azure para elevada disponibilidade. 

**O clustering de redis dá-lhe dimensionamento máximo e débito.** Débito aumenta linearmente à medida que aumenta o número de shards (nós) no cluster. Por ex. Se criar um cluster de P4 de 10 shards, então o débito disponível é 250 mil * 10 = 2,5 milhões de pedidos por segundo. Consulte a [do Azure na Cache de Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, débito e largura de banda com premium caches.

Para começar a utilizar com o clustering, veja [como configurar o clustering para uma Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Segurança e isolamento otimizados
As caches criadas no escalão básico ou Standard estão acessíveis na internet pública. Acesso à Cache é restrito com base na chave de acesso. Com o escalão Premium pode mais garantir que apenas os clientes dentro de uma rede especificado podem aceder à Cache. Pode implementar o Cache do Azure para Redis numa [rede Virtual do Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis.

Para obter mais informações, consulte [como configurar o suporte de rede Virtual para uma Cache do Azure Premium para Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importação/Exportação
Importar/exportar é uma Cache do Azure para a operação de gestão de dados de Redis que permite-lhe importar dados para a Cache de Redis do Azure ou exportar dados a partir do Azure Cache de Redis por importar e exportar uma Cache do Azure para o instantâneo de base de dados de Redis (RDB) de uma cache premium para um BLOBs de páginas numa conta de armazenamento do Azure. Isto permite-lhe migrar entre diferentes a Cache do Azure para instâncias de Redis ou povoar a cache com os dados antes de utilização.

Importação pode ser utilizada para colocar Redis compatíveis RDB ficheiros a partir de qualquer servidor Redis em execução em qualquer cloud ou o ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem como Amazon Web Services e outros. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos previamente. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB do armazenamento do Azure na memória e, em seguida, insere as chaves na cache.

Exportação permite-lhe exportar os dados armazenados na Cache do Azure para ficheiros RDB compatíveis do Redis para Redis. Pode utilizar esta funcionalidade para mover dados de um Cache do Azure para a instância de Redis para outro ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a Cache do Azure para a instância de servidor Redis e o ficheiro é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

Para obter mais informações, consulte [como importar dados para e exportar os dados do Azure Cache de Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar
O escalão premium permite-lhe reiniciar uma ou mais nós de seu cache a pedido. Isto permite-lhe testar a sua aplicação para resiliência em caso de falha. Pode reiniciar os seguintes nós.

* Nó principal da cache
* Nó secundário da cache
* Nós principais e secundários da cache
* Ao utilizar uma cache premium com clustering, pode reiniciar o primário, secundário ou ambos os nós para as partições horizontais individuais na cache

Para obter mais informações, consulte [reinicie](cache-administration.md#reboot) e [reiniciar FAQ](cache-administration.md#reboot-faq).

>[!NOTE]
>Reiniciar funcionalidade está agora ativada para todo o Cache do Azure para os escalões de Redis.
>
>

## <a name="schedule-updates"></a>Agendar atualizações
A funcionalidade de atualizações agendadas permite-lhe designar uma janela de manutenção para a sua cache. Quando a janela de manutenção é especificada, todas as atualizações de servidor de Redis são feitas durante esta janela. Para designar uma janela de manutenção, selecione os dias desejados e especificar a manutenção a janela de iniciar a hora para cada dia. Tenha em atenção que o tempo de janela de manutenção é em UTC. 

Para obter mais informações, consulte [agendar atualizações](cache-administration.md#schedule-updates) e [agendar atualizações FAQ](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas servidor atualizações são efetuadas durante a janela de manutenção agendada de Redis. Não se aplica a janela de manutenção para atualizações do Azure ou as atualizações para o sistema operacional VM.
> 
> 

## <a name="geo-replication"></a>Georreplicação

**Replicação geográfica** fornece um mecanismo para ligar duas escalão Premium da Cache do Azure para instâncias de Redis. Um cache é designado como o cache ligada primário e o outro como o cache secundário ligada. Torna-se a cache de ligado secundária só de leitura e escritos na cache primária de dados são replicados para o cache secundário ligada. Esta funcionalidade pode ser utilizada para replicar uma cache entre regiões do Azure.

Para obter mais informações, consulte [como configurar a georreplicação para a Cache do Azure para Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Dimensionar para o escalão premium
Para dimensionar para o escalão premium, basta escolher um dos escalões premium no **alteração escalão de preço** painel. Também pode dimensionar a sua cache para o escalão premium com o PowerShell e CLI. Para obter instruções passo a passo, consulte [como dimensionar a cache do Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passos Seguintes
Criar uma cache e explore as novas funcionalidades do escalão premium.

* [Como configurar a persistência para uma Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md)
* [Como configurar o suporte de rede Virtual para uma Cache do Azure Premium de Redis](cache-how-to-premium-vnet.md)
* [Como configurar o clustering para uma Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md)
* [Como importar dados para e exportar dados da Cache do Azure para Redis](cache-how-to-import-export-data.md)
* [Como administrar o Azure Cache de Redis](cache-administration.md)

