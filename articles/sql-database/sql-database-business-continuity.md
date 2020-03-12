---
title: Continuidade do negócio da nuvem - recuperação da base de dados
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
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096863"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure

**A continuidade** do negócio na Base de Dados Azure SQL refere-se aos mecanismos, políticas e procedimentos que permitem ao seu negócio continuar a operar face à perturbação, particularmente à sua infraestrutura de computação. Na maioria dos casos, a Azure SQL Database irá lidar com os eventos disruptivos que podem ocorrer no ambiente da nuvem e manter as suas aplicações e processos de negócio em funcionamento. No entanto, existem alguns eventos disruptivos que não podem ser tratados automaticamente pela Base de Dados SQL, tais como:

- O utilizador acidentalmente apagou ou atualizou uma linha numa tabela.
- O intruso malicioso conseguiu eliminar dados ou deixar cair uma base de dados.
- O terramoto provocou uma falha de energia e um centro de dados temporário.

Esta descrição geral descreve as capacidades que a Base de Dados SQL do Azure fornece para a continuidade empresarial e a recuperação após desastre. Saiba mais sobre opções, recomendações e tutoriais para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um erro de utilizador ou aplicação afeta a integridade dos dados, uma região do Azure tem uma falha, ou a sua aplicação requer manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial

Do ponto de vista da base de dados, existem quatro grandes cenários de perturbação potencial:

- Falhas de hardware ou software locais que afetam o nó de base de dados, como uma falha de acionamento de disco.
- Corrupção de dados ou eliminação tipicamente causada por um bug de aplicação ou erro humano. Tais falhas são específicas da aplicação e normalmente não podem ser detetadas pelo serviço de base de dados.
- Paragem no datacenter, possivelmente causada por um desastre natural. Este cenário requer algum nível de georedundância com falha de aplicação para um centro de dados alternativo.
- Erros de atualização ou manutenção, problemas inesperados que ocorram durante a manutenção ou atualizações planeadas da infraestrutura podem exigir uma rápida reversão para um estado de base de dados anterior.

Para mitigar as falhas de hardware e software locais, a SQL Database inclui uma arquitetura de [alta disponibilidade,](sql-database-high-availability.md)que garante a recuperação automática destas falhas com até 99,995% de disponibilidade SLA.  

Para proteger o seu negócio da perda de dados, a Base de Dados SQL cria automaticamente cópias de dados completas semanais, backups de bases de dados diferenciais a cada 12 horas, e cópias de segurança de registo de transações a cada 5 a 10 minutos . As cópias de segurança são armazenadas no armazenamento RA-GRS durante pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup configurável de suporte básico para restauro pontual, até 35 dias. 

A Base de Dados SQL também fornece várias funcionalidades de continuidade do negócio, que pode usar para mitigar vários cenários não planeados. 

- As [tabelas temporais](sql-database-temporal-tables.md) permitem-lhe restaurar as versões de linhas a partir de qualquer ponto no tempo.
- [Backups automatizados incorporados](sql-database-automated-backups.md) e [Point in Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore) permite-lhe restaurar a base de dados completa em algum momento dentro do período de retenção configurado até 35 dias.
- Pode [restaurar uma base de dados eliminada](sql-database-recovery-using-backups.md#deleted-database-restore) ao ponto em que foi eliminada se o servidor de base de dados **SQL não tiver sido eliminado**.
- [A retenção de backup](sql-database-long-term-retention.md) a longo prazo permite-lhe manter os backups até 10 anos.
- [A geo-replicação ativa](sql-database-active-geo-replication.md) permite-lhe criar réplicas legíveis e falhar manualmente em qualquer réplica em caso de interrupção de data center ou upgrade de aplicações.
- [O grupo de falha automática](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que a aplicação se reagere automaticamente em caso de interrupção do centro de dados.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar uma base de dados na mesma região de Azure

Pode utilizar cópias de dados automáticas para restaurar uma base de dados a um ponto no passado. Desta forma, pode recuperar de corrupção de dados causadapor erros humanos. O restauro pontual permite-lhe criar uma nova base de dados no mesmo servidor que representa o estado dos dados antes do evento de corrupção. Para a maioria das bases de dados, as operações de restauro demoram menos de 12 horas. Pode demorar mais tempo a recuperar uma base de dados muito grande ou muito ativa. Para mais informações sobre o tempo de recuperação, consulte o tempo de [recuperação da base de dados](sql-database-recovery-using-backups.md#recovery-time). 

Se o período máximo de retenção de backup suportado para restauro pontual (PITR) não for suficiente para a sua aplicação, pode alargá-lo configurando uma política de retenção a longo prazo (LTR) para a base de dados(s). Para mais informações, consulte a [retenção de backup a longo prazo.](sql-database-long-term-retention.md)

## <a name="compare-geo-replication-with-failover-groups"></a>Compare a geo-replicação com grupos failover

[Os grupos de falha automática](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) simplificam a implantação e utilização da [geo-replicação](sql-database-active-geo-replication.md) e adicionam as capacidades adicionais descritas no quadro seguinte:

|                                              | Georreplicação | Grupos de ativação pós-falha  |
|:---------------------------------------------| :-------------- | :----------------|
| Falha automática                           |     Não          |      Sim         |
| Falhar sobre várias bases de dados simultaneamente  |     Não          |      Sim         |
| O utilizador deve atualizar a cadeia de ligação após a falha      |     Sim         |      Não          |
| Instância gerida apoiada                   |     Não          |      Sim         |
| Pode ser na mesma região que as primárias             |     Sim         |      Não          |
| Múltiplas réplicas                            |     Sim         |      Não          |
| Suporta a escala de leitura                          |     Sim         |      Sim         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar uma base de dados para o servidor existente

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas.

- Uma opção é aguardar que a base de dados volte a ficar online quando a indisponibilidade do centro de dados terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um data center tem uma paragem, não sabe quanto tempo a paralisação pode durar, pelo que esta opção só funciona se não precisar da sua base de dados por um tempo.
- Outra opção é restaurar uma base de dados em qualquer servidor em qualquer região do Azure usando cópias de [dados georedundantes](sql-database-recovery-using-backups.md#geo-restore) (geo-restauro). A geo-restauração utiliza uma cópia de segurança georedundante como fonte e pode ser usada para recuperar uma base de dados mesmo que a base de dados ou datacenter esteja inacessível devido a uma falha.
- Finalmente, pode recuperar rapidamente de uma paragem se tiver configurado geo-secundário utilizando [geo-replicação ativa](sql-database-active-geo-replication.md) ou um [grupo de falha automática](sql-database-auto-failover-group.md) para a sua base de dados ou bases de dados. Dependendo da sua escolha destas tecnologias, pode utilizar falhas manuais ou automáticas. Embora a falha em si dereseja apenas alguns segundos, o serviço levará pelo menos 1 hora para o ativar. Isto é necessário para garantir que a falha seja justificada pela escala da paralisação. Além disso, a falha pode resultar em pequena perda de dados devido à natureza da replicação assíncrona. 

À medida que elabora o seu plano de continuidade empresarial, tem de saber qual o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento problemático. O tempo necessário para a candidatura para recuperar totalmente é conhecido como objetivo de tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder quando se recupera de um evento disruptivo não planeado. A perda potencial de dados é conhecida como objetivo do ponto de recuperação (RPO).

Diferentes métodos de recuperação oferecem diferentes níveis de RPO e RTO. Pode escolher um método de recuperação específico ou utilizar uma combinação de métodos para obter a recuperação total da aplicação. O quadro seguinte compara RPO e RTO de cada opção de recuperação. Os grupos de falha automática simplificam a implantação e utilização da geo-replicação e adicionam as capacidades adicionais descritas na tabela seguinte.

| Método de recuperação | RTO | RPO |
| --- | --- | --- | 
| Geo-restauro de backups geo-replicados | 12 h | 1 h |
| Grupos de ativação pós-falha automática | 1 h | 5 s |
| Falha na base de dados manual | 30 s | 5 s |

> [!NOTE]
> *Falha* na base de dados manual refere-se à falha de uma única base de dados para o seu secundário geo-replicado utilizando o [modo não planeado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela mais cedo neste artigo para obter detalhes sobre o RTO e RPO de auto-falha.


Utilize grupos de falha automática se a sua aplicação cumprir algum destes critérios:

- É fundamental para a atividade crítica.
- Tem um acordo de nível de serviço (SLA) que não permite 12 horas ou mais de tempo de inatividade.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Tem uma alta taxa de variação de dados e 1 hora de perda de dados não é aceitável.
- O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Pode optar por utilizar uma combinação de backups de bases de dados e geo-replicação ativa, dependendo dos requisitos de aplicação. Para uma discussão sobre considerações de design para bases de dados autónomas e para piscinas elásticas usando estas características de continuidade do negócio, consulte [design uma aplicação para a recuperação](sql-database-designing-cloud-solutions-for-disaster-recovery.md) de desastres em nuvem e estratégias de recuperação de desastres de piscina [elástica.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

As seguintes secções fornecem uma visão geral das etapas para recuperar utilizando cópias de dados ou geo-replicação ativa. Para etapas detalhadas, incluindo requisitos de planeamento, etapas de pós-recuperação e informações sobre como simular uma paragem para realizar um exercício de recuperação de desastres, consulte [Recuperar uma Base de Dados SQL de uma paragem](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma indisponibilidade

Independentemente da funcionalidade de continuidade empresarial utilizada, tem de:

- Identifique e prepare o servidor alvo, incluindo regras de firewall IP ao nível do servidor, logins e permissões de nível de base de dados principais.
- Determinar como redirecionar os clientes e as aplicações de cliente para o novo servidor
- Documentar outras dependências, tais como definições e alertas de auditoria

Se não se preparar corretamente, colocar as suas aplicações online após uma falha ou uma recuperação de base de dados leva tempo adicional e provavelmente também requer resolução de problemas num momento de stress - uma má combinação.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Falhar numa base de dados secundária geo-replicada

Se estiver a utilizar grupos de geo-replicação ou falha automática ativas como mecanismo de recuperação, pode configurar uma política automática de failover ou utilizar uma [falha manual não planeada](sql-database-active-geo-replication-portal.md#initiate-a-failover). Uma vez iniciado, a falha faz com que o secundário se torne o novo primário e pronto a registar novas transações e a responder a consultas - com a perda mínima de dados para os dados ainda não replicados. Para obter informações sobre a conceção do processo de failover, consulte [design uma aplicação para recuperação](sql-database-designing-cloud-solutions-for-disaster-recovery.md)de desastres em nuvem .

> [!NOTE]
> Quando o centro de dados volta a funcionar, as primárias antigas reconectam-se automaticamente com as novas primárias e tornam-se bases de dados secundárias. Se precisar de realojar a primária de volta para a região original, pode iniciar uma falha planeada manualmente (failback).

### <a name="perform-a-geo-restore"></a>Realizar um geo-restauro

Se estiver a utilizar as cópias de segurança automatizadas com armazenamento geo-redundante (ativado por padrão), pode recuperar a base de dados utilizando [geo-restauro](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação ocorre geralmente dentro de 12 horas - com perda de dados até uma hora determinada pelo momento em que a última cópia de segurança de registo foi tomada e replicada. Até concluir a recuperação, a base de dados não consegue registar quaisquer transações nem responder a consultas. Nota: o georestauro apenas restaura a base de dados até ao último ponto disponível no tempo.

> [!NOTE]
> Se o centro de dados voltar a funcionar antes de mudar a sua aplicação para a base de dados recuperada, pode cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Efetuar a ativação pós-falha/tarefas de recuperação

Após a recuperação a partir de qualquer mecanismo de recuperação, tem de efetuar as seguintes tarefas adicionais antes de os seus utilizadores e aplicações ficarem funcionais:

- Redirecionar os clientes e as aplicações de cliente para o novo servidor e base de dados restaurada
- Certifique-se de que estão em vigor regras de firewall IP adequadas para os utilizadores ligarem ou utilizarem [firewalls de nível de base de dados](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) para permitir as regras adequadas.
- Certificar-se de que estão implementados inícios de sessão e permissões ao nível da base de dados mestra no local adequados (ou utilizar [utilizadores contidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurar auditorias, conforme adequado
- Configurar alertas, conforme adequado

> [!NOTE]
> Se estiver a utilizar um grupo de falhas e a ligar-se às bases de dados utilizando o lstener de leitura, a reorientação após a falha acontecerá de forma automática e transparente à aplicação.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com um período de indisponibilidade mínimo

Por vezes, uma aplicação deve ser desativada devido à manutenção planeada, como uma atualização da aplicação. [Gerir atualizações](sql-database-manage-application-rolling-upgrade.md) de aplicações descreve como usar geo-replicação ativa para permitir atualizações rolantes da sua aplicação na nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação se algo correr mal.

## <a name="next-steps"></a>Passos seguintes

Para uma discussão sobre considerações de design de aplicações para bases de dados autónomas e para piscinas elásticas, consulte [design uma aplicação para a recuperação](sql-database-designing-cloud-solutions-for-disaster-recovery.md) de desastres em nuvem e estratégias de recuperação de desastres de piscina [elástica.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
