---
title: Configure persistência de dados - Premium Azure Cache para Redis
description: Saiba como configurar e gerir a persistência de dados na sua Cache Azure de nível Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9927d4780ea015502151188b61c50ddbd2656819
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339548"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Como configurar a persistência de dados para uma Cache Premium Azure para Redis
Neste artigo, você aprenderá a configurar a persistência em um Azure Cache premium para redis exemplo através do portal Azure. O Azure Cache para Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e funcionalidades da cache, incluindo características de nível Premium, tais como clustering, persistência e suporte de rede virtual. 

## <a name="what-is-data-persistence"></a>O que é a persistência dos dados?
[A persistência do Redis](https://redis.io/topics/persistence) permite-lhe persistir nos dados armazenados no Redis. Também pode tirar fotografias e fazer o back up dos dados, que pode carregar em caso de falha de hardware. Esta é uma enorme vantagem sobre o nível Básico ou Padrão, onde todos os dados são armazenados na memória e pode haver potencial perda de dados em caso de falha em que os nós cache estão baixos. 

A Azure Cache para Redis oferece persistência redis utilizando os seguintes modelos:

* **Persistência RDB** - Quando a persistência rdb (redis database) é configurada, Azure Cache para Redis persiste uma imagem da Cache Azure para Redis num formato binário Redis para o disco com base numa frequência de backup configurável. Se ocorrer um evento catastrófico que desative a cache primária e réplica, a cache é reconstruída usando o instantâneo mais recente. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência de RDB.
* **AOF persistência** - Quando a persistência da AOF (apêndice apenas) é configurada, a Cache Azure para Redis guarda cada operação de escrita para um registo que é guardado pelo menos uma vez por segundo numa conta de Armazenamento Azure. Se ocorrer um evento catastrófico que desative a cache primária e réplica, a cache é reconstruída utilizando as operações de escrita armazenadas. Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF.

A Persistência escreve dados do Redis numa conta de Armazenamento Azure que possui e gere. Pode configurar a partir da **Nova Cache Azure para** lâmina Redis durante a criação da cache e no menu **Recursos** para caches premium existentes.

> [!NOTE]
> 
> O Azure Storage encripta automaticamente os dados quando estes são persistidos. Podes usar as tuas próprias chaves para a encriptação. Para obter mais informações, consulte [as chaves geridas pelo Cliente com o Cofre da Chave Azure](/azure/storage/common/storage-service-encryption).
> 
> 

1. Para criar uma cache premium, inscreva-se no [portal Azure](https://portal.azure.com) e selecione **Criar um recurso.** Para além de criar caches no Portal do Azure, pode também criá-las com os modelos do Resource Manager, o PowerShell ou a CLI do Azure. Para obter mais informações sobre a criação de uma Cache Azure para Redis, consulte [Criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Criar recurso.":::
   
2. Na página **Nova,** selecione **Bases de Dados** e, em seguida, selecione **Azure Cache para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Criar recurso.":::

3. Na página **New Redis Cache,** configufique as definições para a sua nova cache premium.
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Introduza um nome globalmente exclusivo. | O nome da cache deve ser uma cadeia entre 1 e 63 caracteres que contenham apenas números, letras ou hífenes. O nome deve começar e terminar com um número ou letra, e não pode conter hífenes consecutivos. O nome de *anfitrião* do seu caso de cache será * \<DNS name> .redis.cache.windows.net*. | 
   | **Subscrição** | Drop-down e selecione a sua subscrição. | A subscrição sob a qual criar este novo Azure Cache para a instância Redis. | 
   | **Grupo de recursos** | Drop-down e selecione um grupo de recursos, ou **selecione Criar novo** e introduza um novo nome de grupo de recursos. | Nome para o grupo de recursos para criar o seu cache e outros recursos. Ao colocar todos os recursos da sua aplicação num único grupo de recursos, pode facilmente geri-los ou eliminá-los em conjunto. | 
   | **Localização** | Drop-down e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que utilizarão o seu cache. |
   | **Tipo cache** | Drop-down e selecione um cache premium para configurar funcionalidades premium. Para mais detalhes, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/). |  O escalão de preço determina o tamanho, o desempenho e as funcionalidades que estão disponíveis para a cache. Para mais informações, consulte [Azure Cache para ver visão geral do Redis](cache-overview.md). |

4. Selecione o **separador 'Rede'** ou clique no botão **'Rede'** na parte inferior da página.

5. No **separador 'Rede',** selecione o seu método de conectividade. Para instâncias de cache premium, pode ligar publicamente, através de endereços IP públicos ou pontos finais de serviço, ou em particular, usando um ponto final privado.

6. Selecione o **Seguinte: Separador avançado** ou clique no **seguinte: Botão avançado** na parte inferior da página.

7. No separador **Avançado** para uma instância de cache premium, configurar as definições para a porta não-TLS, clustering e persistência de dados. Para a persistência de dados, pode escolher a persistência **de RDB** ou **AOF.** 

8. Para ativar a persistência do RDB, clique em **RDB** e configufique as definições. 
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Frequência de reserva** | Drop-down e selecione um intervalo de backup, as escolhas incluem **15 Minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas**e **24 horas**. | Este intervalo começa a contagem rebatida após a operação de backup anterior ter sido concluída com sucesso e quando decorrer uma nova cópia de segurança é iniciada. | 
   | **Conta de Armazenamento** | Drop-down e selecione a sua conta de armazenamento. | Você deve escolher uma conta de armazenamento na mesma região e subscrição que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem maior rendimento.  | 
   | **Chave de armazenamento** | Desça e escolha a **tecla Primária** ou **a chave secundária** para usar. | Se a chave de armazenamento da sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da gota da chave de **armazenamento.** | 

    A primeira cópia de segurança é iniciada assim que o intervalo de frequência de reserva decorrer.

9. Para ativar a persistência da AOF, clique em **AOF** e configufique as definições. 
   
   | Definição      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Primeira Conta de Armazenamento** | Drop-down e selecione a sua conta de armazenamento. | Esta conta de armazenamento deve estar na mesma região e subscrição que a cache, e uma conta **de Armazenamento Premium** é recomendada porque o armazenamento premium tem um rendimento mais elevado. | 
   | **Primeira chave de armazenamento** | Desça e escolha a **tecla Primária** ou **a chave secundária** para usar. | Se a chave de armazenamento da sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da gota da chave de **armazenamento.** | 
   | **Segunda Conta de Armazenamento** | (Opcional) Drop-down e selecione a sua conta de armazenamento secundário. | Pode configurar opcionalmente uma conta de armazenamento adicional. Se uma segunda conta de armazenamento for configurada, as cópias para a cache de réplica são escritas nesta segunda conta de armazenamento. | 
   | **Segunda chave de armazenamento** | (Opcional) Desça e escolha a **tecla Primária** ou **a chave secundária** para usar. | Se a chave de armazenamento da sua conta de persistência for regenerada, deve reconfigurar a chave desejada a partir da gota da chave de **armazenamento.** | 

    Quando a persistência da AOF estiver ativada, as operações de escrita para a cache são guardadas na conta de armazenamento designada (ou contas se tiver configurado uma segunda conta de armazenamento). Em caso de falha catastrófica que derrube a cache primária e réplica, o log AOF armazenado é usado para reconstruir a cache.

10. Selecione o **separador Seguinte: Tags** ou clique no botão **Seguinte: Tags** na parte inferior da página.

11. Opcionalmente, no separador **Tags, insira** o nome e o valor se desejar categorizar o recurso. 

12. Selecione **Rever + criar**. É levado para o separador 'Rever +' onde o Azure valida a sua configuração.

13. Depois de aparecer a mensagem de validação verde, selecione **Criar**.

Demora um pouco para a cache criar. Pode monitorizar o progresso na cache Azure para a página Redis **Overview.** Quando **o Estado** aparece como **Running,** a cache está pronta a ser utilizada. 

## <a name="persistence-faq"></a>PERsistência FAQ
A lista que se segue contém respostas a perguntas comumente feitas sobre Azure Cache para a persistência de Redis.

* [Posso permitir a persistência numa cache previamente criada?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Posso permitir a persistência da AOF e do RDB ao mesmo tempo?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Que modelo de persistência devo escolher?](#which-persistence-model-should-i-choose)
* [O que acontece se eu tiver escalado para um tamanho diferente e um backup é restaurado que foi feito antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)
* [Posso usar a mesma conta de armazenamento para persistência em dois caches diferentes?](#can-i-use-the-same-storage-account-for-persistence-across-two-different-caches)


### <a name="rdb-persistence"></a>Persistência de RDB
* [Posso mudar a frequência de reserva RDB depois de criar a cache?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Por que se eu tiver uma frequência de reserva RDB de 60 minutos há mais de 60 minutos entre reforços?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [O que acontece com os antigos reforços RDB quando um novo reforço é feito?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF persistência
* [Quando devo usar uma segunda conta de armazenamento?](#when-should-i-use-a-second-storage-account)
* [A persistência da AOF afeta toda a latência ou o desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Como posso remover a segunda conta de armazenamento?](#how-can-i-remove-the-second-storage-account)
* [O que é uma reescrita e como é que isso afeta a minha cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [O que devo esperar ao escalonar uma cache com AOF ativada?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Como é que os meus dados da AOF são organizados no armazém?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Posso permitir a persistência numa cache previamente criada?
Sim, a persistência do Redis pode ser configurada tanto na criação de cache como nas caches premium existentes.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Posso permitir a persistência da AOF e do RDB ao mesmo tempo?

Não, só pode ativar RDB ou AOF, mas não ambos ao mesmo tempo.

### <a name="which-persistence-model-should-i-choose"></a>Que modelo de persistência devo escolher?

A persistência da AOF salva cada escrita para um log, que tem algum impacto na produção, em comparação com a persistência de RDB que poupa backups com base no intervalo de backup configurado, com o mínimo impacto no desempenho. Escolha a persistência da AOF se o seu objetivo principal é minimizar a perda de dados, e pode lidar com uma diminuição da produção para o seu cache. Escolha a persistência rdb se pretende manter a produção ideal na sua cache, mas ainda assim quer um mecanismo de recuperação de dados.

* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#rdb-advantages) e [desvantagens](https://redis.io/topics/persistence#rdb-disadvantages) da persistência de RDB.
* Saiba mais sobre as [vantagens](https://redis.io/topics/persistence#aof-advantages) e [desvantagens](https://redis.io/topics/persistence#aof-disadvantages) da persistência da AOF.

Para obter mais informações sobre o desempenho ao utilizar a persistência da AOF, veja [a persistência da AOF a afetar em toda a parte, latência ou desempenho da minha cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>O que acontece se eu tiver escalado para um tamanho diferente e um backup é restaurado que foi feito antes da operação de escala?

Para a persistência de RDB e AOF:

* Se tiver escalado para um tamanho maior, não há impacto.
* Se tiver escalado para um tamanho menor e tiver uma definição de [bases de dados](cache-configure.md#databases) personalizada que seja maior do que o limite de [bases de dados](cache-configure.md#databases) para o seu novo tamanho, os dados nessas bases de dados não são restaurados. Para mais informações, veja [se a definição de bases de dados personalizadas é afetada durante o dimensionamento?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
* Se você tiver escalado para um tamanho menor, e não há espaço suficiente no tamanho menor para reter todos os dados da última cópia de segurança, as chaves serão despejadas durante o processo de restauro, normalmente usando a política de despejo [allkeys-lru.](https://redis.io/topics/lru-cache)

### <a name="can-i-use-the-same-storage-account-for-persistence-across-two-different-caches"></a>Posso usar a mesma conta de armazenamento para persistência em dois caches diferentes?
Sim, você pode usar a mesma conta de armazenamento para persistência em dois caches diferentes

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Posso mudar a frequência de reserva RDB depois de criar a cache?
Sim, pode alterar a frequência de backup para a persistência rdb na lâmina de persistência de **Dados.** Para obter instruções, consulte a persistência de Configure Redis.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Por que se eu tiver uma frequência de reserva RDB de 60 minutos há mais de 60 minutos entre reforços?
O intervalo de frequência de backup de persistência RDB não começa até que o processo de backup anterior tenha terminado com sucesso. Se a frequência de backup for de 60 minutos e levar um processo de backup de 15 minutos para completar com sucesso, o próximo backup só começará 75 minutos após a hora de início do backup anterior.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>O que acontece com os antigos reforços RDB quando um novo reforço é feito?
Todas as cópias de segurança de persistência RDB, com exceção das mais recentes, são automaticamente eliminadas. Esta supressão pode não acontecer imediatamente, mas as cópias de segurança mais antigas não são persistiu indefinidamente.


### <a name="when-should-i-use-a-second-storage-account"></a>Quando devo usar uma segunda conta de armazenamento?

Deve utilizar uma segunda conta de armazenamento para persistência da AOF quando acredita que tem operações definidas mais altas do que o esperado na cache.  A configuração da conta de armazenamento secundário ajuda a garantir que o seu cache não atinja os limites de largura de banda de armazenamento.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>A persistência da AOF afeta toda a latência ou o desempenho da minha cache?

A persistência da AOF afeta a produção em cerca de 15% – 20% quando a cache está abaixo da carga máxima (CPU e Server Load ambos abaixo de 90%). Não deve haver problemas de latência quando a cache está dentro destes limites. No entanto, a cache atingirá estes limites mais cedo com a AOF ativada.

### <a name="how-can-i-remove-the-second-storage-account"></a>Como posso remover a segunda conta de armazenamento?

Pode remover a conta de armazenamento secundário de persistência da AOF, definindo a segunda conta de armazenamento como a mesma que a primeira conta de armazenamento. Para caches existentes, a lâmina de persistência de **Dados** é acedida a partir do **menu Recursos** para a sua cache. Para desativar a persistência da AOF, clique **em Desativar**.

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>O que é uma reescrita e como é que isso afeta a minha cache?

Quando o ficheiro AOF se torna suficientemente grande, uma reescrita é automaticamente colocada na cache. A reescrita redimensiona o ficheiro AOF com o conjunto mínimo de operações necessárias para criar o conjunto de dados atual. Durante as reescritas, espere atingir os limites de desempenho mais cedo, especialmente quando se lida com grandes conjuntos de dados. As reescritas ocorrem menos frequentemente à medida que o ficheiro AOF se torna maior, mas levará uma quantidade significativa de tempo quando isso acontece.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>O que devo esperar ao escalonar uma cache com AOF ativada?

Se o ficheiro AOF no momento da escala for significativamente grande, então espere que a operação de escala desmave mais tempo do que o esperado, uma vez que estará a recarregar o ficheiro após o dimensionamento ter terminado.

Para obter mais informações sobre o escalonamento, veja [o que acontece se eu tiver escalado para um tamanho diferente e uma cópia de segurança é restaurada que foi feita antes da operação de escala?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Como é que os meus dados da AOF são organizados no armazém?

Os dados armazenados em ficheiros AOF são divididos em várias bolhas de página por nó para aumentar o desempenho da poupança dos dados para armazenamento. A tabela a seguir mostra quantas bolhas de página são utilizadas para cada nível de preços:

| Escalão Premium | Blobs |
|--------------|-------|
| P1           | 4 por fragmento    |
| P2           | 8 por fragmento    |
| P3           | 16 por fragmento   |
| P4           | 20 por fragmento   |

Quando o agrupamento está ativado, cada fragmento na cache tem o seu próprio conjunto de bolhas de página, como indicado na tabela anterior. Por exemplo, uma cache P2 com três fragmentos distribui o seu ficheiro AOF em 24 páginas de bolhas (8 bolhas por fragmento, com 3 fragmentos).

Após uma reescrita, existem dois conjuntos de ficheiros AOF no armazenamento. As reescritas ocorrem em segundo plano e anexam ao primeiro conjunto de ficheiros, enquanto as operações definidas que são enviadas para a cache durante o apêndice de reescrita ao segundo conjunto. Uma cópia de segurança é temporariamente armazenada durante as reescritas em caso de falha, mas é prontamente eliminada após um reescrito finalizado.


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
