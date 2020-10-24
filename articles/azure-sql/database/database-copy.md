---
title: Copiar uma Base de Dados
description: Crie uma cópia transacionalmente consistente de uma base de dados existente na Base de Dados Azure SQL no mesmo servidor ou num servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: ''
ms.date: 07/29/2020
ms.openlocfilehash: 3aaa666ac6b7ddffcf5e0d2f5b62d26bd0f96004
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516210"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Copie uma cópia transaccionalmente consistente de uma base de dados na Base de Dados Azure SQL

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Azure SQL Database fornece vários métodos para criar uma cópia de uma base de [dados](single-database-overview.md) existente no mesmo servidor ou num servidor diferente. Pode copiar uma base de dados utilizando o portal Azure, PowerShell, Azure CLI ou T-SQL.

## <a name="overview"></a>Descrição geral

Uma cópia de base de dados é uma imagem transaccionalmente consistente da base de dados de origem a partir de um ponto no tempo após o pedido de cópia ser iniciado. Pode selecionar o mesmo servidor ou um servidor diferente para a cópia. Também pode optar por manter o tamanho de redundância de backup, nível de serviço e tamanho de cálculo da base de dados de origem, ou utilizar um tamanho de armazenamento de backup diferente e/ou tamanho de cálculo dentro do mesmo ou de um nível de serviço diferente. Após a cópia estar completa, torna-se uma base de dados totalmente funcional e independente. Os logins, utilizadores e permissões na base de dados copiada são geridos independentemente da base de dados de origem. A cópia é criada utilizando a tecnologia de geo-replicação. Após a conclusão da propagação da réplica, a ligação da georreplicação será encerrada automaticamente. Todos os requisitos para utilizar a georreplicação se aplicam à operação da cópia da base de dados. Consulte [a visão geral da replicação de geo-replicação ativa](active-geo-replication-overview.md) para obter mais detalhes.

> [!NOTE]
> Azure SQL Database Configurable Backup Storage Despedimento está atualmente disponível apenas na região do Sudeste Asiático Azure. Na pré-visualização, se a base de dados de origem for criada com redundância de armazenamento de backup redundante local ou redundante, a cópia da base de dados para um servidor numa região de Azure diferente não é suportada. 

## <a name="logins-in-the-database-copy"></a>Logins na cópia da base de dados

Quando copia uma base de dados para o mesmo servidor, os mesmos logins podem ser utilizados em ambas as bases de dados. O diretor de segurança que usa para copiar a base de dados torna-se o dono da base de dados na nova base de dados.

Quando copia uma base de dados para um servidor diferente, o principal de segurança que iniciou a operação de cópia no servidor alvo torna-se o proprietário da nova base de dados.

Independentemente do servidor alvo, todos os utilizadores da base de dados, as suas permissões e os seus identificadores de segurança (SIDs) são copiados para a cópia da base de dados. A utilização [de utilizadores de bases de dados contidos](logins-create-manage.md) para acesso a dados garante que a base de dados copiada tem as mesmas credenciais de utilizador, para que, após a conclusão da cópia, possa aceder imediatamente à mesma com as mesmas credenciais.

Se utilizar inícios de sessão ao nível do servidor para o acesso aos dados e copiar a base de dados para um servidor diferente, o acesso baseado no início de sessão poderá não funcionar. Tal poderá acontecer porque os inícios de sessão não existem no servidor de destino ou porque as palavras-passe e os identificadores de segurança (SIDs) são diferentes. Para saber sobre a gestão de logins quando copiar uma base de dados para um servidor diferente, consulte [Como gerir a segurança da Base de Dados Azure SQL após a recuperação de desastres](active-geo-replication-security-configure.md). Após a operação de cópia para um servidor diferente ter sucesso, e antes que outros utilizadores sejam rempeitidos, apenas o login associado ao proprietário da base de dados, ou o administrador do servidor pode iniciar sessão na base de dados copiada. Para resolver os logins e estabelecer o acesso aos dados após a conclusão da operação de cópia, consulte [Os logins de Resolve](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Copiar com o portal do Azure

Para copiar uma base de dados utilizando o portal Azure, abra a página da sua base de dados e, em seguida, clique em **Copiar**.

   ![Cópia da base de dados](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Cópia utilizando PowerShell ou o Azure CLI

Para copiar uma base de dados, utilize os seguintes exemplos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para powerShell, utilize o [cmdlet New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

A cópia da base de dados é uma operação assíncronea, mas a base de dados-alvo é criada imediatamente após a aceitação do pedido. Se tiver de cancelar a operação de cópia ainda em curso, deixe cair a base de dados-alvo utilizando o cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Para obter uma amostra completa do script PowerShell, consulte [copiar uma base de dados para um novo servidor](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

A cópia da base de dados é uma operação assíncronea, mas a base de dados-alvo é criada imediatamente após a aceitação do pedido. Se precisar cancelar a operação de cópia ainda em curso, deixe cair a base de dados-alvo utilizando o comando [az sql db Delete.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="copy-using-transact-sql"></a>Cópia utilizando Transact-SQL

Inicie sessão na base de dados principal com o login do administrador do servidor ou o login que criou a base de dados que pretende copiar. Para que a cópia da base de dados tenha sucesso, os logins que não são o administrador do servidor devem ser membros da `dbmanager` função. Para obter mais informações sobre logins e ligação ao servidor, consulte [Gerir os logins](logins-create-manage.md).

Comece a copiar a base de dados de origem com a [BASE DE DADOS CREATE ... COMO CÓPIA DE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) DECLARAÇÃO. A declaração T-SQL continua a funcionar até que a operação de cópia da base de dados esteja completa.

> [!NOTE]
> O encerramento da declaração T-SQL não encerra a operação de cópia da base de dados. Para terminar a operação, deixe cair a base de dados do alvo.
>

> [!IMPORTANT]
> Selecionando redundância de armazenamento de backup ao utilizar a base de dados T-SQL CREATE ... AS COPY OF Command ainda não está suportado. 

### <a name="copy-to-the-same-server"></a>Copiar para o mesmo servidor

Inicie sessão na base de dados principal com o login do administrador do servidor ou o login que criou a base de dados que pretende copiar. Para que a cópia da base de dados tenha sucesso, os logins que não são o administrador do servidor devem ser membros da `dbmanager` função.

Este comando copia base de dados1 para uma nova base de dados chamada Database2 no mesmo servidor. Dependendo do tamanho da sua base de dados, a operação de cópia pode demorar algum tempo a ser concluída.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Copiar para uma piscina elástica

Inicie sessão na base de dados principal com o login do administrador do servidor ou o login que criou a base de dados que pretende copiar. Para que a cópia da base de dados tenha sucesso, os logins que não são o administrador do servidor devem ser membros da `dbmanager` função.

Este comando copia a Base de Dados1 para uma nova base de dados chamada Database2 numa piscina elástica chamada pool1. Dependendo do tamanho da sua base de dados, a operação de cópia pode demorar algum tempo a ser concluída.

A base de dados1 pode ser uma base de dados única ou agrizada. A cópia entre diferentes piscinas de nível é suportada, mas algumas cópias transversais não serão bem sucedidas. Por exemplo, você pode copiar um db padrão único ou elástico em uma piscina de propósito geral, mas você não pode copiar um db elástico padrão em uma piscina premium. 

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) ) ;
   ```

### <a name="copy-to-a-different-server"></a>Copiar para um servidor diferente

Inicie sessão na base de dados principal do servidor alvo onde a nova base de dados será criada. Utilize um login com o mesmo nome e senha que o proprietário da base de dados da base de dados de origem no servidor de origem. O login no servidor alvo também deve ser um membro da `dbmanager` função, ou ser o login do administrador do servidor.

Este comando copia base de dados1 no servidor1 para uma nova base de dados chamada Database2 no servidor2. Dependendo do tamanho da sua base de dados, a operação de cópia pode demorar algum tempo a ser concluída.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> As firewalls de ambos os servidores devem ser configuradas para permitir a ligação de entrada a partir do IP do cliente que emite a BASE DE DADOS T-SQL CREATE ... Como cópia do comando.

### <a name="copy-to-a-different-subscription"></a>Copiar para uma subscrição diferente

Pode utilizar os passos na [Base de Dados Copy a SQL para uma](#copy-to-a-different-server) secção de servidor diferente para copiar a sua base de dados para um servidor numa subscrição diferente utilizando o T-SQL. Certifique-se de que utiliza um login com o mesmo nome e senha que o proprietário da base de dados da base de dados de origem. Além disso, o login deve ser um membro da `dbmanager` função ou um administrador do servidor, tanto nos servidores de origem como nos servidores-alvo.

> [!NOTE]
> O [portal Azure,](https://portal.azure.com)o PowerShell e o Azure CLI não suportam cópia de base de dados para uma subscrição diferente.

> [!TIP]
> A cópia da base de dados utilizando o T-SQL suporta a cópia de uma base de dados de uma subscrição num inquilino Azure diferente. Isto só é suportado quando se utiliza um login de autenticação SQL para iniciar sessão no servidor alvo.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorizar o progresso da operação de cópia

Monitorize o processo de cópia consultando as [vistas sys.databases](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys.dm_operation_status.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Enquanto a cópia está em curso, a coluna **state_desc** da vista sys.databases para a nova base de dados está definida como **COPYING**.

* Se a cópia falhar, a coluna **state_desc** da vista sys.databases para a nova base de dados é definida como **SUSPEITA**. Execute a declaração DROP na nova base de dados e tente novamente mais tarde.
* Se a cópia for bem sucedida, a coluna **state_desc** da vista sys.databases para a nova base de dados está definida para **ONLINE**. A cópia está completa e a nova base de dados é uma base de dados regular que pode ser alterada independentemente da base de dados de origem.

> [!NOTE]
> Se decidir cancelar a cópia enquanto estiver em curso, execute a declaração [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) na nova base de dados.

> [!IMPORTANT]
> Se precisar de criar uma cópia com um objetivo de serviço substancialmente menor do que a fonte, a base de dados-alvo pode não ter recursos suficientes para completar o processo de sementeira e pode fazer com que a cópia operaion falhe. Neste cenário, utilize um pedido de geo-restauro para criar uma cópia num servidor diferente e/ou numa região diferente. Consulte [a Recuperação de uma Base de Dados Azure SQL utilizando cópias de dados](recovery-using-backups.md#geo-restore) para obter mais informações.

## <a name="azure-roles-to-manage-database-copy"></a>Funções Azure para gerir cópia de base de dados

Para criar uma cópia de base de dados, terá de estar nas seguintes funções

* Proprietário de Assinatura ou
* Papel de contribuinte do servidor SQL ou
* Função personalizada nas bases de dados de origem e alvo com a seguinte permissão:

   Microsoft.Sql/servidores/bases de dados/ler Microsoft.Sql/servers/databases/write

Para cancelar uma cópia de base de dados, terá de estar nas seguintes funções

* Proprietário de Assinatura ou
* Papel de contribuinte do servidor SQL ou
* Função personalizada nas bases de dados de origem e alvo com a seguinte permissão:

   Microsoft.Sql/servidores/bases de dados/ler Microsoft.Sql/servers/databases/write

Para gerir a cópia da base de dados utilizando o portal Azure, também necessitará das seguintes permissões:

   Microsoft.Resources/subscrições/recursos/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Se quiser ver as operações em execução no grupo de recursos no portal, operações em vários fornecedores de recursos, incluindo operações SQL, necessitará destas funções adicionais de Azure:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Resolver logins

Depois de a nova base de dados estar on-line no servidor alvo, utilize a declaração [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) para remaptar os utilizadores da nova base de dados para iniciar sessão no servidor alvo. Para resolver utilizadores órfãos, consulte [Utilizadores Órfãos resolução de problemas](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Ver também [Como gerir a segurança da Base de Dados Azure SQL após a recuperação de desastres](active-geo-replication-security-configure.md).

Todos os utilizadores da nova base de dados conservam as permissões que tinham na base de dados de origem. O utilizador que iniciou a cópia da base de dados torna-se o proprietário da base de dados da nova base de dados. Após o sucesso da cópia e antes de outros utilizadores serem rempeitidos, apenas o proprietário da base de dados pode iniciar sessão na nova base de dados.

Para saber sobre a gestão de utilizadores e logins quando copiar uma base de dados para um servidor diferente, consulte [Como gerir a segurança da Base de Dados Azure SQL após a recuperação de desastres](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Erros de cópia de base de dados

Os seguintes erros podem ser encontrados ao copiar uma base de dados na Base de Dados Azure SQL. Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](database-copy.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |O cliente com endereço IP '%.&#x2a;ls' está temporariamente desativado. |
| 40637 |16 |A cópia da base de dados criar está atualmente desativada. |
| 40561 |16 |A cópia da base de dados falhou. Ou a base de dados de origem ou alvo não existe. |
| 40562 |16 |A cópia da base de dados falhou. A base de dados de fontes foi retirada. |
| 40563 |16 |A cópia da base de dados falhou. A base de dados do alvo foi abandonada. |
| 40564 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40565 |16 |A cópia da base de dados falhou. Não é permitida mais do que uma cópia simultânea da base de dados da mesma fonte. Por favor, largue a base de dados do alvo e tente de novo mais tarde. |
| 40566 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40567 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40568 |16 |A cópia da base de dados falhou. A base de dados de origem tornou-se indisponível. Por favor, largue a base de dados do alvo e tente de novo. |
| 40569 |16 |A cópia da base de dados falhou. A base de dados-alvo ficou indisponível. Por favor, largue a base de dados do alvo e tente de novo. |
| 40570 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo mais tarde. |
| 40571 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo mais tarde. |

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre logins, consulte [Gerir logins](logins-create-manage.md) e como gerir a segurança da Base de [Dados Azure SQL após a recuperação de desastres.](active-geo-replication-security-configure.md)
* Para exportar uma base de dados, consulte [exporte a base de dados para um BACPAC.](database-export.md)
