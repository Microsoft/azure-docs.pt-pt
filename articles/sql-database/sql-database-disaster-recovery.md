---
title: Recuperação após desastre
description: Saiba como recuperar uma base de dados de uma falha ou falha regional do datacenter com a geo-replicação ativa da Base de Dados Azure SQL e capacidades de geo-restauro.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: d28edd28dcbe31bfe63c2d0a9c3e975967efef04
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359146"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar uma Base de Dados SQL Azure ou falhar a um secundário

A Base de Dados Azure SQL oferece as seguintes capacidades para recuperar de uma paragem:

- [Georreplicação ativa](sql-database-active-geo-replication.md)
- [Grupos de ativação pós-falha automática](sql-database-auto-failover-group.md)
- [Geo-restauro](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Bases de dados redundantes em zonas](sql-database-high-availability.md)

Para conhecer cenários de continuidade de negócios e as características que suportam estes cenários, consulte a [continuidade do Negócio.](sql-database-business-continuity.md)

> [!NOTE]
> Se estiver a utilizar bases de dados ou piscinas premium ou business critical redundantes, o processo de recuperação é automatizado e o resto deste material não se aplica.

> [!NOTE]
> Tanto as bases de dados primárias como secundárias são obrigadas a ter o mesmo nível de serviço. Recomenda-se também fortemente que a base de dados secundária seja criada com o mesmo tamanho de computação (DTUs ou vCores) que o primário. Para mais informações, consulte [A tualização ou degradação como base de dados primária](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Utilize um ou vários grupos de failover para gerir falhas de várias bases de dados.
> Se adicionar uma relação geo-replicação existente ao grupo failover, certifique-se de que o geo-secundário está configurado com o mesmo nível de serviço e tamanho de computação que o principal. Para obter mais informações, consulte Utilize grupos de falha automática para permitir falhas [transparentes e coordenadas de várias bases de dados](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Prepare-se para o evento de uma paralisação

Para o sucesso com a recuperação para outra região de dados usando grupos de failover ou backups geo-redundantes, você precisa preparar um servidor em outra falha de data center para se tornar o novo servidor primário se a necessidade surgir, bem como ter passos bem definidos documentados e testado para garantir uma recuperação suave. Estes passos de preparação incluem:

- Identifique o servidor de base de dados SQL noutra região para se tornar o novo servidor primário. Para geo-restauro, este é geralmente um servidor na [região emparelhada](../best-practices-availability-paired-regions.md) para a região em que a sua base de dados está localizada. Isto elimina o custo adicional de tráfego durante as operações de georestauro.
- Identificar e definir opcionalmente as regras de firewall IP de nível de servidor necessárias para que os utilizadores acedam à nova base de dados primária.
- Determine como vai redirecionar os utilizadores para o novo servidor primário, como por exemplo alterando as cordas de ligação ou alterando as entradas de DNS.
- Identifique e crie opcionalmente os logins que devem estar presentes na base de dados principal do novo servidor primário, e certifique-se de que estes logins têm permissões adequadas na base de dados principal, se houver. Para mais informações, consulte a segurança da Base de [Dados SQL após a recuperação](sql-database-geo-replication-security-config.md) de desastres
- Identifique as regras de alerta que precisam de ser atualizadas para mapear a nova base de dados primária.
- Documentar a configuração de auditoria na base de dados primária atual
- Execute um exercício de [recuperação](sql-database-disaster-recovery-drills.md)de desastres. Para simular uma paragem para geo-restauro, pode eliminar ou renomear a base de dados de origem para causar falha na conectividade da aplicação. Para simular uma falha utilizando grupos failover, pode desativar a aplicação web ou a máquina virtual ligada à base de dados ou falhar na base de dados para causar falhas na conectividade da aplicação.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação

A operação de recuperação tem impacto na aplicação. Requer a alteração da cadeia de ligação SQL ou a reorientação utilizando DNS e pode resultar numa perda permanente de dados. Portanto, só deve ser feito quando a paralisação pode durar mais tempo do que o objetivo de tempo de recuperação da sua aplicação. Quando a aplicação for implantada para produção, deverá efetuar uma monitorização regular da saúde da aplicação e utilizar os seguintes pontos de dados para afirmar que a recuperação se justifica:

1. Falha de conectividade permanente do nível de aplicação para a base de dados.
2. O portal Azure mostra um alerta sobre um incidente na região com grande impacto.

> [!NOTE]
> Se estiver a utilizar grupos de failover e optar por uma falha automática, o processo de recuperação é automatizado e transparente para a aplicação.

Dependendo da tolerância à sua aplicação ao tempo de inatividade e possível responsabilidade comercial, pode considerar as seguintes opções de recuperação.

Utilize a base de [dados Get Recoverable](https://msdn.microsoft.com/library/dn800985.aspx) *(LastAvailableBackupDate)* para obter o mais recente ponto de restauro geo-replicado.

## <a name="wait-for-service-recovery"></a>Aguarde a recuperação do serviço

As equipas azure trabalham diligentemente para restaurar a disponibilidade de serviço o mais rápido possível, mas dependendo da causa raiz pode levar horas ou dias.  Se a sua aplicação puder tolerar tempos de paragem significativos, pode simplesmente esperar que a recuperação esteja concluída. Neste caso, não é necessária qualquer ação da sua parte. Pode ver o estado de serviço atual no nosso Painel de Saúde de [Serviço Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade da sua aplicação é restaurada.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Falhar no servidor secundário geo-replicado no grupo failover

Se o tempo de inatividade da sua aplicação pode resultar em responsabilidade empresarial, deverá utilizar grupos de failover. Permite que a aplicação restabeleça rapidamente a disponibilidade numa região diferente em caso de paralisação. Para um tutorial, consulte [Implementar uma base de dados geodistribuída](sql-database-implement-geo-distributed-database.md).

Para restaurar a disponibilidade da(s) base de dados, é necessário iniciar a falha no servidor secundário utilizando um dos métodos suportados.

Utilize um dos seguintes guias para falhar numa base de dados secundária geo-replicada:

- [Falhar num servidor secundário geo-replicado utilizando o portal Azure](sql-database-geo-replication-portal.md)
- [Falhar no servidor secundário utilizando o PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Falhar num servidor secundário utilizando o Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Recuperar usando geo-restauro

Se o tempo de inatividade da sua aplicação não resultar em responsabilidade comercial, pode utilizar o [geo-restauro](sql-database-recovery-using-backups.md) como método para recuperar a sua base de dados de aplicações. Cria uma cópia da base de dados a partir do seu mais recente backup geo-redundante.

## <a name="configure-your-database-after-recovery"></a>Configure a sua base de dados após a recuperação

Se estiver a utilizar geo-restauro para recuperar de uma paragem, deve certificar-se de que a conectividade com as novas bases de dados está corretamente configurada para que a função de aplicação normal possa ser retomada. Esta é uma lista de tarefas para preparar a sua produção de base de dados recuperada.

### <a name="update-connection-strings"></a>Atualizar cadeias de ligação

Como a sua base de dados recuperada reside num servidor diferente, precisa de atualizar a cadeia de ligação da sua aplicação para apontar para esse servidor.

Para obter mais informações sobre a alteração das cordas de ligação, consulte a linguagem de desenvolvimento adequada para a sua biblioteca de [ligação](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configure regras de firewall

Tem de se certificar de que as regras de firewall configuradas no servidor e na base de dados correspondem às que foram configuradas no servidor primário e na base de dados primária. Para mais informações, consulte Como configurar as definições de firewall (Base de [Dados Azure SQL)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configure logins e utilizadores de bases de dados

Tem de se certificar de que todos os logins utilizados pela sua aplicação existem no servidor que está a alojar a sua base de dados recuperada. Para mais informações, consulte configuração de [segurança para geo-replicação](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Deve configurar e testar as regras e logins do seu servidor (e respetivas permissões) durante um exercício de recuperação de desastres. Estes objetos ao nível do servidor e a sua configuração podem não estar disponíveis durante a interrupção.

### <a name="setup-telemetry-alerts"></a>Alertas de telemetria de configuração

Tem de se certificar de que as definições de regra de alerta existentes são atualizadas para mapear a base de dados recuperada e o servidor diferente.

Para obter mais informações sobre as regras de alerta de base de dados, consulte [Receber Notificações](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) de Alerta e [Saúde do Serviço de Rastreio](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Ativar a auditoria

Se for necessária uma auditoria para aceder à sua base de dados, tem de ativar a Auditoria após a recuperação da base de dados. Para mais informações, consulte [a auditoria da Base de Dados.](sql-database-auditing.md)

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre cópias de segurança automatizadas da Base de Dados Azure SQL, consulte cópias de segurança automatizadas da Base de [Dados SQL](sql-database-automated-backups.md)
- Para conhecer cenários de design de continuidade de negócios e de recuperação, consulte [cenários de continuidade](sql-database-business-continuity.md)
- Para aprender sobre o uso de backups automatizados para recuperação, consulte [restaurar uma base de dados das cópias de segurança iniciadas pelo serviço](sql-database-recovery-using-backups.md)
