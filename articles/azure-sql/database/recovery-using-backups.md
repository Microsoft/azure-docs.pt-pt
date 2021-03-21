---
title: Restaurar uma base de dados a partir de uma cópia de segurança
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Saiba mais sobre a restauração pontual, o que lhe permite reverter uma base de dados na Base de Dados Azure SQL ou uma instância em Azure SQL Managed Instance até 35 dias.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 0c3db3b3f22f9f2639012068924708537f9ada77
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98795322"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>Recuperar usando cópias de dados automatizadas - Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

As seguintes opções estão disponíveis para a recuperação de bases de dados utilizando [cópias de dados automatizadas](automated-backups-overview.md). Pode:

- Crie uma nova base de dados no mesmo servidor, recuperada a um ponto especificado no tempo dentro do período de retenção.
- Crie uma base de dados no mesmo servidor, recuperada até ao tempo de eliminação de uma base de dados eliminada.
- Crie uma nova base de dados em qualquer servidor da mesma região, recuperada ao ponto das cópias de segurança mais recentes.
- Crie uma nova base de dados em qualquer servidor de qualquer outra região, recuperada ao ponto das cópias de segurança replicadas mais recentes.

Se configurar [a retenção de longo prazo de backup,](long-term-retention-overview.md)também pode criar uma nova base de dados a partir de qualquer cópia de segurança de retenção a longo prazo em qualquer servidor.

> [!IMPORTANT]
> Não pode substituir uma base de dados existente durante a restauração.

Quando estiver a utilizar o nível de serviço Standard ou Premium, a sua restauração da base de dados pode incorrer num custo extra de armazenamento. O custo extra é incorrido quando o tamanho máximo da base de dados restaurada é maior do que a quantidade de armazenamento incluída com o nível de serviço e nível de desempenho da base de dados-alvo. Para obter detalhes sobre preços de armazenamento extra, consulte a [página de preços da Base de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for inferior à quantidade de armazenamento incluída, pode evitar este custo extra definindo o tamanho máximo da base de dados para a quantidade incluída.

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar uma base de dados utilizando cópias de dados automatizadas é afetado por vários fatores:

- O tamanho da base de dados.
- O tamanho do cálculo da base de dados.
- O número de registos de transações envolvidos.
- A quantidade de atividade que precisa de ser reproduzida para recuperar até ao ponto de restauro.
- A largura de banda da rede se o restauro for para uma região diferente.
- O número de pedidos de restauro simultâneos que estão a ser processados na região-alvo.

Para uma base de dados grande ou muito ativa, a restauração pode demorar várias horas. Se houver uma paragem prolongada numa região, é possível que um elevado número de pedidos de geo-restauro seja iniciado para a recuperação de desastres. Quando há muitos pedidos, o tempo de recuperação para bases de dados individuais pode aumentar. A maioria das casas de dados restaura em menos de 12 horas.

Para uma única subscrição, existem limitações no número de pedidos de restauro simultâneos. Estas limitações aplicam-se a qualquer combinação de restauros pontuais, geo-restauros e restauros a partir de backup de retenção a longo prazo.

| **Opção de implementação** | **Max # de pedidos simultâneos sendo processados** | **Max # de pedidos simultâneos sendo submetidos** |
| :--- | --: | --: |
|**Base de dados única (por subscrição)**|30|100|
|**Piscina elástica (por piscina)**|4|2000|


Não há um método incorporado para restaurar todo o servidor. Para um exemplo de como realizar esta tarefa, consulte [a Base de Dados Azure SQL: Recuperação completa do servidor](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Para recuperar utilizando cópias de segurança automatizadas, deve ser membro da função de contribuinte do servidor SQL ou da função de contribuinte de instância gerida sql (dependendo do destino de recuperação) na subscrição, ou deve ser o proprietário da subscrição. Para mais informações, consulte [Azure RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md). Pode recuperar utilizando o portal Azure, PowerShell ou a API REST. Não podes usar o Transact-SQL.

## <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Pode restaurar uma base de dados autónoma, agrizada ou de instância para um ponto anterior no tempo, utilizando o portal Azure, [o PowerShell,](/powershell/module/az.sql/restore-azsqldatabase)ou a [API REST](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.). O pedido pode especificar qualquer nível de serviço ou tamanho de cálculo para a base de dados restaurada. Certifique-se de que dispõe de recursos suficientes no servidor para o qual está a restaurar a base de dados. 

Quando concluído, a restauração cria uma nova base de dados no mesmo servidor que a base de dados original. A base de dados restaurada é carregada a taxas normais, com base no seu nível de serviço e no tamanho do cálculo. Não incorrem em acusações até que a base de dados esteja completa.

Geralmente, devolve-se uma base de dados a um ponto anterior para fins de recuperação. Pode tratar a base de dados restaurada como uma substituição da base de dados original ou usá-la como fonte de dados para atualizar a base de dados original.

- **Substituição da base de dados**

  Se pretender que a base de dados restaurada seja um substituto da base de dados original, deverá especificar o tamanho e o nível de serviço da base de dados original. Em seguida, pode renomear a base de dados original e dar à base de dados restaurada o nome original utilizando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) em T-SQL.

- **Recuperação de dados**

  Se planeia obter dados da base de dados restaurada para recuperar de um erro de utilizador ou aplicação, tem de escrever e executar um script de recuperação de dados que extrai dados da base de dados restaurada e se aplica à base de dados original. Embora a operação de restauro possa demorar muito tempo a ser concluída, a base de dados de restauro está visível na lista de bases de dados durante todo o processo de restauro. Se eliminar a base de dados durante a restauração, a operação de restauro será cancelada e não será cobrado para a base de dados que não tenha concluído a restauração.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Restauro pontual através do portal Azure

Pode recuperar uma base de dados de um único ou caso a um ponto no tempo a partir da lâmina de visão geral da base de dados que pretende restaurar no portal Azure.

#### <a name="sql-database"></a>Base de Dados SQL

Para recuperar uma base de dados até um ponto no tempo, utilizando o portal Azure, abra a página geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha a fonte de backup e selecione o ponto de backup pontual a partir do qual será criada uma nova base de dados.

  ![Screenshot das opções de restauro da base de dados para a Base de Dados SQL.](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para recuperar uma base de dados de instância gerida a um ponto no tempo, utilizando o portal Azure, abra a página geral da base de dados e selecione **Restaurar** na barra de ferramentas. Escolha o ponto de reserva a partir do qual será criada uma nova base de dados.

  ![Screenshot das opções de restauro da base de dados para o exemplo gerido pelo SQL.](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Para restaurar programáticamente uma base de dados a partir de uma cópia de segurança, consulte [a recuperação programática utilizando cópias de segurança automatizadas.](recovery-using-backups.md)

## <a name="deleted-database-restore"></a>Restauração da base de dados eliminada

Pode restaurar uma base de dados eliminada para o tempo de eliminação, ou um ponto anterior no tempo, no mesmo servidor ou na mesma instância gerida. Pode fazê-lo através do portal Azure, [PowerShell,](/powershell/module/az.sql/restore-azsqldatabase)ou do [REST (createMode=Restore)](/rest/api/sql/databases/createorupdate). Restaura uma base de dados eliminada criando uma nova base de dados a partir da cópia de segurança.

> [!IMPORTANT]
> Se eliminar um servidor ou uma instância gerida, todas as suas bases de dados também são eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor apagado ou uma instância gerida.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Restaurada base de dados eliminada utilizando o portal Azure

Restaura bases de dados eliminadas do portal Azure a partir do servidor ou recurso de instância gerido.

> [!TIP]
> Pode levar vários minutos para que as bases de dados recentemente eliminadas apareçam na página **de bases de dados eliminadas** no portal Azure ou ao apresentar bases de dados eliminadas [programáticamente](#programmatic-recovery-using-automated-backups).

#### <a name="sql-database"></a>Base de Dados SQL

Para recuperar uma base de dados eliminada para o tempo de eliminação utilizando o portal Azure, abra a página geral do servidor e selecione **bases de dados eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e digite o nome para a nova base de dados que será criada com dados restaurados a partir da cópia de segurança.

  ![Screenshot da base de dados restaurada](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para recuperar uma base de dados gerida utilizando o portal Azure, abra a página geral de instâncias gerida e selecione **bases de dados eliminadas**. Selecione uma base de dados eliminada que pretende restaurar e digite o nome para a nova base de dados que será criada com dados restaurados a partir da cópia de segurança.

  ![Screenshot da restauração eliminada Azure SQL Gestd Instance database](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Restauro da base de dados eliminada utilizando o PowerShell

Utilize os seguintes scripts de amostra para restaurar uma base de dados eliminada para a Base de Dados SQL ou para a SQL Managed Instance utilizando o PowerShell.

#### <a name="sql-database"></a>Base de Dados SQL

Para obter uma amostra do script PowerShell que mostre como restaurar uma base de dados eliminada na Base de Dados Azure SQL, consulte [Restaurar uma base de dados utilizando o PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para uma amostra do script PowerShell que mostra como restaurar uma base de dados de instâncias eliminada, consulte [a base de dados de instâncias eliminada do Restore usando o PowerShell](../managed-instance/point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Para restaurar programáticamente uma base de dados eliminada, consulte [a recuperação programática utilizando cópias de segurança automatizadas.](recovery-using-backups.md)

## <a name="geo-restore"></a>Georrestauro

> [!IMPORTANT]
> O geo-restauro só está disponível para bases de dados SQL ou instâncias geridas configuradas com armazenamento de [backup](automated-backups-overview.md#backup-storage-redundancy)geo-redundante .

Pode restaurar uma base de dados em qualquer servidor de base de dados SQL ou uma base de dados de instâncias em qualquer instância gerida em qualquer região do Azure a partir das cópias de segurança geo-replicadas mais recentes. Geo-restauração utiliza uma cópia de segurança geo-replicada como fonte. Pode solicitar a geo-restauração mesmo que a base de dados ou o datacenter esteja inacessível devido a uma paragem.

Geo-restauração é a opção de recuperação padrão quando a sua base de dados não está disponível devido a um incidente na região de hospedagem. Pode restaurar a base de dados para um servidor em qualquer outra região. Há um atraso entre quando um backup é tomado e quando é geo-replicado para uma bolha Azure em uma região diferente. Como resultado, a base de dados restaurada pode estar até uma hora atrás da base de dados original. A seguinte ilustração mostra uma recuperação da base de dados a partir da última cópia de segurança disponível noutra região.

![Gráfico de geo-restauro](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-restauro utilizando o portal Azure

A partir do portal Azure, cria uma nova base de dados de instância única ou gerida e seleciona uma cópia de segurança de geo-restauro disponível. A base de dados recentemente criada contém os dados de cópia de segurança geo-restaurados.

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma única base de dados a partir do portal Azure na região e servidor da sua escolha, siga estes passos:

1. A partir do **Painel de Instrumentos**, selecione **Add**  >  **Create SQL Database**. No **separador Básicos, insira** as informações necessárias.
2. Selecione **definições adicionais**.
3. Para **utilizar os dados existentes**, selecione **Backup**.
4. Para **cópia de segurança,** selecione uma cópia de segurança da lista de cópias de segurança de geo-restauro disponíveis.

    ![Screenshot das opções de Criação de Bases de Dados SQL](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Complete o processo de criação de uma nova base de dados a partir da cópia de segurança. Quando cria uma base de dados na Base de Dados Azure SQL, contém a cópia de segurança restaurada do geo-restauro.

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para restaurar uma base de dados de instância gerida do portal Azure para um caso gerido existente numa região à sua escolha, selecione uma instância gerida em que deseja que uma base de dados seja restaurada. Siga estes passos:

1. Selecione **Nova base de dados**.
2. Digite um nome de base de dados desejado.
3. Em **Utilizar os dados existentes**, selecione **Backup**.
4. Selecione uma cópia de segurança da lista de cópias de segurança de geo-restauro disponíveis.

    ![Screenshot de novas opções de base de dados](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Complete o processo de criação de uma nova base de dados. Quando cria a base de dados de casos, contém a cópia de segurança restaurada do geo-restauro.

### <a name="geo-restore-by-using-powershell"></a>Geo-restauração utilizando o PowerShell

#### <a name="sql-database"></a>Base de Dados SQL

Para obter um script PowerShell que mostre como executar o geo-restauro para uma única base de [dados, consulte Use PowerShell para restaurar uma única base de dados para um ponto anterior no tempo](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para obter um script PowerShell que mostre como executar o geo-restauro para uma base de dados de instância gerida, consulte [Use PowerShell para restaurar uma base de dados de instância gerida para outra geo-região](../managed-instance/scripts/restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Considerações de geo-restauro

Não se pode fazer uma restauração pontual numa base de dados geoconsusídua. Só pode fazê-lo numa base de dados primária. Para obter informações detalhadas sobre a utilização de geo-restauro para recuperar de uma paragem, consulte [recuperar de uma paragem](../../key-vault/general/disaster-recovery-guidance.md).

> [!IMPORTANT]
> A geo-restauração é a solução de recuperação de desastres mais básica disponível na Base de Dados SQL e na SQL Managed Instance. Baseia-se em backups geo-replicados automaticamente com um objetivo de ponto de recuperação (RPO) até 1 hora e um tempo estimado de recuperação de até 12 horas. Não garante que a região alvo tenha a capacidade de restaurar as suas bases de dados após uma paragem regional, porque é provável um aumento acentuado da procura. Se a sua aplicação utilizar bases de dados relativamente pequenas e não for fundamental para o negócio, a geo-restauração é uma solução adequada de recuperação de desastres. 
>
> Para aplicações críticas ao negócio que exijam grandes bases de dados e que garantam a continuidade do negócio, utilize [grupos de falha automática](auto-failover-group-overview.md). Oferece um objetivo de RPO e tempo de recuperação muito mais baixo, e a capacidade é sempre garantida. 
>
> Para obter mais informações sobre as escolhas de continuidade do negócio, consulte [a visão geral da continuidade do negócio.](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="programmatic-recovery-using-automated-backups"></a>Recuperação programática usando backups automatizados

Também pode utilizar a Azure PowerShell ou a API REST para recuperação. As tabelas seguintes descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela SQL Database e pela SQL Managed Instance, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos Azure Resource Manager são, em grande medida, idênticos.

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma base de dados autónoma ou em conjunto, consulte [a Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Base de Dados Get-AzSql](/powershell/module/az.sql/get-azsqldatabase) |Obtém uma ou mais bases de dados. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Obtém uma base de dados eliminada que pode restaurar. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Obtém uma cópia de segurança georredundante de uma base de dados. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Restaura uma base de dados. |

  > [!TIP]
  > Para obter uma amostra do script PowerShell que mostre como executar uma restauração pontual de uma base de dados, consulte Restaurar uma base de [dados utilizando o PowerShell](scripts/restore-database-powershell.md).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para restaurar uma base de dados de instância gerida, consulte [a Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Descrição |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Obtém uma ou mais instâncias geridas. |
  | [Sebase de Dados Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstancedatabase) | Tem uma base de dados de casos. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Restaura uma base de dados de casos. |

### <a name="rest-api"></a>API REST

Para restaurar uma base de dados utilizando a API REST:

| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](/rest/api/sql/databases) |Restaura uma base de dados. |
| [Obtenha criar ou atualizar o estado da base de dados](/rest/api/sql/operations) |Devolve o estado durante uma operação de restauro. |

### <a name="azure-cli"></a>CLI do Azure

#### <a name="sql-database"></a>Base de Dados SQL

Para restaurar uma base de dados utilizando o Azure CLI, consulte [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="sql-managed-instance"></a>Instância Gerida do SQL

Para restaurar uma base de dados de instância gerida utilizando o Azure CLI, consulte [o restauro do az sql midb](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Resumo

As cópias de segurança automáticas protegem as suas bases de dados contra erros de utilizador e aplicação, eliminação acidental da base de dados e interrupções prolongadas. Esta capacidade incorporada está disponível para todos os níveis de serviço e tamanhos de cálculo.

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da continuidade de negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [Backups automatizados da Base de Dados SQL](automated-backups-overview.md)
- [Retenção a longo prazo](long-term-retention-overview.md)
- Para saber mais rapidamente opções de recuperação, consulte [grupos](auto-failover-group-overview.md) [de geo-replicação ativa](active-geo-replication-overview.md) ou de falha automática .
