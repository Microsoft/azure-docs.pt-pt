---
title: Recuperação após desastre de base de dados SQL | Documentos da Microsoft
description: Saiba como recuperar uma base de dados a partir de uma falha do regional datacenter ou a falha com a base de dados do Azure SQL georreplicação ativa e o georrestauro capacidades.
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
ms.date: 01/25/2019
ms.openlocfilehash: 1e1bc92c684bf6ddbb7dc4ff0f882ad61ddeb27e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61075817"
---
# <a name="restore-an-azure-sql-database-or-failover-to-a-secondary"></a>Restaurar uma base de dados do Azure SQL ou a ativação pós-falha para uma secundária

Base de dados SQL do Azure oferece as seguintes funcionalidades para recuperar a partir de uma falha:

- [Georreplicação ativa](sql-database-active-geo-replication.md)
- [Grupos de ativação pós-falha automática](sql-database-auto-failover-group.md)
- [Restauro geográfico](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Bases de dados com redundância de zona](sql-database-high-availability.md)

Para saber mais sobre os cenários de continuidade de negócios e os recursos para esses cenários, consulte [continuidade do negócio](sql-database-business-continuity.md).

> [!NOTE]
> Se estiver a utilizar ou conjuntos de bases de dados de Premium ou críticas para a empresa com redundância de zona, o processo de recuperação é automatizado e o restante deste material não se aplica.

> [!NOTE]
> Bases de dados primários e secundários têm de ter a mesma camada de serviço. É também vivamente recomendado que a base de dados secundário é criado com o mesmo tamanho de computação (DTUs ou vCores) como principal. Para obter mais informações, consulte [Upgrading ou fazer downgrade como base de dados primária](sql-database-active-geo-replication.md#upgrading-or-downgrading-a-primary-database).

> [!NOTE]
> Utilize um ou vários grupos de ativação pós-falha para gerir a ativação pós-falha de várias bases de dados.
> Se adicionar uma relação de georreplicação existente para o grupo de ativação pós-falha, certifique-se de que GEO-secundária está configurado com o mesmo escalão de serviço e o tamanho de computação como principal. Para obter mais informações, consulte [utilizar grupos de ativação pós-falha automática para ativar a ativação pós-falha transparente e coordenada de várias bases de dados](sql-database-auto-failover-group.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Preparar para o evento de um período de indisponibilidade

Para o êxito da recuperação para outra região de dados através de grupos de ativação pós-falha ou cópias de segurança georredundante, tem de preparar um servidor no Centro de dados de outra falha se tornar o novo servidor primário caso seja preciso surgir, bem como tem bem definidos passos documentados e testadas para assegurar uma recuperação sem problemas. Estes passos de preparação incluem:

- Identifica o servidor de base de dados SQL em outra região para se tornar o novo servidor primário. Para o restauro geográfico, isso geralmente é um servidor no [região emparelhada](../best-practices-availability-paired-regions.md) para a região em que se encontra a base de dados. Isso elimina o custo de tráfego adicional durante as operações de restauração de georreplicação.
- Identifique e definir opcionalmente, as regras de firewall ao nível do servidor IP necessárias em que os utilizadores acedam a nova base de dados primário.
- Determine como vai redirecionar utilizadores para o novo servidor primário, por exemplo, alterando as cadeias de ligação ou alterando as entradas de DNS.
- Identifique e, opcionalmente, criar, os inícios de sessão que têm de estar presente na base de dados mestra no novo servidor primário e certifique-se de que estes inícios de sessão tem as permissões adequadas na base de dados mestra, se houver. Para obter mais informações, consulte [segurança da base de dados SQL após a recuperação após desastre](sql-database-geo-replication-security-config.md)
- Identifica as regras de alerta que precisam ser atualizados para mapear para a nova base de dados primário.
- Documentar a configuração de auditoria na base de dados primária atual
- Efetuar uma [teste de recuperação após desastre](sql-database-disaster-recovery-drills.md). Para simular uma falha para o restauro geográfico, pode eliminar ou mudar o nome da base de dados de origem para fazer com que a falha de conectividade da aplicação. Para simular uma falha através de grupos de ativação pós-falha, pode desativar a aplicação web ou máquina virtual ligada à base de dados ou ativação pós-falha da base de dados para fazer com que as falhas de conectividade de aplicativo.

## <a name="when-to-initiate-recovery"></a>Quando iniciar a recuperação

A operação de recuperação tem impacto sobre a aplicação. Ele exige a alteração a cadeia de ligação de SQL ou o redirecionamento através de DNS e pode resultar em perda de dados permanente. Por conseguinte, deve ser feito apenas quando a falha for provável que durar mais do que o objetivo de tempo de recuperação da sua aplicação. Quando a aplicação é implementada em produção deve executar o monitoramento regular do Estado de funcionamento da aplicação e utilizar os seguintes pontos de dados para que a recuperação é garantida de declaração:

1. Falha de conectividade permanente da camada de aplicativos para a base de dados.
2. O portal do Azure mostra um alerta sobre um incidente na região com impacto.

> [!NOTE]
> Se estiver a utilizar grupos de ativação pós-falha e escolheu a ativação pós-falha automática, o processo de recuperação é automatizada e transparente para a aplicação.

Dependendo da sua tolerância ao aplicativo para o período de indisponibilidade e possíveis responsabilidade de negócios, pode considerar as seguintes opções de recuperação.

Utilize o [obter base de dados recuperáveis](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de restauro georreplicado mais recente.

## <a name="wait-for-service-recovery"></a>Aguarde pela recuperação de serviço

O trabalho de equipes do Azure rapidamente para restaurar a disponibilidade de serviço como fazê-lo rapidamente possível, mas dependendo de raiz pode demorar horas ou dias.  Se a sua aplicação pode tolerar períodos de indisponibilidade significativos, que pode simplesmente esperar para a recuperação concluir. Neste caso, nenhuma ação da sua parte é necessária. Pode ver o estado atual do serviço no nosso [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/). Após a recuperação da região, a disponibilidade da aplicação é restaurada.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Ativação pós-falha para o servidor secundário georreplicado no grupo de ativação pós-falha

Se o tempo de inatividade do seu aplicativo pode resultar em encargos de negócios, deve usar grupos de ativação pós-falha. Permite que a aplicação restaurar rapidamente a disponibilidade numa região diferente em caso de indisponibilidade. Para obter um tutorial, veja [implementar uma base de dados distribuída geograficamente](sql-database-implement-geo-distributed-database.md).

Para restaurar a disponibilidade das bases de dados terá de iniciar a ativação pós-falha para o servidor secundário, através de um dos métodos suportados.

Utilize um dos seguintes guias para efetuar a ativação pós-falha para um georreplicada secundária da base de dados:

- [Efetuar a ativação pós-falha num servidor secundário georreplicado com o portal do Azure](sql-database-geo-replication-portal.md)
- [Efetuar a ativação pós-falha para o servidor secundário com o PowerShell](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [Efetuar a ativação pós-falha num servidor secundário com o Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Recuperar ao utilizar o restauro geográfico

Se o tempo de inatividade do seu aplicativo não resulta em encargos de negócios pode utilizar [georrestauro](sql-database-recovery-using-backups.md) como um método para recuperar as suas bases de dados do aplicativo. Ele cria uma cópia da base de dados da sua mais recente cópia de segurança georredundante.

## <a name="configure-your-database-after-recovery"></a>Configurar a sua base de dados após a recuperação

Se estiver a utilizar o restauro geográfico para recuperar a partir de um período de indisponibilidade, deve certificar-se de que a conectividade com as novas bases de dados está corretamente configurada para que a função normal de aplicativos pode ser retomada. Esta é uma lista de verificação de tarefas para preparar a sua produção de base de dados recuperada.

### <a name="update-connection-strings"></a>Atualizar cadeias de ligação

Como a base de dados recuperada reside num servidor diferente, tem de atualizar a cadeia de ligação da sua aplicação para apontar para esse servidor.

Para obter mais informações sobre como alterar as cadeias de ligação, veja a linguagem de desenvolvimento adequadas para sua [biblioteca de conexão](sql-database-libraries.md).

### <a name="configure-firewall-rules"></a>Configurar regras de Firewall

Tem de certificar-se de que as regras de firewall configuradas no servidor e na base de dados corresponderem aos que foram configurados no servidor primário e base de dados primária. Para obter mais informações, consulte [como: Configurar as definições da Firewall (base de dados SQL do Azure)](sql-database-configure-firewall-settings.md).

### <a name="configure-logins-and-database-users"></a>Configurar os inícios de sessão e utilizadores de base de dados

Tem de certificar-se de que todos os inícios de sessão utilizados pela sua aplicação existem no servidor que está a alojar a base de dados recuperada. Para obter mais informações, consulte [configuração de segurança para georreplicação](sql-database-geo-replication-security-config.md).

> [!NOTE]
> Deve configurar e testar as suas regras de firewall do servidor e inícios de sessão (e as respetivas permissões) durante um teste de recuperação após desastre. Esses objetos ao nível do servidor e a configuração podem não estar disponíveis durante o período de inatividade.

### <a name="setup-telemetry-alerts"></a>Configurar alertas de telemetria

Tem de certificar-se de que as definições da regra de alerta existentes são atualizadas para mapear para a base de dados recuperada e o servidor diferente.

Para obter mais informações sobre regras de alerta de base de dados, consulte [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) e [estado de funcionamento de serviço de controle](../monitoring-and-diagnostics/insights-service-health.md).

### <a name="enable-auditing"></a>Ativar a auditoria

Se a auditoria é necessário para acessar seu banco de dados, terá de ativar a auditoria após a recuperação de base de dados. Para obter mais informações, consulte [auditoria de base de dados](sql-database-auditing.md).

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre SQL do Azure, base de dados automatizada de cópias de segurança, consulte [cópias de segurança automatizadas de base de dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e a recuperação de continuidade de negócio, veja [cenários de continuidade](sql-database-business-continuity.md)
- Para saber mais sobre a utilização de cópias de segurança automatizadas para recuperação, consulte [restaurar uma base de dados a partir de cópias de segurança iniciadas pelo serviço](sql-database-recovery-using-backups.md)
