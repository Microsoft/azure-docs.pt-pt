---
title: Restaurar uma base de dados a partir de uma cópia de segurança
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba sobre a restauração pontual, que lhe permite reverter uma base de dados Azure SQL ou uma base de dados Azure SQL Managed Instance até 35 dias.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: 45981322eed2828bc2beb12a8cf16b29fba7b918
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046566"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Recuperar utilizando backups de base de dados automatizados - Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Por predefinição, as cópias de segurança Azure SQL Database e Azure SQL Managed Instance são armazenadas em armazenamento de blob geo-replicado (tipo de armazenamento RA-GRS). As seguintes opções estão disponíveis para a recuperação da base de dados utilizando cópias de [dados automatizadas](automated-backups-overview.md). Pode:

- Crie uma nova base de dados no mesmo servidor, recuperada num determinado ponto no tempo dentro do período de retenção.
- Crie uma base de dados no mesmo servidor, recuperada até ao tempo de eliminação de uma base de dados eliminada.
- Crie uma nova base de dados em qualquer servidor da mesma região, recuperada ao ponto das cópias de segurança mais recentes.
- Crie uma nova base de dados em qualquer servidor em qualquer outra região, recuperada ao ponto das mais recentes cópias de segurança replicadas.

Se configurar a retenção a [longo prazo,](long-term-retention-overview.md)também pode criar uma nova base de dados a partir de qualquer cópia de segurança de retenção a longo prazo em qualquer servidor.

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
> Para recuperar utilizando cópias de segurança automatizadas, deve ser membro da função de Colaborador do Servidor SQL ou da função de Colaborador de Instância Gerida sQL (dependendo do destino de recuperação) na subscrição, ou ser o proprietário da subscrição. Para mais informações, consulte [As funções RBAC: Incorporadas.](../../role-based-access-control/built-in-roles.md) Pode recuperar utilizando o portal Azure, PowerShell ou a Rest API. Não podes usar a Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma base de dados autónoma, reunida ou por exemplo para um ponto de situação anterior, utilizando o portal Azure, [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou a [Rest API](https://docs.microsoft.com/rest/api/sql/databases). O pedido pode especificar qualquer nível de serviço ou tamanho de cálculo para a base de dados restaurada. Certifique-se de que dispõe de recursos suficientes no servidor para o qual está a restaurar a base de dados. Quando concluído, o restauro cria uma nova base de dados no mesmo servidor que a base de dados original. A base de dados restaurada é cobrada a taxas normais, com base no seu nível de serviço e no tamanho da computação. Não incorre em encargos até que a base de dados esteja completa.

Geralmente, restaura uma base de dados para um ponto anterior para fins de recuperação. Pode tratar a base de dados restaurada como substituto da base de dados original ou utilizá-la como fonte de dados para atualizar a base de dados original.

- **Substituição da base de dados**

  Se pretender que a base de dados restaurada seja um substituto para a base de dados original, deve especificar o tamanho e o nível de serviço da base de dados original. Em seguida, pode renomear a base de dados original e dar à base de dados restaurada o nome original utilizando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) em T-SQL.

- **Recuperação de dados**

  Se planeia recuperar dados da base de dados restaurada para recuperar de um erro de utilizador ou aplicação, tem de escrever e executar um script de recuperação de dados que extrai dados da base de dados restaurada e se aplica à base de dados original. Embora a operação de restauro possa demorar muito tempo a ser concluída, a base de dados de restauro é visível na lista de bases de dados durante todo o processo de restauro. Se apagar a base de dados durante a restauração, a operação de restauro será cancelada e não será cobrado pela base de dados que não tenha concluído a restauração.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauro pontual utilizando portal Azure

Pode recuperar uma base de dados única ou instância a um ponto de tempo a partir da lâmina de visão geral da base de dados que pretende restaurar no portal Azure.

#### <a name="sql-database"></a>Base de Dados SQL

Para recuperar uma única ou agritada base de dados a um ponto de tempo utilizando o portal Azure, abra a página de visão geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup ponto-a-tempo a partir do qual será criada uma nova base de dados.

  ![Screenshot das opções de restauro da base de dados](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para recuperar uma base de dados de instância gerida a um ponto do tempo utilizando o portal Azure, abra a página de visão geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha o ponto de apoio pontual a partir do qual será criada uma nova base de dados.

  ![Screenshot das opções de restauro da base de dados](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programáticamente uma base de dados a partir de uma cópia de segurança, consulte a [recuperação programática utilizando cópias de segurança automatizadas](recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Restauro de base de dados eliminada

Pode restaurar uma base de dados eliminada no tempo de eliminação, ou um ponto no tempo anterior, no mesmo servidor ou na mesma instância gerida. Pode fazê-lo através do portal Azure, [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)ou do [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Restaurá-lo com uma base de dados eliminada criando uma nova base de dados a partir do backup.

> [!IMPORTANT]
> Se eliminar um servidor ou uma instância gerida, todas as suas bases de dados também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado ou uma instância gerida.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restauração da base de dados eliminada utilizando o portal Azure

Restaurá-lo com bases de dados apagadas do portal Azure a partir do servidor ou no recurso de instância gerido.

#### <a name="sql-database"></a>Base de Dados SQL

Para recuperar uma única ou agrofada base de dados eliminada para o tempo de eliminação utilizando o portal Azure, abra a página de visão geral do servidor e selecione bases de **dados eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e escreva o nome para a nova base de dados que será criada com os dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados eliminada de restaurar](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para recuperar uma base de dados gerida utilizando o portal Azure, abra a página de visão geral da instância gerida e selecione bases de **dados Eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e escreva o nome para a nova base de dados que será criada com os dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados de instância sql do Restauro eliminada](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restaurar a base de dados eliminada utilizando o PowerShell

Utilize as seguintes scripts de amostra para restaurar uma base de dados eliminada para base de dados SQL ou SQL Managed Instance utilizando powerShell.

#### <a name="sql-database"></a>Base de Dados SQL

Para obter uma amostra de script PowerShell que mostre como restaurar uma base de dados eliminada na Base de Dados Azure SQL, consulte [Restaurar uma base de dados SQL utilizando powerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para obter uma amostra de script PowerShell que mostre como restaurar uma base de dados de instâncias eliminada, consulte restaurar a base de [dados de instâncias eliminadas utilizando o PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Para restaurar programáticamente uma base de dados eliminada, consulte a [recuperação programática utilizando cópias de segurança automatizadas](recovery-using-backups.md).

## <a name="geo-restore"></a>Georrestauro

Pode restaurar uma base de dados em qualquer servidor de base de dados SQL ou uma base de dados de instância em qualquer instância gerida em qualquer região azure a partir das cópias de segurança geo-replicadas mais recentes. A Geo-restore usa uma cópia de segurança geo-replicada como fonte. Pode solicitar a geo-restauração mesmo que a base de dados ou datacenter esteja inacessível devido a uma falha.

A geo-restauração é a opção de recuperação padrão quando a sua base de dados está indisponível devido a um incidente na região de acolhimento. Pode restaurar a base de dados para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é geo-replicado para uma bolha Azure em uma região diferente. Como resultado, a base de dados restaurada pode estar até uma hora atrás da base de dados original. A ilustração seguinte mostra uma base de dados restaurada do último backup disponível em outra região.

![Gráfico de geo-restauro](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-restauro utilizando o portal Azure

A partir do portal Azure, cria uma nova base de dados de instâncias únicaou gerida e seleciona uma cópia de segurança de georestauro disponível. A base de dados recém-criada contém os dados de backup georestaurados.

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma única base de dados SQL do portal Azure na região e servidor à sua escolha, siga estes passos:

1. A partir **do Dashboard,** selecione **Criar**base  >  **de dados SQL**. No separador Basics, introduza as informações **necessárias.**
2. Selecione **definições adicionais**.
3. Para **utilizar os dados existentes,** selecione **Backup**.
4. Para **backup,** selecione uma cópia de segurança da lista de cópias de segurança de georestauro disponíveis.

    ![Screenshot das opções de base de dados SQL Create](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Complete o processo de criação de uma nova base de dados a partir do backup. Quando cria uma base de dados na Base de Dados Azure SQL, contém a cópia de segurança restaurada.

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para restaurar uma base de dados de instância gerida do portal Azure para uma instância gerida existente numa região à sua escolha, selecione uma instância gerida na qual pretende restaurar uma base de dados. Siga estes passos.

1. Selecione **Nova base de dados**.
2. Digite um nome de base de dados desejado.
3. Em **utilizar os dados existentes,** selecione **Backup**.
4. Selecione uma cópia de segurança da lista de cópias de segurança de georestauro disponíveis.

    ![Screenshot de novas opções de base de dados](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Complete o processo de criação de uma nova base de dados. Quando cria a base de dados de instâncias, contém a cópia de segurança restaurada.

### <a name="geo-restore-by-using-powershell"></a>Geo-restaurar usando powerShell

#### <a name="sql-database"></a>Base de Dados SQL

Para um script PowerShell que mostre como executar geo-restauro para uma única base de dados SQL, consulte use PowerShell para restaurar uma base de [dados única Azure SQL para um ponto de tempo anterior](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para um script PowerShell que mostre como realizar geo-restauro para uma base de dados de instância gerida, consulte [use PowerShell para restaurar uma base de dados de instância gerida para outra geo-região](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações de geo-restauro

Não é possível fazer um restauro pontual numa base de dados geosecundária. Só pode fazê-lo numa base de dados primária. Para obter informações detalhadas sobre o uso de geo-restauração para recuperar de uma paragem, consulte [Recuperar de uma paragem](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> A geo-restauração é a solução de recuperação de desastres mais básica disponível na Base de Dados SQL e na Instância Gerida SQL. Baseia-se em backups geo-replicados automaticamente com objetivo de ponto de recuperação (RPO) igual a 1 hora, e o tempo estimado de recuperação de até 12 horas. Não garante que a região alvo tenha a capacidade de restaurar as suas bases de dados após uma paragem regional, porque é provável um aumento acentuado da procura. Se a sua aplicação utilizar bases de dados relativamente pequenas e não for crítica para o negócio, a geo-restauração é uma solução adequada para a recuperação de desastres. Para aplicações críticas ao negócio que requerem grandes bases de dados e devem garantir a continuidade do negócio, utilize [grupos de falha automática](auto-failover-group-overview.md). Oferece um rpo muito mais baixo e objetivo de tempo de recuperação, e a capacidade é sempre garantida. Para obter mais informações sobre as escolhas de continuidade do negócio, consulte [a visão geral da continuidade do negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Execução programática da recuperação utilizando backups automatizados

Também pode utilizar o Azure PowerShell ou a API REST para recuperação. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela SQL Database e pela SQL Managed Instance, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são, em grande medida, idênticos.

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma base de dados autónoma ou reunida, consulte [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restaurar-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados SQL. |

  > [!TIP]
  > Para obter uma amostra de script PowerShell que mostre como executar um restauro pontual de uma base de dados, consulte Restaurar uma base de [dados SQL utilizando powerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

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

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma base de dados única ou agrinada utilizando o Azure CLI, consulte [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para restaurar uma base de dados de instância gerida utilizando o Azure CLI, consulte [az sql midb restaurar](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

As cópias de segurança automáticas protegem as suas bases de dados de erros de utilizador e aplicação, eliminação acidental da base de dados e interrupções prolongadas. Esta capacidade incorporada está disponível para todos os níveis de serviço e tamanhos de computação.

## <a name="next-steps"></a>Próximos passos

- [Visão geral da continuidade do negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Backups automatizados sQL Database](automated-backups-overview.md)
- [Retenção de longa duração](long-term-retention-overview.md)
- Para aprender sobre opções de recuperação mais rápidas, consulte grupos de [geo-replicação ativa](active-geo-replication-overview.md) ou [de falha automática](auto-failover-group-overview.md).
