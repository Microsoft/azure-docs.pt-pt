---
title: Soluções de recuperação de desastres de design
description: Aprenda a desenhar a sua solução de nuvem para a recuperação de desastres, escolhendo o padrão de falha certo.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0463d11466859c0f30901a0afd960bdc7b2599a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357793"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Estratégias de recuperação de desastres para aplicações que utilizam piscinas elásticas Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database fornece várias capacidades para garantir a continuidade do negócio da sua aplicação quando ocorrem incidentes catastróficos. [Piscinas elásticas](elastic-pool-overview.md) e bases de dados individuais suportam o mesmo tipo de capacidades de recuperação de desastres (DR). Este artigo descreve várias estratégias de DR para piscinas elásticas que alavancam estas funcionalidades de continuidade de negócios da Azure SQL Database.

Este artigo utiliza o seguinte padrão de aplicação canónica SaaS ISV:

Uma aplicação web moderna baseada na nuvem fornece uma base de dados para cada utilizador final. O ISV tem muitos clientes e, portanto, utiliza muitas bases de dados, conhecidas como bases de dados de inquilinos. Como as bases de dados dos inquilinos normalmente têm padrões de atividade imprevisíveis, o ISV usa uma piscina elástica para tornar a base de dados custo muito previsível durante longos períodos de tempo. A piscina elástica também simplifica a gestão do desempenho quando a atividade do utilizador aumenta. Além das bases de dados dos inquilinos, a aplicação também utiliza várias bases de dados para gerir perfis de utilizador, segurança, recolher padrões de utilização, etc. A disponibilidade dos inquilinos individuais não afeta a disponibilidade da aplicação como um todo. No entanto, a disponibilidade e desempenho das bases de dados de gestão é fundamental para a função da aplicação e se as bases de dados de gestão estiverem offline, toda a aplicação está offline.

Este artigo discute estratégias de DR que abrangem uma série de cenários, desde aplicações de startups sensíveis a custos a aplicações com requisitos de disponibilidade rigorosos.

> [!NOTE]
> Se estiver a utilizar bases de dados Premium ou Business Critical e piscinas elásticas, pode torná-las resistentes a interrupções regionais, convertendo-as para configuração de implementação redundante de zona. Consulte [bases de dados redundantes da zona.](high-availability-sla.md)

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Startup sensível a custos

Sou um negócio de startups e sou extremamente sensível aos custos.  Quero simplificar a implementação e gestão da aplicação e posso ter um SLA limitado para clientes individuais. Mas quero garantir que a aplicação como um todo nunca esteja offline.

Para satisfazer o requisito de simplicidade, coloque todas as bases de dados de inquilinos num pool elástico na região Azure à sua escolha e implemente bases de dados de gestão como bases de dados únicas geo-replicadas. Para a recuperação de desastres dos inquilinos, utilize o geo-restauro, que não tem custos adicionais. Para garantir a disponibilidade das bases de dados de gestão, replique-as para outra região utilizando um grupo de auto-failover (passo 1). O custo contínuo da configuração de recuperação de desastres neste cenário é igual ao custo total das bases de dados secundárias. Esta configuração é ilustrada no diagrama seguinte.

![Figura 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Se ocorrer uma paralisação na região primária, os passos de recuperação para colocar a sua aplicação online são ilustrados pelo próximo diagrama.

* O grupo de failover inicia o failover automático da base de dados de gestão para a região DR. A aplicação é automaticamente reconectada à nova primária e todas as novas contas e bases de dados de inquilinos são criadas na região DR. Os clientes existentes vêem os seus dados temporariamente indisponíveis.
* Crie a piscina elástica com a mesma configuração que a piscina original (2).
* Utilize geo-restauro para criar cópias das bases de dados dos inquilinos (3). Pode considerar desencadear as restaurações individuais pelas ligações do utilizador final ou utilizar outro esquema prioritário específico para aplicações.

Neste momento a sua aplicação está novamente online na região de DR, mas alguns clientes sentem atrasos no acesso aos seus dados.

![Figura 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a paralisação foi temporária, é possível que a região primária seja recuperada pelo Azure antes que todas as casas de dados sejam restauradas na região dr. Neste caso, orquestrar a mudança da aplicação para a região primária. O processo dá os passos ilustrados no próximo diagrama.

* Cancele todos os pedidos de geo-restauro pendentes.
* Falha nas bases de dados de gestão para a região primária (5). Após a recuperação da região, as primárias antigas tornaram-se automaticamente secundárias. Agora mudam de papel de novo.
* Mude a cadeia de ligação da aplicação para voltar para a região primária. Agora, todas as novas contas e bases de dados de inquilinos são criadas na região primária. Alguns clientes existentes vêem os seus dados temporariamente indisponíveis.
* Coloque todas as bases de dados na piscina DR apenas para garantir que não podem ser modificadas na região DR (6).
* Para cada base de dados no pool DR que mudou desde a recuperação, mude de nome ou elimine as bases de dados correspondentes no pool primário (7).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (8).
* Apagar a piscina DR (9)

Neste momento a sua aplicação está online na região primária com todas as bases de dados de inquilinos disponíveis na piscina primária.

![Figura 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Vantagem

O principal benefício desta estratégia é o baixo custo contínuo para a redundância do nível de dados. A Azure SQL Database faz automaticamente o back-up de bases de dados sem reescrita de aplicação sem custos adicionais. O custo só é incorrido quando as bases de dados elásticas são restauradas. 

### <a name="trade-off"></a>Compensação

A compensação é que a recuperação completa de todas as bases de dados dos inquilinos leva um tempo significativo. O tempo depende do número total de restauros que inicia na região dr e do tamanho global das bases de dados dos inquilinos. Mesmo que dê prioridade a alguns restauradores de inquilinos em relação a outros, está a competir com todas as outras restaurações que são iniciadas na mesma região que os árbitros de serviço e aceleradores para minimizar o impacto global nas bases de dados dos clientes existentes. Além disso, a recuperação das bases de dados dos inquilinos não pode começar até que seja criada a nova piscina elástica na região DR.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicação madura com serviço hierarquizado

Sou uma aplicação SaaS madura com ofertas de serviços hierárquicos e diferentes SLAs para clientes experimentais e para clientes pagadores. Para os clientes do julgamento, tenho que reduzir o custo o máximo possível. Os clientes de teste podem tirar tempo de descanso, mas quero reduzir a sua probabilidade. Para os clientes pagadores, qualquer tempo de inatividade é um risco de fuga. Por isso, quero certificar-me de que os clientes pagadores podem sempre aceder aos seus dados.

Para apoiar este cenário, separe os inquilinos do julgamento dos inquilinos pagos colocando-os em piscinas elásticas separadas. Os clientes de teste têm eDTU mais baixo ou vCores por inquilino e SLA mais baixo com um tempo de recuperação mais longo. Os clientes pagadores estão em uma piscina com eDTU mais alto ou vCores por inquilino e um SLA mais alto. Para garantir o menor tempo de recuperação, as bases de dados dos inquilinos pagadores são geo-replicadas. Esta configuração é ilustrada no diagrama seguinte.

![O diagrama mostra uma região primária e uma região D R que empregam a geo-replicação entre a base de dados de gestão e os clientes pagos piscina primária e piscina secundária sem replicação para o pool de clientes experimentais.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como no primeiro cenário, as bases de dados de gestão estão bastante ativas, pelo que utiliza uma única base de dados geo-replicada para ela (1). Isto garante o desempenho previsível para novas subscrições de clientes, atualizações de perfis e outras operações de gestão. A região em que residem as primárias das bases de dados de gestão é a região primária e a região em que residem os secundários das bases de dados de gestão é a região DR.

As bases de dados dos inquilinos dos clientes pagadores têm bases de dados ativas no pool "pago" a provisionado na região primária. Disponibilização de uma piscina secundária com o mesmo nome na região DR. Cada inquilino é geo-replicado para a piscina secundária (2). Isto permite uma rápida recuperação de todas as bases de dados de inquilinos usando failover.

Se ocorrer uma paralisação na região primária, os passos de recuperação para colocar a sua aplicação online são ilustrados no diagrama seguinte:

![O diagrama mostra uma paragem para a região primária, com falha na base de dados de gestão, piscina secundária de clientes pagos, e criação e restauro para clientes experimentais.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Falha imediata nas bases de dados de gestão para a região DR (3).
* Altere o fio de ligação da aplicação para apontar para a região DR. Agora, todas as novas contas e bases de dados de inquilinos são criadas na região dr. Os clientes de teste existentes vêem os seus dados temporariamente indisponíveis.
* Falhe nas bases de dados pagas do arrendatário até à piscina da região DR para restabelecer imediatamente a sua disponibilidade (4). Uma vez que o failover é uma rápida alteração do nível de metadados, considere uma otimização onde as falhas individuais são desencadeadas a pedido pelas ligações do utilizador final.
* Se o seu tamanho eDTU secundário ou valor vCore foi inferior ao valor primário porque as bases de dados secundárias apenas necessitavam da capacidade de processar os registos de mudança enquanto eram secundários, aumentaria imediatamente a capacidade da piscina agora para acomodar toda a carga de trabalho de todos os inquilinos (5).
* Crie a nova piscina elástica com o mesmo nome e a mesma configuração na região DR para as bases de dados dos clientes experimentais (6).
* Uma vez criada a piscina dos clientes experimentais, utilize o geo-restauro para restaurar as bases de dados individuais dos inquilinos experimentais na nova piscina (7). Considere desencadear as restaurações individuais pelas ligações do utilizador final ou utilizar outro esquema prioritário específico para a aplicação.

Neste momento, a sua candidatura está novamente online na região dr. Todos os clientes pagadores têm acesso aos seus dados enquanto os clientes do ensaio experimentam atrasos no acesso aos seus dados.

Quando a região primária for recuperada pelo Azure *depois de* ter restaurado a aplicação na região dr pode continuar a executar a aplicação naquela região ou pode decidir voltar à região primária. Se a região primária for recuperada *antes* do processo de incumprimento estar concluído, considere a falha de imediato. O failback toma os passos ilustrados no diagrama seguinte:

![O diagrama mostra os passos de recuo para implementar depois de restaurar a região primária.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele todos os pedidos de geo-restauro pendentes.
* Falha nas bases de dados de gestão (8). Após a recuperação da região, as antigas primárias tornam-se automaticamente secundárias. Agora torna-se o principal novamente.  
* Falha nas bases de dados de inquilinos pagos (9). Da mesma forma, após a recuperação da região, as antigas primárias tornam-se automaticamente secundárias. Agora voltam a ser as primárias.
* Desajuste as bases de dados de ensaio restauradas que mudaram na região dr para apenas leitura (10).
* Para cada base de dados no conjunto de clientes de testes DR que mudou desde a recuperação, renomear ou eliminar a base de dados correspondente na piscina primária dos clientes experimentais (11).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (12).
* Apague a piscina DR (13).

> [!NOTE]
> A operação de falha é assíncronea. Para minimizar o tempo de recuperação é importante que execute o comando de failover das bases de dados dos inquilinos em lotes de pelo menos 20 bases de dados.

### <a name="benefit"></a>Vantagem

O principal benefício desta estratégia é que fornece o SLA mais elevado para os clientes pagadores. Também garante que os novos ensaios serão desbloqueados assim que o ensaio dr pool for criado. 

### <a name="trade-off"></a>Compensação

A compensação é que esta configuração aumenta o custo total das bases de dados dos inquilinos pelo custo da piscina de DR secundária para clientes pagos. Além disso, se a piscina secundária tiver um tamanho diferente, os clientes pagadores experimentam um desempenho mais baixo após o failover até que o upgrade da piscina na região DR esteja concluído.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicação distribuída geograficamente com serviço hierarquizado

Tenho uma aplicação SaaS madura com ofertas de serviços hierárquicos. Quero oferecer um SLA muito agressivo aos meus clientes pagos e minimizar o risco de impacto quando ocorrem interrupções porque mesmo uma breve interrupção pode causar insatisfação do cliente. É fundamental que os clientes pagadores possam sempre aceder aos seus dados. Os ensaios são gratuitos e um SLA não é oferecido durante o período experimental.

Para apoiar este cenário, utilize três piscinas elásticas separadas. Provisão de dois pools de tamanho igual com eDTUs elevados ou vCores por base de dados em duas regiões diferentes para conter as bases de dados de inquilinos dos clientes pagos. A terceira piscina que contenha os inquilinos experimentais pode ter eDTUs mais baixos ou vCores por base de dados e ser abastado numa das duas regiões.

Para garantir o menor tempo de recuperação durante as paralisações, as bases de dados dos inquilinos pagadores são geo-replicadas com 50% das bases de dados primárias em cada uma das duas regiões. Da mesma forma, cada região tem 50% das bases de dados secundárias. Desta forma, se uma região estiver offline, apenas 50% das bases de dados dos clientes pagos são impactadas e têm de falhar. As outras bases de dados permanecem intactas. Esta configuração é ilustrada no seguinte diagrama:

![O diagrama mostra uma região primária chamada Região A e região secundária chamada Região B que empregam a geo-replicação entre a base de dados de gestão e os clientes pagos piscina primária e piscina secundária sem replicação para o pool de clientes experimentais.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Tal como nos cenários anteriores, as bases de dados de gestão estão bastante ativas, pelo que as configuram como bases de dados monografas geo-replicadas (1). Isto garante o desempenho previsível das novas subscrições de clientes, atualizações de perfis e outras operações de gestão. A região A é a principal região das bases de dados de gestão e a região B é utilizada para a recuperação das bases de dados de gestão.

As bases de dados dos inquilinos dos clientes pagadores também são geo-replicadas, mas com primárias e secundários divididos entre a região A e a região B (2). Desta forma, as bases de dados primárias dos inquilinos afetadas pela paralisação podem falhar na outra região e ficar disponíveis. A outra metade das bases de dados dos inquilinos não é afetada.

O diagrama seguinte ilustra os passos de recuperação a tomar se ocorrer uma paragem na região A.

![O diagrama mostra uma paralisação para a região primária, com falha na base de dados de gestão, piscina secundária de clientes pagos, e criação e restauro para clientes experimentais na região B.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Falhe imediatamente nas bases de dados de gestão para a região B (3).
* Altere o fio de ligação da aplicação para apontar para as bases de dados de gestão na região B. Modifique as bases de dados de gestão para garantir que as novas contas e bases de dados de inquilinos são criadas na região B e as bases de dados de inquilinos existentes também lá são encontradas. Os clientes de teste existentes vêem os seus dados temporariamente indisponíveis.
* Falhe nas bases de dados pagas do arrendatário para o agrupamento 2 da região B para restaurar imediatamente a sua disponibilidade (4). Uma vez que o failover é uma rápida alteração do nível de metadados, pode considerar uma otimização onde as falhas individuais são desencadeadas a pedido pelas ligações do utilizador final.
* Uma vez que agora o pool 2 contém apenas bases de dados primárias, a carga de trabalho total na piscina aumenta e pode imediatamente aumentar o seu tamanho eDTU (5) ou número de vCores.
* Crie a nova piscina elástica com o mesmo nome e a mesma configuração na região B para as bases de dados dos clientes experimentais (6).
* Uma vez criada a piscina, utilize o geo-restauro para restaurar a base de dados individual do inquilino experimental na piscina (7). Pode considerar desencadear as restaurações individuais pelas ligações do utilizador final ou utilizar outro esquema prioritário específico para aplicações.

> [!NOTE]
> A operação de falha é assíncronea. Para minimizar o tempo de recuperação, é importante que execute o comando de failover das bases de dados dos inquilinos em lotes de pelo menos 20 bases de dados.

Neste momento, a sua candidatura está novamente online na região B. Todos os clientes pagadores têm acesso aos seus dados enquanto os clientes do ensaio experimentam atrasos no acesso aos seus dados.

Quando a região A é recuperada, você precisa decidir se você quer usar a região B para clientes experimentais ou não voltar a usar o pool de clientes experimentais na região A. Um dos critérios poderia ser a percentagem de bases de dados de inquilinos em ensaio alteradas desde a recuperação. Independentemente dessa decisão, você precisa reequilibrar os inquilinos pagos entre duas piscinas. o diagrama seguinte ilustra o processo quando as bases de dados dos inquilinos do ensaio falham na região A.  

![O diagrama mostra os passos de recuo para implementar após o restabelecimento da Região A.](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele todos os pedidos de geo-restauro pendentes para testar a piscina DR.
* Falha na base de dados de gestão (8). Após a recuperação da região, as antigas primárias tornaram-se automaticamente secundárias. Agora torna-se o principal novamente.  
* Selecione quais as bases de dados pagas dos inquilinos que não voltam ao pool 1 e inicie o failover para os seus secundários (9). Após a recuperação da região, todas as bases de dados do pool 1 tornaram-se automaticamente secundárias. Agora, 50% deles voltam a ser primárias.
* Reduza o tamanho da piscina 2 para o eDTU original (10) ou número de vCores.
* Definir todas as bases de dados de ensaio restauradas na região B apenas para ler (11).
* Para cada base de dados no conjunto DR experimental que mudou desde a recuperação, renomear ou eliminar a base de dados correspondente no pool primário experimental (12).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (13).
* Apague a piscina DR (14).

### <a name="benefit"></a>Vantagem

Os principais benefícios desta estratégia são:

* Apoia o SLA mais agressivo para os clientes pagadores porque garante que uma paralisação não pode afetar mais de 50% das bases de dados dos inquilinos.
* Garante que os novos ensaios são desbloqueados assim que o trail DR pool for criado durante a recuperação.
* Permite uma utilização mais eficiente da capacidade da piscina, uma vez que 50% das bases de dados secundárias no pool 1 e no pool 2 são garantidamente menos ativas do que as bases de dados primárias.

### <a name="trade-offs"></a>Compensações

As principais compensações são:

* As operações crud contra as bases de dados de gestão têm uma latência mais baixa para os utilizadores finais ligados à região A do que para os utilizadores finais ligados à região B, uma vez que são executados contra as bases de dados primárias da gestão.
* Requer um design mais complexo da base de dados de gestão. Por exemplo, cada registo de inquilino tem uma etiqueta de localização que precisa de ser alterada durante o failover e o failback.  
* Os clientes pagadores podem ter um desempenho inferior ao habitual até que o upgrade da piscina na região B esteja concluído.

## <a name="summary"></a>Resumo

Este artigo centra-se nas estratégias de recuperação de desastres para o nível de base de dados utilizado por uma aplicação de multi-inquilino saas ISV. A estratégia que escolhe baseia-se nas necessidades da aplicação, como o modelo de negócio, o SLA que pretende oferecer aos seus clientes, constrangimento orçamental, etc. Cada estratégia descrita descreve os benefícios e a troca para que possa tomar uma decisão informada. Além disso, a sua aplicação específica provavelmente inclui outros componentes Azure. Por isso, revê-se a orientação de continuidade do negócio e orquestra a recuperação do nível da base de dados com eles. Para saber mais sobre a gestão da recuperação de aplicações de base de dados em Azure, consulte [soluções de nuvem de design para recuperação de desastres.](designing-cloud-solutions-for-disaster-recovery.md)  

## <a name="next-steps"></a>Passos seguintes

* Para obter mais sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte [as cópias de segurança automatizadas da Base de Dados Azure SQL](automated-backups-overview.md).
* Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para saber mais sobre a utilização de backups automatizados para recuperação, consulte [restaurar uma base de dados a partir das cópias de segurança iniciadas pelo serviço.](recovery-using-backups.md)
* Para saber mais rapidamente opções de recuperação, consulte os [grupos de geo-replicação ativa](active-geo-replication-overview.md) e [auto-failover](auto-failover-group-overview.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para arquivar, consulte a [cópia da base de dados](database-copy.md).
