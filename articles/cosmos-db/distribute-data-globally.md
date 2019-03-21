---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Mais informações sobre recuperação de dados, ativação pós-falha, com vários mestres e georreplicação de escala planetária com bases de dados global do Azure Cosmos DB, um serviço de base de dados com múltiplos modelos distribuída globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 70ead36e20861026e08e864f438071948c526844
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294421"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados global com o Azure Cosmos DB - descrição geral

Aplicações atuais são necessários para estar sempre online e elevada capacidade de resposta. Para alcançar a baixa latência e elevada disponibilidade, instâncias desses aplicativos precisam ser implantados nos centros de dados que estão próximas dos seus usuários. Esses aplicativos são normalmente implementados em vários datacenters e são chamados e distribuídos globalmente. Aplicações distribuídas globalmente tem uma base de dados distribuído globalmente que transparente pode replicar os dados em qualquer parte do mundo para permitir que os aplicativos operar numa cópia dos dados que está próximo de seus usuários. 

O Azure Cosmos DB é um serviço de base de dados globalmente distribuída que foi concebido para fornecer a baixa latência, escalabilidade elástica de débito, semântica bem definida para consistência dos dados e elevada disponibilidade. Em resumo, se seu aplicativo precisa tempo de resposta rápida garantido em qualquer lugar do mundo, caso seja necessário para estar sempre online e necessita de uma escalabilidade ilimitada e elástica de débito e armazenamento, deve criar seu aplicativo no Azure Cosmos DB.

Pode configurar seus bancos de dados para ser globalmente distribuídas e disponível em qualquer uma das regiões do Azure. Para reduzir a latência, coloque os dados perto de onde estão os utilizadores. Escolher as regiões necessárias depende o alcance global da sua aplicação e onde estão localizados os seus utilizadores. O cosmos DB replica de forma transparente os dados para todas as regiões à sua conta do Cosmos. Ele fornece uma única imagem do sistema de distribuídas globalmente base de dados do Cosmos do Azure e contentores que seu aplicativo pode ler e escrever localmente. 

Com o Azure Cosmos DB, pode adicionar ou remover regiões associadas à sua conta em qualquer altura. Seu aplicativo não precisa ser colocada em pausa ou implantados novamente para adicionar ou remover uma região. Continua a ser de elevada disponibilidade sempre devido as capacidades multi-homing, que o serviço de forma nativa fornece.

![Topologia de implementação de elevada disponibilidade](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Crie aplicações globais de ativo-ativo.** Com o protocolo de nova replicação multimestre, todas as regiões suporta gravações e leituras. Também permite a capacidade de vários mestre:

- Elástico ilimitada escrever e ler a escalabilidade. 
- 99,999% de leitura e escrita a disponibilidade em todo o mundo.
- A garantia de leituras e gravações servidas em menos de 10 milissegundos no percentil 99.

Ao utilizar o Azure Cosmos DB multi-homing APIs, a sua aplicação tem conhecimento da região mais próxima e pode enviar pedidos para essa região. A região mais próxima é identificada sem quaisquer alterações de configuração. Como adicionar e remover regiões para e da sua conta do Cosmos do Azure, seu aplicativo não precisa de ser novamente implementada ou colocada em pausa, ele continua a ser de elevada disponibilidade em todos os momentos.

**Crie aplicações de elevada capacidade de resposta.** Seu aplicativo pode realizar quase em tempo real leituras e escritas em relação a todas as regiões que escolheu para a sua base de dados. O Azure Cosmos DB processa internamente a replicação de dados entre regiões com garantias de nível de consistência do nível que selecionou.

**Crie aplicações de elevada disponibilidade.** Executar uma base de dados em várias regiões em todo o mundo aumenta a disponibilidade de uma base de dados. Se uma região não estiver disponível, outras regiões processam automaticamente pedidos de aplicações. O Azure Cosmos DB oferece 99,999% de leitura e escrita de disponibilidade para bases de dados de várias regiões.

**Manter a continuidade do negócio durante falhas regionais.** Azure Cosmos DB suporta [a ativação pós-falha automática](how-to-manage-database-account.md#automatic-failover) durante uma falha regional. Durante uma falha regional, o Azure Cosmos DB continua manter SLAs, disponibilidade, consistência, débito e latência. Para ajudar a tornar-se de que todo o seu aplicativo está altamente disponível, o Cosmos DB oferece uma ativação pós-falha manual API para simular uma falha regional. Ao utilizar esta API, pode executar testes de continuidade comercial normal.

**Escala de leitura e escrita débito globalmente.** Pode ativar a cada região seja gravável e dimensionar de leituras e escritas em todo o mundo. O débito que configura o seu aplicativo num banco de dados do Cosmos do Azure ou um contentor é garantido que seja entregue em todas as regiões à conta do Cosmos do Azure. O débito aprovisionado é garantido de cópia de segurança por [apoiado financeiramente SLAs](https://aka.ms/acdbsla).

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem um compromisso entre a consistência e desempenho. Utilize estes modelos de consistência para criar aplicações distribuídas globalmente com facilidade.

## <a id="Next Steps"></a>Passos seguintes

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global - sob definições avançadas](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar os clientes de multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Adicionar ou remover regiões a partir da sua conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflito personalizado para contas da API de SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)