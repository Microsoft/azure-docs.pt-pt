---
title: Resolução de problemas de cópia de segurança do SQL Server da base de dados com cópia de segurança do Azure | Documentos da Microsoft
description: Informações de resolução de problemas para fazer backup de bancos de dados do SQL Server em execução em VMs do Azure com o Azure Backup.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 03/13/2019
ms.author: anuragm
ms.openlocfilehash: e5565e257e511203043c84e499712cc6a0a78c3f
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286019"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Resolver problemas de cópia de segurança do SQL Server no Azure

Este artigo fornece informações de resolução de problemas para proteger VMs do SQL Server no Azure (pré-visualização).

## <a name="feature-consideration-and-limitations"></a>Funcionalidade considerações e limitações

Para ver a consideração de funcionalidade, consulte o artigo [cópia de segurança sobre o SQL Server em VMs do Azure](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Permissões do SQL Server

Para configurar a proteção para uma base de dados do SQL Server numa máquina virtual, o **AzureBackupWindowsWorkload** extensão tem de estar instalada nessa máquina virtual. Se receber o erro **UserErrorSQLNoSysadminMembership**, significa que a instância de SQL não tem as permissões de cópia de segurança necessárias. Para corrigir este erro, siga os passos em [defina permissões para as VMs do SQL externas](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

## <a name="troubleshooting-errors"></a>Resolução de problemas de erros

Utilize as informações nas tabelas seguintes para resolver problemas e erros encontrados ao proteger o SQL Server para o Azure.

## <a name="alerts"></a>Alertas

### <a name="backup-type-unsupported"></a>Tipo de cópia de segurança não suportado

| Gravidade | Descrição | Causas possíveis | Ação recomendada |
|---|---|---|---|
| Aviso | As definições atuais desta base de dados não suportam a determinado tipo de tipos de cópia de segurança presentes na política de associados. | <li>**DB de dominar**: Apenas uma operação de cópia de segurança completa da base de dados pode ser executada na base de dados mestra; nem **diferencial** cópia de segurança nem transação **registos** cópia de segurança é possível. </li> <li>Qualquer base de dados **modelo de recuperação simples** não permite a transação **registos** cópia de segurança a tomar.</li> | Modifica as definições de base de dados, de modo a que todos os tipos de cópia de segurança na política são suportados. Em alternativa, altere a política atual para incluir apenas os tipos de cópia de segurança suportados. Caso contrário, os tipos de cópia de segurança não suportados serão ignorados durante a cópia de segurança agendada ou a tarefa de cópia de segurança irá falhar para cópia de segurança ad hoc.


## <a name="backup-failures"></a>Falhas de cópia de segurança

As tabelas a seguir são organizadas por código de erro.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Esta base de dados SQL não suporta o tipo de cópia de segurança solicitado. | Ocorre quando o modelo de recuperação de base de dados não permite que o tipo de cópia de segurança solicitado. O erro pode ocorrer nas seguintes situações: <br/><ul><li>Uma base de dados usando um modelo de recuperação simples não permite a cópia de segurança do registo.</li><li>Backups de log e diferenciais não são permitidas para um modelo da base de dados.</li></ul>Para obter mais detalhes, consulte a [modelos de recuperação do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) documentação. | Se falhar a cópia de segurança do registo para o banco de dados no modelo de recuperação simples, experimente uma destas opções:<ul><li>Se a base de dados está no modo de recuperação simples, desative as cópias de segurança do registo.</li><li>Utilize o [documentação do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação de base de dados para completo ou registadas em massa. </li><li> Se não pretender alterar o modelo de recuperação e tem uma política padrão para fazer cópias de segurança de várias bases de dados que não podem ser alteradas, ignore o erro. As cópias de segurança completas e diferenciais irão funcionar por agendamento. Os backups de log serão ignorados, que é esperado neste caso.</li></ul>Se for um mestre de base de dados e tiver configurado diferenciais ou registo de utilização de cópia de segurança, qualquer um dos seguintes passos:<ul><li>Utilize o portal para alterar o agendamento de política de cópia de segurança para o mestre de bases de dados, como Full.</li><li>Se tiver uma política padrão para fazer cópias de segurança de várias bases de dados que não podem ser alteradas, ignore o erro. A cópia de segurança completa irá funcionar por agendamento. Cópias de segurança diferenciais ou log não acontecem, o que é esperado neste caso.</li></ul> |
| Operação cancelada como uma operação em conflito já estava em execução no mesmo banco de dados. | Consulte a [entrada de blogue sobre cópia de segurança e restaurar limitações](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) executados simultaneamente.| [Utilize o SQL Server Management Studio (SSMS) para monitorizar as tarefas de cópia de segurança.](manage-monitor-sql-database-backup.md) Assim que a operação em conflito falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Base de dados SQL não existe. | A base de dados foi eliminado ou renomeado. | Verifique se a base de dados foi acidentalmente eliminado ou renomeado.<br/><br/> Se a base de dados foi eliminado acidentalmente, para continuar a cópias de segurança, restaure a base de dados para a localização original.<br/><br/> Se eliminar a base de dados e não precisa de futuras cópias de segurança, em seguida, no cofre dos serviços de recuperação, clique em [parar cópia de segurança com "Delete/manter dados"](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Cadeia de registos está quebrada. | A base de dados ou a VM é feita com a solução de cópia de segurança outro, que trunca a cadeia de registos.|<ul><li>Verifique se a outra solução de cópia de segurança ou de script está a ser utilizado. Se assim for, pare a solução de cópia de segurança. </li><li>Se a cópia de segurança foi uma cópia de segurança do registo ad hoc, acione uma cópia de segurança completa para iniciar uma nova cadeia de registos. Para backups de log agendadas, é necessária nenhuma ação como o serviço de cópia de segurança do Azure irá acionar automaticamente uma cópia de segurança completa para corrigir este problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| O Azure Backup não é possível estabelecer ligação à instância do SQL. | O Azure Backup não é possível ligar à instância do SQL. | Utilize os detalhes adicionais no menu do erro portal do Azure para restringir as causas de raiz. Consulte a [resolução de problemas cópia de segurança do SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as predefinições de SQL não permitir ligações remotas, altere as definições. Consulte as ligações para alterar as definições abaixo.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se existirem problemas de início de sessão, consulte as ligações para corrigi-lo abaixo:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Primeiro backup completo está em falta para esta origem de dados. | Cópia de segurança completa está em falta para a base de dados. Log e diferenciais principal de cópias de segurança para uma cópia de segurança completa, pelo que devem tomar antes de acionar diferenciais de cópias de segurança completas ou backups de log. | Acione uma cópia de segurança completa ad hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| Não é possível efetuar cópia de segurança, como o log de transação para a origem de dados está cheio. | O espaço do registo transacional de base de dados está cheio. | Para corrigir este problema, consulte a [documentação do SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Esta base de dados SQL não suporta o tipo de cópia de segurança solicitado. | Réplicas secundárias do sempre em AG não suportam cópias de segurança completas e diferenciais. | <ul><li>Se acionada uma cópia de segurança ad hoc, acione as cópias de segurança no nó principal.</li><li>Se a cópia de segurança foi agendada pela política, certifique-se de que o nó principal está registado. Para registar o nó [siga os passos para detetar uma base de dados do SQL Server](backup-sql-server-database-azure-vms.md#discover-sql-server-databases).</li></ul> |

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

## <a name="configure-backup-failures"></a>Configurar a falhas de cópia de segurança

O seguinte erro são códigos para configurar falhas de cópia de segurança.

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Mensagem de erro | Causas possíveis | Ação recomendada |
|---|---|---|
| A intenção de Autoproteção foi removida ou é não é mais válida. | Ao ativar a proteção automática numa instância SQL **configurar a cópia de segurança** tarefas são executadas para todas as bases de dados nessa instância. Se desativar a proteção automática, enquanto as tarefas estiverem em execução, o **em curso** tarefas são canceladas com este código de erro. | Ative a proteção automática mais uma vez proteger todas as bases de dados restantes. |

## <a name="re-registration-failures"></a>Falhas de re-registo

Procurar um ou mais da [sintomas](#symptoms) antes de acionar a operação de voltar a registar.

### <a name="symptoms"></a>Sintomas

* Todas as operações, tais como cópia de segurança, restaurar e configurar a cópia de segurança estão a falhar na VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* O **estado de cópia de segurança** para a cópia de segurança está mostrando o item **não está acessível**. Embora deve descartar todas as outras razões que também poderão resultar no mesmo Estado:

  * Falta de permissão para efetuar a cópia de segurança relacionados com operações na VM  
  * VM foi encerrada devido a que as cópias de segurança não podem ter lugar
  * Problemas de rede  

    ![Voltar a registar a VM](./media/backup-azure-sql-database/re-register-vm.png)

* No caso de sempre no grupo de disponibilidade, as cópias de segurança começou a falhar depois alterado a preferência de cópia de segurança ou quando ocorreu uma ativação pós-falha

### <a name="causes"></a>Causas
Esses sintomas podem surgir devido a um ou mais dos seguintes motivos:

  * Extensão foi eliminada ou desinstalada a partir do portal 
  * Extensão foi desinstalada dos **painel de controlo** da VM sob **desinstalar ou alterar um programa** interface do Usuário
  * VM foi restaurada para trás no tempo através de restauro de discos no local
  * VM foi encerrada durante um período prolongado devido a que a configuração da extensão na mesma expirou
  * A VM foi eliminada e a outra VM foi criada com o mesmo nome e no mesmo grupo de recursos da VM eliminada
  * Um de nós AG não recebeu a configuração de cópia de segurança completa, isto pode acontecer uma no momento do registo de grupo de disponibilidade para o cofre ou quando é adicionado um novo nó  <br>
    Nos cenários acima, é recomendado para acionar a operação de voltar a registar na VM. Esta opção só está disponível através do PowerShell e estará brevemente disponível no portal do Azure também.


## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure Backup para VMs do SQL Server (pré-visualização pública), consulte [cópia de segurança do Azure para VMs do SQL (pré-visualização pública)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
