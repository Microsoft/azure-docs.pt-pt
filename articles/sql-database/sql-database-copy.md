---
title: Copiar uma Base de Dados
description: Crie uma cópia transaccionalmente consistente de uma base de dados Azure SQL existente no mesmo servidor ou num servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473749"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Copiar uma cópia transaccionalmente consistente de uma base de dados Azure SQL

A Base de Dados Azure SQL fornece vários métodos para criar uma cópia transacionalmente consistente de uma base de dados Azure SQL existente[(base](sql-database-single-database.md)de dados única ) no mesmo servidor ou num servidor diferente. Pode copiar uma base de dados SQL utilizando o portal Azure, PowerShell ou T-SQL.

## <a name="overview"></a>Descrição geral

Uma cópia da base de dados é uma imagem instantânea da base de dados de origem a partir da hora do pedido de cópia. Pode selecionar o mesmo servidor ou um servidor diferente. Também pode optar por manter o seu nível de serviço e tamanho de computação, ou utilizar um tamanho de computação diferente dentro do mesmo nível de serviço (edição). Após a cópia estar completa, torna-se uma base de dados totalmente funcional e independente. Neste ponto, você pode atualitá-lo ou desclassificá-lo para qualquer edição. Os logins, utilizadores e permissões podem ser geridos de forma independente. A cópia é criada utilizando a tecnologia de geo-replicação e uma vez concluída a sementeação, a ligação de geo-replicação é automaticamente terminada. Todos os requisitos para a utilização da geo-replicação aplicam-se à operação de cópia da base de dados. Consulte a visão geral da [geo-replicação ativa](sql-database-active-geo-replication.md) para obter mais detalhes.

> [!NOTE]
> [As cópias](sql-database-automated-backups.md) de dados automatizadas são utilizadas quando cria uma cópia da base de dados.

## <a name="logins-in-the-database-copy"></a>Logins na cópia da base de dados

Quando copia uma base de dados para o mesmo servidor de base de dados SQL, os mesmos logins podem ser utilizados em ambas as bases de dados. O diretor de segurança que usa para copiar a base de dados torna-se o dono da base de dados na nova base de dados. 

Quando copia uma base de dados para um servidor de base de dados SQL diferente, o diretor de segurança que iniciou a operação de cópia no servidor alvo torna-se o proprietário da nova base de dados. 

Independentemente do servidor-alvo, todos os utilizadores da base de dados, as suas permissões e os seus identificadores de segurança (SIDs) são copiados para a cópia da base de dados. A utilização de [utilizadores de bases de dados contidos](sql-database-manage-logins.md) para acesso a dados garante que a base de dados copiada tem as mesmas credenciais de utilizador, para que, após a conclusão da cópia, possa aceder imediatamente a ela com as mesmas credenciais.

Se utilizar os logins do nível do servidor para acesso a dados e copiar a base de dados para um servidor diferente, o acesso baseado em login pode não funcionar. Isto pode acontecer porque os logins não existem no servidor alvo, ou porque as suas palavras-passe e identificadores de segurança (SIDs) são diferentes. Para aprender sobre a gestão de logins quando copia uma base de dados para um servidor de base de dados SQL diferente, consulte como gerir a segurança da [base de dados Azure SQL após](sql-database-geo-replication-security-config.md)a recuperação de desastres . Após a operação de cópia para um servidor diferente, e antes de outros utilizadores serem remapeados, apenas o login associado ao proprietário da base de dados, ou o administrador do servidor pode iniciar sessão na base de dados copiada. Para resolver os logins e estabelecer o acesso aos dados após a conclusão da operação de cópia, consulte [os logins resolve.](#resolve-logins)

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiar uma base de dados utilizando o portal Azure

Para copiar uma base de dados utilizando o portal Azure, abra a página para a sua base de dados e, em seguida, clique em **Copiar**.

   ![Cópia da base de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>Copiar uma base de dados utilizando powerShell ou Azure CLI

Para copiar uma base de dados, utilize os seguintes exemplos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para o PowerShell, utilize o [cmdlet New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

A cópia da base de dados é uma operação assíncrona, mas a base de dados do alvo é criada imediatamente após a aceitação do pedido. Se precisar cancelar a operação de cópia ainda em curso, deixe cair a base de dados do alvo utilizando o cmdlet de base de [dados Remove-AzSql.](/powershell/module/az.sql/new-azsqldatabase)

Para obter um script powerShell de amostra completa, consulte [Copiar uma base de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

A cópia da base de dados é uma operação assíncrona, mas a base de dados do alvo é criada imediatamente após a aceitação do pedido. Se precisar cancelar a operação de cópia ainda em curso, deixe cair a base de dados do alvo utilizando o comando [de eliminação az sql db.](/cli/azure/sql/db#az-sql-db-delete)

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>Funções RBAC para gerir cópia de base de dados

Para criar uma cópia da base de dados, terá de estar nas seguintes funções

- Proprietário de subscrição ou
- Função de Colaborador do Servidor SQL ou
- Papel personalizado nas bases de dados de origem e alvo com a seguinte permissão:

   Microsoft.Sql/servidores/bases de dados/ler Microsoft.Sql/servidores/bases de dados/escrita

Para cancelar uma cópia da base de dados, terá de estar nas seguintes funções

- Proprietário de subscrição ou
- Função de Colaborador do Servidor SQL ou
- Papel personalizado nas bases de dados de origem e alvo com a seguinte permissão:

   Microsoft.Sql/servidores/bases de dados/ler Microsoft.Sql/servidores/bases de dados/escrita

Para gerir a cópia da base de dados utilizando o portal Azure, também necessitará das seguintes permissões:

   Microsoft.Resources/subscrições/recursos/read Microsoft.Resources/subscrições/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Se quiser ver as operações em implementação no grupo de recursos no portal, operações em vários fornecedores de recursos, incluindo operações SQL, necessitará destas funções rBAC adicionais:

   Microsoft.Resources/subscrições/resourcegroups/deployments/operations/read Microsoft.Resources/subscrições/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Copiar uma base de dados utilizando a Transact-SQL

Inicie sessão na base de dados principal com o administrador do servidor ou o login que criou a base de dados que pretende copiar. Para que a cópia da base de dados tenha sucesso, `dbmanager` os logins que não são o administrador do servidor devem ser membros da função. Para obter mais informações sobre logins e ligação ao servidor, consulte [Gerir logins](sql-database-manage-logins.md).

Comece a copiar a base de dados de origem com a [BASE DE DADOS CREATE ... COMO CÓPIA DA](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) DECLARAÇÃO. A declaração T-SQL continua a funcionar até que a operação de cópia da base de dados esteja concluída.

> [!NOTE]
> A cessação da declaração T-SQL não termina a operação de cópia da base de dados. Para terminar a operação, deixe cair a base de dados do alvo.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar uma base de dados SQL para o mesmo servidor

Inicie sessão na base de dados principal com o administrador do servidor ou o login que criou a base de dados que pretende copiar. Para que a cópia da base de dados tenha sucesso, `dbmanager` os logins que não são o administrador do servidor devem ser membros da função.

Este comando copia base de dados 1 para uma nova base de dados chamada Database2 no mesmo servidor. Dependendo do tamanho da sua base de dados, a operação de cópia pode demorar algum tempo a ser concluída.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar uma base de dados SQL para um servidor diferente

Inicie sessão na base de dados principal do servidor alvo onde será criada a nova base de dados. Utilize um login com o mesmo nome e senha que o dono da base de dados da base de dados de origem no servidor de origem. O login no servidor alvo também deve `dbmanager` ser um membro da função, ou ser o administrador do servidor iniciar sessão.

Este comando copia base de dados 1 no servidor1 para uma nova base de dados chamada Database2 no servidor2. Dependendo do tamanho da sua base de dados, a operação de cópia pode demorar algum tempo a ser concluída.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> As firewalls de ambos os servidores devem ser configuradas para permitir a ligação de entrada a partir do IP do cliente que emite a BASE DE DADOS DE CRIAÇÃO T-SQL... COMO CÓPIA DO comando.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiar uma base de dados SQL para uma subscrição diferente

Pode utilizar os passos na [base de dados Copy a SQL para uma](#copy-a-sql-database-to-a-different-server) secção de servidor diferente para copiar a sua base de dados para um servidor de base de dados SQL numa subscrição diferente utilizando o T-SQL. Certifique-se de que utiliza um login com o mesmo nome e senha que o dono da base de dados da base de dados fonte. Além disso, o login deve `dbmanager` ser um membro da função ou um administrador do servidor, tanto nos servidores de origem como nos servidores-alvo.

> [!NOTE]
> O [portal Azure,](https://portal.azure.com)PowerShell e Azure CLI não suportam cópia de base de dados para uma subscrição diferente.

### <a name="monitor-the-progress-of-the-copying-operation"></a>Acompanhar o progresso da operação de cópia

Monitorize o processo de cópia consultando as bases de [dados sys.,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)e [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) vistas. Enquanto a cópia estiver em andamento, a coluna **state_desc** da visão de bases de dados sys.para a nova base de dados está definida para **COPYING**.

* Se a cópia falhar, **a** state_desc coluna das bases de dados sys.base de dados para a nova base de dados está definida como **SUSPEITA**. Execute a declaração DROP na nova base de dados e tente novamente mais tarde.
* Se a cópia for bem sucedida, **a** state_desc coluna da visão sys.bases de dados para a nova base de dados está definida para **ONLINE**. A cópia está completa e a nova base de dados é uma base de dados regular que pode ser alterada independentemente da base de dados de origem.

> [!NOTE]
> Se decidir cancelar a cópia enquanto estiver em curso, execute a declaração drop [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) na nova base de dados.

> [!IMPORTANT]
> Se precisar de criar uma cópia com um objetivo de serviço substancialmente menor do que a fonte, a base de dados-alvo pode não ter recursos suficientes para completar o processo de sementeire e pode fazer com que a ópera da cópia falhe. Neste cenário, utilize um pedido de geo-restauro para criar uma cópia num servidor diferente e/ou numa região diferente. Consulte A Recuperação de uma base de [dados Azure SQL utilizando cópias](sql-database-recovery-using-backups.md#geo-restore) de dados para obter mais informações.

## <a name="resolve-logins"></a>Resolver logins

Depois de a nova base de dados estar on-line no servidor alvo, utilize a declaração [do UTILIZADOR ALTER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) para remapear os utilizadores da nova base de dados para iniciar sessão no servidor alvo. Para resolver utilizadores órfãos, consulte [Utilizadores Órfãos de Resolução de Problemas](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Veja também como gerir a segurança da base de [dados Azure SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres .

Todos os utilizadores da nova base de dados retêm as permissões que tinham na base de dados de origem. O utilizador que iniciou a cópia da base de dados torna-se o proprietário da base de dados da nova base de dados. Após o sucesso da cópia e antes de outros utilizadores serem remapeados, apenas o proprietário da base de dados pode iniciar sessão na nova base de dados.

Para saber sobre a gestão de utilizadores e logins quando copia uma base de dados para um servidor de base de dados SQL diferente, consulte como gerir a segurança da [base de dados Azure SQL após](sql-database-geo-replication-security-config.md)a recuperação de desastres .

## <a name="database-copy-errors"></a>Erros de cópia de base de dados

Os seguintes erros podem ser encontrados enquanto copiamos uma base de dados na Base de Dados Azure SQL. Para mais informações, consulte [Copiar uma Base de Dados SQL do Azure](sql-database-copy.md).

| Código de erro | Gravidade | Descrição |
| ---:| ---:|:--- |
| 40635 |16 |Cliente com endereço IP '%.&#x2a;ls' está temporariamente incapacitado. |
| 40637 |16 |Criar cópia de base de dados está atualmente desativada. |
| 40561 |16 |A cópia da base de dados falhou. Ou a base de dados de origem ou alvo não existe. |
| 40562 |16 |A cópia da base de dados falhou. A base de dados de origem foi retirada. |
| 40563 |16 |A cópia da base de dados falhou. A base de dados do alvo foi retirada. |
| 40564 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40565 |16 |A cópia da base de dados falhou. Não é permitida uma cópia de base de dados simultânea da mesma fonte. Por favor, largue a base de dados do alvo e tente novamente mais tarde. |
| 40566 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40567 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente de novo. |
| 40568 |16 |A cópia da base de dados falhou. A base de dados de origem tornou-se indisponível. Por favor, largue a base de dados do alvo e tente de novo. |
| 40569 |16 |A cópia da base de dados falhou. A base de dados do alvo tornou-se indisponível. Por favor, largue a base de dados do alvo e tente de novo. |
| 40570 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente novamente mais tarde. |
| 40571 |16 |A cópia da base de dados falhou devido a um erro interno. Por favor, largue a base de dados do alvo e tente novamente mais tarde. |

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre logins, consulte [Gerir logins](sql-database-manage-logins.md) e como gerir a segurança da [base de dados Azure SQL após a recuperação](sql-database-geo-replication-security-config.md)de desastres .
- Para exportar uma base de dados, consulte [Exportar a base de dados para um BACPAC](sql-database-export.md).
