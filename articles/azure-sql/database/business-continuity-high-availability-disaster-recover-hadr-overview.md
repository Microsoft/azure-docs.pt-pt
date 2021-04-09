---
title: Continuidade do negócio em nuvem - recuperação de bases de dados
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba como a Azure SQL Database e a SQL Managed Instance suportam a continuidade do negócio na nuvem e a recuperação de bases de dados e ajude a manter as aplicações em nuvem crítica da missão em funcionamento.
keywords: continuidade empresarial, continuidade empresarial na nuvem, recuperação de base de dados após desastre, recuperação de base de dados
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222569"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**A continuidade do negócio** na Azure SQL Database e na SQL Managed Instance refere-se aos mecanismos, políticas e procedimentos que permitem ao seu negócio continuar a operar face a perturbações, nomeadamente à sua infraestrutura de computação. Na maioria dos casos, a SQL Database e a SQL Managed Instance tratarão dos eventos disruptivos que podem ocorrer no ambiente de nuvem e manterão as suas aplicações e processos de negócio em funcionamento. No entanto, existem alguns eventos disruptivos que não podem ser tratados automaticamente pela SQL Database, tais como:

- O utilizador apagou ou atualizou acidentalmente uma linha numa tabela.
- O agressor malicioso conseguiu apagar dados ou largar uma base de dados.
- O terramoto causou uma falha de energia e um centro de dados temporário.

Esta visão geral descreve as capacidades que a SQL Database e a SQL Managed Instance proporcionam para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções, recomendações e tutoriais para recuperar de eventos disruptivos que possam causar perda de dados ou fazer com que a sua base de dados e aplicação fiquem indisponíveis. Saiba o que fazer quando um utilizador ou erro de aplicação afeta a integridade dos dados, uma região do Azure tem uma paragem ou a sua aplicação requer manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial

Do ponto de vista da base de dados, existem quatro grandes cenários de perturbação potencial:

- Falhas de hardware ou software locais que afetam o nó de base de dados, como uma falha de unidade de disco.
- Corrupção de dados ou eliminação normalmente causada por um bug de aplicação ou erro humano. Tais falhas são específicas da aplicação e normalmente não podem ser detetadas pelo serviço de base de dados.
- Falha no datacenter, possivelmente causada por um desastre natural. Este cenário requer algum nível de geo-redundância com a aplicação falhando num centro de dados alternativo.
- Erros de atualização ou manutenção, problemas não previstos que ocorram durante a manutenção ou atualizações planeadas da infraestrutura podem exigir um rápido retorno para um estado de base de dados anterior.

Para mitigar as falhas de hardware e software locais, a SQL Database inclui uma arquitetura de [alta disponibilidade,](high-availability-sla.md)que garante a recuperação automática destas falhas com até 99,995% de disponibilidade SLA.  

Para proteger o seu negócio da perda de dados, a SQL Database e a SQL Managed Instance criam automaticamente cópias de dados completas semanais, cópias de dados diferenciais a cada 12 horas e cópias de segurança de registo de transações a cada 5 - 10 minutos. As cópias de segurança são armazenadas no armazenamento RA-GRS durante pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup de suporte básico configurável para a restauração pontual, até 35 dias.

A SQL Database e a SQL Managed Instance também fornecem várias funcionalidades de continuidade de negócios que pode usar para mitigar vários cenários não planeados.

- As [tabelas temporais](../temporal-tables.md) permitem-lhe restaurar as versões de linhas a partir de qualquer ponto no tempo.
- [Cópias de segurança automatizadas incorporadas](automated-backups-overview.md) e [Point in Time Restore](recovery-using-backups.md#point-in-time-restore) permitem-lhe restaurar a base de dados completa até algum ponto no tempo dentro do período de retenção configurado até 35 dias.
- Pode [restaurar uma base de dados eliminada](recovery-using-backups.md#deleted-database-restore) ao ponto em que foi eliminada se o **servidor não tiver sido eliminado**.
- [A retenção de backup](long-term-retention-overview.md) a longo prazo permite-lhe manter as cópias de segurança até 10 anos. Isto está em pré-visualização pública limitada para SQL Managed Instance
- [A geo-replicação ativa permite-lhe](active-geo-replication-overview.md) criar réplicas legíveis e falhar manualmente em qualquer réplica em caso de interrupção do datacenter ou atualização de aplicações.
- [O grupo de falha automática](auto-failover-group-overview.md#terminology-and-capabilities) permite que a aplicação recupere automaticamente em caso de interrupção do datacenter.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar uma base de dados dentro da mesma região de Azure

Pode utilizar cópias de segurança automáticas da base de dados para restaurar uma base de dados a um ponto no tempo no passado. Desta forma pode recuperar de corrupçãos de dados causadas por erros humanos. A restauração pontual permite-lhe criar uma nova base de dados no mesmo servidor que representa o estado dos dados antes do evento de corrupção. Para a maioria das bases de dados, as operações de restauro demoram menos de 12 horas. Pode demorar mais tempo a recuperar uma base de dados muito grande ou muito ativa. Para obter mais informações sobre o tempo de recuperação, consulte [o tempo de recuperação da base de dados.](recovery-using-backups.md#recovery-time)

Se o período máximo de retenção de backup suportado para a restauração pontual (PITR) não for suficiente para a sua aplicação, pode alargá-lo configurando uma política de retenção a longo prazo (LTR) para a(s) base de dados. Para obter mais informações, consulte [a retenção de backup a longo prazo.](long-term-retention-overview.md)

## <a name="compare-geo-replication-with-failover-groups"></a>Compare a geo-replicação com os grupos de failover

[Os grupos de falha automática](auto-failover-group-overview.md#terminology-and-capabilities) simplificam a implantação e utilização da [geo-replicação](active-geo-replication-overview.md) e adicionam as capacidades adicionais, tal como descritos no quadro seguinte:

|                                              | Georreplicação | Grupos de ativação pós-falha  |
|:---------------------------------------------| :-------------- | :----------------|
| **Ativação pós-falha automática**                          |     No          |      Yes         |
| **Efetuar ativação pós-falha em várias bases de dados simultaneamente**  |     No          |      Yes         |
| **O utilizador deve atualizar a cadeia de ligação após a ativação pós-falha**      |     Yes         |      No          |
| **Suporte ao SQL Managed Instance**                   |     No          |      Yes         |
| **Pode estar na mesma região que a primária**             |     Yes         |      No          |
| **Várias réplicas**                            |     Yes         |      No          |
| **Suporta o escalamento horizontal de leituras**                          |     Yes         |      Yes         |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar uma base de dados para o servidor existente

Embora raro, um datacenter Azure pode ter uma paragem. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas.

- Uma opção é esperar que a sua base de dados volte a estar on-line quando a paragem do datacenter terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um datacenter tem uma paragem, não sabe quanto tempo a paralisação pode durar, por isso esta opção só funciona se não precisar da sua base de dados por um tempo.
- Outra opção é restaurar uma base de dados em qualquer servidor em qualquer região do Azure utilizando [cópias de dados geo-redundantes](recovery-using-backups.md#geo-restore) (geo-restauro). A geo-restauração utiliza uma cópia de segurança geo-redundante como fonte e pode ser usada para recuperar uma base de dados, mesmo que a base de dados ou o datacenter estejam inacessíveis devido a uma falha.
- Finalmente, pode recuperar rapidamente de uma paragem se tiver configurado geo-secundário usando [a geo-replicação ativa](active-geo-replication-overview.md) ou um [grupo de falha automática](auto-failover-group-overview.md) para a sua base de dados ou bases de dados. Dependendo da escolha destas tecnologias, pode utilizar o failover manual ou automático. Embora o fracasso por si só leve apenas alguns segundos, o serviço levará pelo menos 1 hora para ativá-lo. Isto é necessário para garantir que a falência se justifique pela escala da paralisação. Além disso, a falha pode resultar em pequena perda de dados devido à natureza da replicação assíncronea.

À medida que elabora o seu plano de continuidade empresarial, tem de saber qual o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento problemático. O tempo necessário para a aplicação para recuperar totalmente é conhecido como objetivo do tempo de recuperação (RTO). Também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que a aplicação pode tolerar perder ao recuperar de um evento disruptivo não planeado. A perda de dados potencial é conhecida como objetivo de ponto de recuperação (RPO).

Diferentes métodos de recuperação oferecem diferentes níveis de RPO e RTO. Pode escolher um método de recuperação específico ou usar uma combinação de métodos para obter a recuperação completa da aplicação. A tabela seguinte compara RPO e RTO de cada opção de recuperação. Os grupos de falha automática simplificam a implantação e utilização da geo-replicação e adicionam as capacidades adicionais, tal como descritos na tabela seguinte.

| Método de recuperação | RTO | RPO |
| --- | --- | --- |
| Geo-restauro a partir de backups geo-replicados | 12 h | 1 h |
| Grupos de ativação pós-falha automática | 1 h | 5 s |
| Falha na base de dados manual | 30 s | 5 s |

> [!NOTE]
> *O failover da base* de dados manual refere-se à falha de uma única base de dados para o seu secundário geo-replicado utilizando o modo não [planeado](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela mais cedo neste artigo para obter detalhes sobre o RTO e RPO de falha automática.

Utilize grupos de falha automática se a sua aplicação cumprir qualquer um destes critérios:

- É fundamental para a atividade crítica.
- Tem um contrato de nível de serviço (SLA) que não permite 12 horas ou mais de tempo de inatividade.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Tem uma alta taxa de alteração de dados e 1 hora de perda de dados não é aceitável.
- O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Pode optar por utilizar uma combinação de backups de bases de dados e geo-replicação ativa, dependendo dos requisitos da sua aplicação. Para uma discussão de considerações de design para bases de dados autónomas e para piscinas elásticas usando estas funcionalidades de continuidade de negócio, consulte [Design uma aplicação para recuperação](designing-cloud-solutions-for-disaster-recovery.md) de desastres em nuvem e [estratégias elásticas de recuperação de desastres em piscinas.](disaster-recovery-strategies-for-applications-with-elastic-pool.md)

As secções seguintes fornecem uma visão geral das etapas para recuperar usando backups de base de dados ou geo-replicação ativa. Para etapas detalhadas, incluindo requisitos de planeamento, passos de recuperação pós-recuperação e informações sobre como simular uma paragem para realizar um exercício de recuperação de desastres, consulte [recuperar uma base de dados na Base de Dados SQL de uma paragem](disaster-recovery-guidance.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma indisponibilidade

Independentemente da funcionalidade de continuidade empresarial utilizada, tem de:

- Identifique e prepare o servidor alvo, incluindo regras de firewall IP de nível de servidor, logins e permissões de nível de base de dados principal.
- Determinar como redirecionar os clientes e as aplicações de cliente para o novo servidor
- Documentar outras dependências, tais como definições e alertas de auditoria

Se não se preparar corretamente, colocar as suas aplicações on-line após uma falha ou uma recuperação de uma base de dados leva tempo adicional e provavelmente também requer uma resolução de problemas num momento de stress - uma má combinação.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Falha numa base de dados secundária geo-replicada

Se estiver a utilizar grupos de geo-replicação ou de falha automática como mecanismo de recuperação, pode configurar uma política automática de falha ou utilizar [o failover manual não planeado](active-geo-replication-configure-portal.md#initiate-a-failover). Uma vez iniciado, o failover faz com que o secundário se torne o novo primário e pronto para registar novas transações e responder a consultas - com perda mínima de dados para os dados ainda não replicados. Para obter informações sobre a conceção do processo de failover, consulte [Design uma aplicação para recuperação de desastres na nuvem.](designing-cloud-solutions-for-disaster-recovery.md)

> [!NOTE]
> Quando o datacenter volta a estar online, as primárias antigas voltam automaticamente a ligar-se às novas bases de dados primárias e tornam-se bases de dados secundárias. Se precisar de transferir o primário de volta para a região original, pode iniciar uma falha planeada manualmente (falha).

### <a name="perform-a-geo-restore"></a>Efetuar um georrestauro

Se estiver a utilizar as cópias de segurança automatizadas com armazenamento geo-redundante (ativada por padrão), pode recuperar a base de dados utilizando [o geo-restauro](disaster-recovery-guidance.md#recover-using-geo-restore). A recuperação ocorre geralmente dentro de 12 horas - com perda de dados de até uma hora determinada pela altura em que a última cópia de segurança foi feita e replicada. Até concluir a recuperação, a base de dados não consegue registar quaisquer transações nem responder a consultas. Note que o geo-restauro só restaura a base de dados até ao último ponto disponível no tempo.

> [!NOTE]
> Se o datacenter voltar a estar online antes de mudar a sua aplicação para a base de dados recuperada, pode cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Efetuar a ativação pós-falha/tarefas de recuperação

Após a recuperação a partir de qualquer mecanismo de recuperação, tem de efetuar as seguintes tarefas adicionais antes de os seus utilizadores e aplicações ficarem funcionais:

- Redirecione os clientes e as aplicações do cliente para o novo servidor e para a base de dados restaurada.
- Certifique-se de que existem regras adequadas de firewall IP ao nível do servidor para que os utilizadores conectem ou utilizem [firewalls de nível de base de dados](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) para permitir regras apropriadas.
- Certifique-se de que estão em vigor os logins adequados e as permissões de nível de base de dados principais (ou utilize [utilizadores contidos).](/sql/relational-databases/security/contained-database-users-making-your-database-portable)
- Configurar a auditoria, conforme apropriado.
- Alertas de configuração, conforme apropriado.

> [!NOTE]
> Se estiver a utilizar um grupo de failover e ligar-se às bases de dados utilizando o ouvinte de leitura-escrita, a reorientação após a falha acontecerá de forma automática e transparente à aplicação.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com um período de indisponibilidade mínimo

Por vezes, uma aplicação deve ser retirada offline devido à manutenção planeada, como uma atualização de aplicações. [Gerir atualizações de aplicações](manage-application-rolling-upgrade.md) descreve como usar a geo-replicação ativa para permitir atualizações rolantes da sua aplicação em nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação se algo correr mal.

## <a name="next-steps"></a>Passos seguintes

Para uma discussão de considerações de design de aplicações para bases de dados únicas e para piscinas elásticas, consulte [Design uma aplicação para recuperação de desastres em nuvem](designing-cloud-solutions-for-disaster-recovery.md) e [estratégias elásticas de recuperação de desastres em piscinas.](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
