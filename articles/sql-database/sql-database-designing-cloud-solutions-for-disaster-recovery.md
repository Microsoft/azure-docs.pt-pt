---
title: Design de serviços disponíveis globalmente
description: Conheça o design de aplicações para serviços altamente disponíveis utilizando a Base de Dados Azure SQL.
keywords: recuperação de desastres em nuvem,soluções de recuperação de desastres,backup de dados de aplicações, geo-replicação, planeamento de continuidade de negócios
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 348bd2b92801217a5aea2ef4d1426c020085e4c1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359154"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Conceber serviços disponíveis globalmente utilizando base de dados Azure SQL

Ao construir e implantar serviços em nuvem com base de dados Azure SQL, você usa [geo-replicação ativa](sql-database-active-geo-replication.md) ou [grupos de auto-falha](sql-database-auto-failover-group.md) para fornecer resiliência a interrupções regionais e falhas catastróficas. A mesma funcionalidade permite criar aplicações distribuídas globalmente otimizadas para o acesso local aos dados. Este artigo discute padrões comuns de aplicação, incluindo os benefícios e as compensações de cada opção.

> [!NOTE]
> Se estiver a utilizar bases de dados Premium ou Business Critical e piscinas elásticas, pode torná-las resilientes a interrupções regionais, convertendo-as em configuração de implantação redundante da zona. Consulte [bases de dados redundantes](sql-database-high-availability.md)da Zona .  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Utilização de duas regiões azure para continuidade de negócios com o mínimo de tempo de paragem

Neste cenário, as aplicações têm as seguintes características:

* Aplicação está ativa numa região do Azure
* Todas as sessões de base de dados requerem acesso a leitura e escrita (RW) aos dados
* O nível web e o nível de dados devem ser coocados para reduzir a latência e os custos de tráfego
* Fundamentalmente, o tempo de inatividade é um risco de negócio maior para estas aplicações do que a perda de dados

Neste caso, a topologia de implementação de aplicações é otimizada para lidar com desastres regionais quando todos os componentes da aplicação precisam de falhar em conjunto. O diagrama abaixo mostra esta topologia. Para redundância geográfica, os recursos da aplicação são distribuídos para a região A e B. No entanto, os recursos na região B não são utilizados até que a região A falhe. Um grupo failover está configurado entre as duas regiões para gerir a conectividade da base de dados, replicação e falha. O serviço web em ambas as regiões está configurado para aceder à base de dados através do ouvinte de leitura&lt;nome de **grupo failover-name&gt;.database.windows.net** (1). O gestor de tráfego está criado para utilizar o método de [encaminhamento prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [O gestor de tráfego azure](../traffic-manager/traffic-manager-overview.md) é usado em todo este artigo apenas para fins de ilustração. Pode utilizar qualquer solução de equilíbrio de carga que suporte o método de encaminhamento prioritário.

O diagrama seguinte mostra esta configuração antes de uma paragem:

![Cenário 1. Configuração antes da paragem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma paragem na região primária, o serviço de base de dados SQL deteta que a base de dados primária não está acessível e dispara falhas na região secundária com base nos parâmetros da política de failover automática (1). Dependendo da sua aplicação SLA, pode configurar um período de carência que controla o tempo entre a deteção da paragem e a falha em si. É possível que o gestor de tráfego inicie a falha do ponto final antes que o grupo failover desencadeie a falha da base de dados. Nesse caso, a aplicação web não pode voltar imediatamente a ligar-se à base de dados. Mas as reconexões serão automaticamente bem sucedidas assim que a base de dados falhar. Quando a região falhada é restaurada e novamente on-line, a antiga primária reconecta-se automaticamente como uma nova secundária. O diagrama abaixo ilustra a configuração após a falha.

> [!NOTE]
> Todas as transações cometidas após a falha são perdidas durante a reconexão. Após a conclusão da falha, a aplicação na região B é capaz de reconectar e reiniciar o processamento dos pedidos do utilizador. Tanto a aplicação web como a base de dados primária estão agora na região B e permanecem co-localizadas.

![Cenário 1. Configuração após falha](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se ocorrer uma paragem na região B, o processo de replicação entre a base de dados primária e secundária é suspenso, mas a ligação entre os dois permanece intacta (1). O tráfego gerido deteta que a conectividade com a região B está quebrada e marca a aplicação web de ponto final 2 como Degradada (2). O desempenho da aplicação não é impactado neste caso, mas a base de dados fica exposta e, portanto, em maior risco de perda de dados caso a região A falhe sucessivamente.

> [!NOTE]
> Para a recuperação de desastres, recomendamos a configuração com implantação de aplicações limitada a duas regiões. Isto porque a maioria das geografias azure têm apenas duas regiões. Esta configuração não protege a sua aplicação de uma falha catastrófica simultânea de ambas as regiões. Num caso improvável de tal falha, pode recuperar as suas bases de dados numa terceira região utilizando a [operação de georestauro](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Uma vez atenuada a paragem, a base de dados secundária resincroniza-se automaticamente com a primária. Durante a sincronização, o desempenho da primária pode ser impactado. O impacto específico depende da quantidade de dados que a nova primária adquiriu desde o fracasso. O diagrama que se segue ilustra uma paragem na região secundária:

![Cenário 1. Configuração após uma paragem na região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

As **principais vantagens** deste padrão de design são:

* A mesma aplicação web é implementada em ambas as regiões sem qualquer configuração específica da região e não requer lógica adicional para gerir o failover.
* O desempenho da aplicação não é impactado por falhas, uma vez que a aplicação web e a base de dados estão sempre co-localizadas.

A principal **contrapartida** é que os recursos de aplicação na região B são subutilizados na maior parte do tempo.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: Regiões azure para continuidade de negócios com a máxima preservação de dados

Esta opção é mais adequada para aplicações com as seguintes características:

* Qualquer perda de dados é um alto risco para o negócio. A falha na base de dados só pode ser usada como último recurso se a paralisação for causada por uma falha catastrófica.
* A aplicação suporta modos de funcionamento apenas de leitura e leitura e pode funcionar em "modo de leitura apenas" por um período de tempo.

Neste padrão, a aplicação muda para o modo de leitura apenas quando as ligações de leitura-escrita começam a obter erros de tempo-out. A Aplicação Web é implantada em ambas as regiões e inclui uma ligação ao ponto final do ouvinte de leitura e ligação diferente ao ponto final do ouvinte de leitura (1). O perfil do gestor de tráfego deve utilizar [o encaminhamento prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [O controlo](../traffic-manager/traffic-manager-monitoring.md) do ponto final deve ser ativado para o ponto final da aplicação em cada região (2).

O diagrama seguinte ilustra esta configuração antes de uma paragem:

![Cenário 2. Configuração antes da paragem.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o gestor de tráfego deteta uma falha de conectividade na região A, muda automaticamente o tráfego do utilizador para a instância de aplicação na região B. Com este padrão, é importante que você coloque o período de graça com perda de dados para um valor suficientemente elevado, por exemplo 24 horas. Garante que a perda de dados é evitada se a interrupção for atenuada dentro desse tempo. Quando a aplicação Web na região B é ativada, as operações de leitura-escrita começam a falhar. Nessa altura, deve mudar para o modo de leitura (1). Neste modo, os pedidos são automaticamente encaminhados para a base de dados secundária. Se a paralisação for causada por uma falha catastrófica, o mais provável é que não possa ser atenuada dentro do período de carência. Quando expira, o grupo failover desencadeia a falha. Depois disso, o ouvinte de leitura-escrita fica disponível e as ligações ao mesmo deixam de falhar (2). O diagrama seguinte ilustra as duas fases do processo de recuperação.

> [!NOTE]
> Se a paralisação na região primária for atenuada no período de carência, o gestor de tráfego deteta o restabelecimento da conectividade na região primária e muda o tráfego de utilizadores de volta para a instância de aplicação na região A. Esta instância de aplicação retoma e funciona em modo de leitura-escrita utilizando a base de dados primária na região A, como ilustrado pelo diagrama anterior.

![Cenário 2. Fases de recuperação de desastres.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se ocorrer uma paragem na região B, o gestor de tráfego deteta a falha do ponto final web-app-2 na região B e marca-o degradado (1). Entretanto, o grupo failover muda o ouvinte apenas para a região A (2). Esta paragem não afeta a experiência do utilizador final, mas a base de dados primária é exposta durante a interrupção. O diagrama que se segue ilustra uma falha na região secundária:

![Cenário 2. Paralisação da região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Uma vez atenuada a paragem, a base de dados secundária é imediatamente sincronizada com o principal e o ouvinte só de leitura é recolocado para a base de dados secundária na região B. Durante a sincronização, o desempenho do primário pode ser ligeiramente impactado dependendo da quantidade de dados que precisam de ser sincronizados.

Este padrão de design tem **várias vantagens:**

* Evita a perda de dados durante as interrupções temporárias.
* O tempo de inatividade depende apenas da rapidez com que o gestor de tráfego deteta a falha de conectividade, que é configurável.

A **contrapartida** é que a aplicação deve poder funcionar em modo de leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: Deslocalização de candidaturas para uma geografia diferente sem perda de dados e quase zero tempo de inatividade

Neste cenário, a aplicação tem as seguintes características:

* Os utilizadores finais acedem à aplicação a partir de diferentes geografias
* A aplicação inclui cargas de trabalho apenas de leitura que não dependem da sincronização completa com as últimas atualizações
* O acesso aos dados de escrita deve ser suportado na mesma geografia para a maioria dos utilizadores
* Ler latência é fundamental para a experiência do utilizador final

Para satisfazer estes requisitos é necessário garantir que o dispositivo utilizador se liga **sempre** à aplicação implementada na mesma geografia para as operações de leitura, tais como dados de navegação, análises, etc. Considerando que as operações oLTP são processadas na mesma geografia na **maior parte do tempo.** Por exemplo, durante o dia as operações oLTP são processadas na mesma geografia, mas durante as horas de folga podem ser processadas numa geografia diferente. Se a atividade do utilizador final ocorrer maioritariamente durante o horário de trabalho, pode garantir o desempenho ideal para a maioria dos utilizadores na maior parte do tempo. O diagrama que se segue mostra esta topologia.

Os recursos da aplicação devem ser implantados em cada geografia onde você tem uma procura de utilização substancial. Por exemplo, se a sua aplicação for ativamente utilizada nos Estados Unidos, união europeia e Sudeste Asiático, a aplicação deve ser implementada em todas estas geografias. A base de dados primária deve ser dinamicamente alterada de uma geografia para outra no final do horário de trabalho. Este método chama-se "siga o sol". A carga de trabalho OLTP liga-se sempre à base de dados através do ouvinte de leitura&lt;nome de grupo de **falha&gt;.database.windows.net** (1). A carga de trabalho apenas para leitura liga-se à base de dados local utilizando diretamente o ponto final do servidor **&lt;o nome do servidor&gt;.database.windows.net** (2). O gestor de tráfego está configurado com o método de [encaminhamento](../traffic-manager/traffic-manager-configure-performance-routing-method.md)de desempenho . Garante que o dispositivo do utilizador final está ligado ao serviço web na região mais próxima. O gestor de tráfego deve ser criado com uma monitorização do ponto final ativada para cada ponto final do serviço web (3).

> [!NOTE]
> A configuração do grupo failover define qual a região que é usada para a falha. Como a nova primária está numa geografia diferente, o failover resulta em latência mais longa tanto para o OLTP como para as cargas de trabalho apenas para leitura até que a região impactada volte a estar online.

![Cenário 3. Configuração com primária nos EUA Orientais.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No final do dia, por exemplo, às 23:00 locais, as bases de dados ativas devem ser transferidas para a região seguinte (Norte da Europa). Esta tarefa pode ser totalmente automatizada utilizando [aplicações lógicas do Azure.](../logic-apps/logic-apps-overview.md) A tarefa envolve os seguintes passos:

* Altere o servidor primário no grupo failover para a Europa do Norte usando failover amigável (1)
* Remova o grupo de falhas entre os EUA Orientais e o Norte da Europa
* Crie um novo grupo de failover com o mesmo nome, mas entre a Europa do Norte e a Ásia Oriental (2).
* Adicione as primárias no Norte da Europa e secundária na Ásia Oriental a este grupo de failover (3).

O diagrama que se segue ilustra a nova configuração após a falha planeada:

![Cenário 3. Transição primária para o Norte da Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se uma paralisação ocorrer no Norte da Europa, por exemplo, a falha da base de dados automática é iniciada pelo grupo failover, o que resulta efetivamente na mudança da aplicação para a próxima região antes do previsto (1).  Nesse caso, o Leste dos EUA é a única região secundária restante até que o Norte da Europa volte a funcionar. As restantes duas regiões servem os clientes nas três geografias, mudando de funções. As Aplicações Lógicas Azure têm de ser ajustadas em conformidade. Dado que as restantes regiões obtêm tráfego adicional de utilizadores da Europa, o desempenho da aplicação é impactado não só pela latência adicional, mas também por um aumento do número de ligações ao utilizador final. Uma vez atenuada a paralisação no Norte da Europa, a base de dados secundária é imediatamente sincronizada com as primárias atuais. O diagrama que se segue ilustra uma paragem no Norte da Europa:

![Cenário 3. Paralisação no Norte da Europa.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Pode reduzir o tempo em que a experiência do utilizador final na Europa é degradada pela longa latência. Para tal, deve implantar proactivamente uma cópia de aplicação e criar a base de dados secundária de outra região local (Europa Ocidental) como substituto da instância de aplicação offline no Norte da Europa. Quando este último estiver de volta à internet, pode decidir se continua a utilizar a Europa Ocidental ou a remover a cópia da aplicação e voltar a utilizar o Norte da Europa.

Os principais **benefícios** deste design são:

* A carga de trabalho de aplicação apenas de leitura acede a dados na região dos armários em todos os momentos.
* A carga horária de leitura-escrita acede aos dados da região mais próxima durante o período da maior atividade em cada geografia
* Como a aplicação é implantada em várias regiões, pode sobreviver a uma perda de uma das regiões sem qualquer tempo de inatividade significativo.

Mas há **algumas trocas:**

* Uma paralisação regional resulta na geografia a ser impactada por uma latência mais longa. Tanto as cargas de trabalho de leitura como de leitura são servidas pela aplicação numa geografia diferente.
* As cargas de trabalho apenas de leitura devem ligar-se a um ponto final diferente em cada região.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planeamento de continuidade de negócios: Escolha um projeto de aplicação para recuperação de desastres na nuvem

A sua estratégia específica de recuperação de desastres em nuvem pode combinar ou estender estes padrões de design para melhor atender às necessidades da sua aplicação.  Como mencionado anteriormente, a estratégia que escolheu baseia-se no SLA que pretende oferecer aos seus clientes e na topologia de implementação de aplicações. Para ajudar a orientar a sua decisão, a tabela seguinte compara as escolhas com base no objetivo do ponto de recuperação (RPO) e no tempo estimado de recuperação (ERT).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação ativa passiva para recuperação de desastres com acesso a base de dados co-localizada |Acesso de leitura e escrita < 5 seg |Tempo de deteção de falhas + DNS TTL |
| Implantação ativa para o equilíbrio da carga de aplicação |Acesso de leitura e escrita < 5 seg |Tempo de deteção de falhas + DNS TTL |
| Implantação passiva ativa para a preservação de dados |Acesso apenas de leitura < 5 seg | Acesso apenas de leitura = 0 |
||Acesso de leitura-escrita = zero | Acesso de leitura-escrita = Tempo de deteção de falhas + período de carência com perda de dados |
|||

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral da continuidade do negócio e cenários, consulte a [visão geral da continuidade do Negócio](sql-database-business-continuity.md)
* Para aprender sobre geo-replicação ativa, consulte [a geo-replicação ativa.](sql-database-active-geo-replication.md)
* Para conhecer os grupos de auto-failover, consulte [os grupos de falha automática](sql-database-auto-failover-group.md).
* Para obter informações sobre geo-replicação ativa com piscinas elásticas, consulte estratégias de recuperação de desastres de [piscina elástica.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
