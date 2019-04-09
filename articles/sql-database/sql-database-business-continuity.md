---
title: Continuidade empresarial na nuvem - recuperação de base de dados - Base de Dados SQL | Microsoft Docs
description: Saiba como a Base de Dados SQL do Azure suporta a continuidade empresarial na nuvem e a recuperação de base de dados, e ajuda a manter as aplicações fundamentais na nuvem em execução.
keywords: continuidade empresarial, continuidade empresarial na nuvem, recuperação de base de dados após desastre, recuperação de base de dados
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/04/2019
ms.openlocfilehash: dfa5d4cb2d782f1466329300157a64fd17765460
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057171"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure

**Continuidade do negócio** na base de dados do Azure SQL refere-se para os mecanismos, políticas e procedimentos que permitem a continuar a funcionar em caso de interrupção, especialmente para a sua infraestrutura de computação em sua empresa. Em que a maioria dos casos, a base de dados do Azure SQL irá processar os eventos disruptivos que podem ocorrer no ambiente de cloud e manter os seus aplicativos e processos de negócios em execução. No entanto, existem alguns eventos disruptivos que não podem ser processados pela base de dados SQL, tais como:

- Utilizador acidentalmente eliminou ou atualizou uma linha numa tabela.
- Invasor mal-intencionado foi concluída com êxito para eliminar dados ou remover bases de dados.
- Sismo causado uma queda de energia e temporário Centro de dados desativado.

Nestes casos não podem ser controlados pela base de dados SQL do Azure, por isso terá de utilizar as funcionalidades de continuidade de negócio na base de dados SQL permite-lhe recuperar os seus dados e manter as suas aplicações em execução.

Esta descrição geral descreve as capacidades que a Base de Dados SQL do Azure fornece para a continuidade empresarial e a recuperação após desastre. Saiba mais sobre as opções, recomendações e tutoriais para recuperar de eventos disruptivos que podem causar perda de dados ou fazer com que a sua base de dados e a aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure fica indisponível ou a aplicação requerer manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial

Da perspectiva da base de dados, existem quatro principais cenários de interrupções potenciais:

- Locais de hardware ou software falhas que afetam o nó de base de dados como uma falha de unidade de disco.
- Danos em dados ou eliminação habitualmente causado por um bug de aplicação ou o erro humano. Tais falhas são intrinsecamente específico do aplicativo e não não como uma regra ser detetada ou mitigada automaticamente a infraestrutura.
- Falha do Datacenter, possivelmente causada por um desastre natural. Este cenário requer algum nível de redundância geográfica com o failover de aplicativos para um datacenter alternativo.
- Erros de atualização ou de manutenção, planejado de problemas imprevistos que ocorrem durante as atualizações ou manutenção para uma aplicação ou a base de dados pode exigir a reversão rápida para um estado anterior da base de dados.

Base de dados SQL fornece várias funcionalidades de continuidade de negócio, incluindo cópias de segurança automáticas e os replicação de base de dados opcional capazes de atenuar esses cenários. Em primeiro lugar, precisa entender como base de dados SQL [arquitetura de elevada disponibilidade](sql-database-high-availability.md) fornece 99,99% de disponibilidade e resiliência a alguns eventos disruptivos que podem afetar o processo de negócios.
Em seguida, pode aprender sobre os mecanismos adicionais que pode utilizar para recuperar a partir de eventos disruptivos que não podem ser processados pela arquitetura de elevada disponibilidade da base de dados SQL, tais como:

- [Tabelas temporais](sql-database-temporal-tables.md) permitem-lhe restaurar versões de linha a partir de qualquer ponto no tempo.
- [Incorporado de cópias de segurança automatizadas](sql-database-automated-backups.md) e [um ponto anterior no tempo de restauro](sql-database-recovery-using-backups.md#point-in-time-restore) permite-lhe restaurar o banco de dados completo para algum ponto no tempo nos últimos 35 dias.
- Pode [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que tiver sido eliminado se a **servidor de base de dados SQL não foi eliminada**.
- [Retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md) permite-lhe acompanhar as cópias de segurança para os 10 anos.
- [Replicação geográfica activa](sql-database-active-geo-replication.md) permite-lhe criar réplicas legíveis e manualmente a ativação pós-falha para qualquer réplica em caso de uma atualização de aplicação ou de indisponibilidade da Centro de dados.
- [Grupo de ativação pós-falha automática](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo automaticamente a recuperação em caso de uma indisponibilidade do Centro de dados.

Cada uma possui características diferentes para o tempo de recuperação estimado (ERT) e a potencial perda de dados de transações recentes. Assim que compreender estas opções, pode escolher entre elas - e, na maioria dos cenários, utilizá-las em conjunto para cenários diferentes. Desenvolver o seu plano de continuidade do negócio, precisa entender o tempo máximo aceitável antes da aplicação recuperar totalmente após o evento problemático. O tempo necessário para a aplicação recuperar totalmente é conhecido como o objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de Atualizações recentes de dados (intervalo de tempo) da aplicação pode tolerar perder ao recuperar após o evento problemático. O período de tempo de atualizações que poderá estar a perder é conhecido como o objetivo de ponto de recuperação (RPO).

A tabela seguinte compara o ERT e o RPO para cada camada de serviço para os cenários mais comuns.

| Capacidade | Básica | Standard | Premium | Fins Gerais | Crítico para a Empresa
| --- | --- | --- | --- |--- |--- |
| Restauro para um Ponto Anterior no Tempo a partir de cópia de segurança |Qualquer ponto de restauro dentro de sete dias |Qualquer ponto de restauro num período de 35 dias |Qualquer ponto de restauro num período de 35 dias |Qualquer ponto de restauro no período configurado (até 35 dias)|Qualquer ponto de restauro no período configurado (até 35 dias)|
| Georrestauro a partir de cópias de segurança georreplicado |ERT < 12 h<br> RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h |ERT < 12 h<br>RPO < 1 h|ERT < 12 h<br>RPO < 1 h|
| Grupos de ativação pós-falha automática |RTO = 1 h<br>RPO < 5s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s |RTO = 1 h<br>RPO < 5 s|RTO = 1 h<br>RPO < 5 s|
| Ativação pós-falha da base de dados manual |ERT = 30 s<br>RPO < 5s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s |ERT = 30 s<br>RPO < 5 s|ERT = 30 s<br>RPO < 5 s|

> [!NOTE]
> *Ativação pós-falha da base de dados manual* refere-se a ativação pós-falha de uma base de dados para seu georreplicado secundário utilizando o [modo não planeado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).

## <a name="recover-a-database-to-the-existing-server"></a>Recuperar uma base de dados para o servidor existente

Base de dados SQL efetua automaticamente uma combinação de cópias de segurança da base de dados completa semanalmente, geralmente adotam a cada 12 horas, cópias de segurança da base de dados diferenciais e backups de log a cada 5 a 10 minutos para proteger a sua empresa contra perda de dados de transações. As cópias de segurança são armazenadas no armazenamento RA-GRS durante 35 dias para todos os escalões de serviço, exceto os escalões de serviço básico DTU onde as cópias de segurança são armazenadas durante sete dias. Para obter mais informações, consulte [cópias de segurança da base de dados automática](sql-database-automated-backups.md). Pode restaurar um formulário de base de dados existente as cópias de segurança automáticas para um ponto anterior no tempo como uma nova base de dados no mesmo servidor de base de dados SQL com o portal do Azure, PowerShell ou a API REST. Para obter mais informações, consulte [restaurodepontonotempoda](sql-database-recovery-using-backups.md#point-in-time-restore).

Se o máximo suportado ponto anterior no tempo restaurar (PITR), o período de retenção não é suficiente para a sua aplicação, pode expandi-lo ao configurar uma política de retenção (LTR) de longo prazo para as bases de dados. Para obter mais informações, consulte [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md).

Pode utilizar estas cópias de segurança automáticas da base de dados para recuperar uma base de dados após vários eventos problemáticos, tanto no seu centro de dados como para outro centro de dados. O tempo de recuperação é normalmente inferior a 12 horas. Pode demorar mais tempo para recuperar uma base de dados muito grande ou Active Directory. Ao utilizar cópias de segurança automáticas de bases de dados, o tempo estimado de recuperação depende de vários fatores, incluindo o número total de bases de dados a recuperar na mesma região ao mesmo tempo, o tamanho da base de dados, o tamanho do registo de transações e a largura de banda de rede. Para obter mais informações sobre o tempo de recuperação, consulte [hora da recuperação de base de dados](sql-database-recovery-using-backups.md#recovery-time). Ao recuperar para outra região de dados, a potencial perda de dados está limitada a 1 hora, com uma utilização de cópias de segurança georredundante.

Utilizar cópias de segurança automáticas e [restauro de ponto no tempo](sql-database-recovery-using-backups.md#point-in-time-restore) como o mecanismo de recuperação e continuidade de negócio se seu aplicativo:

- Não for considerada fundamental para a atividade crítica.
- Não tem um SLA de enlace - um período de indisponibilidade de 24 horas ou já não resulta em encargos financeiros.
- Tiver uma taxa reduzida de alteração de dados (poucas transações por hora) e perder até uma hora de alterações é uma perda de dados aceitável.
- For sensível aos custos.

Se precisar de uma recuperação mais rápida, utilize [georreplicação ativa](sql-database-active-geo-replication.md) ou [grupos de ativação pós-falha automática](sql-database-auto-failover-group.md). Se tiver de ser capaz de recuperar dados de um período com mais de 35 dias, utilize [retenção a longo prazo](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region"></a>Recuperar uma base de dados para outra região

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas.

- Uma opção é aguardar que a base de dados volte a ficar online quando a indisponibilidade do centro de dados terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um centro de dados fica indisponível, não sabe quanto pode durar a indisponibilidade, pelo que esta opção só funciona se não precisar de sua base de dados durante algum tempo.
- Outra opção consiste em restaurar uma base de dados em qualquer servidor em qualquer região do Azure, utilizando [cópias de segurança da base de dados georredundante](sql-database-recovery-using-backups.md#geo-restore) (georrestauro). O restauro geográfico utiliza uma cópia de segurança georredundante como origem e pode ser usado para recuperar uma base de dados, mesmo que a base de dados ou o Centro de dados não está acessível devido a uma falha.
- Por fim, pode recuperar rapidamente a partir de uma falha se tiver configurado a geo-secundária usando [georreplicação ativa](sql-database-active-geo-replication.md) ou uma [grupo de ativação pós-falha automática](sql-database-auto-failover-group.md) para a sua base de dados ou bases de dados. Dependendo de sua escolha dessas tecnologias, pode usar a ativação pós-falha manual ou automática. Embora a ativação pós-falha em si demora apenas alguns segundos, o serviço irá demorar, pelo menos, 1 hora para ativá-lo. Isso é necessário para se certificar de que a ativação pós-falha é justificada pela escala da falha. Além disso, a ativação pós-falha pode resultar em perda de dados pequeno devido à natureza da replicação assíncrona. Consulte a tabela apresentada anteriormente neste artigo para obter detalhes da ativação pós-falha automática RTO e RPO.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> Para utilizar a georreplicação ativa e grupos de ativação pós-falha automática, tem de ser o proprietário da subscrição ou ter permissões administrativas no SQL Server. Pode configurar e efetuar a ativação pós-falha utilizando o Azure portal, PowerShell ou a API de REST com permissões de subscrição do Azure ou com o Transact-SQL com permissões do SQL Server.

Utilize grupos de ativação pós-falha automática se a sua aplicação cumprir qualquer um dos seguintes critérios:

- É fundamental para a atividade crítica.
- Tem um contrato de nível de serviço (SLA) que não permite para 12 horas ou mais de tempo de inatividade.
- Tempo de inatividade pode resultar em encargos financeiros.
- Tem uma taxa elevada de dados, alterar e 1 hora de perda de dados não é aceitável.
- O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

Quando tomar medidas, o período de tempo que demora a recuperar e a perda de dados incorrida depende de como optar por utilizar estas funcionalidades de continuidade de negócio na sua aplicação. Na verdade, pode optar por utilizar uma combinação de cópias de segurança da base de dados e a georreplicação ativa consoante os requisitos da aplicação. Para ver um debate das considerações de design do aplicativo para bases de dados autónomas e para conjuntos elásticos com estas funcionalidades de continuidade de negócio, veja [estruturar uma aplicação para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [elástico estratégias de recuperação após desastre do conjunto](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).


As secções seguintes fornecem uma visão geral dos passos para recuperar através de cópias de segurança da base de dados ou a georreplicação ativa. Para passos detalhados, incluindo o planejamento de requisitos, passos após recuperação e informações sobre como simular uma falha para executar um teste de recuperação após desastre, veja [recuperar uma base de dados SQL a partir de um período de indisponibilidade](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma indisponibilidade

Independentemente da funcionalidade de continuidade empresarial utilizada, tem de:

- Identificar e preparar o servidor de destino, incluindo regras de firewall do IP ao nível do servidor, inícios de sessão e permissões ao nível da base de dados mestra.
- Determinar como redirecionar os clientes e as aplicações de cliente para o novo servidor
- Documentar outras dependências, tais como definições e alertas de auditoria

Se não preparar corretamente, colocar as aplicações online após uma ativação pós-falha ou uma recuperação de base de dados demora mais tempo e provavelmente também exigem solução de problemas numa altura tensa - uma má combinação.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Ativação pós-falha para um georreplicada secundária da base de dados

Se estiver a utilizar replicação geográfica activa ou grupos de ativação pós-falha automática como mecanismo de recuperação, pode configurar uma política de ativação pós-falha automática ou utilizar [a ativação pós-falha manual](sql-database-active-geo-replication-portal.md#initiate-a-failover). Uma vez iniciada, a ativação pós-falha faz com que o secundário para a primária e está preparado para registar novas transações e responder a consultas - com perda mínima de dados para os dados ainda não replicadas. Para informações sobre como estruturar o processo de ativação pós-falha, consulte [estruturar uma aplicação para recuperação de desastres em nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o Centro de dados online novamente as cores primárias antigas restabelecer a ligação para a nova principal automaticamente e tornar-se do bancos de dados secundários. Se precisar de alteração da localização a cópia principal para a região original, pode iniciar manualmente uma ativação pós-falha planeada (reativação pós-falha).

### <a name="perform-a-geo-restore"></a>Efetuar um georrestauro

Se estiver a utilizar as cópias de segurança automatizadas com o armazenamento georredundante (ativado por predefinição), pode recuperar a base de dados com [georrestauro](sql-database-disaster-recovery.md#recover-using-geo-restore). Recuperação têm normalmente decorre no prazo de 12 horas - com perda de dados de até uma hora, determinada por quando a última cópia de segurança do registo foi executada e replicada. Até concluir a recuperação, a base de dados não consegue registar quaisquer transações nem responder a consultas. Tenha em atenção de que o restauro geográfico restaura apenas a base de dados para o último ponto disponível no tempo.

> [!NOTE]
> Se o Centro de dados estiver online novamente antes de passar seu aplicativo para a base de dados recuperada, pode cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Efetuar a ativação pós-falha/tarefas de recuperação

Após a recuperação a partir de qualquer mecanismo de recuperação, tem de efetuar as seguintes tarefas adicionais antes de os seus utilizadores e aplicações ficarem funcionais:

- Redirecionar os clientes e as aplicações de cliente para o novo servidor e base de dados restaurada
- Certifique-se de regras de firewall IP ao nível do servidor adequadas estão em vigor para os utilizadores a ligar ou usar [firewalls ao nível da base de dados](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) para ativar as regras adequadas.
- Certificar-se de que estão implementados inícios de sessão e permissões ao nível da base de dados mestra no local adequados (ou utilizar [utilizadores contidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurar auditorias, conforme adequado
- Configurar alertas, conforme adequado

> [!NOTE]
> Se estiver a utilizar um grupo de ativação pós-falha e ligar às bases de dados usando o lstener de leitura / escrita, o redirecionamento após a ativação pós-falha acontecerá automática e transparente com a aplicação.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com um período de indisponibilidade mínimo

Por vezes, um aplicativo deve ser colocado offline devido a manutenção planeada, como uma atualização da aplicação. [Gerir atualizações de aplicações](sql-database-manage-application-rolling-upgrade.md) descreve como utilizar a georreplicação ativa para permitir a implementação de atualizações da sua aplicação na cloud para minimizar o período de indisponibilidade durante as atualizações e fornecer um caminho de recuperação caso algo corra mal.

## <a name="next-steps"></a>Passos Seguintes

Para ver um debate das considerações de design do aplicativo para bases de dados autónomas e para conjuntos elásticos, veja [estruturar uma aplicação para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastre do conjunto elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
