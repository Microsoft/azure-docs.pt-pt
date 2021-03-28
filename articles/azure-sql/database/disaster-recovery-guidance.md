---
title: Recuperação após desastre
description: Saiba como recuperar uma base de dados de uma falha ou falha do centro de dados regional com a geo-replicação ativa da Base de Dados Azure SQL e capacidades de geo-restauro.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 11c83a6ec364865eb3478112c9f33add22a5c09d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643260"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar a sua Base de Dados Azure SQL ou falhar para um secundário
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database oferece as seguintes capacidades para recuperar de uma paragem:

- [Georreplicação ativa](active-geo-replication-overview.md)
- [Grupos de ativação pós-falha automática](auto-failover-group-overview.md)
- [Geo-restauro](recovery-using-backups.md#point-in-time-restore)
- [Bases de dados redundantes em zonas](high-availability-sla.md)

Para conhecer os cenários de continuidade do negócio e as funcionalidades que suportam estes cenários, consulte [a continuidade do Negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

> [!NOTE]
> Se estiver a utilizar bases de dados premium ou business critical redundantes, o processo de recuperação é automatizado e o resto deste material não se aplica.
>
> As bases de dados primárias e secundárias são necessárias para ter o mesmo nível de serviço. Recomenda-se também que a base de dados secundária seja criada com o mesmo tamanho de cálculo (DTUs ou vCores) que o principal. Para obter mais informações, consulte [a atualização ou a degradação como base de dados primária.](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database)
>
> Utilize um ou vários grupos de failover para gerir o failover de várias bases de dados.
> Se adicionar uma relação de geo-replicação existente ao grupo failover, certifique-se de que o geo-secundário está configurado com o mesmo nível de serviço e tamanho de cálculo que o principal. Para obter mais informações, consulte [utilize grupos de falha automática para permitir a falha transparente e coordenada de várias bases de dados](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Preparar para o evento de uma paralisação

Para o sucesso com a recuperação para outra região de dados usando grupos de failover ou backups geo-redundantes, você precisa preparar um servidor em outra falha do centro de dados para se tornar o novo servidor primário em caso de necessidade, bem como ter passos bem definidos documentados e testados para garantir uma recuperação suave. Estes passos de preparação incluem:

- Identifique o servidor noutra região para se tornar o novo servidor primário. Para o geo-restauro, este é geralmente um servidor na [região emparelhada](../../best-practices-availability-paired-regions.md) para a região em que a sua base de dados está localizada. Isto elimina os custos adicionais de tráfego durante as operações de geo-restauro.
- Identifique e defina opcionalmente as regras de firewall IP ao nível do servidor necessárias para que os utilizadores acedam à nova base de dados primária.
- Determine como vai redirecionar os utilizadores para o novo servidor primário, como por exemplo alterando as cadeias de ligação ou alterando as entradas de DNS.
- Identifique e crie opcionalmente os logins que devem estar presentes na base de dados principal do novo servidor primário, e certifique-se de que estes logins têm permissões apropriadas na base de dados principal, se houver. Para mais informações, consulte a [segurança da Base de Dados SQL após a recuperação de desastres](active-geo-replication-security-configure.md)
- Identifique as regras de alerta que precisam de ser atualizadas para mapear para a nova base de dados primária.
- Documente a configuração de auditoria na base de dados primária atual
- Realizar um [exercício de recuperação de desastres](disaster-recovery-drills.md). Para simular uma paragem para geo-restauro, pode eliminar ou mudar o nome da base de dados de origem para causar falha na conectividade da aplicação. Para simular uma falha utilizando grupos de failover, pode desativar a aplicação web ou a máquina virtual ligada à base de dados ou falhar na base de dados para causar falhas na conectividade da aplicação.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação

A operação de recuperação tem impacto na aplicação. Requer a alteração da cadeia de ligação SQL ou reorientação utilizando DNS e pode resultar em perda permanente de dados. Portanto, só deve ser feito quando a paralisação é suscetível de durar mais do que o objetivo de tempo de recuperação da sua aplicação. Quando a aplicação for implementada para a produção, deve efetuar uma monitorização regular da saúde da aplicação e utilizar os seguintes pontos de dados para afirmar que a recuperação é justificada:

1. Falha permanente da conectividade do nível de aplicação para a base de dados.
2. O portal Azure mostra um alerta sobre um incidente na região com grande impacto.

> [!NOTE]
> Se estiver a utilizar grupos de failover e escolher o failover automático, o processo de recuperação é automatizado e transparente para a aplicação.

Dependendo da tolerância à sua aplicação para o tempo de inatividade e possível responsabilidade comercial, pode considerar as seguintes opções de recuperação.

Utilize a [Base de Dados Get Recoverable](/previous-versions/azure/reference/dn800985(v=azure.100)) *(LastAvailableBackupDate)* para obter o mais recente ponto de restauro geo-replicado.

## <a name="wait-for-service-recovery"></a>Aguarde a recuperação do serviço

As equipas do Azure trabalham diligentemente para restaurar a disponibilidade do serviço o mais rapidamente possível, mas dependendo da causa principal pode levar horas ou dias.  Se a sua aplicação pode tolerar tempos de inatividade significativos, pode simplesmente esperar que a recuperação esteja concluída. Neste caso, não é necessária qualquer ação da sua parte. Pode ver o estado atual do serviço no nosso [Painel de Saúde do Serviço Azure.](https://azure.microsoft.com/status/) Após a recuperação da região, a disponibilidade da sua aplicação é restaurada.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Falha no servidor secundário geo-replicado no grupo de failover

Se o tempo de inatividade da sua aplicação pode resultar em responsabilidade comercial, deve utilizar grupos de failover. Permite que a aplicação restabeleça rapidamente a disponibilidade numa região diferente em caso de paragem. Para obter um tutorial, consulte [implementar uma base de dados geo-distribuída.](geo-distributed-application-configure-tutorial.md)

Para restaurar a disponibilidade da(s) base de dados,s, é necessário iniciar a falha no servidor secundário utilizando um dos métodos suportados.

Utilize um dos seguintes guias para falhar numa base de dados secundária geo-replicada:

- [Falha num servidor secundário geo-replicado utilizando o portal Azure](active-geo-replication-configure-portal.md)
- [Falha no servidor secundário utilizando o PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Falha num servidor secundário utilizando o Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Recuperar usando o geo-restauro

Se o tempo de inatividade da sua aplicação não resultar em responsabilidade comercial, pode utilizar [o geo-restauro](recovery-using-backups.md) como método para recuperar a sua(s) base de dados de aplicações. Cria uma cópia da base de dados a partir da sua mais recente cópia de segurança geo-redundante.

## <a name="configure-your-database-after-recovery"></a>Configure a sua base de dados após a recuperação

Se estiver a utilizar o geo-restauro para recuperar de uma paragem, deve certificar-se de que a conectividade com as novas bases de dados está devidamente configurada para que a função normal de aplicação possa ser retomada. Esta é uma lista de tarefas para preparar a sua produção de base de dados recuperada.

### <a name="update-connection-strings"></a>Atualizar cadeias de ligação

Como a base de dados recuperada reside num servidor diferente, precisa de atualizar a cadeia de ligação da sua aplicação para apontar para esse servidor.

Para obter mais informações sobre a alteração das cordas de ligação, consulte o linguagem de desenvolvimento apropriado para a sua [biblioteca de ligação](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Configurar as regras de firewall

Tem de se certificar de que as regras de firewall configuradas no servidor e na base de dados coincidem com as que foram configuradas no servidor primário e na base de dados primária. Para obter mais informações, consulte [Como: Configurações de firewall configure (Base de dados Azure SQL)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Configurar logins e utilizadores de bases de dados

Tem de se certificar de que todos os logins utilizados pela sua aplicação existem no servidor que está a hospedar a sua base de dados recuperada. Para obter mais informações, consulte [a Configuração de Segurança para geo-replicação](active-geo-replication-security-configure.md).

> [!NOTE]
> Deve configurar e testar as regras e logins de firewall do servidor (e as suas permissões) durante um exercício de recuperação de desastres. Estes objetos ao nível do servidor e a sua configuração podem não estar disponíveis durante a paragem.

### <a name="setup-telemetry-alerts"></a>Alertas de telemetria de configuração

Tem de se certificar de que as definições de regra de alerta existentes são atualizadas para mapear para a base de dados recuperada e para o servidor diferente.

Para obter mais informações sobre as regras de alerta de base de dados, consulte [receber notificações de alerta](../../azure-monitor/alerts/alerts-overview.md) e serviço de rastreio de [saúde.](../../service-health/service-notifications.md)

### <a name="enable-auditing"></a>Permitir a auditoria

Se for necessária uma auditoria para aceder à sua base de dados, tem de ativar a Auditoria após a recuperação da base de dados. Para obter mais informações, consulte [a auditoria da Base de Dados.](../../azure-sql/database/auditing-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre backups automatizados da Base de Dados Azure SQL, consulte [backups automatizados da BASE de Dados SQL](automated-backups-overview.md)
- Para conhecer os cenários de design e recuperação de continuidade de negócios, consulte [cenários de continuidade](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Para saber mais sobre a utilização de backups automatizados para recuperação, consulte [restaurar uma base de dados a partir das cópias de segurança iniciadas pelo serviço](recovery-using-backups.md)