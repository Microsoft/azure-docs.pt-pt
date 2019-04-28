---
title: Desenhe os serviços globalmente disponíveis com a base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre o design do aplicativo para serviços de elevada disponibilidade com a base de dados do Azure SQL.
keywords: cloud de recuperação após desastre, as soluções de recuperação após desastre, cópia de segurança de dados de aplicação, a georreplicação, planejamento da continuidade dos negócios
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 46232afcaf9504d4cfbd80160e2d7e7ea958d600
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488187"
---
# <a name="designing-globally-available-services-using-azure-sql-database"></a>Criando serviços globalmente disponíveis com a base de dados do Azure SQL

Ao criar e implementar serviços em nuvem com a base de dados do Azure SQL, usa [georreplicação ativa](sql-database-active-geo-replication.md) ou [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md) para fornecer maior resiliência às falhas regionais e de falhas catastróficas. A mesma funcionalidade permite-lhe criar aplicações distribuídas globalmente, otimizadas para acesso local aos dados. Este artigo aborda os padrões comuns de aplicação, incluindo as vantagens e desvantagens de cada opção.

> [!NOTE]
> Se estiver a utilizar conjuntos elásticos e bases de dados Premium ou críticas para a empresa, pode torná-los resiliente a falhas regionais convertendo-os em configuração de implementação com redundância de zona. Ver [basesdedadosredundância de zona](sql-database-high-availability.md).  

## <a name="scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime"></a>Cenário 1: Usando duas regiões do Azure para a continuidade do negócio com o tempo de inatividade mínimo

Neste cenário, os aplicativos têm as seguintes características:

* Aplicação está ativa numa única região do Azure
* Todas as sessões de base de dados requerem acesso de escrita (RW) para dados de leitura e
* Camada Web e camada de dados devem ser colocalizados para reduzir o custo de latência e de tráfego
* Fundamentalmente, o tempo de inatividade é um risco mais alto do negócio para estas aplicações de perda de dados

Neste caso, a topologia de implementação de aplicações está otimizada para manipular a desastres regionais quando precisam de todos os componentes da aplicação para ativação pós-falha em conjunto. O diagrama abaixo mostra esta topologia. Para a redundância geográfica, os recursos do aplicativo são implementados para a região A e B. No entanto, os recursos na região B não serão utilizados até que a região A falha. Um grupo de ativação pós-falha está configurado entre as duas regiões para gerir a conectividade da base de dados, replicação e ativação pós-falha. O serviço web em ambas as regiões está configurado para acessar o banco de dados através do serviço de escuta de leitura / escrita  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** (1). O Gestor de tráfego está configurado para utilizar [método de encaminhamento prioritário](../traffic-manager/traffic-manager-configure-priority-routing-method.md) (2).  

> [!NOTE]
> [O Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) ao longo deste artigo, é utilizado apenas para fins ilustrativos. Pode usar qualquer solução de balanceamento de carga de mensagens em fila que suporta o método de encaminhamento de prioridade.

O diagrama seguinte mostra esta configuração antes de uma falha:

![Cenário 1. Configuração antes da falha.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-a.png)

Após uma falha na região primária, o serviço de base de dados SQL Deteta que a base de dados primária não está acessível e aciona a ativação pós-falha para a região secundária com base nos parâmetros da política de ativação pós-falha automática (1). Consoante o SLA de aplicativo, pode configurar um período de tolerância que controla o tempo entre a detecção da falha e a ativação pós-falha em si. É possível que o Gestor de tráfego inicia a ativação pós-falha do ponto final antes do grupo de ativação pós-falha aciona a ativação pós-falha da base de dados. Nesse caso a aplicação web não é imediatamente voltar a ligar à base de dados. Mas os reconnections serão automaticamente bem-sucedidas assim que concluir a ativação pós-falha de base de dados. Quando a região com falha é restaurado e voltar a ficar online, o principal anterior volta ligar automaticamente como um secundário novo. O diagrama abaixo ilustra a configuração após a ativação pós-falha.

> [!NOTE]
> Todas as transações consolidadas após a ativação pós-falha são perdidas durante o restabelecimento. Depois de concluída a ativação pós-falha, a aplicação na região B é capaz de voltar a ligar e reinicie o processamento de pedidos de utilizador. A aplicação web e a base de dados primária estão agora na região B e permanecem localizadas conjuntamente.

![Cenário 1. Configuração após a ativação pós-falha](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-b.png)

Se ocorrer uma falha na região B, o processo de replicação entre a primária e a base de dados secundário obtém suspenso, mas a ligação entre os dois permanece intacta (1). Tráfego gerido Deteta que a conectividade para o B de região está interrompida e marca a aplicação web de ponto final 2 como Degraded (2). Desempenho do aplicativo não é afetado neste caso, mas a base de dados torna-se exposto e, por isso em maior risco de perda de dados em caso de região A falha em sucessão.

> [!NOTE]
> Para recuperação após desastre, recomendamos a configuração com a implementação de aplicação limitada em duas regiões. Isto acontece porque a maioria das áreas geográficas indicadas a do Azure tem apenas duas regiões. Esta configuração não protegem o aplicativo contra uma falha catastrófica simultânea de ambas as regiões. No caso pouco provável de uma falha deste tipo, pode recuperar as bases de dados numa terceira região com [operação de restauro geográfico](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

 Assim que a falha é minimizada, a base de dados secundária resynchronizes-se automaticamente com o principal. Durante a sincronização, o desempenho dos principais pode ser afetado. O impacto específico depende da quantidade de dados a nova principal adquirida desde a ativação pós-falha. O diagrama seguinte ilustra uma falha na região secundária:

![Cenário 1. Configuração após uma falha na região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario1-c.png)

A chave **vantagens** deste padrão de design são:

* O mesmo aplicativo web é implementado para ambas as regiões sem qualquer configuração específico da região e não requer lógica adicional para gerir a ativação pós-falha.
* Desempenho da aplicação não é afetado por ativação pós-falha do aplicativo web e a base de dados sempre são localizadas conjuntamente.

Os principais **compensação** é que os recursos de aplicação na região B estão subutilizados a maior parte do tempo.

## <a name="scenario-2-azure-regions-for-business-continuity-with-maximum-data-preservation"></a>Cenário 2: Regiões do Azure para continuidade do negócio com preservação máxima de dados

Esta opção é mais adequada para aplicações com as seguintes características:

* Qualquer perda de dados é o risco de negócios elevada. A ativação pós-falha de base de dados só pode ser utilizada como um último recurso, se a falha for provocada por uma falha catastrófica.
* O aplicativo oferece suporte a modos de só de leitura e escrita de leitura das operações e pode funcionar no "modo só de leitura" por um período de tempo.

Neste padrão, a aplicação muda para o modo só de leitura quando as conexões de leitura / escrita começar a obter erros de tempo limite. O aplicativo Web é implementada para ambas as regiões e incluem uma ligação para o ponto de extremidade do serviço de escuta de leitura / escrita e de ligação diferente para o ponto de extremidade do serviço de escuta só de leitura (1). Deve utilizar o perfil do Gestor de tráfego [prioridade encaminhamento](../traffic-manager/traffic-manager-configure-priority-routing-method.md). [Monitorização do ponto final](../traffic-manager/traffic-manager-monitoring.md) deve estar ativado para o ponto final da aplicação em cada região (2).

O diagrama seguinte ilustra esta configuração antes de uma falha:

![Cenário 2. Configuração antes da falha.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-a.png)

Quando o Gestor de tráfego Deteta uma falha de conectividade para a região A, ele muda automaticamente o tráfego de utilizador para a instância da aplicação na região B. Com esse padrão, é importante que defina o período de tolerância de perda de dados para um valor suficientemente elevado, por exemplo 24 horas. Ele garante que a perda de dados é impedida se a falha é atenuada nesse período de tempo. Quando a aplicação Web na região B é ativada as operações de leitura / escrita começam a falhar. Nesse ponto, ele deve mudar para o modo só de leitura (1). Neste modo as solicitações são automaticamente encaminhadas para a base de dados secundário. Se a falha for provocada por uma falha catastrófica, provavelmente ele não pode ser atenuado dentro do período de tolerância. Quando expirar os acionadores de grupo de ativação pós-falha a ativação pós-falha. Depois que o serviço de escuta de leitura / escrita torna-se disponíveis e as ligações ao mesmo parar (2) a falhar. O diagrama seguinte ilustra as duas fases do processo de recuperação.

> [!NOTE]
> Se a falha na região primária é atenuada dentro do período de tolerância, Gestor de tráfego Deteta o restauro de conectividade na região primária e muda o tráfego de utilizador para a instância de aplicação na região A. Essa instância de aplicação retoma e funciona em modo de leitura / escrita com a base de dados primária na região A conforme ilustrado o diagrama anterior.

![Cenário 2. Fases de recuperação após desastre.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-b.png)

Se ocorrer uma falha na região B, o Gestor de tráfego detetar a falha de ponto final web-app-2 na região B e marcas de ele degradado (1). Entretanto, o grupo de ativação pós-falha muda a escuta só de leitura para a região A (2). Este período de inatividade não afeta a experiência do utilizador final, mas a base de dados primária está exposta durante o período de inatividade. O diagrama seguinte ilustra uma falha na região secundária:

![Cenário 2. Falha da região secundária.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario2-c.png)

Assim que a falha é minimizada, a base de dados secundária imediatamente está sincronizado com o principal e o serviço de escuta só de leitura é alternado de volta para a base de dados secundária numa região B. Durante a sincronização de desempenho dos principais pode ser afetado ligeiramente dependendo da quantidade de dados que precisam de ser sincronizados.

Este padrão de design possui vários **vantagens**:

* Ele evita a perda de dados durante as falhas temporárias.
* Tempo de inatividade depende apenas com o Gestor de tráfego detetar a falha de conectividade, o que é configurável.

O **compensação** é que o aplicativo deve ser capaz de operar em modo só de leitura.

## <a name="scenario-3-application-relocation-to-a-different-geography-without-data-loss-and-near-zero-downtime"></a>Cenário 3: Reposicionamento de aplicação para uma geografia diferente sem perda de dados e quase nenhum tempo de inatividade

Neste cenário, o aplicativo tem as seguintes características:

* Os usuários finais aceder à aplicação a partir de diferentes geografias
* O aplicativo inclui as cargas de trabalho só de leitura que não dependem de uma sincronização completa com as atualizações mais recentes
* Acesso de escrita aos dados deve ser suportado na mesma área geográfica para a maioria dos utilizadores
* Latência de leitura é essencial para a experiência do utilizador final

Para atender a esses requisitos terá de garantir que o dispositivo de usuário **sempre** liga-se para a aplicação implementada na mesma área geográfica para as operações só de leitura, por exemplo, dados de navegação, análise, etc. Ao passo que as operações de OLTP são processadas na mesma geografia **maior parte do tempo**. Por exemplo, durante a hora do dia, operações de OLTP são processadas na mesma região, mas durante as horas off foi processar de uma localização geográfica de diferente. Se a atividade do utilizador final principalmente acontece durante o horário de trabalho, pode garantir o desempenho ideal para a maioria dos usuários maior parte do tempo. O diagrama seguinte mostra esta topologia.

Os recursos do aplicativo devem ser implementados em cada geografia em que tem a pedido de uma utilização significativa. Por exemplo, se a sua aplicação é utilizada ativamente nos Estados Unidos, União Europeia e Sudeste asiático, o aplicativo devem ser implementados para todas essas áreas geográficas. A base de dados principal deve ser alternado dinamicamente de uma geografia para a próxima no fim do horário de trabalho. Este método é chamado "seguir o sol". A carga de trabalho OLTP liga-se sempre à base de dados através do serviço de escuta de leitura / escrita  **&lt;nome do grupo de ativação pós-falha&gt;. database.windows.net** (1). A carga de trabalho só de leitura se liga a base de dados local diretamente com o ponto de final do servidor de bases de dados  **&lt;nome do servidor&gt;. database.windows.net** (2). O Gestor de tráfego está configurado com o [método de encaminhamento de desempenho](../traffic-manager/traffic-manager-configure-performance-routing-method.md). Ele garante que o dispositivo do utilizador final está ligado ao serviço web na região mais próxima. O Gestor de tráfego deve ser configurado com a monitorização do ponto final ativado para cada ponto de final de serviço web (3).

> [!NOTE]
> A configuração do grupo de ativação pós-falha define qual a região é utilizada para a ativação pós-falha. Uma vez que a nova principal é de uma localização geográfica de diferente os resultados de ativação pós-falha de latência de mais tempo para OLTP e cargas de trabalho só de leitura até que a região afetada esteja novamente online.

![Cenário 3. Configuração com principal na região E.U.A. Leste.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-a.png)

No final do dia (por exemplo, na hora local do 23 horas), bases de dados do Active Directory devem ser mudados para a próxima região (Europa do Norte). Esta tarefa pode ser totalmente automatizada através de [do Azure de serviço de agendamento](../scheduler/scheduler-intro.md).  A tarefa envolve os seguintes passos:

* Mudar o servidor primário no grupo de ativação pós-falha para Europa do Norte usando a ativação pós-falha amigável (1)
* Remover o grupo de ativação pós-falha entre os E.U.A. leste e Europa do Norte
* Crie um novo grupo de ativação pós-falha com o mesmo nome mas entre Europa do Norte e Ásia Oriental (2).
* Adicione principal na Europa do Norte e na Ásia Oriental secundário a este grupo de ativação pós-falha (3).

O diagrama seguinte ilustra a nova configuração após a ativação pós-falha planeada:

![Cenário 3. Transição da primária para a Europa do Norte.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-b.png)

Se um período de indisponibilidade ocorre na Europa do Norte por exemplo, a ativação pós-falha automática da base de dados é iniciada pelo grupo de ativação pós-falha, o que resulta efetivamente em mover o aplicativo para a região seguinte à frente do cronograma (1).  Nesse caso o E.U. a leste é a região secundária restante até que a Europa do Norte esteja novamente online. As duas regiões restantes servem os clientes em todas as três geografias alternando funções. O Azure scheduler tem de ser ajustado em conformidade. Uma vez que as regiões restantes receber tráfego de utilizador adicionais da Europa, do desempenho do aplicativo é afetado não apenas pela latência adicional, mas também por um número maior de ligações de utilizador final. Assim que a indisponibilidade for minimizada na Europa do Norte, a base de dados secundária lá imediatamente é sincronizada com o principal atual. O diagrama seguinte ilustra uma falha na Europa do Norte:

![Cenário 3. Falha na Europa do Norte.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/scenario3-c.png)

> [!NOTE]
> Pode reduzir o tempo quando a experiência do utilizador final na Europa está degradada pela latência longo. Para fazer isso, deve proativamente implementar uma cópia do aplicativo e criar bases de dados secundárias noutra região local (Europa Ocidental) como uma substituição da instância da aplicação offline na Europa do Norte. Quando a última opção é voltar a ficar online pode decidir se pretende continuar a utilizar a Europa ocidental ou remova a cópia da aplicação lá e mudar para o Norte da Europa.

A chave **benefícios** deste design são:

* A carga de trabalho de aplicação só de leitura acessa dados na região armários durante todo o tempo.
* A carga de trabalho de aplicação de leitura / escrita aceda aos dados na região mais próxima durante o período da atividade mais elevado de cada localização geográfica
* Uma vez que a aplicação é implementada em várias regiões, pode sobreviver uma perda de uma das regiões sem qualquer período de indisponibilidade significativo.

No entanto, existem algumas **compensações**:

* Uma falha regional resulta na geografia a ser afetado pela latência de mais tempo. Cargas de trabalho de leitura / escrita e só de leitura são servidos pela aplicação de uma localização geográfica de diferente.
* As cargas de trabalho só de leitura tem de ligar para um ponto de extremidade diferente em cada região.

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planejamento de continuidade de negócios: Escolha de design de um aplicativo para recuperação de desastre na nuvem

Sua estratégia de recuperação de desastres de cloud específico pode combinar ou expandir estes padrões de design para melhor satisfazer as necessidades da sua aplicação.  Como mencionado anteriormente, a estratégia que escolher baseia-se no SLA que pretende para oferecer aos seus clientes e a topologia de implementação de aplicação. Para ajudar a orientar a sua decisão, a tabela seguinte compara as opções com base no objetivo de ponto de recuperação (RPO) e a hora da recuperação estimada (ERT).

| Padrão | RPO: | ERT |
|:--- |:--- |:--- |
| Implementação ativa-passiva para recuperação após desastre com acesso de base de dados localizadas conjuntamente |Acesso de leitura-escrita < 5 s |Hora da deteção de falha + TTL de DNS |
| Implementação de ativo-ativo para o balanceamento de carga do aplicativo |Acesso de leitura-escrita < 5 s |Hora da deteção de falha + TTL de DNS |
| Implementação ativa-passiva para preservação de dados |Acesso só de leitura < 5 s | Acesso só de leitura = 0 |
||Acesso de leitura-escrita = zero | Acesso de leitura-escrita = hora da deteção de falha + período de tolerância de perda de dados |
|||

## <a name="next-steps"></a>Passos Seguintes

* Para uma visão geral de continuidade de negócio e cenários, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md)
* Para saber mais sobre a georreplicação ativa, veja [georreplicação ativa](sql-database-active-geo-replication.md).
* Para saber mais sobre grupos de ativação pós-falha automática, consulte [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md).
* Para obter informações sobre a georreplicação ativa com os conjuntos elásticos, veja [estratégias de recuperação de desastre do conjunto elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
