---
title: 'Transparent Data Encryption para o banco de dados SQL do Azure e data warehouse '
description: Uma visão geral da Transparent Data Encryption para o banco de dados SQL e data warehouse. O documento aborda seus benefícios e as opções de configuração, que inclui criptografia de dados transparente gerenciada por serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: ff712f956278a2a54584c0b0346f8f1147add189
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686802"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent Data Encryption para o banco de dados SQL e data warehouse

A TDE (Transparent Data Encryption) ajuda a proteger o banco de dados SQL do Azure, o SQL Instância Gerenciada do Azure e o Azure data warehouse contra a ameaça de atividades offline mal-intencionadas, criptografando os dados em repouso. Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, o TDE está habilitado para todos os bancos de dados SQL do Azure implantados recentemente. TDE não pode ser usado para criptografar o banco de dados **mestre** lógico no banco de dados SQL.  O banco de dados **mestre** contém objetos que são necessários para executar as operações TDE nos bancos de dados de usuário.

O TDE precisa ser habilitado manualmente para bancos de dados mais antigos do banco de dados SQL do Azure, Azure SQL Instância Gerenciada ou SQL Data Warehouse do Azure.
Instância Gerenciada bancos de dados criados por meio da restauração herdam o status de criptografia do banco de dados de origem.

A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados. Essa chave de criptografia de banco de dados é protegida pelo protetor de Transparent Data Encryption. O protetor é um certificado gerenciado por serviço (criptografia de dados transparente gerenciada pelo serviço) ou uma chave assimétrica armazenada em Azure Key Vault (Bring Your Own Key). Você define o protetor de Transparent Data Encryption no nível do servidor para o banco de dados SQL do Azure e data warehouse e o nível de instância para o SQL Instância Gerenciada do Azure. O termo *servidor* se refere ao servidor e à instância em todo este documento, a menos que indicado de forma diferente.

Na inicialização do banco de dados, a chave de criptografia do banco de dados criptografado é descriptografada e, em seguida, usada para descriptografia e recriptografia dos arquivos de banco de dados no processo de Mecanismo de Banco de Dados SQL Server. A Transparent Data Encryption executa criptografia de e/s em tempo real e a descriptografia dos dados no nível da página. Cada página é descriptografada quando lida na memória e, em seguida, criptografada antes de ser gravada no disco. Para obter uma descrição geral da Transparent Data Encryption, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server em execução em uma máquina virtual do Azure também podem usar uma chave assimétrica de Key Vault. As etapas de configuração são diferentes de usar uma chave assimétrica no banco de dados SQL e no SQL Instância Gerenciada. Para obter mais informações, consulte [gerenciamento extensível de chaves usando Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Criptografia de dados transparente gerenciada por serviço

No Azure, a configuração padrão para Transparent Data Encryption é que a chave de criptografia do banco de dados é protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor e o algoritmo de criptografia usado é o AES 256. Se um banco de dados estiver em um relacionamento de replicação geográfica, o banco de dados primário e o secundário geográfico serão protegidos pela chave de servidor pai do banco de dados primário. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles também compartilharão o mesmo certificado interno.  A Microsoft gira automaticamente esses certificados em conformidade com a política de segurança interna e a chave raiz é protegida por um armazenamento de segredo interno da Microsoft.  Os clientes podem verificar a conformidade do banco de dados SQL com políticas de segurança interna em relatórios de auditoria de terceiros independentes disponíveis na [central de confiabilidade da Microsoft](https://servicetrust.microsoft.com/).

A Microsoft também move e gerencia diretamente as chaves conforme necessário para a replicação e as restaurações geográficas.

> [!IMPORTANT]
> Todos os bancos de dados SQL criados recentemente e bancos de dado Instância Gerenciada são criptografados por padrão usando a Transparent Data Encryption gerenciada por serviço. Os bancos de dados SQL existentes criados antes de 2017 de maio e de SQL criados por meio da restauração, da replicação geográfica e da cópia do banco de dados não são criptografados por padrão. Os bancos de dados Instância Gerenciada existentes criados antes de fevereiro de 2019 não são criptografados por padrão. Instância Gerenciada bancos de dados criados por meio da restauração herdam o status de criptografia da origem.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gerenciada pelo cliente-Bring Your Own Key

[TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) permite criptografar a DEK (chave de criptografia de banco de dados) com uma chave assimétrica gerenciada pelo cliente chamada protetor TDE.  Isso também é conhecido como suporte a Bring Your Own Key (BYOK) para Transparent Data Encryption. No cenário BYOK, o protetor de TDE é armazenado em um [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)gerenciado e de Propriedade do cliente, o sistema de gerenciamento de chaves externas baseado em nuvem do Azure. O protetor de TDE pode ser [gerado pelo cofre de chaves ou transferido para o cofre de chaves](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) de um dispositivo HSM local. O TDE DEK, que é armazenado na página de inicialização de um banco de dados, é criptografado e descriptografado pelo protetor TDE, que é armazenado em Azure Key Vault e nunca deixa o cofre de chaves.  O banco de dados SQL precisa receber permissões para o cofre de chaves de Propriedade do cliente para descriptografar e criptografar o DEK. Se as permissões do SQL Server lógico para o cofre de chaves forem revogadas, um banco de dados ficará inacessível e todos eles serão criptografados. Para o banco de dados SQL do Azure, o protetor TDE é definido no nível lógico do SQL Server e é herdado por todos os bancos de dados associados a esse servidor. Para o [Azure SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), o protetor de TDE é definido no nível de instância e é herdado por todos os bancos de dados *criptografados* nessa instância. O termo *servidor* se refere ao servidor e à instância em todo este documento, a menos que indicado de forma diferente.

Com a integração do TDE com o Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chaves, permissões do Key Vault, backups de chave e habilitar a auditoria/relatórios em todos os protetores do TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento de chaves central, aproveita os HSMs (módulos de segurança de hardware) monitorados e permite a separação de tarefas entre o gerenciamento de chaves e dados para ajudar a atender às políticas de segurança.
Para saber mais sobre a Transparent Data Encryption com integração de Azure Key Vault (suporte a Bring Your Own Key) para o banco de dados SQL do Azure, SQL Instância Gerenciada e data warehouse, consulte [Transparent Data Encryption com integração de Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Para começar a usar a criptografia de dados transparente com a integração de Azure Key Vault (suporte a Bring Your Own Key), consulte o guia de instruções [ativar a Transparent Data Encryption usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover um banco de dados protegido por Transparent Data Encryption

Você não precisa descriptografar bancos de dados para operações no Azure. As configurações de Transparent Data Encryption no banco de dados de origem ou banco de dado primário são herdadas de forma transparente no destino. As operações incluídas envolvem:

- Georrestauro
- Restauração pontual do autoatendimento
- Restauração de um banco de dados excluído
- Georreplicação ativa
- Criação de uma cópia de banco de dados
- Restauração do arquivo de backup no Azure SQL Instância Gerenciada

> [!IMPORTANT]
> Não é permitido fazer backup somente cópia manual de um banco de dados criptografado pelo TDE gerenciado pelo serviço no Azure SQL Instância Gerenciada, pois o certificado usado para criptografia não está acessível. Use o recurso de restauração pontual para mover esse tipo de banco de dados para outro Instância Gerenciada.

Quando você exporta um banco de dados protegido por Transparent Data Encryption, o conteúdo exportado não é criptografado. Esse conteúdo exportado é armazenado em arquivos BACPAC não criptografados. Certifique-se de proteger os arquivos BACPAC adequadamente e habilitar a Transparent Data Encryption após a conclusão da importação do novo banco de dados.

Por exemplo, se o arquivo BACPAC for exportado de uma instância de SQL Server local, o conteúdo importado do novo banco de dados não será criptografado automaticamente. Da mesma forma, se o arquivo BACPAC for exportado para uma instância de SQL Server local, o novo banco de dados também não será criptografado automaticamente.

A única exceção é quando você exporta de e para um banco de dados SQL. A Transparent Data Encryption está habilitada no novo banco de dados, mas o próprio arquivo BACPAC ainda não é criptografado.


## <a name="manage-transparent-data-encryption"></a>Gerenciar a Transparent Data Encryption
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Gerencie a Transparent Data Encryption no portal do Azure.

Para configurar a Transparent Data Encryption por meio do portal do Azure, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.

Você ativa e desativa a Transparent Data Encryption no nível do banco de dados. Para habilitar a Transparent Data Encryption em um banco de dados, acesse o [portal do Azure](https://portal.azure.com) e entre com sua conta de administrador ou colaborador do Azure. Localize as configurações de Transparent Data Encryption no seu banco de dados de usuário. Por padrão, a criptografia de dados transparente gerenciada por serviço é usada. Um certificado de Transparent Data Encryption é gerado automaticamente para o servidor que contém o banco de dados. Para o Azure SQL Instância Gerenciada use o T-SQL para ativar e desativar a Transparent Data Encryption em um banco de dados.

![Criptografia de dados transparente gerenciada por serviço](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Você define a chave mestra de Transparent Data Encryption, também conhecida como o protetor de Transparent Data Encryption, no nível do servidor. Para usar a Transparent Data Encryption com suporte a Bring Your Own Key e proteger seus bancos de dados com uma chave de Key Vault, abra as configurações de Transparent Data Encryption em seu servidor.

![Transparent Data Encryption com suporte a Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Gerencie a Transparent Data Encryption usando o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para configurar a Transparent Data Encryption por meio do PowerShell, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Cmdlets para o banco de dados SQL do Azure e data warehouse

Use os seguintes cmdlets para o banco de dados SQL do Azure e data warehouse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado da Transparent Data Encryption para um banco de dados |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso da criptografia de um banco de dados |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave de Key Vault a uma instância de SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obter as chaves de Key Vault para um servidor de banco de dados SQL do Azure  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de Transparent Data Encryption para uma instância de SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obter o protetor de Transparent Data Encryption |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave de Key Vault de uma instância de SQL Server |
|  | |

> [!IMPORTANT]
> Para o Azure SQL Instância Gerenciada, use o comando T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) para ativar e desativar a Transparent Data Encryption em um nível de banco de dados e verifique o [exemplo de script do PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) para gerenciar a Transparent Data Encryption em um nível de instância.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gerencie a Transparent Data Encryption usando o Transact-SQL.

Conecte-se ao banco de dados usando um logon que seja um administrador ou membro da função **DBManager** no banco de dados mestre.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (banco de dados SQL do Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | DEFINIR ativar/desativar criptografia criptografa ou descriptografa um banco de dados |
| [sys. dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de um banco de dados e suas chaves de criptografia de banco de dados associadas |
| [sys. dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de cada nó de data warehouse e suas chaves de criptografia de banco de dados associadas |
|  | |

Não é possível alternar o protetor de Transparent Data Encryption para uma chave de Key Vault usando o Transact-SQL. Use o PowerShell ou o portal do Azure.

# <a name="rest-apitabazure-restapi"></a>[API REST](#tab/azure-RESTAPI)
Gerencie a Transparent Data Encryption usando a API REST.

Para configurar a Transparent Data Encryption por meio da API REST, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.
Use o seguinte conjunto de comandos para o banco de dados SQL do Azure e data warehouse:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar servidor](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Adiciona uma identidade de Azure Active Directory a uma instância de SQL Server (usada para conceder acesso ao Key Vault)|
|[Criar ou atualizar chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave de Key Vault a uma instância de SQL Server|
|[Excluir chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Remove uma chave de Key Vault de uma instância de SQL Server|
|[Obter chaves de servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtém uma chave de Key Vault específica de uma instância de SQL Server|
|[Listar chaves de servidor por servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obter as chaves de Key Vault para uma instância de SQL Server |
|[Criar ou atualizar o protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor de Transparent Data Encryption para uma instância de SQL Server|
|[Obter o protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obter o protetor de Transparent Data Encryption para uma instância de SQL Server|
|[Listar protetores de criptografia por servidor](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obter os protetores de Transparent Data Encryption para uma instância de SQL Server |
|[Criar ou atualizar Transparent Data Encryption configuração](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
|[Obter Transparent Data Encryption configuração](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração de Transparent Data Encryption para um banco de dados|
|[Listar resultados de configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado da criptografia para um banco de dados|

## <a name="next-steps"></a>Passos seguintes

- Para obter uma descrição geral da Transparent Data Encryption, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre a Transparent Data Encryption com suporte de Bring Your Own Key para o banco de dados SQL do Azure, o SQL Instância Gerenciada do Azure e o data warehouse, consulte [Transparent Data Encryption com suporte a Bring your own Key](transparent-data-encryption-byok-azure-sql.md).
- Para começar a usar a Transparent Data Encryption com suporte a Bring Your Own Key, consulte o guia de instruções [ativar a Transparent Data Encryption usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Para obter mais informações sobre Key Vault, consulte a [página de documentação do Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
