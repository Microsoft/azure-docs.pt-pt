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
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442041"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure

**Continuidade do negócio** na base de dados do Azure SQL refere-se para os mecanismos, políticas e procedimentos que permitem a continuar a funcionar em caso de interrupção, especialmente para a sua infraestrutura de computação em sua empresa. Em que a maioria dos casos, a base de dados do Azure SQL irá processar os eventos disruptivos que podem ocorrer no ambiente de cloud e manter os seus aplicativos e processos de negócios em execução. No entanto, existem alguns eventos disruptivos que não podem ser processados pela base de dados SQL automaticamente, tais como:

- Utilizador acidentalmente eliminou ou atualizou uma linha numa tabela.
- Invasor mal-intencionado foi concluída com êxito para eliminar dados ou remover bases de dados.
- Sismo causado uma queda de energia e temporário Centro de dados desativado.

Esta descrição geral descreve as capacidades que a Base de Dados SQL do Azure fornece para a continuidade empresarial e a recuperação após desastre. Saiba mais sobre as opções, recomendações e tutoriais para recuperar de eventos disruptivos que podem causar perda de dados ou fazer com que a sua base de dados e a aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure fica indisponível ou a aplicação requerer manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial

Da perspectiva da base de dados, existem quatro principais cenários de interrupções potenciais:

- Locais de hardware ou software falhas que afetam o nó de base de dados como uma falha de unidade de disco.
- Danos em dados ou eliminação habitualmente causado por um bug de aplicação ou o erro humano. Tais falhas são específicas da aplicação e, normalmente, não não possível detetar o serviço de base de dados.
- Falha do Datacenter, possivelmente causada por um desastre natural. Este cenário requer algum nível de redundância geográfica com o failover de aplicativos para um datacenter alternativo.
- Erros de atualização ou de manutenção, problemas imprevistos que ocorrem durante a infraestrutura a manutenção planeada ou as atualizações podem exigir a reversão rápida para um estado anterior da base de dados.

Para mitigar o local hardware e falhas de software, base de dados SQL inclui um [arquitetura de elevada disponibilidade](sql-database-high-availability.md), que garante a recuperação automática dessas falhas com até 99,995% SLA de disponibilidade.  

Para proteger a sua empresa contra perda de dados, base de dados SQL automaticamente cria cópias de segurança da base de dados completa semanal, diferencial cópias de segurança a cada 12 horas e transação backups de log a cada 5 - 10 minutos. As cópias de segurança são armazenadas no armazenamento RA-GRS durante, pelo menos, sete dias para todos os escalões de serviço. Todos os escalões de serviço, exceto básico suportam período de retenção de cópia de segurança configuráveis para ponto anterior no tempo de restauro, até 35 dias. 

Base de dados SQL também fornece várias funcionalidades de continuidade de negócio, que pode utilizar para atenuar os vários cenários não planeados. 

- As [tabelas temporais](sql-database-temporal-tables.md) permitem-lhe restaurar as versões de linhas a partir de qualquer ponto no tempo.
- [Incorporado de cópias de segurança automatizadas](sql-database-automated-backups.md) e [um ponto anterior no tempo de restauro](sql-database-recovery-using-backups.md#point-in-time-restore) permite-lhe de restauro da base de dados completa para algum ponto no tempo dentro do período de retenção configurado até 35 dias.
- Pode [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que tiver sido eliminado se a **servidor de base de dados SQL não foi eliminada**.
- [Retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md) permite-lhe acompanhar as cópias de segurança para os 10 anos.
- [Replicação geográfica activa](sql-database-active-geo-replication.md) permite-lhe criar réplicas legíveis e manualmente a ativação pós-falha para qualquer réplica em caso de uma atualização de aplicação ou de indisponibilidade da Centro de dados.
- [Grupo de ativação pós-falha automática](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo automaticamente a recuperação em caso de uma indisponibilidade do Centro de dados.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar uma base de dados dentro da mesma região do Azure

Pode utilizar cópias de segurança automáticas da base de dados para restaurar uma base de dados para um ponto anterior no tempo no passado. Desta forma pode recuperar da Corrupção de dados causadas por erros humanos. O restauro de ponto anterior no tempo permite-lhe criar uma nova base de dados no mesmo servidor que representa o estado dos dados antes do evento corrupting. Para a maioria das bases de dados as operações de restauração leva menos de 12 horas. Pode demorar mais tempo para recuperar uma base de dados muito grande ou muito ativo. Para obter mais informações sobre o tempo de recuperação, consulte [hora da recuperação de base de dados](sql-database-recovery-using-backups.md#recovery-time). 

Se o período de retenção de cópia de segurança máxima suportada para o restauro de ponto no tempo (PITR) não é suficiente para a sua aplicação, pode estendê-lo ao configurar uma política de retenção (LTR) de longo prazo para as bases de dados. Para obter mais informações, consulte [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Recuperar uma base de dados para outra região do Azure

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas.

- Uma opção é aguardar que a base de dados volte a ficar online quando a indisponibilidade do centro de dados terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um centro de dados fica indisponível, não sabe quanto pode durar a indisponibilidade, pelo que esta opção só funciona se não precisar de sua base de dados durante algum tempo.
- Outra opção consiste em restaurar uma base de dados em qualquer servidor em qualquer região do Azure, utilizando [cópias de segurança da base de dados georredundante](sql-database-recovery-using-backups.md#geo-restore) (georrestauro). O restauro geográfico utiliza uma cópia de segurança georredundante como origem e pode ser usado para recuperar uma base de dados, mesmo que a base de dados ou o Centro de dados não está acessível devido a uma falha.
- Por fim, pode recuperar rapidamente a partir de uma falha se tiver configurado a geo-secundária usando [georreplicação ativa](sql-database-active-geo-replication.md) ou uma [grupo de ativação pós-falha automática](sql-database-auto-failover-group.md) para a sua base de dados ou bases de dados. Dependendo de sua escolha dessas tecnologias, pode usar a ativação pós-falha manual ou automática. Embora a ativação pós-falha em si demora apenas alguns segundos, o serviço irá demorar, pelo menos, 1 hora para ativá-lo. Isso é necessário para se certificar de que a ativação pós-falha é justificada pela escala da falha. Além disso, a ativação pós-falha pode resultar em perda de dados pequeno devido à natureza da replicação assíncrona. 

À medida que elabora o seu plano de continuidade empresarial, tem de saber qual o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento problemático. O tempo necessário para a aplicação recuperar totalmente é conhecido como o objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de Atualizações recentes de dados (intervalo de tempo) da aplicação pode tolerar perder ao recuperar a partir de um evento de interrupção não planeado. A potencial perda de dados é conhecida como o objetivo de ponto de recuperação (RPO).

Métodos de recuperação diferentes oferecem diferentes níveis de RPO e RTO. Pode escolher um método de recuperação específico, ou usar uma combinação de métodos para achicethe a recuperação de completo da aplicação. A tabela seguinte compara o RPO e RTO de cada opção de recuperação.

| Método de recuperação | RTO | RPO |
| --- | --- | --- | 
| Georrestauro a partir de cópias de segurança georreplicado | 12 h | 1 h |
| Grupos de ativação pós-falha automática | 1 h | 5 s |
| Ativação pós-falha da base de dados manual | 30 s | 5 s |

> [!NOTE]
> *Ativação pós-falha da base de dados manual* refere-se a ativação pós-falha de uma base de dados para seu georreplicado secundário utilizando o [modo não planeado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela apresentada anteriormente neste artigo para obter detalhes da ativação pós-falha automática RTO e RPO.


Utilize grupos de ativação pós-falha automática se a sua aplicação cumprir qualquer um dos seguintes critérios:

- É fundamental para a atividade crítica.
- Tem um contrato de nível de serviço (SLA) que não permite para 12 horas ou mais de tempo de inatividade.
- Tempo de inatividade pode resultar em encargos financeiros.
- Tem uma taxa elevada de dados, alterar e 1 hora de perda de dados não é aceitável.
- O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Pode optar por utilizar uma combinação de cópias de segurança da base de dados e a georreplicação ativa consoante os requisitos da aplicação. Para ver um debate das considerações de design para bases de dados autónomas e para conjuntos elásticos com estas funcionalidades de continuidade de negócio, veja [estruturar uma aplicação para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [após desastre do conjunto elástico estratégias de recuperação](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

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
