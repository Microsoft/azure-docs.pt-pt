---
title: Restaurar uma base de dados a partir de uma cópia de segurança
description: Saiba sobre a restauração pontual, que lhe permite reverter uma base de dados Azure SQL até 35 dias.
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
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194492"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Recuperar uma base de dados Azure SQL utilizando cópias de dados automatizadas

Por padrão, as cópias de segurança da Base de Dados Azure SQL são armazenadas em armazenamento de blob geo-replicado (tipo de armazenamento RA-GRS). As seguintes opções estão disponíveis para a recuperação da base de dados utilizando cópias de [dados automatizadas](sql-database-automated-backups.md). Pode:

- Crie uma nova base de dados no mesmo servidor de base de dados SQL, recuperado num determinado ponto no tempo dentro do período de retenção.
- Crie uma base de dados no mesmo servidor de base de dados SQL, recuperado ao tempo de eliminação de uma base de dados eliminada.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL na mesma região, recuperado ao ponto das cópias de segurança mais recentes.
- Crie uma nova base de dados em qualquer servidor de base de dados SQL em qualquer outra região, recuperado ao ponto das cópias de segurança replicadas mais recentes.

Se configurar a retenção a [longo prazo,](sql-database-long-term-retention.md)também pode criar uma nova base de dados a partir de qualquer cópia de segurança de retenção a longo prazo em qualquer servidor de base de dados SQL.

> [!IMPORTANT]
> Não pode substituir uma base de dados existente durante o restauro.

Quando estiver a utilizar os níveis de serviço Standard ou Premium, a sua base de dados de restauro pode incorrer num custo extra de armazenamento. O custo extra é incorrido quando o tamanho máximo da base de dados restaurada é maior do que a quantidade de armazenamento incluída com o nível de serviço e o nível de desempenho da base de dados-alvo. Para obter detalhes sobre o armazenamento extra, consulte a página de preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for inferior à quantidade de armazenamento incluída, pode evitar este custo extra, fixando o tamanho máximo da base de dados para o valor incluído.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar uma base de dados utilizando cópias de dados automatizadas é afetado por vários fatores:

- O tamanho da base de dados.
- O tamanho da base de dados.
- O número de registos de transações envolvidos.
- A quantidade de atividade que precisa de ser reproduzida para recuperar até ao ponto de restauro.
- A largura de banda da rede se o restauro for para uma região diferente.
- O número de pedidos de restauro simultâneos que estão a ser processados na região-alvo.

Para uma base de dados grande ou muito ativa, o restauro pode demorar várias horas. Se houver uma paragem prolongada numa região, é possível que um elevado número de pedidos de geo-restauro sejam iniciados para a recuperação de desastres. Quando há muitos pedidos, o tempo de recuperação das bases de dados individuais pode aumentar. A maioria da base de dados restaura em menos de 12 horas.

Para uma única subscrição, existem limitações no número de pedidos de restauro simultâneos. Estas limitações aplicam-se a qualquer combinação de restauros ponto-a-tempo, geo-restauros e restauros a partir de backup de retenção a longo prazo.

| | **Max # de pedidos simultâneos sendo processados** | **Max # de pedidos simultâneos sendo submetidos** |
| :--- | --: | --: |
|Base de dados única (por subscrição)|10|60|
|Piscina elástica (por piscina)|4|200|
||||

Não há um método incorporado para restaurar todo o servidor. Para um exemplo de como realizar esta tarefa, consulte a Base de [Dados Azure SQL: Recuperação completa do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Para recuperar utilizando cópias de segurança automatizadas, deve ser membro da função de colaborador do SQL Server na subscrição ou ser o proprietário da subscrição. Para mais informações, consulte [As funções RBAC: Incorporadas.](../role-based-access-control/built-in-roles.md) Pode recuperar utilizando o portal Azure, PowerShell ou a Rest API. Não podes usar a Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma base de dados autónoma, reunida ou por exemplo para um ponto de situação anterior, utilizando o portal Azure, [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou a [Rest API](https://docs.microsoft.com/rest/api/sql/databases). O pedido pode especificar qualquer nível de serviço ou tamanho de cálculo para a base de dados restaurada. Certifique-se de que dispõe de recursos suficientes no servidor para o qual está a restaurar a base de dados. Quando concluído, o restauro cria uma nova base de dados no mesmo servidor que a base de dados original. A base de dados restaurada é cobrada a taxas normais, com base no seu nível de serviço e no tamanho da computação. Não incorre em encargos até que a base de dados esteja completa.

Geralmente, restaura uma base de dados para um ponto anterior para fins de recuperação. Pode tratar a base de dados restaurada como substituto da base de dados original ou utilizá-la como fonte de dados para atualizar a base de dados original.

- **Substituição da base de dados**

  Se pretender que a base de dados restaurada seja um substituto para a base de dados original, deve especificar o tamanho e o nível de serviço da base de dados original. Em seguida, pode renomear a base de dados original e dar à base de dados restaurada o nome original utilizando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) em T-SQL.

- **Recuperação de dados**

  Se planeia recuperar dados da base de dados restaurada para recuperar de um erro de utilizador ou aplicação, tem de escrever e executar um script de recuperação de dados que extrai dados da base de dados restaurada e se aplica à base de dados original. Embora a operação de restauro possa demorar muito tempo a ser concluída, a base de dados de restauro é visível na lista de bases de dados durante todo o processo de restauro. Se apagar a base de dados durante a restauração, a operação de restauro será cancelada e não será cobrado pela base de dados que não tenha concluído a restauração.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauro pontual utilizando portal Azure

Pode recuperar uma única base de dados SQL ou uma base de dados de instância a um ponto no tempo a partir da lâmina de visão geral da base de dados que pretende restaurar no portal Azure.

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para recuperar uma única ou agritada base de dados a um ponto de tempo utilizando o portal Azure, abra a página de visão geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup ponto-a-tempo a partir do qual será criada uma nova base de dados. 

  ![Screenshot das opções de restauro da base de dados](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para recuperar uma base de dados de instância gerida a um ponto do tempo utilizando o portal Azure, abra a página de visão geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha o ponto de apoio pontual a partir do qual será criada uma nova base de dados. 

  ![Screenshot das opções de restauro da base de dados](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programáticamente uma base de dados a partir de uma cópia de segurança, consulte a [recuperação programática utilizando cópias de segurança automatizadas](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauro de base de dados eliminada

Pode restaurar uma base de dados eliminada no tempo de eliminação, ou um ponto no tempo anterior, no mesmo servidor de base de dados SQL ou na mesma instância gerida. Pode fazê-lo através do portal Azure, [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou do [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Restaurá-lo com uma base de dados eliminada criando uma nova base de dados a partir do backup.

> [!IMPORTANT]
> Se eliminar um servidor de base de dados Azure SQL ou uma instância gerida, todas as suas bases de dados também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado ou uma instância gerida.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauração da base de dados eliminada utilizando o portal Azure

Restaurá-lo com bases de dados apagadas do portal Azure a partir do servidor e do recurso de instância.

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para recuperar uma única ou agrofada base de dados eliminada para o tempo de eliminação utilizando o portal Azure, abra a página de visão geral do servidor e selecione bases de **dados eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e escreva o nome para a nova base de dados que será criada com os dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados Azure SQL eliminada](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para recuperar uma base de dados gerida utilizando o portal Azure, abra a página de visão geral da instância gerida e selecione bases de **dados Eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e escreva o nome para a nova base de dados que será criada com os dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados de instância sql do Restauro eliminada](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restaurar a base de dados eliminada utilizando o PowerShell

Utilize as seguintes scripts de amostra para restaurar uma base de dados eliminada para a Base de Dados Azure SQL e uma instância gerida utilizando o PowerShell.

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para obter uma amostra de script PowerShell que mostre como restaurar uma base de dados SQL eliminada, consulte [Restaurar uma base de dados SQL utilizando powerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para obter uma amostra de script PowerShell que mostre como restaurar uma base de dados de instâncias eliminadas, consulte restaurar a base de [dados eliminada em instância gerida utilizando o PowerShell](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Para restaurar programáticamente uma base de dados eliminada, consulte a [recuperação programática utilizando cópias de segurança automatizadas](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Georrestauro

Pode restaurar uma base de dados SQL em qualquer servidor em qualquer região do Azure a partir das mais recentes cópias de segurança geo-replicadas. A Geo-restore usa uma cópia de segurança geo-replicada como fonte. Pode solicitar a geo-restauração mesmo que a base de dados ou datacenter esteja inacessível devido a uma falha.

A geo-restauração é a opção de recuperação padrão quando a sua base de dados está indisponível devido a um incidente na região de acolhimento. Pode restaurar a base de dados para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é geo-replicado para uma bolha Azure em uma região diferente. Como resultado, a base de dados restaurada pode estar até uma hora atrás da base de dados original. A ilustração seguinte mostra uma base de dados restaurada do último backup disponível em outra região.

![Gráfico de geo-restauro](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-restauro utilizando o portal Azure

A partir do portal Azure, cria uma nova base de dados de instâncias únicaou gerida e seleciona uma cópia de segurança de georestauro disponível. A base de dados recém-criada contém os dados de backup georestaurados.

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para restaurar uma única base de dados SQL do portal Azure na região e servidor à sua escolha, siga estes passos:

1. A partir **do Dashboard,** selecione **Adicionar** > Criar base de **dados SQL**. No separador Basics, introduza as informações **necessárias.**
2. Selecione **definições adicionais**.
3. Para **utilizar os dados existentes,** selecione **Backup**.
4. Para **backup,** selecione uma cópia de segurança da lista de cópias de segurança de georestauro disponíveis.

    ![Screenshot das opções de base de dados SQL Create](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Complete o processo de criação de uma nova base de dados a partir do backup. Ao criar a única base de dados Azure SQL, contém a cópia de segurança restaurada.

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para restaurar uma base de dados de instância gerida do portal Azure para uma instância gerida existente numa região à sua escolha, selecione uma instância gerida na qual pretende restaurar uma base de dados. Siga estes passos.

1. Selecione **Nova base de dados**.
2. Digite um nome de base de dados desejado.
3. Em **utilizar os dados existentes,** selecione **Backup**.
4. Selecione uma cópia de segurança da lista de cópias de segurança de georestauro disponíveis.

    ![Screenshot de novas opções de base de dados](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Complete o processo de criação de uma nova base de dados. Quando cria a base de dados de instâncias, contém a cópia de segurança restaurada.

### <a name="geo-restore-by-using-powershell"></a>Geo-restaurar usando powerShell

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para um script PowerShell que mostre como executar geo-restauro para uma única base de dados SQL, consulte use PowerShell para restaurar uma base de [dados única Azure SQL para um ponto de tempo anterior](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para um script PowerShell que mostre como realizar geo-restauro para uma base de dados de instância gerida, consulte [use PowerShell para restaurar uma base de dados de instância gerida para outra geo-região](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações de geo-restauro

Não é possível fazer um restauro pontual numa base de dados geosecundária. Só pode fazê-lo numa base de dados primária. Para obter informações detalhadas sobre o uso de geo-restauração para recuperar de uma paragem, consulte [Recuperar de uma paragem](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A geo-restauração é a solução de recuperação de desastres mais básica disponível na Base de Dados SQL. Baseia-se em backups geo-replicados automaticamente com objetivo de ponto de recuperação (RPO) igual a 1 hora, e o tempo estimado de recuperação de até 12 horas. Não garante que a região alvo tenha a capacidade de restaurar as suas bases de dados após uma paragem regional, porque é provável um aumento acentuado da procura. Se a sua aplicação utilizar bases de dados relativamente pequenas e não for crítica para o negócio, a geo-restauração é uma solução adequada para a recuperação de desastres. Para aplicações críticas ao negócio que requerem grandes bases de dados e devem garantir a continuidade do negócio, utilize [grupos de falha automática](sql-database-auto-failover-group.md). Oferece um rpo muito mais baixo e objetivo de tempo de recuperação, e a capacidade é sempre garantida. Para obter mais informações sobre as escolhas de continuidade do negócio, consulte [a visão geral da continuidade do negócio.](sql-database-business-continuity.md)

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Execução programática da recuperação utilizando backups automatizados

Também pode utilizar o Azure PowerShell ou a API REST para recuperação. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são, em grande medida, idênticos.

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para restaurar uma base de dados autónoma ou reunida, consulte [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restaurar-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para obter uma amostra de script PowerShell que mostre como executar um restauro pontual de uma base de dados, consulte Restaurar uma base de [dados SQL utilizando powerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para restaurar uma base de dados de instância gerida, consulte [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém um ou mais casos geridos. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Tem uma base de dados de instâncias. |
  | [Restaurar-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura uma base de dados de instâncias. |

### <a name="rest-api"></a>API REST

Para restaurar uma única ou agr0eda base de dados utilizando a API REST:

| API | Descrição |
| --- | --- |
| [REPOUSO (criarMode=Recuperação)](https://docs.microsoft.com/rest/api/sql/databases) |Restaura uma base de dados. |
| [Obter criar ou atualizar estado da base de dados](https://docs.microsoft.com/rest/api/sql/operations) |Devolve o estado durante uma operação de restauro. |

### <a name="azure-cli"></a>CLI do Azure

#### <a name="single-azure-sql-database"></a>Base de dados SQL Azure Única

Para restaurar uma base de dados única ou agrinada utilizando o Azure CLI, consulte [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Base de dados de instâncias geridas

Para restaurar uma base de dados de instância gerida utilizando o Azure CLI, consulte [az sql midb restaurar](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

As cópias de segurança automáticas protegem as suas bases de dados de erros de utilizador e aplicação, eliminação acidental da base de dados e interrupções prolongadas. Esta capacidade incorporada está disponível para todos os níveis de serviço e tamanhos de computação.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da continuidade do negócio](sql-database-business-continuity.md)
- [Backups automatizados sQL Database](sql-database-automated-backups.md)
- [Retenção de longa duração](sql-database-long-term-retention.md)
- Para aprender sobre opções de recuperação mais rápidas, consulte grupos de [geo-replicação ativa](sql-database-active-geo-replication.md) ou [de falha automática](sql-database-auto-failover-group.md).
