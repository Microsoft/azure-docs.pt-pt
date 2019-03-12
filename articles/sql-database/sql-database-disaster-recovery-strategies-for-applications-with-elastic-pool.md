---
title: Conceber soluções de recuperação após desastre - base de dados SQL do Azure | Documentos da Microsoft
description: Aprenda a conceber a sua solução de cloud para recuperação após desastre ao escolher o padrão de ativação pós-falha certo.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6a332ce265a4bb41a9ad3c0c3a29683187a0f0d4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760500"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Estratégias de recuperação após desastre para aplicações que utilizam conjuntos elásticos da base de dados SQL

Ao longo dos anos, que Ficamos cientes de que os serviços cloud não são à prova de falhas e catastróficas incidentes acontece. Base de dados SQL fornece diversos recursos para fornecer para a continuidade de negócio da sua aplicação quando ocorrem esses incidentes. [Conjuntos elásticos](sql-database-elastic-pool.md) e após desastre (DR) de capacidades de recuperação, o mesmo tipo de suporte de bases de dados individuais. Este artigo descreve várias estratégias de DR para conjuntos elásticos que tirar partido destas funcionalidades de continuidade de negócio de base de dados SQL.

Este artigo usa o padrão de aplicação SaaS ISV canônico seguinte:

Um aplicativo web moderno com base na cloud Aprovisiona uma base de dados SQL para cada utilizador final. O ISV tem muitos clientes e, portanto, utiliza muitas bases de dados, conhecidos como bases de dados do inquilino. Como as bases de dados do inquilino normalmente têm padrões de atividade imprevisível, o ISV utiliza um conjunto elástico para fazer com que a base de dados de custo muito previsível durante longos períodos de tempo. O conjunto elástico também simplifica o gerenciamento de desempenho quando os picos de atividade do utilizador. Além das bases de dados do inquilino a aplicação utiliza também vários bancos de dados para gerir perfis de usuário, segurança, recolher padrões de uso etc. Disponibilidade dos inquilinos individuais não afeta a disponibilidade do aplicativo, como todo. No entanto, a disponibilidade e desempenho de bases de dados de gestão é essencial para a função do aplicativo e se as bases de dados de gestão estiverem offline toda a aplicação está offline.

Este artigo discute as estratégias de DR que abrangem uma gama de cenários de aplicativos de inicialização confidenciais de custo para aqueles com requisitos de disponibilidade rigorosas.

> [!NOTE]
> Se estiver a utilizar conjuntos elásticos e bases de dados Premium ou críticas para a empresa, pode torná-los resiliente a falhas regionais convertendo-os em configuração de implementação com redundância de zona. Ver [basesdedadosredundância de zona](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Cenário 1. Arranque confidencial de custos

Sou uma empresa startup e estou de custos extremamente confidencial.  Eu quero simplificar a implementação e gestão da aplicação e pode ter um SLA com suporte limitado para clientes individuais. Mas eu queira garantir que o aplicativo como um todo nunca está offline.

Para satisfazer o requisito de simplicidade, implementar todas as bases de dados de inquilinos para um conjunto elástico na região do Azure à sua escolha e implantar bancos de dados de gestão como georreplicado bases de dados individuais. Para a recuperação após desastre de inquilinos, utilize o georrestauro, que é fornecido sem custos adicionais. Para garantir a disponibilidade dos bancos de dados de gestão, georreplicá-las para outra região com uma ativação pós-falha automática de grupo (etapa 1). O custo contínuo da configuração de recuperação após desastre neste cenário é igual ao custo total das bases de dados secundários. Esta configuração é ilustrada no diagrama seguinte.

![Figura 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Se ocorrer uma falha na região primária, os passos de recuperação para trazer a sua aplicação online são ilustrados pelo diagrama seguinte.

* O grupo de ativação pós-falha, inicia a ativação pós-falha automática da base de dados de gestão para a região de DR. O aplicativo for reconectado automaticamente para as novas contas de principal e todos os novos e bases de dados de inquilino são criadas na região DR. Os clientes existentes vejam os seus dados temporariamente indisponíveis.
* Crie o conjunto elástico com a mesma configuração de que o conjunto original (2).
* Utilize o restauro geográfico para criar cópias do inquilino bases de dados (3). Pode considerar a acionar as restaurações individuais, as ligações de utilizador final ou utilize outra esquema de prioridade de específico do aplicativo.

Neste ponto seu aplicativo esteja novamente online na região DR, mas alguns clientes experiência atraso ao aceder a seus dados.

![Figura 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Se a falha foi temporária, é possível que a região primária é recuperada pelo Azure antes de todas as restaurações de base de dados completas na região DR. Neste caso, orquestre mover o aplicativo novamente para a região primária. O processo demora passos ilustrados no diagrama seguinte.

* Cancele todas as solicitações pendentes o restauro geográfico.
* Efetuar a ativação pós-falha as bases de dados de gestão para a região primária (5). Após a recuperação da região, as cores primárias antigas automaticamente têm se tornado bases de dados secundárias. Agora eles alternar funções novamente.
* Altere a cadeia de ligação da aplicação para apontar para a região primária. Agora todas as novas contas e bases de dados de inquilino são criadas na região primária. Alguns clientes existentes vejam os seus dados temporariamente indisponíveis.
* Defina todas as bases de dados no agrupamento de DR para só de leitura para garantir que eles não podem ser modificados na região DR (6).
* Para cada base de dados no agrupamento de DR que foram alterados desde a recuperação, mude o nome ou eliminar as bases de dados correspondentes no conjunto principal (7).
* Copie as bases de dados atualizados do conjunto de DR para o conjunto principal (8).
* Eliminar o conjunto de DR (9)

Neste momento a seu aplicativo está online na região primária com todas as bases de dados inquilinas disponível no agrupamento de primário.

![Figura 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A chave **beneficiar** dessa estratégia tem baixo custo em curso para redundância de camada de dados. As cópias de segurança são que automaticamente o serviço de base de dados SQL com nenhuma reescrita de aplicativo e sem custos adicionais.  O custo é incorrido apenas quando as bases de dados elásticas são restaurados. O **compensação** é que a recuperação completa de todas as bases de dados do inquilino leva um tempo significativo. O período de tempo depende do total geral tamanho das bases de dados de inquilino e o número de restauros iniciar na região DR. Mesmo que a priorizar as restaurações de alguns dos inquilinos através de outras pessoas, estejam competindo com todos os outros restauros que são iniciados na mesma região que o serviço arbitrates e limita para minimizar o impacto geral nas bases de dados dos clientes existentes. Além disso, a recuperação de bases de dados do inquilino não é possível iniciar até que o novo conjunto elástico na região DR é criado.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Cenário 2. Aplicativo com o serviço em camadas

Sou um aplicativo SaaS com ofertas de serviço em camadas e SLAs diferentes para os clientes de avaliação e de pagamento de clientes. Para os clientes de avaliação, tenho de reduzir o custo tanto quanto possível. Os clientes de avaliação podem demorar um período de indisponibilidade, mas eu quero reduzir a probabilidade. Para clientes pagantes, qualquer período de inatividade é um risco de voo. Então, quero Certifique-se de que pagar os clientes podem sempre aceder aos respetivos dados.

Para suportar este cenário, separe os inquilinos de avaliação de inquilinos pagos, colocando-os em conjuntos elásticos separados. Os clientes de avaliação tem inferior eDTU ou vCores por inquilino e o SLA inferior com mais tempo de recuperação. Os clientes pagantes estão num conjunto com superior eDTU ou vCores por inquilino e um SLA mais elevado. Para garantir o tempo de recuperação mais baixo, bases de dados dos clientes pagantes inquilinos são georreplicado. Esta configuração é ilustrada no diagrama seguinte.

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Como no primeiro cenário, as bases de dados de gestão são bastante Active Directory, pelo que utilizar uma única base de dados georreplicada para ele (1). Isso garante o desempenho previsível para novas subscrições de clientes, atualizações de perfil e outras operações de gestão. A região na qual residem as cores primárias das bases de dados de gestão é a região primária e a região na qual residem as bases de dados secundárias das bases de dados de gestão é a região de DR.

Bases de dados dos clientes pagantes inquilinos têm bases de dados ativas no conjunto de "pago" aprovisionado na região primária. Aprovisione um conjunto secundário com o mesmo nome na região DR. Cada inquilino é georreplicado ao agrupamento de secundário (2). Isto permite uma recuperação rápida de todas as bases de dados inquilinas através de ativação pós-falha.

Se ocorrer uma falha na região primária, os passos de recuperação para trazer a sua aplicação online são ilustrados no diagrama seguinte:

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Imediatamente a ativação pós-falha as bases de dados de gestão para a região de DR (3).
* Altere a cadeia de ligação da aplicação para apontar para a região de DR. Agora todas as novas contas e bases de dados de inquilino são criadas na região DR. Os clientes de avaliação existentes vejam os seus dados temporariamente indisponíveis.
* Efetuar a ativação pós-falha bases de dados do inquilino paga para o conjunto na região DR para restaurar imediatamente a sua disponibilidade (4). Uma vez que a ativação pós-falha é uma alteração de nível de metadados rápida, considere uma otimização onde as ativações pós-falha individuais são acionadas a pedido, as ligações de utilizador final.
* Se o valor de tamanho ou vCore de eDTU do conjunto secundário menor do que o primário porque as bases de dados secundárias necessária apenas a capacidade para processar os registos de alteração, enquanto estivessem secundários, imediatamente aumentar a capacidade de agrupamento agora para acomodar a carga de trabalho completa de todos os inquilinos (5).
* Crie o novo conjunto elástico com o mesmo nome e a mesma configuração na região DR para bases de dados de avaliação dos clientes (6).
* Depois de criar o conjunto de avaliação dos clientes, utilize o restauro geográfico para restaurar as bases de dados do inquilino de avaliação individuais para o novo conjunto (7). Considere a acionar as restaurações individuais, as ligações de utilizador final ou utilize outra esquema de prioridade de específico do aplicativo.

Neste momento a seu aplicativo esteja novamente online na região DR. Todos os clientes pagantes têm acesso aos respetivos dados, enquanto os clientes de avaliação de atraso experiência quando aceder aos seus dados.

Quando a região primária é recuperada pelo Azure *depois de* restaurou a aplicação na região DR pode continuar a executar o aplicativo nessa região ou pode optar por efetuar a ativação pós-falha para a região primária. Se a região primária é recuperada *antes de* concluído o processo de ativação pós-falha, considere efetuar a reativação pós-falha imediatamente. A reativação pós-falha demora passos ilustrados no diagrama seguinte:

![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Cancele todas as solicitações pendentes o restauro geográfico.
* Efetuar a ativação pós-falha as bases de dados de gestão (8). Após a recuperação da região, o principal anterior tornam-se automaticamente o secundário. Agora, é o principal novamente.  
* Efetuar a ativação pós-falha as bases de dados do inquilino paga (9). Da mesma forma, após a recuperação da região, as cores primárias antigas tornam-se automaticamente as bases de dados secundárias. Agora que se tornem as cores primárias novamente.
* Defina as avaliação e restauradas bases de dados que foram alterados na região DR para só de leitura (10).
* Para cada base de dados no conjunto de clientes avaliação DR que sofreram alterações desde a recuperação, mude o nome ou eliminar a base de dados correspondente no conjunto de principais de clientes de avaliação (11).
* Copie as bases de dados atualizados do conjunto de DR para o conjunto principal (12).
* Elimine o conjunto de DR (13).

> [!NOTE]
> A operação de ativação pós-falha é assíncrona. Para minimizar o tempo de recuperação é importante que execute o comando de ativação pós-falha do inquilino bancos de dados em lotes de, pelo menos, 20 bases de dados.

A chave **beneficiar** desta estratégia é que ele fornece o SLA mais elevado para os clientes pagantes. Esta ação garante também que as novas avaliações estiverem desbloqueadas, assim que o conjunto de DR avaliação é criado. O **compensação** é que esta configuração aumenta o custo total de bases de dados do inquilino, o custo do conjunto de DR secundário para pagas aos clientes. Além disso, se o conjunto de secundário tem um tamanho diferente, os clientes pagantes detetar um desempenho inferior após a ativação pós-falha até que a atualização de agrupamento na região DR é concluída.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Cenário 3. Aplicação distribuída geograficamente com o serviço em camadas

Tenho um aplicativo SaaS com ofertas de serviço em camadas. Eu quero oferecem um SLA com suporte muito agressivo para meus clientes pagas e minimizar o risco de impacto quando ocorrerem falhas porque mesmo breve interrupção pode causar pequena insatisfação do cliente. É fundamental que os clientes pagantes sempre podem aceder aos respetivos dados. As avaliações são gratuitas e um SLA não é oferecido durante o período de avaliação.

Para suportar este cenário, utilize três conjuntos elásticos separados. Aprovisione dois conjuntos de tamanho igual com alta eDTUs ou vCores por base de dados em duas regiões diferentes para conter as bases de dados de inquilinos dos clientes pagos. O terceiro conjunto que contém os inquilinos de avaliação pode ter mais baixos eDTUs ou vCores por base de dados e ser aprovisionado em uma de duas regiões.

Para garantir o tempo de recuperação mais baixo durante as falhas, bases de dados dos clientes pagantes inquilinos são georreplicado com 50% das bases de dados primários em cada uma das duas regiões. Da mesma forma, cada região tem 50% das bases de dados secundários. Dessa forma, se uma região estiver offline, os apenas 50% de bases de dados dos clientes pagos são afetados e tem de efetuar a ativação pós-falha. As outras bases de dados permanecem intactas. Esta configuração é ilustrada no diagrama seguinte:

![Figura 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Como nos cenários anteriores, as bases de dados de gestão estão muito ativos então configurá-las como georreplicado bases de dados individuais (1). Isso garante o desempenho previsível de novo cliente subscrições, atualizações de perfil e outras operações de gestão. Região A é a região primária para as bases de dados de gestão e a região B é utilizada para recuperação das bases de dados de gestão.

Bases de dados dos clientes pagantes inquilinos também são georreplicado, mas com cores primárias e secundárias divididas entre a região A e B (2) de região. Desta forma, os inquilino principal bases de dados afetados pela falha podem efetuar a ativação pós-falha para a outra região e se tornarem disponíveis. A outra metade das bases de dados de inquilino não são de ser afetados em todos os.

O diagrama seguinte ilustra as etapas de recuperação a tomar se ocorrer uma falha na região A.

![Figura 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Imediatamente a ativação pós-falha gestão bases de dados para a região B (3).
* Alterar a cadeia de ligação da aplicação para apontar para as bases de dados de gestão na região B. Modifique as bases de dados de gestão para se certificar de que as novas contas e bases de dados de inquilino são criados numa região B e as bases de dados existente do inquilino são encontrados aqui também. Os clientes de avaliação existentes vejam os seus dados temporariamente indisponíveis.
* Efetuar a ativação pós-falha bases de dados do inquilino paga para o agrupamento 2 na região B para restaurar imediatamente a sua disponibilidade (4). Uma vez que a ativação pós-falha é uma alteração de nível de metadados rápida, pode considerar uma otimização onde as ativações pós-falha individuais são acionadas a pedido, as ligações de utilizador final.
* Desde agora o agrupamento de 2 contém apenas primários bases de dados, a carga de trabalho total os aumentos de agrupamento e imediatamente pode aumentar o tamanho de eDTU (5) ou o número de vCores.
* Crie o novo conjunto elástico com o mesmo nome e a mesma configuração na região B para bases de dados de avaliação dos clientes (6).
* Assim que o conjunto for criado, utilize o restauro geográfico para restaurar a base de dados do inquilino de avaliação individuais para o pool (7). Pode considerar a acionar as restaurações individuais, as ligações de utilizador final ou utilize outra esquema de prioridade de específico do aplicativo.

> [!NOTE]
> A operação de ativação pós-falha é assíncrona. Para minimizar o tempo de recuperação, é importante que execute o comando de ativação pós-falha do inquilino bancos de dados em lotes de, pelo menos, 20 bases de dados.

Neste ponto seu aplicativo está novamente online na região B. Todos os clientes pagantes têm acesso aos respetivos dados, enquanto os clientes de avaliação de atraso experiência quando aceder aos seus dados.

Quando a região A é recuperada terá de decidir se pretende utilizar região B para clientes de avaliação ou a reativação pós-falha para utilizar o agrupamento de clientes de avaliação na região A. Critérios de uma possível a % de bases de dados de inquilino de avaliação modificado desde a recuperação. Independentemente dessa decisão, terá de voltar a equilibras os inquilinos pagos entre dois pools. o diagrama seguinte ilustra o processo quando as bases de dados do inquilino de avaliação a ativação pós-falha para a região A.  

![Figura 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Cancele todas as solicitações de georrestauro pendentes para o conjunto de avaliações DR.
* Efetuar a ativação pós-falha da base de dados de gestão (8). Após a recuperação da região, o principal anterior ficou automaticamente o secundário. Agora, é o principal novamente.  
* Selecione qual o inquilino pago failback de bases de dados para agrupamento 1 e iniciar a ativação pós-falha para as suas bases de dados secundárias (9). Após a recuperação da região, todas as bases de dados no conjunto 1 ficaram automaticamente bases de dados secundárias. Agora 50% deles se tornará cores primárias novamente.
* Reduza o tamanho do agrupamento de 2 para a eDTU original (10) ou o número de vCores.
* Conjunto de todos os restaurado avaliação bases de dados na região B para só de leitura (11).
* Para cada base de dados no conjunto de DR avaliação que foram alterados desde a recuperação, mude o nome ou eliminar a base de dados correspondente no conjunto principal de avaliação (12).
* Copie as bases de dados atualizados do conjunto de DR para o conjunto principal (13).
* Elimine o conjunto de DR (14).

A chave **benefícios** dessa estratégia são:

* Suporta o SLA mais agressivo para os clientes pagantes porque ela garante que uma falha não pode afetar mais de 50% das bases de dados de inquilino.
* Esta ação garante que as novas avaliações estiverem desbloqueadas, assim que o registo de conjunto de DR é criado durante a recuperação.
* Permite uma utilização mais eficiente a capacidade do agrupamento como 50% de bases de dados secundárias no agrupamento de 1 e 2 do conjunto é garantida menos ativos que as bases de dados primários.

Os principais **compensações** são:

* As operações CRUD contra as bases de dados de gestão têm uma latência mais baixa para os usuários finais ligados a região A que, para os usuários finais conectados à região B, conforme eles são executados no principal das bases de dados de gestão.
* Ele requer um design mais complexo da base de dados de gestão. Por exemplo, cada registo de inquilino tem uma marca de localização que tem de ser alterada durante a ativação pós-falha e reativação pós-falha.  
* Os clientes pagantes poderão experienciar um desempenho menor que o normal até que a atualização do agrupamento na região B estiver concluída.

## <a name="summary"></a>Resumo

Este artigo enfoca as estratégias de recuperação após desastre para o escalão de base de dados utilizado por um aplicativo do ISV do SaaS multi-inquilino. A estratégia que escolher baseia-se nas necessidades do aplicativo, como o modelo de negócios, o SLA que pretende oferecer aos seus clientes, orçamento restrição etc. Cada estratégia descrita descreve os benefícios e as compensações para que pode tomar uma decisão informada. Além disso, seu aplicativo específico provavelmente inclui outros componentes do Azure. Então, reveja as suas orientações de continuidade do negócio e organizar a recuperação de camada de base de dados com os mesmos. Para saber mais sobre como gerir a recuperação de aplicativos de banco de dados no Azure, consulte [soluções de nuvem de conceção para recuperação após desastre](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md).
* Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md).
* Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, veja [restaurar uma base de dados a partir de cópias de segurança iniciadas pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre as opções de recuperação mais rápidas, veja [georreplicação ativa](sql-database-active-geo-replication.md) e [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
* Para saber mais sobre a utilização de cópias de segurança automáticas de arquivamento, veja [base de dados de cópia](sql-database-copy.md).
