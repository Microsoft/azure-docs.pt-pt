---
title: Continuidade de negócios na nuvem-recuperação de banco de dados
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
ms.openlocfilehash: 5a6a96f478c4402a830cc522657f56cfd11fa56f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821831"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Descrição geral da continuidade empresarial com a Base de Dados SQL do Azure

A **continuidade dos negócios** no banco de dados SQL do Azure refere-se aos mecanismos, às políticas e aos procedimentos que permitem que sua empresa continue operando diante de interrupções, especialmente em sua infraestrutura computacional. Na maioria dos casos, o banco de dados SQL do Azure manipulará os eventos de interrupção que podem acontecer no ambiente de nuvem e manterá seus aplicativos e processos de negócios em execução. No entanto, há alguns eventos de interrupção que não podem ser manipulados pelo banco de dados SQL automaticamente, como:

- O usuário excluiu acidentalmente ou atualizou uma linha em uma tabela.
- O invasor mal-intencionado teve êxito ao excluir dados ou remover um banco de dado.
- O terremoto causou uma interrupção de energia e um Data Center desabilitado temporário.

Esta descrição geral descreve as capacidades que a Base de Dados SQL do Azure fornece para a continuidade empresarial e a recuperação após desastre. Saiba mais sobre opções, recomendações e tutoriais para recuperação de eventos de interrupção que poderiam causar perda de dados ou fazer com que seu banco de dado e aplicativo se tornem indisponíveis. Saiba o que fazer quando um erro de usuário ou aplicativo afeta a integridade dos dados, uma região do Azure tem uma interrupção ou seu aplicativo requer manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Funcionalidades da Base de Dados SQL que pode utilizar para proporcionar continuidade empresarial

Do ponto de vista do banco de dados, há quatro principais cenários de interrupção em potencial:

- Falhas de hardware ou software local que afetam o nó do banco de dados, como uma falha da unidade de disco.
- Corrupção ou exclusão de dados normalmente causadas por um bug de aplicativo ou erro humano. Essas falhas são específicas do aplicativo e normalmente não podem ser detectadas pelo serviço de banco de dados.
- Interrupção do datacenter, possivelmente causada por um desastre natural. Esse cenário requer algum nível de redundância geográfica com failover de aplicativo para um datacenter alternativo.
- Erros de atualização ou manutenção, problemas inesperados que ocorrem durante a manutenção de infraestrutura planejada ou atualizações podem exigir uma reversão rápida para um estado de banco de dados anterior.

Para reduzir as falhas de hardware e software locais, o banco de dados SQL inclui uma [arquitetura de alta disponibilidade](sql-database-high-availability.md), que garante a recuperação automática dessas falhas com um SLA de disponibilidade de até 99,995%.  

Para proteger seu negócio contra perda de dados, o banco de dados SQL cria automaticamente backups completos semanalmente, backups de banco de dado diferenciais a cada 12 horas e backups de log de transações a cada 5-10 minutos. Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todas as camadas de serviço. Todas as camadas de serviço, exceto suporte Básico período de retenção de backup configurável para restauração pontual, até 35 dias. 

O banco de dados SQL também fornece vários recursos de continuidade de negócios, que você pode usar para atenuar vários cenários não planejados. 

- As [tabelas temporais](sql-database-temporal-tables.md) permitem-lhe restaurar as versões de linhas a partir de qualquer ponto no tempo.
- [Os backups automatizados internos e a](sql-database-automated-backups.md) [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore) permitem que você restaure o banco de dados completo para algum momento dentro do período de retenção configurado de até 35 dias.
- Você pode [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que ele foi excluído se o **servidor do banco de dados SQL não tiver sido excluído**.
- A [retenção de backup de longo prazo](sql-database-long-term-retention.md) permite manter os backups de até 10 anos.
- A [replicação geográfica ativa](sql-database-active-geo-replication.md) permite que você crie réplicas legíveis e faça failover manualmente para qualquer réplica no caso de uma interrupção Data Center ou atualização do aplicativo.
- O [grupo de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo seja automaticamente recuperado no caso de uma interrupção Data Center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar um banco de dados na mesma região do Azure

Você pode usar backups de banco de dados automáticos para restaurar um banco de dados para um ponto no tempo no passado. Dessa forma, você pode se recuperar de corrompimentos de dados causados por erros humanos. A restauração pontual permite que você crie um novo banco de dados no mesmo servidor que representa o estado dos dados antes do evento corrompido. Para a maioria dos bancos de dados, as operações de restauração levam menos de 12 horas. Pode levar mais tempo para recuperar um banco de dados muito grande ou muito ativo. Para obter mais informações sobre o tempo de recuperação, consulte [tempo de recuperação do banco de dados](sql-database-recovery-using-backups.md#recovery-time). 

Se o período máximo de retenção de backup com suporte para a restauração pontual (PITR) não for suficiente para seu aplicativo, você poderá estendê-lo Configurando uma política de retenção de longo prazo (EPD) para os bancos de dados. Para obter mais informações, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Comparar a replicação geográfica com grupos de failover

Os [grupos de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) simplificam a implantação e o uso da [replicação geográfica](sql-database-active-geo-replication.md) e adicionam os recursos adicionais, conforme descrito na tabela a seguir:

|                                              | Georreplicação | Grupos de ativação pós-falha  |
|:---------------------------------------------| :-------------- | :----------------|
| Failover automático                           |     Não          |      Sim         |
| Fazer failover de vários bancos de dados simultaneamente  |     Não          |      Sim         |
| Atualizar cadeia de conexão após o failover      |     Sim         |      Não          |
| Instância gerenciada com suporte                   |     Não          |      Sim         |
| Pode estar na mesma região que o primário             |     Sim         |      Não          |
| Várias réplicas                            |     Sim         |      Não          |
| Dá suporte à escala de leitura                          |     Sim         |      Sim         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar um banco de dados no servidor existente

Embora seja raro, um centro de dados do Azure pode ficar indisponível. Quando ocorre uma indisponibilidade, esta causa uma interrupção do negócio que poderá demorar apenas alguns minutos ou pode durar horas.

- Uma opção é aguardar que a base de dados volte a ficar online quando a indisponibilidade do centro de dados terminar. Isto funciona para as aplicações que podem dar-se ao luxo de ter a base de dados offline. Por exemplo, um projeto de desenvolvimento ou uma versão de avaliação gratuita nos quais não tem de trabalhar constantemente. Quando um data center tem uma interrupção, você não sabe por quanto tempo a interrupção pode durar, portanto, essa opção só funcionará se você não precisar do seu banco de dados por algum tempo.
- Outra opção é restaurar um banco de dados em qualquer servidor em qualquer região do Azure usando [backups de banco de dados com redundância geográfica](sql-database-recovery-using-backups.md#geo-restore) (restauração geográfica). A restauração geográfica usa um backup com redundância geográfica como sua origem e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter estiver inacessível devido a uma interrupção.
- Por fim, você pode se recuperar rapidamente de uma interrupção se tiver configurado um secundário geográfico usando [a replicação geográfica ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md) para seu banco de dados ou bancos de dados. Dependendo de sua escolha dessas tecnologias, você pode usar o failover manual ou automático. Enquanto o failover leva apenas alguns segundos, o serviço levará pelo menos uma hora para ativá-lo. Isso é necessário para garantir que o failover seja justificado pela escala da interrupção. Além disso, o failover pode resultar em pequena perda de dados devido à natureza da replicação assíncrona. 

À medida que elabora o seu plano de continuidade empresarial, tem de saber qual o tempo máximo aceitável antes de a aplicação recuperar totalmente após o evento problemático. O tempo necessário para a recuperação completa do aplicativo é conhecido como RTO (objetivo de tempo de recuperação). Você também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar a perda ao recuperar-se de um evento de interrupção não planejado. A possível perda de dados é conhecida como RPO (objetivo de ponto de recuperação).

Métodos de recuperação diferentes oferecem níveis diferentes de RPO e RTO. Você pode escolher um método de recuperação específico ou usar uma combinação de métodos para obter a recuperação completa do aplicativo. A tabela a seguir compara o RPO e o RTO de cada opção de recuperação. Os grupos de failover automático simplificam a implantação e o uso da replicação geográfica e adicionam os recursos adicionais, conforme descrito na tabela a seguir.

| Método de recuperação | RTO | RPO |
| --- | --- | --- | 
| Restauração geográfica de backups replicados geograficamente | 12 h | 1 h |
| Grupos de ativação pós-falha automática | 1 h | 5 s |
| Failover manual de banco de dados | 30 s | 5 s |

> [!NOTE]
> *Failover de banco de dados manual* refere-se ao failover de um banco de dados individual para seu secundário replicado geograficamente usando o [modo não planejado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela anterior neste artigo para obter detalhes do RTO e RPO de failover automático.


Use grupos de failover automático se seu aplicativo atender a qualquer um destes critérios:

- É fundamental para a atividade crítica.
- Tem um SLA (contrato de nível de serviço) que não permite 12 horas ou mais de tempo de inatividade.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Tem uma alta taxa de alteração de dados e uma hora de perda de dados não é aceitável.
- O custo adicional da georreplicação ativa é inferior aos potenciais encargos financeiros e perda empresarial associada.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Você pode optar por usar uma combinação de backups de banco de dados e replicação geográfica ativa, dependendo dos requisitos do aplicativo. Para obter uma discussão sobre considerações de design para bancos de dados autônomos e pools elásticos usando esses recursos de continuidade de negócios, consulte [criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

As seções a seguir fornecem uma visão geral das etapas para recuperar usando backups de banco de dados ou replicação geográfica ativa. Para obter etapas detalhadas, incluindo requisitos de planejamento, etapas de pós-recuperação e informações sobre como simular uma interrupção para executar uma análise de recuperação de desastre, consulte [recuperar um banco de dados SQL de uma interrupção](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Preparar para uma indisponibilidade

Independentemente da funcionalidade de continuidade empresarial utilizada, tem de:

- Identifique e prepare o servidor de destino, incluindo regras de firewall de IP de nível de servidor, logons e permissões de nível de banco de dados mestre.
- Determinar como redirecionar os clientes e as aplicações de cliente para o novo servidor
- Documentar outras dependências, tais como definições e alertas de auditoria

Se você não se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação de banco de dados levará tempo adicional e, provavelmente, também exigirá a solução de problemas em um momento de estresse-uma combinação incorreta.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Fazer failover para um banco de dados secundário replicado geograficamente

Se você estiver usando a replicação geográfica ativa ou grupos de failover automático como seu mecanismo de recuperação, você poderá configurar uma política de failover automático ou usar o [failover manual não planejado](sql-database-active-geo-replication-portal.md#initiate-a-failover). Depois de iniciado, o failover faz com que o secundário se torne o novo primário e pronto para registrar novas transações e responder a consultas-com perda mínima de dados para os dados ainda não replicados. Para obter informações sobre como criar o processo de failover, consulte [criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o data center volta a ficar online, os primários antigos se reconectam automaticamente à nova primária e se tornam bancos de dados secundários. Se precisar realocar o primário de volta para a região original, você poderá iniciar um failover planejado manualmente (failback).

### <a name="perform-a-geo-restore"></a>Executar uma restauração geográfica

Se você estiver usando os backups automatizados com armazenamento com redundância geográfica (habilitado por padrão), poderá recuperar o banco de dados usando a [restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação geralmente ocorre dentro de 12 horas-com perda de dados de até uma hora determinada pelo momento em que o último backup de log foi feito e replicado. Até concluir a recuperação, a base de dados não consegue registar quaisquer transações nem responder a consultas. Observe que a restauração geográfica restaura apenas o banco de dados para o último ponto no tempo disponível.

> [!NOTE]
> Se o data center voltar a ficar online antes de alternar o aplicativo para o banco de dados recuperado, você poderá cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Efetuar a ativação pós-falha/tarefas de recuperação

Após a recuperação a partir de qualquer mecanismo de recuperação, tem de efetuar as seguintes tarefas adicionais antes de os seus utilizadores e aplicações ficarem funcionais:

- Redirecionar os clientes e as aplicações de cliente para o novo servidor e base de dados restaurada
- Verifique se as regras apropriadas de firewall de IP no nível de servidor estão em vigor para que os usuários se conectem ou usem [firewalls de nível de banco de dados](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) para habilitar as regras apropriadas.
- Certificar-se de que estão implementados inícios de sessão e permissões ao nível da base de dados mestra no local adequados (ou utilizar [utilizadores contidos](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurar auditorias, conforme adequado
- Configurar alertas, conforme adequado

> [!NOTE]
> Se você estiver usando um grupo de failover e se conectar aos bancos de dados usando o lstener de leitura/gravação, o redirecionamento após o failover ocorrerá de forma automática e transparente para o aplicativo.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualizar uma aplicação com um período de indisponibilidade mínimo

Às vezes, um aplicativo deve ser colocado offline devido à manutenção planejada, como uma atualização de aplicativo. [Gerenciar atualizações de aplicativos](sql-database-manage-application-rolling-upgrade.md) descreve como usar a replicação geográfica ativa para habilitar as atualizações sem interrupção do seu aplicativo de nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação se algo der errado.

## <a name="next-steps"></a>Passos seguintes

Para obter uma discussão sobre as considerações de design de aplicativos para bancos de dados autônomos e pools elásticos, consulte [criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres do pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
