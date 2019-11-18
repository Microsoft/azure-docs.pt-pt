---
title: Introdução ao cache do Azure para a camada Premium do Redis
description: Saiba como criar e gerenciar persistência de Redis, clustering de Redis e suporte a VNET para o cache do Azure da camada Premium para instâncias de Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: aadcc13d2397f10ea40f06d1259c86b9a179c38b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121666"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Introdução ao cache do Azure para a camada Premium do Redis
O cache do Azure para Redis é um cache gerenciado e distribuído que ajuda você a criar aplicativos altamente escalonáveis e responsivos, fornecendo acesso extremamente rápido aos seus dados. 

A nova camada Premium é uma camada pronta para empresas, que inclui todos os recursos de camada Standard e muito mais, como melhor desempenho, cargas de trabalho maiores, recuperação de desastre, importação/exportação e segurança aprimorada. Continue lendo para saber mais sobre os recursos adicionais da camada de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Melhor desempenho em comparação com a camada Standard ou básica
**Melhor desempenho em relação à camada Standard ou básica.** Os caches na camada Premium são implantados em hardware que tem processadores mais rápidos e oferece melhor desempenho em comparação com a camada básica ou Standard. Os caches da camada Premium têm mais taxa de transferência e latências menores. 

**A taxa de transferência para o mesmo cache de tamanho é maior em Premium em comparação com a camada Standard.** Por exemplo, a taxa de transferência de um cache P4 (Premium) de 53 GB é 250 mil solicitações por segundo em comparação com 150 mil para C6 (Standard).

Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches Premium, consulte [perguntas frequentes sobre o cache do Azure para Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Persistência de dados de Redis
A camada Premium permite que você persista os dados de cache em uma conta de armazenamento do Azure. Em um cache básico/Standard, todos os dados são armazenados apenas na memória. No caso de problemas de infraestrutura subjacente, pode haver possível perda de dados. É recomendável usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra perda de dados. O cache do Azure para Redis oferece opções de RDB e AOF (em breve) no [Redis Persistence](https://redis.io/topics/persistence). 

Para obter instruções sobre como configurar a persistência, consulte [como configurar a persistência para um cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

## <a name="redis-cluster"></a>Cluster de Redis
Se você quiser criar caches maiores que 53 GB ou desejar fragmentar dados em vários nós do Redis, poderá usar o clustering do Redis que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica gerenciado pelo Azure para alta disponibilidade. 

**O clustering do Redis oferece escala e taxa de transferência máximas.** A taxa de transferência aumenta linearmente à medida que você aumenta o número de fragmentos (nós) no cluster. Por ex. Se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será 250 mil * 10 = 2,5 milhões solicitações por segundo. Consulte o [cache do Azure para obter perguntas frequentes](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) sobre o Redis para obter mais detalhes sobre tamanho, taxa de transferência e largura de banda com caches Premium.

Para começar a usar o clustering, consulte [como configurar o clustering para um cache Premium do Azure para Redis](cache-how-to-premium-clustering.md).

## <a name="enhanced-security-and-isolation"></a>Segurança e isolamento otimizados
Os caches criados na camada básica ou Standard podem ser acessados na Internet pública. O acesso ao cache é restrito com base na chave de acesso. Com a camada Premium, você pode garantir que somente os clientes em uma rede especificada possam acessar o cache. Você pode implantar o cache do Azure para Redis em uma [VNet (rede virtual) do Azure](https://azure.microsoft.com/services/virtual-network/). Pode utilizar todas as funcionalidades de VNet, como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringir ainda mais o acesso ao Redis.

Para obter mais informações, consulte [como configurar o suporte de rede virtual para um cache do Azure Premium para Redis](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importação/Exportação
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados do Redis, que permite importar dados para o cache do Azure para Redis ou exportar dados do cache do Azure para Redis importando e exportando um instantâneo do RDB (cache do Azure para o Redis de armazenamento) de um cache Premium para um blob de páginas em uma conta do Azure Storage. Isso permite que você migre entre diferentes cache do Azure para instâncias Redis ou preencha o cache com os dados antes de usar.

A importação pode ser usada para colocar arquivos RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. A importação de dados é uma maneira fácil de criar um cache com dados preenchidos previamente. Durante o processo de importação, o cache do Azure para Redis carrega os arquivos de RDB do armazenamento do Azure na memória e, em seguida, insere as chaves no cache.

A exportação permite exportar os dados armazenados no cache do Azure para Redis para arquivos RDB compatíveis com o Redis. Você pode usar esse recurso para mover dados de um cache do Azure para instância Redis para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com um status de êxito ou falha, o arquivo temporário será excluído.

Para obter mais informações, consulte [como importar dados e exportar dados do cache do Azure para Redis](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Reiniciar
A camada Premium permite reinicializar um ou mais nós do cache sob demanda. Isso permite que você teste seu aplicativo quanto à resiliência em caso de falha. Você pode reinicializar os nós a seguir.

* Nó mestre do seu cache
* Nó secundário do seu cache
* Nós primários e secundários do cache
* Ao usar um cache Premium com clustering, você pode reinicializar os nós primário, secundário ou ambos para fragmentos individuais no cache

Para obter mais informações, consulte perguntas frequentes de [reinicialização](cache-administration.md#reboot) e [reinicialização](cache-administration.md#reboot-faq).

>[!NOTE]
>A funcionalidade de reinicialização agora está habilitada para todo o cache do Azure para camadas Redis.
>
>

## <a name="schedule-updates"></a>Agendar atualizações
O recurso de atualizações agendadas permite designar uma janela de manutenção para seu cache. Quando a janela de manutenção é especificada, todas as atualizações do servidor Redis são feitas durante essa janela. Para designar uma janela de manutenção, selecione os dias desejados e especifique a hora de início da janela de manutenção para cada dia. Observe que o tempo da janela de manutenção está em UTC. 

Para obter mais informações, consulte [perguntas frequentes sobre](cache-administration.md#schedule-updates-faq) [agendar atualizações](cache-administration.md#schedule-updates) e agendar atualizações.

> [!NOTE]
> Somente as atualizações do Redis Server são feitas durante a janela de manutenção agendada. A janela de manutenção não se aplica a atualizações do Azure ou a atualizações para o sistema operacional da VM.
> 
> 

## <a name="geo-replication"></a>Georreplicação

**A replicação geográfica** fornece um mecanismo para vincular duas camadas do cache do Azure de camada Premium para instâncias de Redis. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário torna-se somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache em regiões do Azure.

Para obter mais informações, consulte [como configurar a replicação geográfica para o cache do Azure para Redis](cache-how-to-geo-replication.md).


## <a name="to-scale-to-the-premium-tier"></a>Para dimensionar para a camada Premium
Para dimensionar para a camada Premium, basta escolher uma das camadas Premium na folha **alterar tipo de preço** . Você também pode dimensionar o cache para a camada Premium usando o PowerShell e a CLI. Para obter instruções detalhadas, consulte [como dimensionar o cache do Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passos seguintes
Crie um cache e explore os novos recursos da camada Premium.

* [Como configurar a persistência para um cache do Azure Premium para Redis](cache-how-to-premium-persistence.md)
* [Como configurar o suporte de rede virtual para um cache Premium do Azure para Redis](cache-how-to-premium-vnet.md)
* [Como configurar o clustering para um cache Premium do Azure para Redis](cache-how-to-premium-clustering.md)
* [Como importar dados e exportar dados do cache do Azure para Redis](cache-how-to-import-export-data.md)
* [Como administrar o cache do Azure para Redis](cache-administration.md)

