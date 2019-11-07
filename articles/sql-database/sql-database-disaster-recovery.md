---
title: Recuperação de desastre do banco de dados SQL
description: Saiba como recuperar um banco de dados de uma interrupção regional do Datacenter ou uma falha com a replicação geográfica ativa do banco de dados SQL do Azure e recursos de restauração geográfica.
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
ms.openlocfilehash: bc4968541c29e3bf18eb4caca2abe648d17342a4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690599"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar um banco de dados SQL do Azure ou fazer failover para um secundário

O banco de dados SQL do Azure oferece os seguintes recursos para a recuperação de uma interrupção:

- [Georreplicação ativa](sql-database-active-geo-replication.md)
- [Grupos de ativação pós-falha automática](sql-database-auto-failover-group.md)
- [Restauração geográfica](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Bancos de dados com redundância de zona](sql-database-high-availability.md)

Para saber mais sobre cenários de continuidade de negócios e os recursos que dão suporte a esses cenários, consulte [continuidade de negócios](sql-database-business-continuity.md).

> [!NOTE]
> Se você estiver usando pools ou bancos de dados Premium ou Comercialmente Crítico com redundância de zona, o processo de recuperação será automatizado e o restante deste material não se aplicará.

> [!NOTE]
> Os bancos de dados primário e secundário precisam ter a mesma camada de serviço. Também é altamente recomendável que o banco de dados secundário seja criado com o mesmo tamanho de computação (DTUs ou vCores) que o primário. Para obter mais informações, consulte [atualizando ou fazendo downgrade como banco de dados primário](sql-database-active-geo-replication.md#upgrading-or-downgrading-primary-database).

> [!NOTE]
> Use um ou vários grupos de failover para gerenciar o failover de vários bancos de dados.
> Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, verifique se o secundário geográfico está configurado com a mesma camada de serviço e o mesmo tamanho de computação que o primário. Para obter mais informações, consulte [usar grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Preparar-se para o evento de uma interrupção

Para obter êxito com a recuperação para outra região de dados usando grupos de failover ou backups com redundância geográfica, você precisa preparar um servidor em outro data center a interrupção para se tornar o novo servidor primário, caso seja necessária a necessidade, bem como ter etapas bem definidas documentadas e testado para garantir uma recuperação tranqüila. Essas etapas de preparação incluem:

- Identifique o servidor do banco de dados SQL em outra região para se tornar o novo servidor primário. Para a restauração geográfica, isso geralmente é um servidor na [região emparelhada](../best-practices-availability-paired-regions.md) para a região em que seu banco de dados está localizado. Isso elimina o custo de tráfego adicional durante as operações de restauração geográfica.
- Identificar e, opcionalmente, definir as regras de firewall de IP de nível de servidor necessárias para que os usuários acessem o novo banco de dados primário.
- Determine como você pretende redirecionar os usuários para o novo servidor primário, por exemplo, alterando as cadeias de conexão ou alterando as entradas DNS.
- Identifique e, opcionalmente, crie os logons que devem estar presentes no banco de dados mestre no novo servidor primário e verifique se esses logons têm as permissões apropriadas no banco de dados mestre, se houver. Para obter mais informações, consulte [segurança do banco de dados SQL após a recuperação de desastre](sql-database-geo-replication-security-config.md)
- Identifique as regras de alerta que precisam ser atualizadas para mapear para o novo banco de dados primário.
- Documentar a configuração de auditoria no banco de dados primário atual
- Execute uma [análise de recuperação de desastre](sql-database-disaster-recovery-drills.md). Para simular uma interrupção para a restauração geográfica, você pode excluir ou renomear o banco de dados de origem para causar falha na conectividade do aplicativo. Para simular uma interrupção usando grupos de failover, você pode desabilitar o aplicativo Web ou a máquina virtual conectada ao banco de dados ou fazer failover do banco de dados para causar falhas de conectividade do aplicativo.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação

A operação de recuperação afeta o aplicativo. Ele requer a alteração da cadeia de conexão SQL ou do redirecionamento usando DNS e pode resultar em perda permanente de dados. Portanto, isso deve ser feito somente quando a interrupção provavelmente durar mais do que o objetivo de tempo de recuperação do seu aplicativo. Quando o aplicativo é implantado na produção, você deve executar o monitoramento regular da integridade do aplicativo e usar os seguintes pontos de dados para declarar que a recuperação é garantida:

1. Falha de conectividade permanente da camada de aplicativo para o banco de dados.
2. O portal do Azure mostra um alerta sobre um incidente na região com grande impacto.

> [!NOTE]
> Se você estiver usando grupos de failover e escolher failover automático, o processo de recuperação será automatizado e transparente para o aplicativo.

Dependendo da tolerância do aplicativo ao tempo de inatividade e à possível responsabilidade de negócios, você pode considerar as opções de recuperação a seguir.

Use o*LastAvailableBackupDate*( [obter banco de dados recuperável](https://msdn.microsoft.com/library/dn800985.aspx) ) para obter o ponto de restauração mais recente replicado geograficamente.

## <a name="wait-for-service-recovery"></a>Aguardar a recuperação do serviço

As equipes do Azure trabalham para restaurar a disponibilidade do serviço o mais rápido possível, mas dependendo da causa raiz, pode levar horas ou dias.  Se seu aplicativo puder tolerar um tempo de inatividade significativo, você pode simplesmente aguardar a conclusão da recuperação. Nesse caso, nenhuma ação de sua parte é necessária. Você pode ver o status atual do serviço em nosso [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade do aplicativo é restaurada.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Failover para servidor secundário replicado geograficamente no grupo de failover

Se o tempo de inatividade do aplicativo puder resultar em responsabilidade comercial, você deverá usar grupos de failover. Ele permite que o aplicativo restaure rapidamente a disponibilidade em uma região diferente em caso de uma interrupção. Para obter um tutorial, consulte [implementar um banco de dados distribuído geograficamente](sql-database-implement-geo-distributed-database.md).

Para restaurar a disponibilidade dos bancos de dados, você precisa iniciar o failover para o servidor secundário usando um dos métodos com suporte.

Use um dos seguintes guias para fazer failover para um banco de dados secundário replicado geograficamente:

- [Fazer failover para um servidor secundário replicado geograficamente usando o portal do Azure](sql-database-geo-replication-portal.md)
- [Fazer failover para o servidor secundário usando o PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Fazer failover para um servidor secundário usando Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Recuperar usando a restauração geográfica

Se o tempo de inatividade do aplicativo não resultar em responsabilidade comercial, você poderá usar a [restauração geográfica](sql-database-recovery-using-backups.md) como um método para recuperar seus bancos de dados de aplicativo. Ele cria uma cópia do banco de dados de seu backup com redundância geográfica mais recente.

## <a name="configure-your-database-after-recovery"></a>Configurar seu banco de dados após a recuperação

Se estiver usando a restauração geográfica para se recuperar de uma interrupção, você deve verificar se a conectividade com os novos bancos de dados está configurada corretamente para que a função normal do aplicativo possa ser retomada. Esta é uma lista de verificação de tarefas para preparar sua produção de banco de dados recuperada.

### <a name="update-connection-strings"></a>Atualizar cadeias de conexão

Como o banco de dados recuperado reside em um servidor diferente, você precisa atualizar a cadeia de conexão do aplicativo para apontar para esse servidor.

Para obter mais informações sobre como alterar cadeias de conexão, consulte a linguagem de desenvolvimento apropriada para sua [biblioteca de conexões](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar regras de firewall

Você precisa certificar-se de que as regras de firewall configuradas no servidor e no banco de dados correspondam às que foram configuradas no servidor primário e no banco de dados primário. Para obter mais informações, consulte [como: definir configurações de firewall (banco de dados SQL do Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurar logons e usuários de banco de dados

Você precisa certificar-se de que todos os logons usados pelo seu aplicativo existam no servidor que está hospedando o banco de dados recuperado. Para obter mais informações, consulte [configuração de segurança para replicação geográfica](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Você deve configurar e testar suas regras de firewall do servidor e logons (e suas permissões) durante uma análise de recuperação de desastre. Esses objetos de nível de servidor e sua configuração podem não estar disponíveis durante a interrupção.

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetria

Você precisa certificar-se de que as configurações de regra de alerta existentes sejam atualizadas para mapear para o banco de dados recuperado e para o servidor diferente.

Para obter mais informações sobre regras de alerta de banco de dados, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [acompanhar a integridade do serviço](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Habilitar auditoria

Se a auditoria for necessária para acessar seu banco de dados, você precisará habilitar a auditoria após a recuperação do banco de dados. Para obter mais informações, consulte [auditoria de banco de dados](sql-database-auditing.md).

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os backups automatizados do banco de dados SQL do Azure, confira [backups automatizados](sql-database-automated-backups.md)
- Para saber mais sobre cenários de recuperação e design de continuidade de negócios, consulte [cenários de continuidade](sql-database-business-continuity.md)
- Para saber mais sobre como usar backups automatizados para recuperação, consulte [restaurar um banco de dados dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
