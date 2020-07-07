---
title: Introdução à Cache Azure para o nível Redis Premium
description: Saiba como criar e gerir a Revalência de Redis, o agrupamento Redis e o suporte VNET para o seu nível Premium Azure Cache para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74121666"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introdução à Cache Azure para o nível Redis Premium
O Azure Cache for Redis é uma cache distribuída e gerida que o ajuda a construir aplicações altamente escaláveis e responsivas, proporcionando acesso super rápido aos seus dados. 

O novo nível Premium é um nível pronto para a Enterprise, que inclui todas as funcionalidades de nível Standard e muito mais, tais como melhor desempenho, maiores cargas de trabalho, recuperação de desastres, importação/exportação e segurança reforçada. Continuar a ler Para saber mais sobre as características adicionais do nível de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em comparação com o nível padrão ou básico
**Melhor desempenho em relação ao nível standard ou básico.** Caches no nível Premium são implantados em hardware que tem processadores mais rápidos e dá melhor desempenho em comparação com o Tier Básico ou Standard. Caches de nível premium têm maior produção e latências mais baixas. 

**A produção para o mesmo tamanho Cache é maior em Premium em comparação com o nível Standard.** Por exemplo, a produção de uma cache P4 (Premium) de 53 GB é de 250K pedidos por segundo em comparação com 150K para C6 (Standard).

Para obter mais informações sobre tamanho, produção e largura de banda com caches premium, consulte [Azure Cache para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados de Redis
O nível Premium permite-lhe persistir os dados da cache numa conta de Armazenamento Azure. Numa cache Basic/Standard todos os dados são armazenados apenas na memória. Em caso de problemas de infraestrutura subjacentes, pode haver uma potencial perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados. A Azure Cache para Redis oferece opções RDB e AOF (em breve) na [persistência de Redis.](https://redis.io/topics/persistence) 

Para obter instruções sobre a configuração da persistência, consulte [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster de Redis
Se pretender criar caches maiores do que 53 GB ou quiser obter dados de fragmentos em vários nós Redis, pode utilizar o clustering Redis que está disponível no nível Premium. Cada nó consiste num par de cache primário/réplica gerido pela Azure para uma elevada disponibilidade. 

**O agrupamento redis dá-lhe a máxima escala e produção.** A produção aumenta linearmente à medida que aumenta o número de fragmentos (nós) no cluster. Por ex. Se criar um cluster P4 de 10 fragmentos, então a produção disponível é de 250K *10 = 2,5 Milhões de pedidos por segundo. Consulte a [Cache Azure para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) para obter mais detalhes sobre tamanho, produção e largura de banda com caches premium.

Para começar com o agrupamento, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)

## <a name="enhanced-security-and-isolation"></a>Segurança reforçada e isolamento
Caches criados no nível Básico ou Standard são acessíveis na internet pública. O acesso à Cache é restrito com base na chave de acesso. Com o nível Premium pode ainda garantir que apenas os clientes dentro de uma rede especificada podem aceder à Cache. Pode implantar Azure Cache para Redis numa [Rede Virtual Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis.

Para obter mais informações, consulte [Como configurar o suporte da Rede Virtual para uma Cache Premium Azure para Redis.](cache-how-to-premium-vnet.md)

## <a name="importexport"></a>Importação/Exportação
Import/Export é uma operação de gestão de dados Azure Cache que permite importar dados para Azure Cache para Redis ou exportar dados da Azure Cache para Redis importando e exportando uma cache Azure para Redis Database (RDB) de uma cache premium para uma bolha de página numa Conta de Armazenamento Azure. Isto permite-lhe migrar entre diferentes Cache Azure para instâncias Redis ou povoar a cache com dados antes de usar.

A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis a partir de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma maneira fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, a Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as chaves na cache.

A exportação permite exportar os dados armazenados em Cache Azure para Redis para redis ficheiros RDB compatíveis. Pode utilizar esta funcionalidade para mover dados de um Cache Azure para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a Cache Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação estiver concluída com um estado de sucesso ou de insucesso, o ficheiro temporário é suprimido.

Para obter mais informações, consulte [como importar dados e exportar dados da Azure Cache para Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar
O nível premium permite-lhe reiniciar um ou mais nós da sua cache a pedido. Isto permite-lhe testar o seu pedido de resiliência em caso de falha. Pode reiniciar os seguintes nós.

* Nó mestre da sua cache
* Nó secundário da sua cache
* Nós primários e secundários da sua cache
* Ao utilizar uma cache premium com clustering, pode reiniciar os nós primários, secundários ou ambos os nós para fragmentos individuais na cache

Para mais informações, consulte [Reboot](cache-administration.md#reboot) e [Reboot FAQ.](cache-administration.md#reboot-faq)

>[!NOTE]
>A funcionalidade reboot está agora ativada para todos os níveis Azure Cache para os níveis Redis.
>
>

## <a name="schedule-updates"></a>Agendar atualizações
A funcionalidade de atualizações programadas permite-lhe designar uma janela de manutenção para o seu cache. Quando a janela de manutenção é especificada, quaisquer atualizações do servidor Redis são efetuadas durante esta janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique a hora de início da janela de manutenção para cada dia. Note que o tempo de janela de manutenção está na UTC. 

Para mais informações, consulte [atualizações de horários](cache-administration.md#schedule-updates) e [agendar as faqs FAQ](cache-administration.md#schedule-updates-faq).

> [!NOTE]
> Apenas as atualizações do servidor Redis são efetuadas durante a janela de manutenção programada. A janela de manutenção não se aplica às atualizações ou atualizações do Azure ao sistema operativo VM.
> 
> 

## <a name="geo-replication"></a>Georreplicação

**A geo-replicação** fornece um mecanismo para ligar dois Cache Azure de nível Premium para instâncias Redis. Uma cache é designada como a cache principal ligada, e a outra como a cache ligada secundária. A cache ligada secundária torna-se apenas de leitura, e os dados escritos na cache primária são replicados para a cache ligada secundária. Esta funcionalidade pode ser usada para replicar uma cache em todas as regiões de Azure.

Para obter mais informações, consulte [Como configurar a geo-replicação para Azure Cache para Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Para escalar para o nível premium
Para escalar para o nível premium, basta escolher um dos níveis premium na lâmina do **nível de preços change.** Também pode escalar a sua cache para o nível premium usando PowerShell e CLI. Para obter instruções passo a passo, consulte [Como escalar a cache Azure para redis](cache-how-to-scale.md) e como [automatizar uma operação de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Próximos passos
Crie uma cache e explore as novas funcionalidades de nível premium.

* [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md)
* [Como configurar o suporte da Rede Virtual para uma Cache Premium Azure para Redis](cache-how-to-premium-vnet.md)
* [Como configurar o agrupamento para uma Cache Premium Azure para Redis](cache-how-to-premium-clustering.md)
* [Como importar dados e exportar dados da Azure Cache para redis](cache-how-to-import-export-data.md)
* [Como administrar Azure Cache para Redis](cache-administration.md)

