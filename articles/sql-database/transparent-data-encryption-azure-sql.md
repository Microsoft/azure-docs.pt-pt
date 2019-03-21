---
title: Encriptação de dados transparente para o Azure SQL Database e o armazém de dados | Documentos da Microsoft
description: Uma descrição geral da criptografia de dados transparente para a base de dados SQL e o armazém de dados. O documento abrange suas vantagens e as opções de configuração, que inclui encriptação de dados transparente gerida pelo serviço e traga a sua própria chave.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3333355b742d531cc814484bdefbd47e7ab6c75c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878011"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Encriptação de dados transparente para a base de dados SQL e o armazém de dados

Encriptação de dados transparente (TDE) ajuda a proteger a base de dados do Azure SQL, a instância gerida do SQL do Azure e o armazém de dados do Azure contra ameaças de atividades maliciosas. Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação. Por predefinição, a TDE está ativada para todos os recentemente implementados bases de dados do SQL do Azure. TDE não pode ser utilizado para encriptar lógico **mestre** base de dados na base de dados SQL.  O **mestre** base de dados contém objetos que são necessárias para executar as operações de TDE nas bases de dados do utilizador.

TDE precisa ser habilitada manualmente para a instância de gerida de SQL no Azure, bases de dados mais antigos da base de dados do Azure SQL ou o Azure SQL Data Warehouse.  

Encriptação de dados transparente criptografa o armazenamento de uma base de dados completa com uma chave simétrica denominada a chave de encriptação da base de dados. Esta chave de encriptação da base de dados está protegido pelo protetor de encriptação de dados transparente. O componente de proteção é a um serviço gerido certificado (encriptação de dados transparente gerida pelo serviço) ou uma chave assimétrica armazenados no Azure Key Vault (traga a sua própria chave). Definir o protetor de encriptação de dados transparente no nível do servidor de base de dados do Azure SQL e o armazém de dados e nível de instância para a instância gerida do SQL do Azure. O termo *servidor* refere-se tanto ao servidor e a instância em todo este documento, a menos que indicado de forma diferente.

Na inicialização do banco de dados, a chave de encriptação da base de dados encriptado é descriptografada e, em seguida, utilizada para desencriptação e nova criptografia de arquivos de banco de dados no processo de motor de base de dados do SQL Server. Encriptação de dados transparente executa em tempo real e/s criptografia e descriptografia de dados no nível da página. Cada página é descriptografada quando é lido na memória e, em seguida, são encriptados antes de serem escritos no disco. Para obter uma descrição geral da encriptação transparente de dados, consulte [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server em execução numa máquina virtual do Azure também pode utilizar uma chave assimétrica do Key Vault. Os passos de configuração são diferentes de utilizar uma chave assimétrica na base de dados SQL e a instância gerida de SQL. Para obter mais informações, consulte [gestão de chaves extensível com o Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Encriptação de dados transparente gerida pelo serviço

No Azure, a definição de encriptação de dados transparente predefinida é que a chave de encriptação da base de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor. Se uma base de dados está numa relação de replicação geográfica, a base de dados primária e geo-secundária estão protegidas por chave de servidor da base de dados primário principal. Se duas bases de dados estiverem ligados ao mesmo servidor, eles também compartilham o mesmo certificado incorporado. Microsoft gira automaticamente estes certificados em conformidade com a política de segurança interna e a chave de raiz é protegida por um arquivo de segredos interno do Microsoft.

A Microsoft diretamente move e gerencia as chaves conforme necessário para georreplicação e restaura.

> [!IMPORTANT]
> Todas as recém-criadas bases de dados do SQL são criptografadas por padrão, usando a criptografia de dados transparente gerida pelo serviço. Bases de dados de instância gerida SQL do Azure, bases de dados do SQL existentes criadas antes de Maio de 2017 e bases de dados SQL criadas através do restauro, georreplicação e cópia de base de dados não são criptografadas por padrão.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Encriptação de dados transparente gerida pelo cliente – traga a sua própria chave

[TDE com chaves geridas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) permite criptografar a chave de encriptação da base de dados (DEK) com uma chave assimétrica gerida pelo cliente chamada Protetor de TDE.  Isso também geralmente é chamado para suportar a como Bring Your Own Key (BYOK) para encriptação de dados transparente. No cenário BYOK, o Protetor de TDE é armazenado numa propriedade de cliente e geridos [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), sistema de gestão chave externa baseada na nuvem do Azure. Pode ser o Protetor de TDE [gerado pelo Cofre de chaves ou transferidos para o Cofre de chaves](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) de um dispositivo HSM no local. O DEK TDE, que é armazenado na página de arranque de uma base de dados, é encriptado e é desencriptado pelo Protetor TDE, que é armazenada no Azure Key Vault e nunca sai do Cofre de chaves.  Base de dados SQL tem de ser concedidas permissões para o Cofre de chaves pertencentes ao cliente para desencriptar e encriptar o DEK. Se as permissões do servidor SQL lógico para o Cofre de chaves são revogadas, uma base de dados ficarão inacessível e todos os dados são encriptados. Para a base de dados SQL do Azure, o protetor de TDE está definido ao nível do servidor lógico da SQL e é herdado por todas as bases de dados associados a esse servidor. Para [instância gerida do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), o protetor de TDE está definido ao nível da instância e esta é herdada por todos os *encriptados* bases de dados nessa instância. O termo *servidor* refere-se tanto ao servidor e a instância em todo este documento, a menos que indicado de forma diferente.

Com TDE com a integração do Azure Key Vault, os utilizadores podem controlar as tarefas de gestão de chaves, incluindo rotações de chave, as permissões do Cofre de chaves, as cópias de segurança de chaves e ativar a auditoria/relatórios sobre todos os protetores de TDE com a funcionalidade do Azure Key Vault. Key Vault fornece gestão de chaves central, tira partido de módulos de segurança de rigidamente monitorizados de hardware (HSMs) e permite a separação de funções entre a gestão de chaves e dados para o ajudar a cumprir a conformidade com as políticas de segurança.
Para saber mais sobre a encriptação de dados transparente com a integração do Azure Key Vault (suporte de traga a sua própria chave) para a base de dados do Azure SQL, instância gerida SQL e armazém de dados, veja [encriptação de dados transparente com a integração do Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Para começar a utilizar a encriptação de dados transparente com a integração do Azure Key Vault (suporte de traga a sua própria chave), consulte o guia de procedimentos [ativar a criptografia transparente de dados utilizando a sua própria chave do Key Vault com o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover uma base de protegido por encriptação de dados transparente

Não precisa de desencriptar as bases de dados para operações no Azure. As definições de encriptação de dados transparente na base de dados de origem ou base de dados primária transparente são herdadas no destino. Envolvem a operações que estão incluídas:

- Georrestauro
- Restauro de ponto no tempo de self-service
- Restauro de bases de dados eliminadas
- Georreplicação ativa
- Criação de uma cópia da base de dados
- Restauro de ficheiro de cópia de segurança para instância gerida do SQL do Azure

> [!IMPORTANT]
> Criação manual apenas de cópia cópia de segurança das bases de dados criptografados por TDE gerida pelo serviço não é permitida na instância SQL do Azure gerido, uma vez que o certificado utilizado para encriptação não está acessível. Utilize a funcionalidade de ponto de restauro anterior no tempo mover este tipo de base de dados para outra instância gerida.

Ao exportar uma base de protegido por encriptação de dados transparente, o conteúdo exportado da base de dados não está encriptado. Este conteúdo exportado é armazenado nos arquivos BACPAC não encriptados. Certifique-se de que proteger os ficheiros BACPAC adequadamente e ativar a encriptação de dados transparente, depois de concluída a importação da nova base de dados.

Por exemplo, se o ficheiro BACPAC é exportado a partir de uma instância do SQL Server no local, o conteúdo importado da nova base de dados não encriptado automaticamente. Da mesma forma, se o ficheiro BACPAC é exportado para uma instância do SQL Server no local, a nova base de dados também não é automaticamente encriptado.

A única exceção é quando exportar de e para uma base de dados SQL. Encriptação de dados transparente está ativada na base de dados nova, mas o próprio ficheiro BACPAC ainda não está encriptado.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Gerir a encriptação transparente de dados no portal do Azure

Para configurar a encriptação de dados transparente através do portal do Azure, tem de estar ligado como o Azure proprietário, Contribuidor ou Gestor de segurança SQL.

Ativar e desativar a encriptação de dados transparente para o nível de base de dados. Para ativar a encriptação de dados transparente numa base de dados, vá para o [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta de administrador do Azure ou de contribuinte. Localize as definições de encriptação de dados transparente em sua base de dados do utilizador. Por predefinição, é utilizada a encriptação de dados transparente gerida pelo serviço. Um certificado de encriptação de dados transparente é gerado automaticamente para o servidor que contém a base de dados. Para a instância gerida do SQL do Azure, utilize T-SQL para ativar a encriptação de dados transparente e desativar uma base de dados.

![Encriptação de dados transparente gerida pelo serviço](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Defina a chave mestra de encriptação transparente de dados, também conhecido como o protetor de encriptação transparente de dados no nível do servidor. Para utilizar a encriptação de dados transparente com suporte de traga a sua própria chave e proteger seus bancos de dados com uma chave do Key Vault, abra as definições de encriptação de dados transparente no seu servidor.

![Encriptação de dados transparente com suporte de traga a sua própria chave](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Gerir a encriptação de dados transparente com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para configurar a encriptação de dados transparente através do PowerShell, tem de estar ligado como o Azure proprietário, Contribuidor ou Gestor de segurança SQL.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Cmdlets para a base de dados SQL do Azure e o armazém de dados

Utilize os seguintes cmdlets para a base de dados do Azure SQL e o armazém de dados:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Ativa ou desativa a encriptação de dados transparente para uma base de dados|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado de encriptação de dados transparente para uma base de dados |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso de criptografia para uma base de dados |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave de Cofre de chaves para uma instância do SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obtém as chaves do Cofre de chaves para um servidor de base de dados do Azure SQL  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de encriptação de dados transparente para uma instância do SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor de encriptação de dados transparente |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave de Cofre de chaves a partir de uma instância do SQL Server |
|  | |

> [!IMPORTANT]
> Para SQL instância gerida do Azure, utilize o T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) comandos para ativar e desativar a encriptação de dados transparente a um nível de base de dados e verificar [script do PowerShell de exemplo](transparent-data-encryption-byok-azure-sql-configure.md) para gerir dados transparente encriptação num nível de instância.

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Gerir a encriptação de dados transparente com o Transact-SQL

Ligar à base de dados com um início de sessão que seja administrador ou membro de uma a **dbmanager** função na base de dados mestra.

| Comando | Descrição |
| --- | --- |
| [Alterar base de dados (banco de dados SQL do Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | Definir ativa/inativa de encriptação encripta ou desencripta uma base de dados |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de uma base de dados e a respetiva base de dados associado chaves de encriptação |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de cada dados do armazém de nó e as chaves de encriptação de base de dados associada |
|  | |

Não é possível mudar o protetor de encriptação de dados transparente para uma chave do Key Vault com o Transact-SQL. Utilize o PowerShell ou o portal do Azure.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Gerir a encriptação de dados transparente com a API de REST

Para configurar a encriptação de dados transparente através da API REST, tem de estar ligado como o Azure proprietário, Contribuidor ou Gestor de segurança SQL.
Utilize o seguinte conjunto de comandos para a base de dados do Azure SQL e o armazém de dados:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar o servidor](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Adiciona uma identidade do Azure Active Directory para uma instância do SQL Server (utilizada para conceder acesso ao Cofre de chaves)|
|[Criar ou atualizar a chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave de Cofre de chaves para uma instância do SQL Server|
|[Eliminar chave de servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Remove uma chave de Cofre de chaves a partir de uma instância do SQL Server|
|[Obter chaves de servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtém uma chave específica do Key Vault a partir de uma instância do SQL Server|
|[Listar chaves de servidor por servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves do Cofre de chaves para uma instância do SQL Server |
|[Criar ou atualizar o Protetor de encriptação](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor de encriptação de dados transparente para uma instância do SQL Server|
|[Obter o Protetor de encriptação](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obtém o protetor de encriptação de dados transparente para uma instância do SQL Server|
|[Protetores de encriptação de lista pelo servidor](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores de encriptação de dados transparente para uma instância do SQL Server |
|[Criar ou atualizar a configuração da encriptação transparente de dados](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Ativa ou desativa a encriptação de dados transparente para uma base de dados|
|[Obter configuração de encriptação de dados transparente](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração de encriptação de dados transparente para uma base de dados|
|[Resultados de configuração de encriptação de dados transparente de lista](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado de encriptação para uma base de dados|

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma descrição geral da encriptação transparente de dados, consulte [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre a encriptação de dados transparente com suporte de traga a sua própria chave para a base de dados do Azure SQL, a instância gerida do SQL do Azure e a Data Warehouse, veja [encriptação de dados transparente com suporte de traga a sua própria chave](transparent-data-encryption-byok-azure-sql.md).
- Para começar a utilizar a encriptação de dados transparente com suporte de traga a sua própria chave, consulte o guia de procedimentos [ativar a criptografia transparente de dados utilizando a sua própria chave do Key Vault com o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Para obter mais informações sobre o Key Vault, consulte a [página de documentação do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
