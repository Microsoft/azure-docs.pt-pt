---
title: Exportar uma Base de Dados Azure SQL um ficheiro BACPAC (portal Azure)
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Exportar uma base de dados Azure SQL para um ficheiro BACPAC utilizando o portal Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 27e5bee041ca6be2f179a50732f58396b317e179
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045285"
---
# <a name="export-to-a-bacpac-file---azure-sql-database--sql-managed-instance"></a>Exportação para um ficheiro BACPAC - Base de Dados Azure SQL & Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Quando precisa de exportar uma base de dados para arquivamento ou para se mudar para outra plataforma, pode exportar o esquema de base de dados e os dados para um ficheiro [BACPAC.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) Um ficheiro BACPAC é um ficheiro ZIP com uma extensão do BACPAC contendo os metadados e os dados da base de dados. Um ficheiro BACPAC pode ser armazenado no armazenamento da Blob Azure ou no armazenamento local num local e posteriormente importado de volta para a Base de Dados Azure SQL, Azure SQL Managed Instance ou uma instalação sQL Server no local.

## <a name="considerations"></a>Considerações

- Para que uma exportação seja transaccionadamente consistente, deve garantir que não ocorre nenhuma atividade de escrita durante a exportação ou que está a exportar a partir de uma [cópia transaccionalmente consistente](database-copy.md) da sua base de dados.
- Se estiver a exportar para o armazenamento de bolhas, o tamanho máximo de um ficheiro BACPAC é de 200 GB. Para arquivar um ficheiro BACPAC maior, exporte para o armazenamento local.
- A exportação de um ficheiro BACPAC para o armazenamento premium Azure utilizando os métodos discutidos neste artigo não é suportada.
- O armazenamento atrás de uma firewall não é suportado atualmente.
- Se a operação de exportação exceder 20 horas, poderá ser cancelada. Para aumentar o desempenho durante a exportação, pode:

  - Aumente temporariamente o tamanho da sua computação.
  - Cesse toda a atividade de leitura e escrita durante a exportação.
  - Utilize um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices agrupados, uma exportação pode falhar se demorar mais de 6-12 horas. Isto porque o serviço de exportação precisa de completar uma tabela para tentar exportar mesa inteira. Uma boa maneira de determinar se as suas tabelas estão otimizadas para exportação é executar **a DBCC SHOW_STATISTICS** e certificar-se de que o *RANGE_HI_KEY* não é nulo e o seu valor tem uma boa distribuição. Para mais detalhes, consulte [o DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Os BACPACs não se destinam a ser utilizados para operações de backup e restauro. O Azure cria automaticamente cópias de segurança para cada base de dados do utilizador. Para mais detalhes, consulte a [visão geral da continuidade do negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md) e as cópias de segurança da Base de Dados [SQL](automated-backups-overview.md).

## <a name="azure-portal"></a>Portal do Azure

A exportação de um BACPAC de uma base de dados de uma [instância gerida Azure SQL](../managed-instance/sql-managed-instance-paas-overview.md) utilizando o portal Azure não é atualmente suportada. Utilize o Estúdio de Gestão de Servidores SQL ou o SQLPackage.

> [!NOTE]
> As máquinas que processam pedidos de importação/exportação apresentados através do portal Azure ou da PowerShell precisam de armazenar o ficheiro BACPAC, bem como ficheiros temporários gerados pelo Quadro de Aplicação de Nível de Dados (DacFX). O espaço do disco necessário varia significativamente entre bases de dados com o mesmo tamanho e pode requerer espaço de disco até 3 vezes o tamanho da base de dados. As máquinas que executam o pedido de importação/exportação têm apenas 450GB de espaço de disco local. Como resultado, alguns pedidos podem falhar com o erro `There is not enough space on the disk` . Neste caso, a slocalização é executar sqlpackage.exe em uma máquina com espaço suficiente para discos locais. Encorajamos a utilização do [SqlPackage](#sqlpackage-utility) para importar/exportar bases de dados superiores a 150GB para evitar este problema.

1. Para exportar uma base de dados utilizando o [portal Azure,](https://portal.azure.com)abra a página para a sua base de dados e clique em **Exportar** na barra de ferramentas.

   ![Exportação de bases de dados](./media/database-export/database-export1.png)

2. Especifique o nome de ficheiro BACPAC, selecione uma conta de armazenamento e recipiente Azure existentes para a exportação e, em seguida, forneça as credenciais adequadas para o acesso à base de dados de origem. Um **login** de administrador do SQL Server é necessário aqui mesmo que você seja o administrador Azure, uma vez que ser um administrador Azure não equivale a ter permissões de administração em Base de Dados Azure SQL ou Azure SQL Managed Instance.

    ![Exportação de bases de dados](./media/database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorizar o progresso da operação de exportação, abra a página para o servidor que contém a base de dados que está a ser exportada. Abaixo em **Definições** e, em seguida, clique no **histórico de importação/exportação**.

   ![história da exportação](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Utilitário SQLPackage

Para exportar uma base de dados SQL utilizando o utilitário de linha de comando [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) consulte [os parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)da exportação . Os utilitários SQLPackage com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e das Ferramentas de Dados do [Servidor SQL para estúdio visual](https://msdn.microsoft.com/library/mt204009.aspx), ou pode descarregar a versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente do centro de descarregamento da Microsoft.

Recomendamos a utilização do utilitário SQLPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar uma base de dados usando SqlPackage.exe com Autenticação Universal de Diretório Ativo:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornecem um assistente para exportar uma base de dados Azure SQL ou uma base de dados SQL Managed Instance para um ficheiro BACPAC. Consulte a [Exportação de uma Aplicação de nível de Dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [A Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) não suporta atualmente a exportação de uma base de dados para um ficheiro BACPAC utilizando o Azure PowerShell. Para exportar uma instância gerida para um ficheiro BACPAC, utilize o SQL Server Management Studio ou o SQLPackage.

Utilize o [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet para enviar um pedido de base de dados de exportação ao serviço de base de dados Azure SQL. Dependendo do tamanho da sua base de dados, a operação de exportação pode demorar algum tempo a ser concluída.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o estado do pedido de exportação, utilize o [cmdlet Get-AzSqlDatabaseImportExportStatus.](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) Executando isto imediatamente após o pedido geralmente devolve **status: InProgress**. Quando vir **o Estado: Bem sucedido,** a exportação está completa.

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

## <a name="next-steps"></a>Próximos passos

- Para conhecer a retenção de cópias de segurança a longo prazo de uma única base de dados e bases de dados reunidas como alternativa à exportação de uma base de dados para fins de arquivo, consulte a [retenção de backup](long-term-retention-overview.md)a longo prazo . Pode utilizar trabalhos do Agente SQL para agendar [cópias de dados apenas](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como alternativa à retenção de backup a longo prazo.
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para aprender sobre a importação de um BACPAC para uma base de dados do SQL Server, consulte importar um BACPAC para uma base de [dados do Servidor SQL](https://msdn.microsoft.com/library/hh710052.aspx).
- Para aprender sobre a exportação de um BACPAC a partir de uma base de dados do SQL Server, consulte [Exportar uma aplicação de nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para aprender sobre a utilização do Serviço de Migração de Dados para migrar uma base de dados, consulte [migrate SQL Server para Azure SQL Database offline usando DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Se estiver a exportar do SQL Server como prelúdio para a migração para a Base de Dados Azure SQL, consulte Migrate uma base de [dados do Servidor SQL para a Base de Dados Azure SQL](migrate-to-database-from-sql-server.md).
- Para saber gerir e partilhar chaves de armazenamento e assinaturas de acesso partilhadas de forma segura, consulte o Guia de Segurança do [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
