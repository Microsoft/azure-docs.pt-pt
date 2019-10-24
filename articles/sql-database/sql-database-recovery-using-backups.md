---
title: Restaurar um banco de dados SQL do Azure de um backup | Microsoft Docs
description: Saiba mais sobre a restauração pontual, que permite reverter um banco de dados SQL do Azure até 35 dias.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b858776d8309be94a0dd64f994a9e34e589d3c49
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750465"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados

Por padrão, os backups do banco de dados SQL do Azure são armazenados no armazenamento de BLOBs com replicação geográfica (tipo de armazenamento RA-GRS). As opções a seguir estão disponíveis para recuperação de banco de dados usando [backups de banco de dados automatizados](sql-database-automated-backups.md). Pode:

- Crie um novo banco de dados no mesmo servidor de banco de dados SQL, recuperado para um momento determinado dentro do período de retenção.
- Crie um banco de dados no mesmo servidor do banco de dados SQL, recuperado para o momento da exclusão de um banco de dados excluído.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL na mesma região, recuperado para o ponto dos backups mais recentes.
- Crie um novo banco de dados em qualquer servidor de banco de dados SQL em qualquer outra região, recuperado para o ponto dos backups replicados mais recentes.

Se você configurou o [backup de retenção de longo prazo](sql-database-long-term-retention.md), também poderá criar um novo banco de dados de qualquer backup de retenção de longo prazo em qualquer servidor de banco de dados SQL.

> [!IMPORTANT]
> Não é possível substituir um banco de dados existente durante a restauração.

Quando você estiver usando as camadas de serviço Standard ou Premium, a restauração do banco de dados poderá incorrer em um custo de armazenamento extra. O custo extra é incorrido quando o tamanho máximo do banco de dados restaurado é maior do que a quantidade de armazenamento incluída na camada de serviço e no nível de desempenho do banco de dados de destino. Para obter detalhes de preços de armazenamento extra, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor que a quantidade de armazenamento incluída, você poderá evitar esse custo extra definindo o tamanho máximo do banco de dados para o valor incluído.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups de banco de dados automatizados é afetado por vários fatores:

- O tamanho do banco de dados.
- O tamanho de computação do banco de dados.
- O número de logs de transações envolvidos.
- A quantidade de atividade que precisa ser repetida para recuperação no ponto de restauração.
- A largura de banda da rede se a restauração for para uma região diferente.
- O número de solicitações de restauração simultâneas sendo processadas na região de destino.

Para um banco de dados grande ou muito ativo, a restauração pode levar várias horas. Se houver uma interrupção prolongada em uma região, será possível que um número alto de solicitações de restauração geográfica seja iniciado para recuperação de desastre. Quando há muitas solicitações, o tempo de recuperação para bancos de dados individuais pode aumentar. A maioria das restaurações de banco de dados é concluída em menos de 12 horas.

Para uma única assinatura, há limitações no número de solicitações de restauração simultâneas. Essas limitações se aplicam a qualquer combinação de restaurações point-in-time, restaurações geográficas e restaurações do backup de retenção de longo prazo.

| | **Número máximo de solicitações simultâneas sendo processadas** | **Número máximo de solicitações simultâneas sendo enviadas** |
| :--- | --: | --: |
|Banco de dados individual (por assinatura)|10|60|
|Pool elástico (por pool)|4|200|
||||

Não há um método interno para restaurar o servidor inteiro. Para obter um exemplo de como realizar essa tarefa, consulte [banco de dados SQL do Azure: recuperação completa do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deve ser membro da função de colaborador de SQL Server na assinatura ou ser o proprietário da assinatura. Para obter mais informações, consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md). Você pode recuperar usando o portal do Azure, o PowerShell ou a API REST. Você não pode usar o Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Você pode restaurar um banco de dados autônomo, em pool ou de instância para um ponto anterior no tempo usando o portal do Azure, o [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou a [API REST](https://docs.microsoft.com/rest/api/sql/databases). A solicitação pode especificar qualquer camada de serviço ou tamanho de computação para o banco de dados restaurado. Verifique se você tem recursos suficientes no servidor para o qual você está restaurando o banco de dados. Ao concluir, a restauração cria um novo banco de dados no mesmo servidor que o banco de dados original. O banco de dados restaurado é cobrado com tarifas normais, com base em sua camada de serviço e tamanho de computação. Você não incorre em encargos até que a restauração do banco de dados seja concluída.

Geralmente, você restaura um banco de dados para um ponto anterior para fins de recuperação. Você pode tratar o banco de dados restaurado como uma substituição para o banco de dados original ou usá-lo como uma fonte de dados para atualizar o banco de dado original.

- **Substituição de banco de dados**

  Se você pretende que o banco de dados restaurado seja uma substituição para o banco de dados original, especifique o tamanho de computação e a camada de serviço do banco de dados original. Em seguida, você pode renomear o banco de dados original e dar ao banco de dados restaurado o nome original usando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) no T-SQL.

- **Recuperação de dados**

  Se você planeja recuperar dados do banco de dado restaurado para se recuperar de um erro de usuário ou aplicativo, você precisa gravar e executar um script de recuperação de dados que extrai dados do banco de dado restaurado e aplica-se ao banco de dado original. Embora a operação de restauração possa levar muito tempo para ser concluída, o banco de dados de restauração fica visível na lista de banco de dados durante o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados que não concluiu a restauração.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauração pontual usando o portal do Azure

Você pode recuperar um banco de dados SQL individual ou de instância em um ponto no tempo da folha de visão geral do banco de dados que deseja restaurar no portal do Azure.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para recuperar um banco de dados individual ou em pool em um ponto no tempo usando o portal do Azure, abra a página Visão geral do banco de dados e selecione **restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup pontual do qual um novo banco de dados será criado. 

  ![Captura de tela das opções de restauração do banco de dados](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para recuperar um banco de dados de instância gerenciada para um ponto no tempo usando o portal do Azure, abra a página Visão geral do banco de dados e selecione **restaurar** na barra de ferramentas. Escolha o ponto de backup pontual do qual um novo banco de dados será criado. 

  ![Captura de tela das opções de restauração do banco de dados](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programaticamente um banco de dados de um backup, consulte [executando programaticamente a recuperação usando backups automatizados](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluída

Você pode restaurar um banco de dados excluído para a hora de exclusão ou um ponto anterior no tempo, no mesmo servidor de banco de dados SQL ou na mesma instância gerenciada. Você pode fazer isso por meio do portal do Azure, do [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou do [REST (= Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Você restaura um banco de dados excluído criando um novo banco de dados do backup.

> [!IMPORTANT]
> Se você excluir um servidor de banco de dados SQL do Azure ou uma instância gerenciada, todos os seus bancos também serão excluídos e não poderão ser recuperados. Não é possível restaurar um servidor excluído ou uma instância gerenciada.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauração de banco de dados excluída usando o portal do Azure

Você restaura bancos de dados excluídos do portal do Azure do servidor e do recurso de instância.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para recuperar um banco de dados excluído individual ou em pool usando o portal do Azure, abra a página Visão geral do servidor e selecione bancos de dados **excluídos**. Selecione um banco de dados excluído que você deseja restaurar e digite o nome para o novo banco de dados que será criado com a data de restauração do backup.

  ![Captura de tela de opções de bancos de dados excluídos](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Você não pode usar o portal do Azure para restaurar um banco de dados excluído em uma instância gerenciada. Você pode usar o PowerShell para essa finalidade. 

### <a name="deleted-database-restore-by-using-powershell"></a>Restauração de banco de dados excluída usando o PowerShell

Use os scripts de exemplo a seguir para restaurar um banco de dados excluído para o banco de dados SQL do Azure e uma instância gerenciada usando o PowerShell.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para obter um exemplo de script do PowerShell que mostra como restaurar um banco de dados SQL do Azure excluído, consulte [restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para obter um exemplo de script do PowerShell mostrando como restaurar um banco de dados de instância excluído, consulte [restaurar banco de dados excluído na instância gerenciada usando o PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Para restaurar programaticamente um banco de dados excluído, consulte [programaticamente executando a recuperação usando backups automatizados](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Georrestauro

Você pode restaurar um banco de dados SQL em qualquer servidor em qualquer região do Azure dos backups replicados geograficamente mais recentes. A restauração geográfica usa um backup replicado geograficamente como sua origem. Você pode solicitar a restauração geográfica mesmo se o banco de dados ou o datacenter estiver inacessível devido a uma interrupção.

A restauração geográfica é a opção de recuperação padrão quando seu banco de dados não está disponível devido a um incidente na região de hospedagem. Você pode restaurar o banco de dados para um servidor em qualquer outra região. Há um atraso entre o momento em que um backup é feito e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Como resultado, o banco de dados restaurado pode ter até uma hora atrás do banco de dados original. A ilustração a seguir mostra uma restauração de banco de dados do último backup disponível em outra região.

![Gráfico de restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Restauração geográfica usando o portal do Azure

No portal do Azure, você cria um novo banco de dados de instância única ou gerenciada e seleciona um backup de restauração geográfica disponível. O banco de dados criado recentemente contém a data de backup restaurada geograficamente.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para restaurar geograficamente um único banco de dados SQL do portal do Azure na região e no servidor de sua escolha, siga estas etapas:

1. No **painel**, selecione **Adicionar**  > **criar banco de dados SQL**. Na guia **noções básicas** , insira as informações necessárias.
2. Selecione **configurações adicionais**.
3. Para **usar dados existentes**, selecione **backup**.
4. Para **backup**, selecione um backup na lista de backups de restauração geográfica disponíveis.

    ![Captura de tela de criar opções de banco de dados SQL](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Conclua o processo de criação de um novo banco de dados a partir do backup. Quando você cria o banco de dados SQL único do Azure, ele contém o backup de restauração geográfica restaurado.

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para restaurar geograficamente um banco de dados de instância gerenciada do portal do Azure para uma instância gerenciada existente em uma região de sua escolha, selecione uma instância gerenciada na qual você deseja que um banco de dados seja restaurado. Siga estes passos.

1. Selecione **novo banco de dados**.
2. Digite um nome de banco de dados desejado.
3. Em **usar dados existentes**, selecione **backup**.
4. Selecione um backup na lista de backups de restauração geográfica disponíveis.

    ![Captura de tela das novas opções de banco de dados](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Conclua o processo de criação de um novo banco de dados. Quando você cria o banco de dados de instância, ele contém o backup de restauração geográfica restaurado.

### <a name="geo-restore-by-using-powershell"></a>Restauração geográfica usando o PowerShell

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para um script do PowerShell que mostra como executar a restauração geográfica para um único banco de dados SQL, consulte [usar o PowerShell para restaurar um banco de dados individual do SQL do Azure para um ponto anterior no tempo](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para um script do PowerShell que mostra como executar a restauração geográfica para um banco de dados de instância gerenciada, consulte [usar o PowerShell para restaurar um banco de dados de instância gerenciada para outra região geográfica](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações sobre restauração geográfica

Você não pode executar uma restauração pontual em um banco de dados geográfico secundário. Você só pode fazer isso em um banco de dados primário. Para obter informações detalhadas sobre como usar a restauração geográfica para se recuperar de uma interrupção, consulte [recuperar de uma interrupção](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A restauração geográfica é a solução de recuperação de desastre mais básica disponível no banco de dados SQL. Ele se baseia em backups replicados geograficamente criados com RPO (objetivo de ponto de recuperação) igual a 1 hora e o tempo de recuperação estimado de até 12 horas. Ele não garante que a região de destino terá a capacidade de restaurar seus bancos de dados após uma interrupção regional, pois é provável que um aumento preciso da demanda. Se seu aplicativo usa bancos de dados relativamente pequenos e não é essencial para os negócios, a restauração geográfica é uma solução de recuperação de desastre apropriada. Para aplicativos críticos para os negócios que exigem grandes bancos de dados e devem garantir a continuidade dos negócios, use [grupos de failover automático](sql-database-auto-failover-group.md). Ele oferece um RPO muito menor e um objetivo de tempo de recuperação, e a capacidade é sempre garantida. Para obter mais informações sobre as opções de continuidade de negócios, consulte [visão geral da continuidade dos negócios](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Executando programaticamente a recuperação usando backups automatizados

Você também pode usar Azure PowerShell ou a API REST para recuperação. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm têm uma grande extensão idêntica.

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para restaurar um banco de dados autônomo ou em pool, consulte [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para obter um exemplo de script do PowerShell que mostra como executar uma restauração pontual de um banco de dados, consulte [restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para restaurar um banco de dados de instância gerenciada, consulte [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém uma ou mais instâncias gerenciadas. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Obtém um banco de dados de instância. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura um banco de dados de instância. |

### <a name="rest-api"></a>API REST

Para restaurar um banco de dados individual ou em pool usando a API REST:

| API | Descrição |
| --- | --- |
| [REST (= recuperação)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura um banco de dados. |
| [Obter o status de criação ou atualização do banco de dados](https://docs.microsoft.com/rest/api/sql/operations) |Retorna o status durante uma operação de restauração. |

### <a name="azure-cli"></a>CLI do Azure

#### <a name="single-azure-sql-database"></a>Banco de dados SQL do Azure individual

Para restaurar um banco de dados individual ou em pool usando o CLI do Azure, consulte [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Banco de dados de instância gerenciada

Para restaurar um banco de dados de instância gerenciada usando o CLI do Azure, consulte [AZ SQL MidB Restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

Os backups automáticos protegem seus bancos de dados contra erros de usuário e aplicativo, exclusão acidental de banco de dados e interrupções prolongadas. Esse recurso interno está disponível para todas as camadas de serviço e tamanhos de computação.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Backups automatizados do banco de dados SQL](sql-database-automated-backups.md)
- [Retenção de longa duração](sql-database-long-term-retention.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte [replicação geográfica ativa](sql-database-active-geo-replication.md) ou [grupos de failover automático](sql-database-auto-failover-group.md).
