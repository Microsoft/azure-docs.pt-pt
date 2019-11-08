---
title: Exportar um banco de dados individual ou em pool para um arquivo BACPAC
description: Exportar um banco de dados SQL do Azure para um arquivo BACPAC usando o portal do Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b96d2e69b757774afe177341caffe2f3cbf00e2b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823458"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Exportar um banco de dados SQL do Azure para um arquivo BACPAC

Quando você precisa exportar um banco de dados para arquivamento ou para mudar para outra plataforma, você pode exportar o esquema e os dados do banco de dado para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) . Um arquivo BACPAC é um arquivo ZIP com uma extensão de BACPAC que contém os metadados e os dados de um banco de dado SQL Server. Um arquivo BACPAC pode ser armazenado no armazenamento de BLOBs do Azure ou no armazenamento local em um local e posteriormente importado de volta para o banco de dados SQL do Azure ou para uma SQL Server instalação local.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Considerações ao exportar um banco de dados SQL do Azure

- Para que uma exportação seja transacionalmente consistente, você deve garantir que nenhuma atividade de gravação esteja ocorrendo durante a exportação ou que você esteja exportando de uma [cópia transacionalmente consistente](sql-database-copy.md) de seu banco de dados SQL do Azure.
- Se você estiver exportando para o armazenamento de BLOBs, o tamanho máximo de um arquivo BACPAC será de 200 GB. Para arquivar um arquivo BACPAC maior, exporte para o armazenamento local.
- Não há suporte para a exportação de um arquivo BACPAC para o armazenamento Premium do Azure usando os métodos discutidos neste artigo.
- No momento, não há suporte para o armazenamento por trás de um firewall.
- Se a operação de exportação do banco de dados SQL do Azure exceder 20 horas, ela poderá ser cancelada. Para aumentar o desempenho durante a exportação, você pode:

  - Aumente temporariamente o tamanho da computação.
  - Interrompa todas as atividades de leitura e gravação durante a exportação.
  - Use um [índice clusterizado](https://msdn.microsoft.com/library/ms190457.aspx) com valores não nulos em todas as tabelas grandes. Sem índices clusterizados, uma exportação poderá falhar se demorar mais de 6-12 horas. Isso ocorre porque o serviço de exportação precisa concluir uma verificação de tabela para tentar exportar a tabela inteira. Uma boa maneira de determinar se suas tabelas são otimizadas para exportação é executar **DBCC SHOW_STATISTICS** e certificar-se de que o *RANGE_HI_KEY* não é nulo e seu valor tem boa distribuição. Para obter detalhes, consulte [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs não devem ser usados para operações de backup e restauração. O banco de dados SQL do Azure cria backups automaticamente para cada banco de dados de usuário. Para obter detalhes, consulte [visão geral de continuidade de negócios](sql-database-business-continuity.md) e [backups de banco de dados SQL](sql-database-automated-backups.md)

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportar para um arquivo BACPAC usando o portal do Azure

Atualmente, não há suporte para a exportação de um BACPAC de um banco de dados de uma [instância gerenciada](sql-database-managed-instance.md) usando o Azure PowerShell. Em vez disso, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir espaço em disco de até 3 vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Incentivamos o uso do [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) para importar/exportar bancos de dados maiores que 150 GB para evitar esse problema.

1. Para exportar um banco de dados usando o [portal do Azure](https://portal.azure.com), abra a página do banco de dados e clique em **Exportar** na barra de ferramentas.

   ![exportação de banco de dados](./media/sql-database-export/database-export1.png)

2. Especifique o nome de arquivo BACPAC, selecione uma conta de armazenamento do Azure existente e um contêiner para a exportação e forneça as credenciais apropriadas para acesso ao banco de dados de origem. Um **logon de administrador** do SQL Server é necessário aqui mesmo se você for o administrador do Azure, pois ser um administrador do Azure não equivale a ter permissões de administrador de SQL Server.

    ![exportação de banco de dados](./media/sql-database-export/database-export2.png)

3. Clique em **OK**.

4. Para monitorar o progresso da operação de exportação, abra a página do servidor do banco de dados SQL que contém o banco de dados que está sendo exportado. Em **configurações** , clique em **histórico de importação/exportação**.

   ![exportar histórico](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportar para um arquivo BACPAC usando o utilitário SqlPackage

Para exportar um banco de dados SQL usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) , consulte [Exportar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). O utilitário SqlPackage é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e do [SQL Server Data Tools para o Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou você pode baixar a versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) diretamente do centro de download da Microsoft.

Recomendamos o uso do utilitário SqlPackage para escala e desempenho na maioria dos ambientes de produção. Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Este exemplo mostra como exportar um banco de dados usando SqlPackage. exe com Active Directory autenticação universal:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportar para um arquivo BACPAC usando SQL Server Management Studio (SSMS)

As versões mais recentes do SQL Server Management Studio fornecem um assistente para exportar um banco de dados SQL do Azure para um arquivo BACPAC. Consulte [exportar um aplicativo da camada de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportar para um arquivo BACPAC usando o PowerShell

> [!NOTE]
> Atualmente, [uma instância gerenciada](sql-database-managed-instance.md) não dá suporte à exportação de um banco de dados para um arquivo BACPAC usando Azure PowerShell. Para exportar uma instância gerenciada para um arquivo BACPAC, use SQL Server Management Studio ou SqlPackage.

Use o cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) para enviar uma solicitação de exportação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a operação de exportação pode levar algum tempo para ser concluída.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Para verificar o status da solicitação de exportação, use o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Executar isso imediatamente depois que a solicitação geralmente retorna o **status: InProgress**. Quando você vir **status: êxito** , a exportação foi concluída.

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

- Para saber mais sobre a retenção de backup de longo prazo de bancos de dados individuais e bancos de dados em pool como uma alternativa para exportar um banco de dado para fins de arquivamento, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md). Você pode usar trabalhos do SQL Agent para agendar [backups de banco de dados somente cópia](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) como uma alternativa à retenção de backup de longo prazo.
- Para saber mais sobre a migração com ficheiros BACPAC num blogue da Equipa de Aconselhamento ao Cliente do SQL Server, consulte [Migrating from SQL Server to Azure SQL Database using BACPAC Files (Migrar a partir do SQL Server para a Base de Dados SQL do Azure com Ficheiros BACPAC)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Para saber mais sobre como importar um BACPAC para um banco de dados SQL Server, consulte [importar um bacpac para um banco de dados SQL Server](https://msdn.microsoft.com/library/hh710052.aspx).
- Para saber mais sobre como exportar um BACPAC de um banco de dados SQL Server, consulte [exportar um aplicativo da camada de dado](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Para saber mais sobre como usar o serviço de migração de dados para migrar um banco de dado, consulte [migrar SQL Server para o Azure SQL Database offline usando DMS](../dms/tutorial-sql-server-to-azure-sql.md).
- Se você estiver exportando de SQL Server como prelúdio para migração para o banco de dados SQL do Azure, consulte [migrar um banco de dados SQL Server para o banco de dados SQL do Azure](sql-database-single-database-migrate.md).
- Para saber como gerenciar e compartilhar as chaves de armazenamento e as assinaturas de acesso compartilhado com segurança, consulte o [Guia de segurança do armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
