---
title: Cópia de segurança do Azure, solução de problemas para VMs do SQL Server | Documentos da Microsoft
description: Informações de resolução de problemas de cópia de segurança de VMs do SQL Server para o Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 3ad4afc740be01644145704679ee2674ebde3d07
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52720733"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Resolver problemas de cópia de segurança do SQL Server no Azure

Este artigo fornece informações de resolução de problemas para proteger VMs do SQL Server no Azure (pré-visualização).

## <a name="public-preview-limitations"></a>Limitações da pré-visualização públicas

Para ver as limitações de pré-visualização pública, consulte o artigo [cópia de segurança da base de dados do SQL Server no Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Permissões do SQL Server

Para configurar a proteção para uma base de dados do SQL Server numa máquina virtual, o **AzureBackupWindowsWorkload** extensão tem de estar instalada nessa máquina virtual. Se receber o erro **UserErrorSQLNoSysadminMembership**, significa que a instância de SQL não tem as permissões de cópia de segurança necessárias. Para corrigir este erro, siga os passos em [defina permissões para as VMs do SQL externas](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros

Utilize as informações nas tabelas seguintes para resolver problemas e erros encontrados ao proteger o SQL Server para o Azure.

## <a name="backup-failures"></a>Falhas de cópia de segurança

As tabelas a seguir são organizadas por código de erro.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Esta base de dados SQL não suporta o tipo de cópia de segurança solicitado. | Ocorre quando o modelo de recuperação de base de dados não permite que o tipo de cópia de segurança solicitado. O erro pode ocorrer nas seguintes situações: <br/><ul><li>Uma base de dados usando um modelo de recuperação simples não permite a cópia de segurança do registo.</li><li>Backups de log e diferenciais não são permitidas para um modelo da base de dados.</li></ul>Para obter mais detalhes, consulte a [modelos de recuperação do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) documentação. | Se falhar a cópia de segurança do registo para o banco de dados no modelo de recuperação simples, experimente uma destas opções:<ul><li>Se a base de dados está no modo de recuperação simples, desative as cópias de segurança do registo.</li><li>Utilize o [documentação do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação de base de dados para completo ou registadas em massa. </li><li> Se não pretender alterar o modelo de recuperação e tem uma política padrão para fazer cópias de segurança de várias bases de dados que não podem ser alteradas, ignore o erro. As cópias de segurança completas e diferenciais irão funcionar por agendamento. Os backups de log serão ignorados, que é esperado neste caso.</li></ul>Se for um mestre de base de dados e tiver configurado diferenciais ou registo de utilização de cópia de segurança, qualquer um dos seguintes passos:<ul><li>Utilize o portal para alterar o agendamento de política de cópia de segurança para o mestre de bases de dados, como Full.</li><li>Se tiver uma política padrão para fazer cópias de segurança de várias bases de dados que não podem ser alteradas, ignore o erro. A cópia de segurança completa irá funcionar por agendamento. Cópias de segurança diferenciais ou log não acontecem, o que é esperado neste caso.</li></ul> |
| Operação cancelada como uma operação em conflito já estava em execução no mesmo banco de dados. | Consulte a [entrada de blogue sobre cópia de segurança e restaurar limitações](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) executados simultaneamente.| [Utilize o SQL Server Management Studio (SSMS) para monitorizar as tarefas de cópia de segurança.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Assim que a operação em conflito falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Base de dados SQL não existe. | A base de dados foi eliminado ou renomeado. | <ul><li>Verifique se a base de dados foi acidentalmente eliminado ou renomeado.</li><li>Se a base de dados foi eliminado acidentalmente, para continuar a cópias de segurança, restaure a base de dados para a localização original.</li><li>Se eliminar a base de dados e não precisa de futuras cópias de segurança, em seguida, no cofre dos serviços de recuperação, clique em [parar cópia de segurança com "Delete/manter dados"](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Cadeia de registos está quebrada. | A base de dados ou a VM é feita com a solução de cópia de segurança outro, que trunca a cadeia de registos.|<ul><li>Verifique se a outra solução de cópia de segurança ou de script está a ser utilizado. Se assim for, pare a solução de cópia de segurança. </li><li>Se a cópia de segurança foi uma cópia de segurança do registo do ad-hoc, acione uma cópia de segurança completa para iniciar uma nova cadeia de registos. Para backups de log agendadas, é necessária nenhuma ação como o serviço de cópia de segurança do Azure irá acionar automaticamente uma cópia de segurança completa para corrigir este problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| O Azure Backup não é possível estabelecer ligação à instância do SQL. | O Azure Backup não é possível ligar à instância do SQL. | Utilize os detalhes adicionais no menu do erro portal do Azure para restringir as causas de raiz. Consulte a [resolução de problemas cópia de segurança do SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as predefinições de SQL não permitir ligações remotas, altere as definições. Consulte as ligações para alterar as definições abaixo.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se existirem problemas de início de sessão, consulte as ligações para corrigi-lo abaixo:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Primeiro backup completo está em falta para esta origem de dados. | Cópia de segurança completa está em falta para a base de dados. Log e diferenciais principal de cópias de segurança para uma cópia de segurança completa, pelo que devem tomar antes de acionar diferenciais de cópias de segurança completas ou backups de log. | Acione uma cópia de segurança completa de ad-hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Não é possível efetuar cópia de segurança, como o log de transação para a origem de dados está cheio. | O espaço do registo transacional de base de dados está cheio. | Para corrigir este problema, consulte a [documentação do SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Esta base de dados SQL não suporta o tipo de cópia de segurança solicitado. | Réplicas secundárias do sempre em AG não suportam cópias de segurança completas e diferenciais. | <ul><li>Se acionada uma cópia de segurança ad-hoc, acione as cópias de segurança no nó principal.</li><li>Se a cópia de segurança foi agendada pela política, certifique-se de que o nó principal está registado. Para registar o nó [siga os passos para detetar uma base de dados do SQL Server](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>As falhas do restauro

Os seguintes códigos de erro são apresentados quando tarefas de restauro.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Base de dados com o mesmo nome já existe na localização de destino | O destino de restauro de destino já tem uma base de dados com o mesmo nome.  | <ul><li>Altere o nome de base de dados de destino</li><li>Em alternativa, utilize a opção de substituição de force a página de restauro</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Restauro falhou uma vez que não foi possível colocar offline a base de dados. | Ao fazer uma restauração, a base de dados de destino tem de ser colocados offline. O Azure Backup não é possível trazer esses dados offline. | Utilize os detalhes adicionais no menu do erro portal do Azure para restringir as causas de raiz. Para obter mais informações, consulte a [documentação do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Não é possível localizar o certificado de servidor com o thumbprint no destino. | O mestre de base de dados na instância de destino não tem um thumbprint de encriptação válido. | Importe o thumbprint de certificado válido utilizado na instância de origem, para a instância de destino. |

## <a name="registration-failures"></a>Falhas de registo

São os seguintes códigos de erro para falhas de registo.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Preferência de cópia de segurança para SQL grupo de Disponibilidade AlwaysOn não puderem ser alcançada como alguns nós do grupo de disponibilidade não estão registados. | Nós necessários para efetuar cópias de segurança não estão registadas ou estão inacessíveis. | <ul><li>Certifique-se de que todos os nós necessários para efetuar cópias de segurança desta base de dados registados e bom estado de funcionamento e, em seguida, repita a operação.</li><li>Preferência de cópia de segurança de alteração no grupo de disponibilidade Always.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| VM do SQL server está encerrado ou não está acessível ao serviço de cópia de segurança do Azure. | VM é encerrada | Certifique-se de que o SQL server está em execução. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| O serviço de cópia de segurança do Azure utiliza o agente convidado da VM do Azure para fazer a cópia de segurança, mas o agente convidado não está disponível no servidor de destino. | Agente convidado não está ativado ou está danificado | [Instalar o agente convidado da VM](../virtual-machines/extensions/agent-windows.md) manualmente. |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure Backup para VMs do SQL Server (pré-visualização pública), consulte [cópia de segurança do Azure para VMs do SQL (pré-visualização pública)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).