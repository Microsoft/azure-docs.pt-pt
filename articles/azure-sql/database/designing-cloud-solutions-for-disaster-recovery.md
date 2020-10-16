---
title: Design de serviços disponíveis globalmente
description: Saiba mais sobre o design de aplicações para serviços altamente disponíveis utilizando a Base de Dados Azure SQL.
keywords: recuperação de desastres na nuvem,soluções de recuperação de desastres, backup de dados de aplicativos, geo-replicação, planeamento de continuidade de negócios
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: be632ba06edc858e7eadcd6e57a4f7769f69f2cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321684"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Conceber serviços globalmente disponíveis utilizando a Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ao construir e implantar serviços em nuvem com base de dados Azure SQL, utiliza [grupos de geo-replicação ou](active-geo-replication-overview.md) [auto-falha](auto-failover-group-overview.md) para fornecer resiliência a interrupções regionais e falhas catastróficas. A mesma funcionalidade permite criar aplicações distribuídas globalmente otimizadas para acesso local aos dados. Este artigo discute padrões de aplicação comuns, incluindo os benefícios e compensações de cada opção.

> [!NOTE]
> Se estiver a utilizar bases de dados Premium ou Business Critical e piscinas elásticas, pode torná-las resistentes a interrupções regionais, convertendo-as para configuração de implementação redundante de zona. Consulte [bases de dados redundantes da zona.](high-availability-sla.md)  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Utilização de duas regiões do Azure para a continuidade do negócio com tempo de inatividade mínimo

Neste cenário, as aplicações têm as seguintes características:

* A aplicação está ativa numa região do Azure
* Todas as sessões de base de dados requerem leitura e escrita de acesso (RW) a dados
* O nível web e o nível de dados devem ser collocados para reduzir a latência e os custos de tráfego
* Fundamentalmente, o tempo de inatividade é um risco de negócio mais elevado para estas aplicações do que a perda de dados

Neste caso, a topologia de implantação da aplicação é otimizada para lidar com desastres regionais quando todos os componentes da aplicação precisam de falhar em conjunto. O diagrama abaixo mostra esta topologia. Para redundância geográfica, os recursos da aplicação são distribuídos nas regiões A e B. No entanto, os recursos na região B não são utilizados até que a Região A falhe. Um grupo de failover está configurado entre as duas regiões para gerir a conectividade da base de dados, a replicação e o failover. O serviço web em ambas as regiões está configurado para aceder à base de dados através do ** &lt; nome de grupo de failover-database.windows.net &gt; ** (1) do ouvinte de leitura.database.windows.net (1). O Gestor de Tráfego Azure está configurado para utilizar [o método de encaminhamento prioritário](../../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [O Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) é utilizado durante todo este artigo apenas para fins de ilustração. Pode utilizar qualquer solução de equilíbrio de carga que suporte o método de encaminhamento prioritário.

O diagrama a seguir mostra esta configuração antes de uma paragem:

![Cenário 1. Configuração antes da paragem.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma paragem na região primária, a SQL Database deteta que a base de dados primária não é acessível e desencadeia falhas na região secundária com base nos parâmetros da política automática de failover (1). Dependendo da sua aplicação SLA, pode configurar um período de carência que controla o tempo entre a deteção da paragem e a falha em si. É possível que o Azure Traffic Manager inicie a falha do ponto final antes que o grupo de failover desencadeie a falha da base de dados. Nesse caso, a aplicação web não pode voltar imediatamente a ligar-se à base de dados. Mas as reconexões serão automaticamente bem sucedidas assim que a falha da base de dados estiver concluída. Quando a região falhada é restaurada e reativa, a antiga primária reconecta-se automaticamente como um novo secundário. O diagrama abaixo ilustra a configuração após a falha.

> [!NOTE]
> Todas as transações cometidas após a falha são perdidas durante a reconexão. Após a conclusão da falha, a aplicação na região B é capaz de reconectar e reiniciar o processamento dos pedidos do utilizador. Tanto a aplicação web como a base de dados primária estão agora na região B e permanecem co-localizadas.

![Cenário 1. Configuração após falha](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se uma paragem acontecer na região B, o processo de replicação entre a base de dados primária e secundária é suspenso, mas a ligação entre os dois permanece intacta (1). O Gestor de Tráfego deteta que a conectividade com a Região B está quebrada e marca a aplicação web de ponto final 2 como Degradada (2). O desempenho da aplicação não é impactado neste caso, mas a base de dados fica exposta e, portanto, em maior risco de perda de dados na região A falha sucessivamente.

> [!NOTE]
> Para a recuperação de desastres, recomendamos a configuração com implementação de aplicação limitada a duas regiões. Isto porque a maioria das geografias azures têm apenas duas regiões. Esta configuração não protege a sua aplicação de uma falha catastrófica simultânea de ambas as regiões. Em caso improvável de tal falha, pode recuperar as suas bases de dados numa terceira região utilizando a [operação de geo-restauro.](disaster-recovery-guidance.md#recover-using-geo-restore)
>

 Uma vez atenuada a paralisação, a base de dados secundária ressincroniza automaticamente com a primária. Durante a sincronização, o desempenho do primário pode ser impactado. O impacto específico depende da quantidade de dados adquiridos pela nova primária desde o fracasso. 

> [!NOTE]
> Após a interrupção ser atenuada, o Gestor de Tráfego começará a encaminhar as ligações para a aplicação na Região A como um ponto final de prioridade mais elevado. Se pretende manter a primária na Região B por um tempo, deve alterar a tabela de prioridades no perfil do Gestor Trafic em conformidade. 
>
 
 O seguinte diagrama ilustra uma paragem na região secundária:

![Cenário 1. Configuração após uma paragem na região secundária.](./media/designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

As principais **vantagens** deste padrão de design são:

* A mesma aplicação web é implementada em ambas as regiões sem qualquer configuração específica da região e não requer lógica adicional para gerir o failover.
* O desempenho da aplicação não é impactado por falha, uma vez que a aplicação web e a base de dados estão sempre co-localizadas.

A principal **compensação** é que os recursos de aplicação na região B são subutilizados na maior parte do tempo.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: Regiões azure para continuidade empresarial com máxima preservação de dados

Esta opção é mais adequada para aplicações com as seguintes características:

* Qualquer perda de dados é um alto risco de negócio. A falha da base de dados só pode ser utilizada como último recurso se a paralisação for causada por uma falha catastrófica.
* A aplicação suporta modos de operações apenas de leitura e leitura e pode funcionar em "modo apenas de leitura" durante um período de tempo.

Neste padrão, a aplicação muda para o modo apenas de leitura quando as ligações de leitura-escrita começam a obter erros de tempo. A aplicação web é implementada em ambas as regiões e inclui uma ligação ao ponto final do ouvinte de leitura-escrita e ligação diferente ao ponto final do ouvinte apenas de leitura (1). O perfil do Gestor de Tráfego deve utilizar [o encaminhamento prioritário](../../traffic-manager/traffic-manager-configure-priority-routing-method.md). [A monitorização](../../traffic-manager/traffic-manager-monitoring.md) do ponto final deve ser ativada para o ponto final da aplicação em cada região (2).

O diagrama a seguir ilustra esta configuração antes de uma paragem:

![Cenário 2. Configuração antes da paragem.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o Gestor de Tráfego deteta uma falha de conectividade na região A, muda automaticamente o tráfego do utilizador para a instância de aplicação na região B. Com este padrão, é importante que desemalte o período de carência com perda de dados para um valor suficientemente elevado, por exemplo, 24 horas. Garante que a perda de dados é evitada se a paralisação for atenuada dentro desse prazo. Quando a aplicação web na região B é ativada, as operações de leitura-escrita começam a falhar. Nessa altura, deve mudar para o modo de leitura (1). Neste modo, os pedidos são automaticamente encaminhados para a base de dados secundária. Se a paralisação for causada por uma falha catastrófica, é provável que não possa ser atenuada dentro do período de carência. Quando expira, o grupo de failover aciona a falha. Depois disso, o ouvinte de leitura fica disponível e as ligações a ele deixam de falhar (2). O diagrama seguinte ilustra as duas fases do processo de recuperação.

> [!NOTE]
> Se a paralisação na região primária for atenuada dentro do período de carência, o Gestor de Tráfego deteta a restauração da conectividade na região primária e muda o tráfego de utilizadores de volta para a instância de aplicação na região A. Esta instância de aplicação retoma e funciona em modo de leitura-escrita utilizando a base de dados primária na região A, conforme ilustrado pelo diagrama anterior.

![Cenário 2. Fases de recuperação de desastres.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se uma paragem acontecer na região B, o Gestor de Tráfego deteta a falha do ponto final web-app-2 na região B e marca-a degradada (1). Entretanto, o grupo de failover muda o ouvinte apenas de leitura para a região A (2). Esta paralisação não afeta a experiência do utilizador final, mas a base de dados primária está exposta durante a paragem. O seguinte diagrama ilustra uma falha na região secundária:

![Cenário 2. Paralisação da região secundária.](./media/designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Uma vez atenuada a paralisação, a base de dados secundária é imediatamente sincronizada com a primária e o ouvinte apenas de leitura é recíproca para a base de dados secundária na região B. Durante o desempenho da sincronização do primário pode ser ligeiramente impactado dependendo da quantidade de dados que precisa de ser sincronizado.

Este padrão de design tem **várias vantagens:**

* Evita a perda de dados durante as interrupções temporárias.
* O tempo de inatividade depende apenas da rapidez com que o Gestor de Tráfego deteta a falha de conectividade, que é configurável.

A **contrapartida** é que a aplicação deve ser capaz de funcionar apenas em modo de leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: Deslocalização de aplicações para uma geografia diferente sem perda de dados e quase zero tempo de inatividade

Neste cenário, a aplicação tem as seguintes características:

* Os utilizadores finais acedem à aplicação de diferentes geografias
* A aplicação inclui cargas de trabalho apenas de leitura que não dependem da sincronização completa com as últimas atualizações
* O acesso aos dados deve ser suportado na mesma geografia para a maioria dos utilizadores
* Ler latência é fundamental para a experiência do utilizador final

Para satisfazer estes requisitos é necessário garantir que o dispositivo do utilizador se liga **sempre** à aplicação implementada na mesma geografia para as operações de leitura, tais como dados de navegação, análises, etc. Enquanto as operações OLTP são processadas na mesma geografia na **maior parte do tempo.** Por exemplo, durante o dia as operações OLTP são processadas na mesma geografia, mas durante as horas de folga poderiam ser processadas numa geografia diferente. Se a atividade do utilizador final acontecer principalmente durante o horário de trabalho, pode garantir o melhor desempenho para a maioria dos utilizadores na maior parte do tempo. O diagrama que se segue mostra esta topologia.

Os recursos da aplicação devem ser distribuídos em cada geografia onde você tem uma procura de uso substancial. Por exemplo, se a sua aplicação for ativamente utilizada nos Estados Unidos, união europeia e Ásia do Sudeste Asiático, o pedido deve ser implementado em todas estas geografias. A base de dados primária deve ser mudada dinamicamente de uma geografia para a seguinte no final do horário de trabalho. Este método é chamado de "siga o sol". A carga de trabalho OLTP liga-se sempre à base de dados através do ** &lt; nome de grupo de failover-database.windows.net &gt; ** (1) do ouvinte de leitura.database.windows.net (1). A carga de trabalho apenas de leitura liga-se à base de dados local utilizando diretamente o nome do servidor de ponto final do servidor de bases de dados ** &lt; &gt; .database.windows.net** (2). O Gestor de Tráfego está configurado com o [método de encaminhamento de desempenho](../../traffic-manager/traffic-manager-configure-performance-routing-method.md). Garante que o dispositivo do utilizador final está ligado ao serviço web na região mais próxima. O Gestor de Tráfego deve ser configurado com a monitorização do ponto final ativada para cada ponto final do serviço web (3).

> [!NOTE]
> A configuração do grupo failover define qual a região utilizada para o failover. Como a nova primária está numa geografia diferente, o failover resulta em latência mais longa tanto para o OLTP como para as cargas de trabalho só de leitura até que a região impactada esteja novamente on-line.

![Cenário 3. Configuração com primária no Leste dos EUA.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No final do dia, por exemplo, às 23:00 horas locais, as bases de dados ativas deverão ser mudadas para a próxima região (Norte da Europa). Esta tarefa pode ser totalmente automatizada utilizando [aplicações Azure Logic.](../../logic-apps/logic-apps-overview.md) A tarefa envolve os seguintes passos:

* Mude o servidor primário do grupo de failover para o Norte da Europa utilizando o failover amigável (1)
* Remova o grupo de failover entre os EUA e o Norte da Europa
* Criar um novo grupo de falhanços com o mesmo nome, mas entre o Norte da Europa e a Ásia Oriental (2).
* Adicione as primárias no Norte da Europa e secundárias na Ásia Oriental a este grupo de fracasso (3).

O diagrama a seguir ilustra a nova configuração após a falha planeada:

![Cenário 3. Transição das primárias para o Norte da Europa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se uma paralisação acontecer no Norte da Europa, por exemplo, a falha da base de dados automática é iniciada pelo grupo de failover, o que resulta efetivamente na deslocação da aplicação para a região seguinte antes do previsto (1).  Nesse caso, o Leste dos EUA é a única região secundária restante até que o Norte da Europa esteja novamente em linha. As duas regiões restantes servem os clientes nas três geografias através da troca de funções. As Azure Logic Apps têm de ser ajustadas em conformidade. Dado que as restantes regiões obtêm tráfego adicional de utilizadores da Europa, o desempenho da aplicação é impactado não só por latência adicional, mas também por um maior número de ligações ao utilizador final. Uma vez que a paralisação é atenuada no Norte da Europa, a base de dados secundária é imediatamente sincronizada com as primárias atuais. O seguinte diagrama ilustra uma paragem no Norte da Europa:

![Cenário 3. Paralisação no Norte da Europa.](./media/designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Pode reduzir o tempo em que a experiência do utilizador final na Europa é degradada pela longa latência. Para isso, deve implementar proactivamente uma cópia de aplicação e criar a base de dados secundária(s) noutra região local (Europa Ocidental) como uma substituição da instância de aplicação offline no Norte da Europa. Quando este último estiver novamente online, pode decidir se continua a utilizar a Europa Ocidental ou se remove a cópia da aplicação e volta a utilizar o Norte da Europa.

Os principais **benefícios** deste design são:

* A carga de trabalho da aplicação apenas de leitura acede a dados na região dos armários em todos os momentos.
* A carga de trabalho da aplicação de leitura-escrita acede aos dados na região mais próxima durante o período de maior atividade em cada geografia
* Como a aplicação é implementada em várias regiões, pode sobreviver a uma perda de uma das regiões sem qualquer tempo de inatividade significativo.

Mas há **algumas trocas:**

* Uma paralisação regional resulta em que a geografia seja impactada por uma latência mais longa. Tanto as cargas de trabalho de leitura como de leitura são servidas pela aplicação numa geografia diferente.
* As cargas de trabalho apenas de leitura devem ligar-se a um ponto final diferente em cada região.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planeamento de continuidade do negócio: Escolha um projeto de aplicação para recuperação de desastres em nuvem

A sua estratégia específica de recuperação de desastres em nuvem pode combinar ou alargar estes padrões de design para melhor atender às necessidades da sua aplicação.  Como mencionado anteriormente, a estratégia que escolhe baseia-se no SLA que pretende oferecer aos seus clientes e à topologia de implementação de aplicações. Para ajudar a orientar a sua decisão, o quadro que se segue compara as escolhas com base no objetivo do ponto de recuperação (RPO) e no tempo estimado de recuperação (ERT).

| Padrão | RPO | ERT |
|:--- |:--- |:--- |
| Implantação activa-passiva para recuperação de desastres com acesso à base de dados co-localizada |Acesso de leitura-escrita < 5 segundos |Tempo de deteção de falhas + DNS TTL |
| Implantação ativa para equilibrar carga de aplicação |Acesso de leitura-escrita < 5 segundos |Tempo de deteção de falhas + DNS TTL |
| Implantação activa-passiva para a preservação de dados |Acesso apenas de leitura < 5 seg | Acesso apenas para leitura = 0 |
||Acesso de leitura-escrita = zero | Acesso de leitura-escrita = Tempo de deteção de falhas + período de carência com perda de dados |
|||

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral de continuidade do negócio e cenários, consulte [a visão geral da continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* Para saber sobre a geo-replicação ativa, consulte [a geo-replicação ativa.](active-geo-replication-overview.md)
* Para conhecer os grupos de falhas automáticas, consulte [os grupos auto-failover](auto-failover-group-overview.md).
* Para obter informações sobre a geo-replicação ativa com piscinas elásticas, consulte [estratégias elásticas de recuperação de desastres](disaster-recovery-strategies-for-applications-with-elastic-pool.md)na piscina.
