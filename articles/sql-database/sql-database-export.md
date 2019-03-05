---
title: Exportar uma única ou agrupada uma base de dados SQL do Azure para um ficheiro BACPAC | Documentos da Microsoft
description: Exportar uma base de dados SQL do Azure para um ficheiro BACPAC, com o portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: ce5635c97d91514f3fca63162f5356cdf89a7ca6
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307484"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar uma base de dados SQL do Azure para um ficheiro BACPAC

Quando for necessário exportar uma base de dados de arquivamento ou para mover para outra plataforma, pode exportar o esquema de base de dados e os dados para um [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ficheiro. Um ficheiro BACPAC é um arquivo ZIP com uma extensão de BACPAC que contém os metadados e dados a partir de uma base de dados do SQL Server. Um ficheiro BACPAC pode ser armazenado no armazenamento de Blobs do Azure ou no armazenamento local numa localização no local e mais tarde importados back para a base de dados do Azure SQL ou para um SQL Server no local instalação.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerações sobre o quando exportar uma base de dados SQL do Azure

- Para uma exportação transacionalmente consistentes, não verifique se qualquer um dos nenhuma escrita atividade está a ocorrer durante a exportação ou que está a exportar a partir de um [cópia transacional consistente](sql-database-copy.md) da base de dados SQL do Azure.
- Se estiver a exportar para o armazenamento de BLOBs, o tamanho máximo de um ficheiro BACPAC é 200 GB. Arquivar um ficheiro BACPAC maior, exportar para o armazenamento local.
- Exportar um ficheiro BACPAC para o armazenamento premium do Azure com os métodos abordados neste artigo não é suportada.
- Se a operação de exportação da base de dados do Azure SQL exceder 20 horas, poderá ser cancelado. Para aumentar o desempenho durante a exportação, pode:

  - Temporariamente aumente o tamanho de computação.
  - Cessação todos ler e escrever a atividade durante a exportação.
  - Utilize um [índice agrupado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices em cluster, uma exportação poderá falhar se demora mais de 6 a 12 horas. Isto acontece porque o serviço de exportação precisa concluir uma análise de tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se as tabelas são otimizadas para exportação é executada **DBCC SHOW_STATISTICS** e certifique-se de que o *RANGE_HI_KEY* não é nulo e o valor tem de distribuição de bom. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não se destinam a ser utilizada para cópia de segurança e restaurar as operações. Base de dados SQL do Azure cria automaticamente os cópias de segurança para cada base de dados do utilizador. Para obter detalhes, consulte [descrição geral da continuidade de negócio](sql-database-business-continuity.md) e [cópias de segurança da base de dados SQL](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportar para um ficheiro BACPAC, com o portal do Azure

> [!NOTE]
> [Uma instância gerida](sql-database-managed-instance.md) não suporta atualmente a exportação de uma base de dados para um ficheiro BACPAC, com o portal do Azure. Para exportar uma instância gerida para um ficheiro BACPAC, utilize o SQL Server Management Studio ou SQLPackage.

1. Para exportar uma base de dados com o [portal do Azure](https://portal.azure.com), abra a página da base de dados e clique em **exportar** na barra de ferramentas.

   ![exportação da base de dados](./media/sql-database-export/database-export1.png)

2. Especifique o nome de ficheiro BACPAC, selecione uma conta de armazenamento do Azure existente e um contentor para a exportação e, em seguida, forneça as credenciais apropriadas para acessar o banco de dados de origem.

    ![exportação da base de dados](./media/sql-database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorizar o progresso da operação de exportação, abra a página para o servidor de base de dados SQL que contém a base de dados a ser exportado. Sob a **configurações** e, em seguida, clique em **importar/exportar histórico**.

   ![Exportar Histórico](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportar para um ficheiro BACPAC com o utilitário SQLPackage exe

Para exportar uma base de dados SQL com o [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) utilitário de linha de comandos, veja [exportar parâmetros e as propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SQLPackage exe é fornecido com as versões mais recentes [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode baixar a versão mais recente do [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.

Recomendamos a utilização do utilitário SQLPackage exe para dimensionamento e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar uma base de dados com o Active Directory Universal Authentication SqlPackage.exe:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportar para um ficheiro BACPAC com o SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornece um Assistente para exportar uma base de dados SQL do Azure para um ficheiro BACPAC. Consulte a [exportar uma aplicação de camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportar para um ficheiro BACPAC, com o PowerShell

> [!NOTE]
> [Uma instância gerida](sql-database-managed-instance.md) não suporta atualmente a exportação de uma base de dados para um ficheiro BACPAC, com o Azure PowerShell. Para exportar uma instância gerida para um ficheiro BACPAC, utilize o SQL Server Management Studio ou SQLPackage.

Utilize o [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) cmdlet para submeter um pedido de base de dados de exportação para o serviço de base de dados do Azure SQL. Dependendo do tamanho da base de dados, a operação de exportação pode demorar algum tempo a concluir.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o estado do pedido de exportação, utilize o [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) cmdlet. Executar isso imediatamente após a solicitação normalmente devolve **Estado: InProgress**. Quando vir **Estado: Foi efetuada com êxito** a exportação for concluída.

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

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a retenção de cópia de segurança de longa duração de uma única bases de dados agrupadas e de bases de dados como uma alternativa ao exportar uma base de dados para fins de arquivo, consulte [retenção de cópia de segurança de longo prazo](sql-database-long-term-retention.md). Pode usar trabalhos do SQL Agent para agendar [cópias de segurança da base de dados apenas de cópia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa para retenção de cópia de segurança de longo prazo.
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para saber mais sobre como importar um BACPAC para uma base de dados do SQL Server, veja [importar um BACPAC para uma base de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Para saber mais sobre como exportar um BACPAC de uma base de dados do SQL Server, consulte o artigo [exportar uma aplicação de camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para saber mais sobre como utilizar o serviço de migração de dados para migrar uma base de dados, veja [migrar o SQL Server para a base de dados do Azure SQL offline com o DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Se estiver a exportar do SQL Server como um prelúdio para migração para a base de dados do Azure SQL, veja [migrar uma base de dados do SQL Server para a base de dados do Azure SQL](sql-database-single-database-migrate.md).
- Para saber como gerir e partilhar as chaves de armazenamento e acesso partilhado assinaturas de forma segura, consulte [guia de segurança de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
