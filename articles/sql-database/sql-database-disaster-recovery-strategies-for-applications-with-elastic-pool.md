---
title: Criar soluções de recuperação de desastres-banco de dados SQL do Azure
description: Saiba como projetar sua solução de nuvem para recuperação de desastres escolhendo o padrão de failover correto.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 535397dcf32a617038ab4bef4ec7aa227f4563b1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690659"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Estratégias de recuperação de desastre para aplicativos que usam pools elásticos do banco de dados SQL

Ao longo dos anos, aprendemos que os serviços de nuvem não são à prova de falhas e incidentes catastróficos acontecem. O banco de dados SQL fornece vários recursos para fornecer a continuidade dos negócios de seu aplicativo quando esses incidentes ocorrem. [Pools elásticos](sql-database-elastic-pool.md) e bancos de dados individuais dão suporte ao mesmo tipo de recursos de Dr (recuperação de desastre). Este artigo descreve várias estratégias de DR para pools elásticos que aproveitam esses recursos de continuidade de negócios do banco de dados SQL.

Este artigo usa o padrão de aplicativo de ISV do SaaS canônico a seguir:

Um aplicativo Web moderno baseado em nuvem provisiona um banco de dados SQL para cada usuário final. O ISV tem muitos clientes e, portanto, usa muitos bancos de dados, conhecidos como bancos de dados de locatário. Como os bancos de dados de locatário normalmente têm padrões de atividade imprevisíveis, o ISV usa um pool elástico para tornar o banco de custos muito previsível em longos períodos de tempo. O pool elástico também simplifica o gerenciamento de desempenho quando a atividade do usuário é propiconte. Além dos bancos de dados de locatário, o aplicativo também usa vários bancos de dados para gerenciar perfis de usuário, segurança, coletar padrões de uso, etc. A disponibilidade dos locatários individuais não afeta a disponibilidade do aplicativo como um todo. No entanto, a disponibilidade e o desempenho dos bancos de dados de gerenciamento são essenciais para a função do aplicativo e, se os bancos de dados de gerenciamento estiverem offline, todo o aplicativo estará offline.

Este artigo discute estratégias de DR que abrangem uma variedade de cenários de aplicativos de inicialização sensíveis ao custo para aqueles com requisitos de disponibilidade rigorosos.

> [!NOTE]
> Se você estiver usando bancos de dados Premium ou Comercialmente Crítico e pools elásticos, poderá torná-los resilientes a interrupções regionais, convertendo-os para a configuração de implantação com redundância de zona. Consulte [bancos de dados com redundância de zona](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Inicialização sensível ao custo

Sou uma empresa de inicialização e é extremamente sensível ao custo.  Quero simplificar a implantação e o gerenciamento do aplicativo e posso ter um SLA limitado para clientes individuais. Mas quero garantir que o aplicativo como um todo nunca fique offline.

Para atender ao requisito de simplicidade, implante todos os bancos de dados de locatário em um pool elástico na região do Azure de sua escolha e implante bancos de dados de gerenciamento como bancos de dados únicos replicados geograficamente. Para a recuperação de desastre de locatários, use a restauração geográfica, que não tem nenhum custo adicional. Para garantir a disponibilidade dos bancos de dados de gerenciamento, replique-os geograficamente para outra região usando um grupo de failover automático (etapa 1). O custo contínuo da configuração de recuperação de desastre nesse cenário é igual ao custo total dos bancos de dados secundários. Essa configuração é ilustrada no próximo diagrama.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Se ocorrer uma interrupção na região primária, as etapas de recuperação para colocar seu aplicativo online serão ilustradas pelo próximo diagrama.

* O grupo de failover inicia o failover automático do banco de dados de gerenciamento para a região de recuperação de desastres. O aplicativo é automaticamente reconectado ao novo primário e todas as novas contas e bancos de dados de locatário são criados na região de DR. Os clientes existentes veem seus dados temporariamente indisponíveis.
* Crie o pool elástico com a mesma configuração do pool original (2).
* Use a restauração geográfica para criar cópias dos bancos de dados de locatário (3). Você pode considerar disparar as restaurações individuais pelas conexões do usuário final ou usar algum outro esquema de prioridade específico do aplicativo.

Neste ponto, seu aplicativo está online novamente na região de recuperação de desastre, mas alguns clientes experimentam atraso ao acessar seus dados.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a interrupção for temporária, é possível que a região primária seja recuperada pelo Azure antes que todas as restaurações de banco de dados sejam concluídas na região de DR. Nesse caso, orquestrar a movimentação do aplicativo de volta para a região primária. O processo executa as etapas ilustradas no próximo diagrama.

* Cancele todas as solicitações de restauração geográfica pendentes.
* Faça failover dos bancos de dados de gerenciamento para a região primária (5). Após a recuperação da região, os primários antigos se tornaram secundários automaticamente. Agora, eles alternam funções novamente.
* Altere a cadeia de conexão do aplicativo para apontar de volta para a região primária. Agora, todas as novas contas e bancos de dados de locatário são criados na região primária. Alguns clientes existentes veem seus dados temporariamente indisponíveis.
* Defina todos os bancos de dados no pool de recuperação de desastres como somente leitura para garantir que eles não possam ser modificados na região de DR (6).
* Para cada banco de dados no pool de recuperação de desastre que foi alterado desde a recuperação, renomeie ou exclua os bancos de dados correspondentes no pool primário (7).
* Copie os bancos de dados atualizados do pool de recuperação de desastres para o pool primário (8).
* Excluir o pool de DR (9)

Neste ponto, seu aplicativo está online na região primária com todos os bancos de dados de locatário disponíveis no pool primário.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

O principal **benefício** dessa estratégia é o baixo custo contínuo para a redundância da camada de dados. Os backups são feitos automaticamente pelo serviço de banco de dados SQL sem reescrita de aplicativo e sem custo adicional.  O custo é incorrido apenas quando os bancos de dados elásticos são restaurados. A **desvantagem** é que a recuperação completa de todos os bancos de dados de locatário leva um tempo significativo. O período de tempo depende do número total de restaurações que você inicia na região de recuperação de desastres e do tamanho geral dos bancos de dados de locatário. Mesmo que você Priorize as restaurações de alguns locatários sobre outras pessoas, você está competindo com todas as outras restaurações iniciadas na mesma região que o serviço arbitra e limita para minimizar o impacto geral nos bancos de dados dos clientes existentes. Além disso, a recuperação dos bancos de dados de locatário não pode ser iniciada até que o novo pool elástico na região de DR seja criado.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicativo maduro com serviço em camadas

Sou um aplicativo SaaS maduro com ofertas de serviço em camadas e diferentes SLAs para clientes de avaliação e para pagar clientes. Para os clientes de avaliação, preciso reduzir o custo o máximo possível. Os clientes de avaliação podem ter tempo de inatividade, mas quero reduzir sua probabilidade. Para os clientes pagantes, qualquer tempo de inatividade é um risco de voo. Portanto, quero ter certeza de que os clientes pagantes sempre poderão acessar seus dados.

Para dar suporte a esse cenário, separe os locatários de avaliação dos locatários pagos colocando-os em pools elásticos separados. Os clientes de avaliação têm menos eDTU ou vCores por locatário e um SLA menor com um tempo de recuperação mais longo. Os clientes pagantes estão em um pool com maior eDTU ou vCores por locatário e um SLA superior. Para garantir o menor tempo de recuperação, os bancos de dados de locatário dos clientes pagantes são replicados geograficamente. Essa configuração é ilustrada no próximo diagrama.

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como no primeiro cenário, os bancos de dados de gerenciamento estão bastante ativos, portanto, você usa um banco de dados único replicado geograficamente para ele (1). Isso garante o desempenho previsível para novas assinaturas de clientes, atualizações de perfil e outras operações de gerenciamento. A região na qual residem os primários dos bancos de dados de gerenciamento é a região primária e a região em que os secundários dos bancos de dados de gerenciamento residem é a região de DR.

Os bancos de dados de locatário dos clientes pagantes têm bancos de dados ativos no pool "pago" provisionado na região primária. Provisione um pool secundário com o mesmo nome na região de recuperação de desastres. Cada locatário é replicado geograficamente para o pool secundário (2). Isso permite a recuperação rápida de todos os bancos de dados de locatário usando o failover.

Se ocorrer uma interrupção na região primária, as etapas de recuperação para colocar seu aplicativo online serão ilustradas no próximo diagrama:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Faça o failover imediato dos bancos de dados de gerenciamento para a região de DR (3).
* Altere a cadeia de conexão do aplicativo para apontar para a região de DR. Agora, todas as novas contas e bancos de dados de locatário são criados na região de DR. Os clientes de avaliação existentes veem seus dados temporariamente indisponíveis.
* Faça failover dos bancos de dados do locatário pago para o pool na região de recuperação de desastres para restaurar imediatamente sua disponibilidade (4). Como o failover é uma alteração rápida no nível de metadados, considere uma otimização em que os failovers individuais são disparados sob demanda pelas conexões do usuário final.
* Se o tamanho de eDTU do pool secundário ou o valor vCore fosse menor do que o primário porque os bancos de dados secundários exigiam apenas a capacidade de processar os logs de alteração enquanto eram secundários, aumente imediatamente a capacidade do pool agora para acomodar a carga de trabalho completa de todos os locatários (5).
* Crie o novo pool elástico com o mesmo nome e a mesma configuração na região de DR para os bancos de dados dos clientes de avaliação (6).
* Depois que o pool de clientes de avaliação for criado, use a restauração geográfica para restaurar os bancos de dados de locatário de avaliação individuais no novo pool (7). Considere disparar as restaurações individuais pelas conexões do usuário final ou usar algum outro esquema de prioridade específico do aplicativo.

Neste ponto, seu aplicativo está novamente online na região de recuperação de desastre. Todos os clientes pagantes têm acesso aos seus dados enquanto os clientes de avaliação experimentam atrasos ao acessar seus dados.

Quando a região primária for recuperada pelo Azure *após* a restauração do aplicativo na região de recuperação de desastres, você poderá continuar executando o aplicativo nessa região ou poderá optar por fazer failback para a região primária. Se a região primária for recuperada *antes* da conclusão do processo de failover, considere fazer failback imediatamente. O failback executa as etapas ilustradas no próximo diagrama:

![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele todas as solicitações de restauração geográfica pendentes.
* Faça failover dos bancos de dados de gerenciamento (8). Após a recuperação da região, o primário antigo se tornará o secundário automaticamente. Agora ele se torna o primário novamente.  
* Fazer failover dos bancos de dados de locatário pagos (9). Da mesma forma, após a recuperação da região, os primários antigos se tornam os secundários automaticamente. Agora eles se tornam os primários novamente.
* Defina os bancos de dados de avaliação restaurados que foram alterados na região de DR para somente leitura (10).
* Para cada banco de dados no pool de DR de clientes de avaliação que foi alterado desde a recuperação, renomeie ou exclua o banco de dados correspondente no pool primário de clientes de avaliação (11).
* Copie os bancos de dados atualizados do pool de recuperação de desastres para o pool primário (12).
* Exclua o pool de DR (13).

> [!NOTE]
> A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que você execute o comando de failover bancos de dados de locatário em lotes de pelo menos 20 bancos de dados.

O principal **benefício** dessa estratégia é que ela fornece o SLA mais alto para os clientes pagantes. Ele também garante que as novas avaliações sejam desbloqueadas assim que o pool de recuperação de desastres de avaliação for criado. A **desvantagem** é que essa configuração aumenta o custo total dos bancos de dados de locatário pelo custo do pool de recuperação de desastres secundário para clientes pagos. Além disso, se o pool secundário tiver um tamanho diferente, os clientes pagantes terão um desempenho menor após o failover até que a atualização do pool na região de DR seja concluída.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicativo distribuído geograficamente com serviço em camadas

Tenho um aplicativo SaaS maduro com ofertas de serviço em camadas. Quero oferecer um SLA muito agressivo para meus clientes pagos e minimizar o risco de impacto quando ocorrem interrupções porque até mesmo uma breve interrupção pode causar insatisfação do cliente. É fundamental que os clientes pagantes sempre possam acessar seus dados. As avaliações são gratuitas e um SLA não é oferecido durante o período de avaliação.

Para dar suporte a esse cenário, use três pools elásticos separados. Provisione dois pools de tamanho igual com alto eDTUs ou vCores por banco de dados em duas regiões diferentes para conter os bancos de dados de locatário dos clientes pagos. O terceiro pool que contém os locatários de avaliação pode ter menos eDTUs ou vCores por banco de dados e ser provisionado em uma das duas regiões.

Para garantir o menor tempo de recuperação durante interrupções, os bancos de dados de locatário dos clientes pagantes são replicados geograficamente com 50% dos bancos de dados primários em cada uma das duas regiões. Da mesma forma, cada região tem 50% dos bancos de dados secundários. Dessa forma, se uma região estiver offline, somente 50% dos bancos de dados dos clientes pagos serão impactados e precisarão fazer failover. Os outros bancos de dados permanecem intactos. Essa configuração é ilustrada no diagrama a seguir:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Como nos cenários anteriores, os bancos de dados de gerenciamento estão bem ativos, portanto, configure-os como bancos de dados únicos replicados geograficamente (1). Isso garante o desempenho previsível das novas assinaturas de cliente, atualizações de perfil e outras operações de gerenciamento. A região a é a região primária dos bancos de dados de gerenciamento e a região B é usada para recuperação dos bancos de dados de gerenciamento.

Os bancos de dados de locatário dos clientes pagantes também são replicados geograficamente, mas com primários e secundários divididos entre A região A e A região B (2). Dessa forma, os bancos de dados primários do locatário impactados pela interrupção podem fazer failover para a outra região e ficar disponíveis. A outra metade dos bancos de dados de locatário não é afetada.

O próximo diagrama ilustra as etapas de recuperação a serem executadas se ocorrer uma interrupção na região A.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Faça o failover imediato dos bancos de dados de gerenciamento para a região B (3).
* Altere a cadeia de conexão do aplicativo para apontar para os bancos de dados de gerenciamento na região B. modifique os bancos de dados de gerenciamento para garantir que as novas contas e os bancos de dados de locatário sejam criados na região B e que os bancos de dados de locatário existentes também sejam encontrados lá. Os clientes de avaliação existentes veem seus dados temporariamente indisponíveis.
* Faça failover dos bancos de dados do locatário pago para o pool 2 na região B para restaurar imediatamente sua disponibilidade (4). Como o failover é uma alteração rápida no nível de metadados, você pode considerar uma otimização em que os failovers individuais são disparados sob demanda pelas conexões do usuário final.
* Como agora o pool 2 contém somente bancos de dados primários, a carga de trabalho total no pool aumenta e pode aumentar imediatamente seu tamanho de eDTU (5) ou o número de vCores.
* Crie o novo pool elástico com o mesmo nome e a mesma configuração na região B para os bancos de dados dos clientes de avaliação (6).
* Depois que o pool é criado, use restauração geográfica para restaurar o banco de dados de locatário de avaliação individual para o pool (7). Você pode considerar disparar as restaurações individuais pelas conexões do usuário final ou usar algum outro esquema de prioridade específico do aplicativo.

> [!NOTE]
> A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que você execute o comando de failover dos bancos de dados de locatário em lotes de pelo menos 20 bancos de dados.

Neste ponto, seu aplicativo está novamente online na região B. Todos os clientes pagantes têm acesso aos seus dados enquanto os clientes de avaliação experimentam atrasos ao acessar seus dados.

Quando a região A é recuperada, você precisa decidir se deseja usar a região B para clientes de avaliação ou failback para usar o pool de clientes de avaliação na região A. Um critério pode ser o% dos bancos de dados de locatário de avaliação modificados desde a recuperação. Independentemente dessa decisão, você precisa balancear novamente os locatários pagos entre dois pools. o próximo diagrama ilustra o processo quando os bancos de dados de locatário de avaliação executam failback para a região A.  

![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele todas as solicitações de restauração geográfica pendentes para o pool de recuperação de desastres de avaliação.
* Fazer failover do banco de dados de gerenciamento (8). Após a recuperação da região, o antigo primário se torna o secundário automaticamente. Agora ele se torna o primário novamente.  
* Selecione quais bancos de dados de locatários pagos failback para o pool 1 e inicie o failover para seus secundários (9). Após a recuperação da região, todos os bancos de dados no pool 1 se tornaram secundários automaticamente. Agora, 50% delas se tornam primárias novamente.
* Reduza o tamanho do pool 2 para o eDTU original (10) ou o número de vCores.
* Defina todos os bancos de dados de avaliação restaurados na região B para somente leitura (11).
* Para cada banco de dados no pool de recuperação de desastres de avaliação que foi alterado desde a recuperação, renomeie ou exclua o banco de dados correspondente no pool primário de avaliação (12).
* Copie os bancos de dados atualizados do pool de recuperação de desastres para o pool primário (13).
* Exclua o pool de recuperação de desastres (14).

Os principais **benefícios** dessa estratégia são:

* Ele dá suporte ao SLA mais agressivo para os clientes pagantes, pois garante que uma interrupção não possa afetar mais de 50% dos bancos de dados de locatário.
* Ele garante que as novas avaliações sejam desbloqueadas assim que o pool de DR da trilha for criado durante a recuperação.
* Ele permite um uso mais eficiente da capacidade do pool como 50% dos bancos de dados secundários no pool 1 e o pool 2 é garantido como menos ativo do que os bancos de dados primários.

As principais **vantagens** são:

* As operações CRUD nos bancos de dados de gerenciamento têm menor latência para os usuários finais conectados à região A do que para os usuários finais conectados à região B, pois são executados no primário dos bancos de dados de gerenciamento.
* Ele requer um design mais complexo do banco de dados de gerenciamento. Por exemplo, cada registro de locatário tem uma marca de local que precisa ser alterada durante o failover e o failback.  
* Os clientes pagantes podem ter um desempenho menor do que o normal até que a atualização do pool na região B seja concluída.

## <a name="summary"></a>Resumo

Este artigo se concentra nas estratégias de recuperação de desastres para a camada de banco de dados usada por um aplicativo de multilocatários de SaaS do SaaS. A estratégia escolhida é baseada nas necessidades do aplicativo, como o modelo de negócios, o SLA que você deseja oferecer a seus clientes, restrição de orçamento, etc. Cada estratégia descrita descreve os benefícios e a compensação para que você possa tomar uma decisão informada. Além disso, seu aplicativo específico provavelmente inclui outros componentes do Azure. Portanto, você examina suas diretrizes de continuidade de negócios e orquestra a recuperação da camada de banco de dados com elas. Para saber mais sobre como gerenciar a recuperação de aplicativos de banco de dados no Azure, consulte [Projetando soluções de nuvem para recuperação de desastres](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre backups automatizados do banco [de dados SQL](sql-database-automated-backups.md)
* Para obter uma visão geral e cenários de continuidade de negócios, consulte [visão geral da continuidade de negócios](sql-database-business-continuity.md)
* Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre as opções de recuperação mais rápidas, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md) e [grupos de failover automático](sql-database-auto-failover-group.md).
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md).
