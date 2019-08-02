---
title: Copiar um banco de dados SQL do Azure | Microsoft Docs
description: Crie uma cópia transacionalmente consistente de um banco de dados SQL do Azure existente no mesmo servidor ou em um servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/03/2019
ms.openlocfilehash: e9cc5aaaf11a799b17cc87b40113e166fcd93afb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568994"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Copiar uma cópia transacionalmente consistente de um banco de dados SQL do Azure

O banco de dados SQL do Azure fornece vários métodos para criar uma cópia transacionalmente consistente de um banco de dados SQL do Azure existente ([banco de dados individual](sql-database-single-database.md)) no mesmo servidor ou em um servidor diferente. Você pode copiar um banco de dados SQL usando o portal do Azure, o PowerShell ou o T-SQL. 

## <a name="overview"></a>Descrição geral

Uma cópia de banco de dados é um instantâneo do banco de dados de origem a partir do momento da solicitação de cópia. Você pode selecionar o mesmo servidor ou um servidor diferente. Além disso, você pode optar por manter sua camada de serviço e o tamanho da computação ou usar um tamanho de computação diferente dentro da mesma camada de serviço (edição). Depois que a cópia for concluída, ela se tornará um banco de dados totalmente funcional e independente. Neste ponto, você pode atualizar ou fazer downgrade dele para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados de forma independente.  

> [!NOTE]
> [Backups de banco de dados automatizados](sql-database-automated-backups.md) são usados quando você cria uma cópia de banco de dados.

## <a name="logins-in-the-database-copy"></a>Logons na cópia do banco de dados

Quando você copia um banco de dados para o mesmo servidor de banco de dados SQL, os mesmos logons podem ser usados em ambos os bancos. A entidade de segurança usada para copiar o banco de dados torna-se o proprietário do banco de dados no novo banco de dados. Todos os usuários do banco de dados, suas permissões e seus SIDs (identificadores de segurança) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor de banco de dados SQL diferente, a entidade de segurança no novo servidor torna-se o proprietário do banco de dados no novo banco de dados. Se você usar [os usuários de banco](sql-database-manage-logins.md) de dados independente para acesso ao dado, certifique-se de que os bancos de dados primário e secundário sempre tenham as mesmas credenciais de usuário, de modo que depois que a cópia for concluída, você poderá acessá-la imediatamente com as mesmas credenciais. 

Se você usar [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), poderá eliminar completamente a necessidade de gerenciar credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso baseado em logon pode não funcionar, pois os logons não existem no novo servidor. Para saber mais sobre como gerenciar logons ao copiar um banco de dados para um servidor de banco de dados SQL diferente, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). 

Depois que a cópia for realizada com sucesso e antes que outros usuários sejam remapeados, somente o logon que iniciou a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados. Para resolver logons após a conclusão da operação de cópia, consulte [resolver logons](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Copiar um banco de dados usando o portal do Azure

Para copiar um banco de dados usando o portal do Azure, abra a página do banco de dados e clique em **copiar**. 

   ![Cópia do banco de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Copiar um banco de dados usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para copiar um banco de dados usando o PowerShell, use o cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) . 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Para obter um script de exemplo completo, consulte [copiar um banco de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Copiar um banco de dados usando Transact-SQL

Faça logon no banco de dados mestre com o logon da entidade de segurança no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia de banco de dados tenha sucesso, os logons que não são a entidade de segurança no nível do servidor devem ser membros da função dbmanager. Para obter mais informações sobre logons e conexão com o servidor, consulte [Manage logons](sql-database-manage-logins.md).

Comece a copiar o banco de dados de origem com a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . A execução desta instrução inicia o processo de cópia do banco de dados. Como a cópia de um banco de dados é um processo assíncrono, a instrução CREATE DATABASE retorna antes de a cópia do banco de dados ser concluída.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor

Faça logon no banco de dados mestre com o logon da entidade de segurança no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia de banco de dados tenha sucesso, os logons que não são a entidade de segurança no nível do servidor devem ser membros da função dbmanager.

Esse comando copia Database1 para um novo banco de dados chamado Database2 no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia pode levar algum tempo para ser concluída.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino, o servidor do banco de dados SQL no qual o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha que o proprietário do banco de dados de origem no servidor do banco de dados SQL de origem. O logon no servidor de destino também deve ser um membro da função dbmanager ou ser o logon da entidade de segurança no nível do servidor.

Esse comando copia Database1 no Server1 para um novo banco de dados chamado Database2 no Server2. Dependendo do tamanho do banco de dados, a operação de cópia pode levar algum tempo para ser concluída.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Os firewalls de servidores devem ser configurados para permitir a conexão de entrada do IP do cliente que emite o comando de cópia T-SQL.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiar um banco de dados SQL para uma assinatura diferente

Você pode usar as etapas descritas na seção anterior para copiar seu banco de dados para um servidor de banco de dados SQL em uma assinatura diferente. Certifique-se de usar um logon que tenha o mesmo nome e senha que o proprietário do banco de dados de origem e ele é membro da função dbmanager ou é o logon da entidade de segurança no nível do servidor. 

> [!NOTE]
> O [portal do Azure](https://portal.azure.com) não oferece suporte à cópia para uma assinatura diferente porque o portal chama a API do ARM e usa os certificados de assinatura para acessar os dois servidores envolvidos na replicação geográfica.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorar o progresso da operação de cópia

Monitore o processo de cópia consultando os modos de exibição sys. databases e sys. dm _database_copies. Enquanto a cópia estiver em andamento, a coluna **state_desc** da exibição sys. databases para o novo banco de dados será definida como **Copying**.

* Se a cópia falhar, a coluna **state_desc** da exibição sys. databases para o novo banco de dados será definida como **suspeita**. Execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for realizada com sucesso, a coluna **state_desc** da exibição sys. databases para o novo banco de dados será definida como **online**. A cópia está concluída e o novo banco de dados é um banco de dados normal que pode ser alterado independentemente do banco de dados de origem.

> [!NOTE]
> Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [drop Database](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, a execução da instrução DROP DATABASE no banco de dados de origem também cancela o processo de cópia.

## <a name="resolve-logins"></a>Resolver logons

Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para os logons no servidor de destino. Para resolver usuários órfãos, consulte [solucionar problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Consulte também [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os usuários no novo banco de dados retêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se torna o proprietário do novo banco de dados e recebe um novo SID (identificador de segurança). Depois que a cópia for realizada com sucesso e antes que outros usuários sejam remapeados, somente o logon que iniciou a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados.

Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor de banco de dados SQL diferente, consulte [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação](sql-database-geo-replication-security-config.md)de desastres.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre logons, consulte [gerenciar logons](sql-database-manage-logins.md) e [como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para exportar um banco de dados, consulte [exportar o banco de dados para um BACPAC](sql-database-export.md).
