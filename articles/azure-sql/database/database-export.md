---
title: Exportar uma Base de Dados Azure SQL para um ficheiro BACPAC (o portal Azure)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Exporte uma base de dados para um ficheiro BACPAC utilizando o portal Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.topic: how-to
ms.openlocfilehash: c0e62a7d9b9beb8ecdfaabdd44fdd547dd78d38f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328196"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exportação para um ficheiro BACPAC - Azure SQL Database e Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Quando precisa de exportar uma base de dados para arquivar ou para se deslocar para outra plataforma, pode exportar o esquema de base de dados e dados para um ficheiro [BACPAC.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) Um ficheiro BACPAC é um ficheiro ZIP com uma extensão de BACPAC contendo os metadados e dados da base de dados. Um ficheiro BACPAC pode ser armazenado no armazenamento de Azure Blob ou no armazenamento local num local no local e posteriormente importado de volta para a Base de Dados Azure SQL, Instância Gerida Azure SQL ou uma instância do SQL Server.

## <a name="considerations"></a>Considerações

- Para que uma exportação seja transaccionalmente consistente, deve certificar-se de que não ocorre qualquer atividade de escrita durante a exportação, ou que está a exportar de uma [cópia transaccionalmente consistente](database-copy.md) da sua base de dados.
- Se estiver a exportar para o armazenamento de bolhas, o tamanho máximo de um ficheiro BACPAC é de 200 GB. Para arquivar um ficheiro BACPAC maior, exporte para armazenamento local.
- A exportação de um ficheiro BACPAC para o armazenamento premium Azure utilizando os métodos discutidos neste artigo não é apoiada.
- O armazenamento por detrás de uma firewall não é suportado atualmente.
- Se a operação de exportação exceder 20 horas, pode ser cancelada. Para aumentar o desempenho durante a exportação, pode:

  - Aumente temporariamente o seu tamanho de cálculo.
  - Cesse toda a atividade de leitura e escrita durante a exportação.
  - Utilize um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices agrupados, uma exportação pode falhar se demorar mais de 6 a 12 horas. Isto porque o serviço de exportação precisa de completar um quadro para tentar exportar toda a tabela. Uma boa forma de determinar se as suas tabelas estão otimizadas para exportação é executar **a DBCC SHOW_STATISTICS** e certificar-se de que o *RANGE_HI_KEY* não é nulo e o seu valor tem uma boa distribuição. Para mais detalhes, consulte [a DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Os BACPACs não se destinam a ser utilizados para operações de backup e restauro. O Azure cria automaticamente cópias de segurança para cada base de dados do utilizador. Para mais informações, consulte [a visão geral da continuidade do negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md) e as [cópias de segurança da Base de Dados SQL](automated-backups-overview.md).

## <a name="the-azure-portal"></a>O portal do Azure

A exportação de um BACPAC de uma base de dados a partir de [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) utilizando o portal Azure não é suportada atualmente. Utilize o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal Azure ou da PowerShell precisam de armazenar o ficheiro BACPAC, bem como ficheiros temporários gerados pelo Data-Tier Application Framework (DacFX). O espaço em disco necessário varia significativamente entre bases de dados com o mesmo tamanho e pode exigir espaço em disco até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço em disco local. Como resultado, alguns pedidos podem falhar com o erro `There is not enough space on the disk` . Neste caso, a solução é para executar sqlpackage.exe numa máquina com espaço suficiente em disco local. Encorajamos a utilização [da SqlPackage](#sqlpackage-utility) para importar/exportar bases de dados superiores a 150GB para evitar este problema.

1. Para exportar uma base de dados utilizando o [portal Azure,](https://portal.azure.com)abra a página para a sua base de dados e clique em **Exportar** na barra de ferramentas.

   ![Screenshot que realça o botão Exportação.](./media/database-export/database-export1.png)

2. Especifique o nome de ficheiro BACPAC, selecione uma conta de armazenamento Azure existente e um recipiente para a exportação e, em seguida, forneça as credenciais adequadas para o acesso à base de dados de origem. Um **login de administração SQL Server** é necessário aqui mesmo que você seja o administrador Azure, uma vez que ser um administrador Azure não equivale a ter permissões de administração na Base de Dados Azure SQL ou Azure SQL Managed Instance.

    ![Exportação de bases de dados](./media/database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorizar o progresso da operação de exportação, abra a página do servidor que contém a base de dados que está a ser exportada. Em baixo das **Definições** e, em seguida, clique no **histórico de importação/exportação**.

   ![história da exportação](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Utilitário SQLPackage

Para exportar uma base de dados na Base de Dados SQL utilizando o utilitário da linha de comando [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) consulte [os parâmetros e propriedades da Exportação.](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties) O SQLPackage envia navios utilitários com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [das Ferramentas de Dados do SQL Server para o Visual Studio,](https://msdn.microsoft.com/library/mt204009.aspx)ou pode descarregar a versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente do centro de descarregamento da Microsoft.

Recomendamos a utilização do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar uma base de dados utilizando SqlPackage.exe com autenticação universal do Diretório Ativo:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornecem um assistente para exportar uma base de dados na Base de Dados Azure SQL ou numa base de dados SQL Managed Instance para um ficheiro BACPAC. Consulte a [Aplicação de Nível de Dados para exportação.](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [A Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) não suporta atualmente a exportação de uma base de dados para um ficheiro BACPAC utilizando a Azure PowerShell. Para exportar um caso gerido para um ficheiro BACPAC, utilize o SQL Server Management Studio ou o SQLPackage.

Utilize o cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) para apresentar um pedido de base de dados de exportação ao serviço de Base de Dados Azure SQL. Dependendo do tamanho da sua base de dados, a operação de exportação pode demorar algum tempo a ser concluída.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o estado do pedido de exportação, utilize o [cmdlet Get-AzSqlDataImportExportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Executando isto imediatamente após o pedido normalmente devolve **Estado: InProgress**. Quando **vires Status: O sucesso** da exportação está completo.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a retenção de backup a longo prazo de uma única base de dados e bases de dados agrárias como alternativa à exportação de uma base de dados para fins de arquivo, consulte [a retenção de backup a longo prazo](long-term-retention-overview.md). Pode utilizar os trabalhos do SQL Agent para agendar [cópias de segurança na base de dados apenas](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como alternativa à retenção de backup a longo prazo.
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para saber sobre a importação de um BACPAC para uma base de dados do SQL Server, consulte [Import a BACPAC para uma base de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Para saber sobre a exportação de um BACPAC a partir de uma base de dados do SQL Server, consulte [Exportar uma aplicação de nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para saber mais sobre a utilização do Serviço de Migração de Dados para migrar uma base de dados, consulte [Migrar do SQL Server para Azure SQL Database offline utilizando DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Se estiver a exportar do SQL Server como prelúdio para a migração para a Base de Dados Azure SQL, consulte [a base de dados do SqL Server para a Base de Dados Azure SQL](migrate-to-database-from-sql-server.md).
- Para aprender a gerir e partilhar as chaves de armazenamento e as assinaturas de acesso partilhadas de forma segura, consulte o [Guia de Segurança do Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
