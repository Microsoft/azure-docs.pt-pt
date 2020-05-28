---
title: Soluções de recuperação de desastres de design
description: Aprenda a desenhar a sua solução de nuvem para recuperação de desastres, escolhendo o padrão de failover certo.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb-1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 8ba9edc129cc169ccc146c7bc314d8f5ffe573b9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050409"
---
# <a name="disaster-recovery-strategies-for-applications-using-azure-sql-database-elastic-pools"></a>Estratégias de recuperação de desastres para aplicações usando piscinas elásticas Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Base de Dados Azure SQL fornece várias capacidades para fornecer a continuidade do negócio da sua aplicação quando ocorrerem incidentes catastróficos. [Piscinas elásticas](elastic-pool-overview.md) e bases de dados individuais suportam o mesmo tipo de capacidades de recuperação de desastres (DR). Este artigo descreve várias estratégias de DR para piscinas elásticas que alavancam estas funcionalidades de continuidade do negócio da Base de Dados Azure SQL.

Este artigo utiliza o seguinte padrão de aplicação canónica SaaS ISV:

Uma aplicação web moderna baseada na nuvem disponibiliza uma base de dados para cada utilizador final. O ISV tem muitos clientes e, portanto, utiliza muitas bases de dados, conhecidas como bases de dados de inquilinos. Como as bases de dados dos inquilinos normalmente têm padrões de atividade imprevisíveis, o ISV usa uma piscina elástica para tornar o custo da base de dados muito previsível durante longos períodos de tempo. O pool elástico também simplifica a gestão do desempenho quando a atividade do utilizador aumenta. Além das bases de dados dos inquilinos, a aplicação também utiliza várias bases de dados para gerir perfis de utilizador, segurança, recolha de padrões de utilização, etc. A disponibilidade dos inquilinos individuais não afeta a disponibilidade da aplicação no seu conjunto. No entanto, a disponibilidade e desempenho das bases de dados de gestão é fundamental para a função da aplicação e se as bases de dados de gestão estiverem offline, toda a aplicação está offline.

Este artigo discute estratégias de DR que abrangem uma série de cenários, desde aplicações de startups sensíveis a custos até aquelas com rigorosos requisitos de disponibilidade.

> [!NOTE]
> Se estiver a utilizar bases de dados Premium ou Business Critical e piscinas elásticas, pode torná-las resilientes a interrupções regionais, convertendo-as em configuração de implantação redundante da zona. Consulte [bases de dados redundantes](high-availability-sla.md)da Zona .

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Startup sensível ao custo

Sou um negócio de startups e sou extremamente sensível aos custos.  Quero simplificar a implementação e gestão da aplicação e posso ter um SLA limitado para clientes individuais. Mas quero garantir que a aplicação como um todo nunca esteja offline.

Para satisfazer o requisito de simplicidade, coloque todas as bases de dados de inquilinos numa piscina elástica na região azure à sua escolha e implemente bases de dados de gestão como bases de dados individuais geo-replicadas. Para a recuperação de desastres dos inquilinos, use geo-restauro, que não tem custos adicionais. Para garantir a disponibilidade das bases de dados de gestão, geo-replica-as para outra região utilizando um grupo de auto-failover (passo 1). O custo contínuo da configuração de recuperação de desastres neste cenário é igual ao custo total das bases de dados secundárias. Esta configuração é ilustrada no próximo diagrama.

![Figura 1](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Se ocorrer uma paragem na região primária, as etapas de recuperação para colocar a sua aplicação online são ilustradas pelo próximo diagrama.

* O grupo failover inicia a falha automática da base de dados de gestão para a região DR. A aplicação é automaticamente reconectada às novas primárias e todas as novas contas e bases de dados dos inquilinos são criadas na região de DR. Os clientes existentes vêem os seus dados temporariamente indisponíveis.
* Crie a piscina elástica com a mesma configuração que a piscina original (2).
* Utilize geo-restauro para criar cópias das bases de dados dos inquilinos (3). Pode considerar desencadear as restaurações individuais através das ligações do utilizador final ou utilizar outro esquema prioritário específico para a aplicação.

Neste momento, a sua aplicação está novamente online na região DR, mas alguns clientes experimentam atrasos ao aceder aos seus dados.

![Figura 2](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a paralisação foi temporária, é possível que a região primária seja recuperada pelo Azure antes que todas as restaurações da base de dados estejam completas na região de DR. Neste caso, orquestrar a mudança da aplicação para a região primária. O processo dá os passos ilustrados no próximo diagrama.

* Cancele todos os pedidos de georestauro pendentes.
* Falhar nas bases de dados de gestão da região primária (5). Após a recuperação da região, as antigas primárias tornaram-se automaticamente secundárias. Agora mudam de papéis de novo.
* Mude a cadeia de ligação da aplicação para apontar de volta para a região primária. Agora todas as novas contas e bases de dados dos inquilinos são criadas na região primária. Alguns clientes existentes vêem os seus dados temporariamente indisponíveis.
* Desloque todas as bases de dados do conjunto DR para leitura apenas para garantir que não podem ser modificadas na região DR (6).
* Para cada base de dados do conjunto DR que tenha mudado desde a recuperação, renomeou ou elimine as bases de dados correspondentes no pool primário (7).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (8).
* Eliminar a piscina DR (9)

Neste momento, a sua candidatura está online na região primária com todas as bases de dados de inquilinos disponíveis na piscina primária.

![Figura 3](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

### <a name="benefit"></a>Vantagem

O principal benefício desta estratégia é o baixo custo contínuo para a redundância do nível de dados. A Base de Dados Azure SQL faz automaticamente o backup das bases de dados sem reescrever nenhuma aplicação sem custos adicionais. O custo só é incorrido quando as bases de dados elásticas são restauradas. 

### <a name="trade-off"></a>Compensação

A compensação é que a recuperação completa de todas as bases de dados de inquilinos leva um tempo significativo. O tempo depende do número total de restauros iniciados na região DR e da dimensão global das bases de dados dos inquilinos. Mesmo que dê prioridade aos restauros de alguns inquilinos em detrimento de outros, está a competir com todos os outros restauros que são iniciados na mesma região que os árbitros e aceleradores de serviço para minimizar o impacto global nas bases de dados dos clientes existentes. Além disso, a recuperação das bases de dados dos inquilinos não pode começar até que seja criada a nova piscina elástica na região DR.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicação madura com serviço hierárquico

Sou uma aplicação SaaS madura com ofertas de serviço seletivas e Diferentes SLAs para clientes experimentais e para clientes pagadores. Para os clientes do julgamento, tenho de reduzir o custo o máximo possível. Os clientes do julgamento podem fazer um tempo de descanso, mas quero reduzir a sua probabilidade. Para os clientes pagadores, qualquer tempo de inatividade é um risco de fuga. Por isso, quero ter a certeza de que os clientes pagadores são sempre capazes de aceder aos seus dados.

Para apoiar este cenário, separe os inquilinos do julgamento dos inquilinos pagos colocando-os em piscinas elásticas separadas. Os clientes de ensaio têm eDTU ou vCores mais baixos por inquilino e SLA mais baixo com um tempo de recuperação mais longo. Os clientes pagadores estão numa piscina com eDTU ou vCores mais elevado sacana e um SLA mais elevado. Para garantir o menor tempo de recuperação, as bases de dados dos inquilinos dos clientes pagadores são geo-replicadas. Esta configuração é ilustrada no próximo diagrama.

![Figura 4](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Tal como no primeiro cenário, as bases de dados de gestão estão bastante ativas, pelo que utiliza uma única base de dados geo-replicada para ele (1). Isto garante o desempenho previsível para novas subscrições de clientes, atualizações de perfis e outras operações de gestão. A região em que residem as primárias das bases de dados de gestão é a região primária e a região em que residem os secundários das bases de dados de gestão.

As bases de dados dos inquilinos pagadores têm bases de dados ativas no pool "pago" provisionado na região primária. Provisão de uma piscina secundária com o mesmo nome na região de DR. Cada inquilino é geo-replicado para a piscina secundária (2). Isto permite uma rápida recuperação de todas as bases de dados de inquilinos usando o failover.

Se ocorrer uma paragem na região primária, as etapas de recuperação para colocar a sua aplicação online são ilustradas no diagrama seguinte:

![Figura 5](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Falhar imediatamente nas bases de dados de gestão para a região DR (3).
* Altere a cadeia de ligação da aplicação para apontar para a região DR. Agora todas as novas contas e bases de dados dos inquilinos são criadas na região de DR. Os clientes de ensaio existentes vêem os seus dados temporariamente indisponíveis.
* Falhar nas bases de dados pagas do arrendatário para a piscina na região de DR para restaurar imediatamente a sua disponibilidade (4). Uma vez que o failover é uma rápida alteração do nível de metadados, considere uma otimização onde as falhas individuais são desencadeadas a pedido pelas ligações do utilizador final.
* Se o seu tamanho eDTU de piscina secundária ou o valor vCore fosse inferior ao primário porque as bases de dados secundárias apenas requeriam a capacidade de processar os registos de mudança enquanto eram secundários, aumentaria imediatamente a capacidade do pool agora para acomodar toda a carga de trabalho de todos os inquilinos (5).
* Crie o novo conjunto elástico com o mesmo nome e a mesma configuração na região DR para as bases de dados dos clientes experimentais (6).
* Uma vez criada a piscina dos clientes experimentais, utilize geo-restauro para restaurar as bases de dados individuais de inquilinos experimentais na nova piscina (7). Considere desencadear as restaurações individuais através das ligações do utilizador final ou utilize outro esquema prioritário específico para a aplicação.

Neste momento, a sua candidatura está novamente online na região de DR. Todos os clientes pagadores têm acesso aos seus dados enquanto os clientes do teste experimentam atrasos no acesso aos seus dados.

Quando a região primária for recuperada pelo Azure *depois* de ter restaurado a aplicação na região DR pode continuar a executar a aplicação naquela região ou pode decidir falhar na região primária. Se a região primária for recuperada *antes* de o processo de failover estar concluído, considere falhar imediatamente. O failback dá os passos ilustrados no próximo diagrama:

![Figura 6](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele todos os pedidos de georestauro pendentes.
* Falhar nas bases de dados de gestão (8). Após a recuperação da região, as primárias antigas tornam-se automaticamente secundárias. Agora torna-se a primária de novo.  
* Falhar nas bases de dados pagas de inquilinos (9). Da mesma forma, após a recuperação da região, as primárias antigas tornam-se automaticamente os secundários. Agora voltam a ser as primárias.
* Detete as bases de dados de ensaio restauradas que mudaram na região de DR para apenas leitura (10).
* Para cada base de dados no pool de clientes de ensaio DR que mudou desde a recuperação, renomeou ou elimina a base de dados correspondente no pool primário dos clientes experimentais (11).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (12).
* Eliminar a piscina DR (13).

> [!NOTE]
> A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que execute o comando de failover das bases de dados dos inquilinos em lotes de pelo menos 20 bases de dados.

### <a name="benefit"></a>Vantagem

O principal benefício desta estratégia é que fornece o SLA mais elevado para os clientes pagadores. Também garante que os novos ensaios são desbloqueados assim que o conjunto DR do ensaio é criado. 

### <a name="trade-off"></a>Compensação

A compensação é que esta configuração aumenta o custo total das bases de dados dos inquilinos pelo custo do conjunto secundário de DR para clientes pagos. Além disso, se a piscina secundária tiver um tamanho diferente, os clientes pagadores experimentam um desempenho mais baixo após a falha até que a atualização da piscina na região DER esteja concluída.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicação distribuída geograficamente com serviço hierárquico

Tenho uma aplicação SaaS madura com ofertas de serviço seletivas. Quero oferecer um SLA muito agressivo aos meus clientes pagos e minimizar o risco de impacto quando ocorrem interrupções porque mesmo uma breve interrupção pode causar insatisfação do cliente. É fundamental que os clientes pagadores possam sempre aceder aos seus dados. Os julgamentos são gratuitos e um SLA não é oferecido durante o período experimental.

Para apoiar este cenário, utilize três piscinas elásticas separadas. Provisão de dois pools de tamanho igual com eDTUs ou vCores por base de dados em duas regiões diferentes para conter as bases de dados dos inquilinos dos clientes pagos. O terceiro pool contendo os inquilinos experimentais pode ter eDTUs inferiores ou vCores por base de dados e ser provisionado numa das duas regiões.

Para garantir o menor tempo de recuperação durante as interrupções, as bases de dados dos inquilinos dos clientes pagadores são geo-replicadas com 50% das bases de dados primárias em cada uma das duas regiões. Da mesma forma, cada região tem 50% das bases de dados secundárias. Desta forma, se uma região estiver offline, apenas 50% das bases de dados dos clientes pagos são impactadas e têm de falhar. As outras bases de dados permanecem intactas. Esta configuração é ilustrada no seguinte diagrama:

![Figura 4](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Tal como nos cenários anteriores, as bases de dados de gestão estão bastante ativas, por isso configurá-las como bases de dados únicas geo-replicadas (1). Isto garante o desempenho previsível das novas subscrições de clientes, atualizações de perfis e outras operações de gestão. A região A é a região principal das bases de dados de gestão e a região B é utilizada para a recuperação das bases de dados de gestão.

As bases de dados dos inquilinos pagadores também são geo-replicadas, mas com primárias e secundárias divididas entre a região A e a região B (2). Desta forma, as bases de dados primárias dos inquilinos impactadas pela paralisação podem falhar na outra região e ficar disponíveis. A outra metade das bases de dados dos inquilinos não é afetada.

O diagrama seguinte ilustra os passos de recuperação a tomar se ocorrer uma paragem na região A.

![Figura 5](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Falhar imediatamente nas bases de dados de gestão para a região B (3).
* Alterar a cadeia de ligação da aplicação para apontar para as bases de dados de gestão na região B. Modificar as bases de dados de gestão para garantir que as novas contas e bases de dados dos inquilinos são criadas na região B e as bases de dados de inquilinos existentes também são encontradas lá. Os clientes de ensaio existentes vêem os seus dados temporariamente indisponíveis.
* Falhar nas bases de dados pagas do arrendatário para a piscina 2 na região B para restaurar imediatamente a sua disponibilidade (4). Uma vez que a falha é uma rápida alteração do nível de metadados, pode considerar uma otimização em que as falhas individuais são desencadeadas a pedido pelas ligações do utilizador final.
* Uma vez que agora o pool 2 contém apenas bases de dados primárias, a carga de trabalho total na piscina aumenta e pode aumentar imediatamente o seu tamanho eDTU (5) ou o número de vCores.
* Crie o novo conjunto elástico com o mesmo nome e a mesma configuração na região B para as bases de dados dos clientes experimentais (6).
* Uma vez que a piscina é criada use geo-restauro para restaurar a base de dados individual de inquilinos experimentais na piscina (7). Pode considerar desencadear as restaurações individuais através das ligações do utilizador final ou utilizar outro esquema prioritário específico para a aplicação.

> [!NOTE]
> A operação de failover é assíncrona. Para minimizar o tempo de recuperação, é importante que execute o comando de failover das bases de dados de inquilinos em lotes de pelo menos 20 bases de dados.

Neste momento, a sua candidatura está novamente online na região B. Todos os clientes pagadores têm acesso aos seus dados enquanto os clientes do teste experimentam atrasos no acesso aos seus dados.

Quando a região A é recuperada, você precisa decidir se você quer usar a região B para clientes experimentais ou não usar o pool de clientes experimentais na região A. Um dos critérios poderia ser a % das bases de dados de inquilinos experimentais modificadas desde a recuperação. Independentemente dessa decisão, é preciso reequilibrar os inquilinos pagos entre duas piscinas. o diagrama seguinte ilustra o processo quando as bases de dados de inquilinos de julgamento não voltarem à região A.  

![Figura 6](./media/disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele todos os pedidos de georestauro pendentes para a piscina DR experimental.
* Falhar na base de dados de gestão (8). Após a recuperação da região, as primárias antigas tornaram-se automaticamente secundárias. Agora torna-se a primária de novo.  
* Selecione quais bases de dados pagas de inquilinos não voltam à piscina 1 e inicia madeixa para os seus secundários (9). Após a recuperação da região, todas as bases de dados da piscina 1 tornaram-se automaticamente secundárias. Agora 50% deles voltam a ser primárias.
* Reduza o tamanho da piscina 2 para o eDTU original (10) ou o número de vCores.
* Deteto todas as bases de dados de ensaio restauradas na região B para leitura (11).
* Para cada base de dados do conjunto de DR experimental que tenha mudado desde a recuperação, renomeação ou exclusão da base de dados correspondente no pool primário experimental (12).
* Copie as bases de dados atualizadas da piscina DR para a piscina primária (13).
* Eliminar a piscina DR (14).

### <a name="benefit"></a>Vantagem

Os principais benefícios desta estratégia são:

* Suporta o SLA mais agressivo para os clientes pagadores porque garante que uma paralisação não pode afetar mais de 50% das bases de dados dos inquilinos.
* Garante que os novos ensaios são desbloqueados assim que a piscina trail DR é criada durante a recuperação.
* Permite uma utilização mais eficiente da capacidade da piscina, uma vez que 50% das bases de dados secundárias na piscina 1 e na piscina 2 são garantidas menos ativas do que as bases de dados primárias.

### <a name="trade-offs"></a>Compensações

As principais compensações são:

* As operações crud contra as bases de dados de gestão têm menor latência para os utilizadores finais ligados à região A do que para os utilizadores finais ligados à região B, uma vez que são executados contra as principais bases de dados de gestão.
* Requer um design mais complexo da base de dados de gestão. Por exemplo, cada registo de inquilino tem uma etiqueta de localização que precisa de ser alterada durante o failover e failback.  
* Os clientes pagadores podem experimentar um desempenho inferior ao habitual até que a atualização da piscina na região B esteja concluída.

## <a name="summary"></a>Resumo

Este artigo centra-se nas estratégias de recuperação de desastres para o nível de base de dados utilizado por uma aplicação multi-arrendatária SaaS ISV. A estratégia que escolhe baseia-se nas necessidades da aplicação, como o modelo de negócio, o SLA que pretende oferecer aos seus clientes, restrição orçamental, etc. Cada estratégia descrita descreve os benefícios e a compensação para que possa tomar uma decisão informada. Além disso, a sua aplicação específica provavelmente inclui outros componentes Azure. Então, revê a orientação de continuidade do negócio deles e orquestra a recuperação do nível de base de dados com eles. Para saber mais sobre a gestão da recuperação de aplicações de base de dados em Azure, consulte o Design de [soluções em nuvem para recuperação de desastres.](designing-cloud-solutions-for-disaster-recovery.md)  

## <a name="next-steps"></a>Próximos passos

* Para saber sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte cópias de segurança automatizadas da Base de [Dados Azure SQL](automated-backups-overview.md).
* Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para aprender a utilizar cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados das cópias de segurança iniciadas pelo serviço](recovery-using-backups.md).
* Para aprender sobre opções de recuperação mais rápidas, consulte a [geo-replicação ativa](active-geo-replication-overview.md) e [os grupos de auto-failover](auto-failover-group-overview.md).
* Para aprender sobre a utilização de cópias automáticas para arquivamento, consulte [a cópia da base de dados](database-copy.md).
