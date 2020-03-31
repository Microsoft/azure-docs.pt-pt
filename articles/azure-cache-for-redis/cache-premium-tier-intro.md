---
title: Introdução ao Cache Azure para o nível Redis Premium
description: Aprenda a criar e gerir a Persistência Redis, o agrupamento Redis e o suporte vNET para o seu azure cache de nível Premium para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74121666"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introdução ao Cache Azure para o nível Redis Premium
O Azure Cache for Redis é uma cache distribuída e gerida que o ajuda a construir aplicações altamente escaláveis e responsivas, proporcionando acesso super rápido aos seus dados. 

O novo nível Premium é um nível pronto para a Enterprise, que inclui todas as características de nível Standard e muito mais, tais como melhor desempenho, maiores cargas de trabalho, recuperação de desastres, importação/exportação e segurança reforçada. Continuar a ler Para saber mais sobre as características adicionais do nível de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em comparação com o nível Standard ou Básico
**Melhor desempenho em relação ao nível Standard ou Básico.** Os caches no nível Premium são implantados em hardware que tem processadores mais rápidos e dá melhor desempenho em comparação com o Basic ou Standard Tier. Os caches de nível premium têm maior entrada e lestabeleceções mais baixas. 

**A entrada para o mesmo cache de tamanho é maior em Premium em comparação com o nível Standard.** Por exemplo, a entrada de uma cache P4 (Premium) de 53 GB é de 250K pedidos por segundo em comparação com 150K para C6 (Standard).

Para obter mais informações sobre tamanho, entrada e largura de banda com caches premium, consulte [Azure Cache para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados de Redis
O nível Premium permite-lhe persistir os dados de cache numa conta de Armazenamento Azure. Numa cache Basic/Standard, todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacentes, pode haver uma potencial perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados. Azure Cache for Redis oferece opções RDB e AOF (em breve) na [persistência de Redis](https://redis.io/topics/persistence). 

Para obter instruções sobre a configuração da persistência, consulte [Como configurar a persistência de um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster de Redis
Se quiser criar caches superiores a 53 GB ou pretender estoirar dados em vários nós redis, pode utilizar o clusterredis que está disponível no nível Premium. Cada nó consiste num par de cache primário/réplica gerido pelo Azure para alta disponibilidade. 

**O agrupamento redis dá-lhe escala máxima e entrada.** A entrada aumenta linearmente à medida que aumenta o número de fragmentos (nós) no cluster. Por ex. Se criar um cluster P4 de 10 fragmentos, então a entrada disponível é de 250K *10 = 2,5 Milhões de pedidos por segundo. Consulte o [Azure Cache para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) para obter mais detalhes sobre tamanho, entrada e largura de banda com caches premium.

Para começar com o agrupamento, veja [como configurar o agrupamento para um Premium Azure Cache para Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Segurança reforçada e isolamento
Os caches criados no nível Básico ou Standard são acessíveis na internet pública. O acesso ao Cache é restrito com base na chave de acesso. Com o nível Premium pode ainda garantir que apenas os clientes dentro de uma rede especificada podem aceder ao Cache. Pode implantar o Azure Cache para Redis numa [Rede Virtual Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis.

Para mais informações, consulte [como configurar o suporte da Rede Virtual para um Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importação/Exportação
Import/Export é uma operação de gestão de dados Azure Cache para Redis que lhe permite importar dados para Azure Cache para Redis ou exportar dados de Azure Cache para Redis, importando e exportando um cache azure para redis Database (RDB) de uma cache premium para uma página blob em uma conta de armazenamento Azure. Isto permite-lhe migrar entre diferentes casos de Azure Cache para redis ou povoar a cache com dados antes de serem utilizados.

A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma forma fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, o Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as teclas na cache.

A exportação permite-lhe exportar os dados armazenados em Azure Cache para Redis para redis compatíveis com ficheiros RDB compatíveis. Pode utilizar esta funcionalidade para mover dados de um Azure Cache para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a caixa Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação termina com um estatuto de sucesso ou falha, o ficheiro temporário é suprimido.

Para obter mais informações, consulte [como importar dados e exportar dados de Azure Cache para Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar
O nível premium permite-lhe reiniciar um ou mais nós da sua cache a pedido. Isto permite-lhe testar a sua aplicação de resiliência em caso de falha. Pode reiniciar os seguintes nódosos.

* Nó mestre da sua cache
* Nó secundário da sua cache
* Ambos os nós primários e secundários da sua cache
* Ao utilizar uma cache premium com agrupamento, pode reiniciar os nós primários, secundários ou ambos os nós para fragmentos individuais na cache

Para mais informações, consulte [Reboot](cache-administration.md#reboot) e [Reboot FAQ](cache-administration.md#reboot-faq).

>[!NOTE]
>A funcionalidade de reinicialização está agora ativada para todos os níveis De Cache Azure para redis.
>
>

## <a name="schedule-updates"></a>Agendar atualizações
A funcionalidade de atualizações programada permite-lhe designar uma janela de manutenção para a sua cache. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor Redis são feitas durante esta janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique a hora de início da janela de manutenção para cada dia. Note que o tempo da janela de manutenção está na UTC. 

Para mais informações, consulte [atualizações de Agenda](cache-administration.md#schedule-updates) e [atualizar as FAQ](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas as atualizações do servidor Redis são feitas durante a janela de manutenção programada. A janela de manutenção não se aplica às atualizações ou atualizações do Azure ao sistema operativo VM.
> 
> 

## <a name="geo-replication"></a>Georreplicação

**A geo-replicação** fornece um mecanismo para ligar dois acabamentos Premium Azure Cache para instâncias Redis. Uma cache é designada como a cache ligada primária, e a outra como a cache secundária ligada. A cache ligada secundária torna-se apenas de leitura, e os dados escritos para a cache primária são replicados para a cache ligada secundária. Esta funcionalidade pode ser usada para replicar um cache em todas as regiões de Azure.

Para mais informações, consulte [Como configurar a Geo-replicação para Azure Cache for Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Para escalar para o nível premium
Para escalar para o nível premium, basta escolher um dos níveis premium na lâmina de nível de **preços Change.** Também pode escalar a sua cache para o nível premium utilizando powerShell e CLI. Para obter instruções passo a passo, consulte Como escalar o [Cache Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação](cache-how-to-scale.md#how-to-automate-a-scaling-operation)de escala .

## <a name="next-steps"></a>Passos seguintes
Crie um cache e explore as novas funcionalidades de nível premium.

* [Como configurar a persistência de um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md)
* [Como configurar o suporte da Rede Virtual para um Cache Premium Azure para Redis](cache-how-to-premium-vnet.md)
* [Como configurar o agrupamento para um Premium Azure Cache para Redis](cache-how-to-premium-clustering.md)
* [Como importar dados e exportar dados do Azure Cache para redis](cache-how-to-import-export-data.md)
* [Como administrar o Azure Cache para redis](cache-administration.md)

