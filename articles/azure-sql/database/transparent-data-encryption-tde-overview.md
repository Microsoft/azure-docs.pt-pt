---
title: Encriptação de dados transparente
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Uma visão geral da encriptação transparente de dados para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse Analytics. O documento cobre os seus benefícios e as opções de configuração, que inclui encriptação de dados transparentes geridas pelo serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: 8fbbd7a2aabc9de417f1eefd2513edba3119bfc0
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791397"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Encriptação de dados transparente para base de dados SQL, SQL Managed Instance e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[A encriptação de dados transparente (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) ajuda a proteger a Base de Dados Azure SQL, Azure SQL Managed Instance e Azure Synapse Analytics contra a ameaça de atividade offline maliciosa, encriptando dados em repouso. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. Por predefinição, o TDE está ativado para todas as bases de dados SQL recentemente implantadas e deve ser ativado manualmente para bases de dados mais antigas da Base de Dados Azure SQL, Azure SQL Managed Instance. O TDE deve ser ativado manualmente para o Azure Synapse Analytics.

O TDE executa a encriptação e desencriptação em tempo real dos dados ao nível da página. Cada página é desencriptada quando é lida na memória e encriptada antes de ser escrita no disco. O TDE encripta o armazenamento de uma base de dados inteira utilizando uma chave simétrica chamada Chave de Encriptação da Base de Dados (DEK). No arranque da base de dados, o DEK encriptado é desencriptado e depois utilizado para desencriptação e reencriminação dos ficheiros de base de dados no processo do motor do motor de base de dados sql Server. O DEK está protegido pelo protetor TDE. O protetor TDE é um certificado gerido pelo serviço (encriptação de dados transparente gerido pelo serviço) ou uma chave assimétrica armazenada no [Cofre da Chave Azure](../../key-vault/general/secure-your-key-vault.md) (encriptação de dados transparente gerida pelo cliente).

Para a Base de Dados Azure SQL e Azure Synapse, o protetor TDE é definido ao nível do [servidor](logical-servers.md) e é herdado por todas as bases de dados associadas a esse servidor. Para Azure SQL Managed Instance, o protetor TDE é definido ao nível de instância e é herdado por todas as bases de dados encriptadas nesse caso. O *servidor* de termo refere-se tanto ao servidor como à instância em todo este documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Todas as bases de dados recentemente criadas na Base de Dados SQL são encriptadas por padrão utilizando encriptação de dados transparente gerida pelo serviço. As bases de dados SQL existentes criadas antes de maio de 2017 e as bases de dados SQL criadas através da restauração, da geo-replicação e da cópia da base de dados não são encriptadas por padrão. As bases de dados existentes de SqL Managed Instance criadas antes de fevereiro de 2019 não são encriptadas por padrão. Bases de dados SQL Managed Instance criadas através da restauração do estado de encriptação herdada da fonte.

> [!NOTE]
> O TDE não pode ser usado para encriptar bases de dados de sistemas, como a base de dados **principal,** na Base de Dados Azure SQL e na Azure SQL Managed Instance. A base de dados **principal** contém objetos necessários para realizar as operações do TDE nas bases de dados dos utilizadores. Recomenda-se não armazenar dados sensíveis nas bases de dados do sistema. [A encriptação da infraestrutura](transparent-data-encryption-byok-overview.md#doubleencryption) está agora a ser lançada, que encripta as bases de dados do sistema, incluindo o mestre. 

## <a name="service-managed-transparent-data-encryption"></a>Encriptação de dados transparentes gerida pelo serviço

Em Azure, a definição padrão para TDE é que o DEK está protegido por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor e o algoritmo de encriptação utilizado é AES 256. Se uma base de dados estiver numa relação de geo-replicação, as bases de dados primárias e geoconsuais estão protegidas pela chave do servidor principal da base de dados. Se duas bases de dados estiverem ligadas ao mesmo servidor, também partilham o mesmo certificado incorporado. A Microsoft gira automaticamente estes certificados em conformidade com a política de segurança interna e a chave-raiz está protegida por uma loja secreta interna da Microsoft. Os clientes podem verificar o cumprimento da sql Database e sql Managed Instance com as políticas de segurança interna em relatórios independentes de auditoria de terceiros disponíveis no [Microsoft Trust Center](https://servicetrust.microsoft.com/).

A Microsoft também se move e gere as chaves conforme necessário para a geo-replicação e restauro.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Encriptação de dados transparente gerida pelo cliente - Traga a sua própria chave

O TDE gerido pelo cliente também é referido como suporte Bring Your Own Key (BYOK) para tDE. Neste cenário, o TDE Protetor que encripta o DEK é uma chave assimétrica gerida pelo cliente, que é armazenada num cofre de chaves Azure (sistema de gestão de chaves externo baseado na nuvem do Azure) e nunca sai do cofre chave. O Protetor TDE pode ser [gerado pelo cofre de chaves ou transferido para o cofre de uma chave a](../../key-vault/keys/hsm-protected-keys.md) partir de um dispositivo de segurança de hardware no local (HSM). Base de Dados SQL, SQL Managed Instance e Azure Synapse precisam de obter permissões para o cofre de chaves do cliente para desencriptar e encriptar o DEK. Se as permissões do servidor para o cofre de chaves forem revogadas, uma base de dados será inacessível e todos os dados são encriptados

Com a integração do TDE com a Azure Key Vault, os utilizadores podem controlar tarefas de gestão de chaves, incluindo rotações de chaves, permissões de cofre chave, cópias de segurança chave, e permitir a auditoria/reporte em todos os protetores TDE usando a funcionalidade Azure Key Vault. A Key Vault fornece uma gestão central de chaves, alavanca os HSMs rigorosamente monitorizados e permite a separação de deveres entre a gestão de chaves e dados para ajudar a cumprir as políticas de segurança.
Para saber mais sobre a BYOK para Azure SQL Database e Azure Synapse, consulte [encriptação de dados transparente com a integração do Azure Key Vault](transparent-data-encryption-byok-overview.md).

Para começar a utilizar o TDE com a integração do Azure Key Vault, consulte o guia Como guiar Ligue a [encriptação de dados transparente utilizando a sua própria chave a partir do Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover uma base de dados transparente protegida por encriptação

Não é preciso desencriptar bases de dados para operações dentro do Azure. As definições de TDE na base de dados de origem ou na base de dados primária são herdadas de forma transparente no alvo. As operações incluídas envolvem:

- Georrestauro
- Restauro do ponto de autosserviço no tempo
- Restauração de uma base de dados eliminada
- Georreplicação ativa
- Criação de uma cópia de base de dados
- Restaurar o ficheiro de backup para Azure SQL Managed Instance

> [!IMPORTANT]
> A utilização manual de cópias de uma base de dados encriptada pelo TDE gerido pelo serviço não é suportada em Azure SQL Managed Instance, uma vez que o certificado utilizado para encriptação não está acessível. Utilize a função de restauro ponto-em-tempo para mover este tipo de base de dados para outra SQL Managed Instance, ou mudar para a chave gerida pelo cliente.

Quando exporta uma base de dados protegida pelo TDE, o conteúdo exportado da base de dados não está encriptado. Este conteúdo exportado é armazenado em ficheiros BACPAC não encriptados. Certifique-se de proteger adequadamente os ficheiros BACPAC e permitir o TDE após a importação da nova base de dados.

Por exemplo, se o ficheiro BACPAC for exportado a partir de uma instância do SQL Server, o conteúdo importado da nova base de dados não é automaticamente encriptado. Da mesma forma, se o ficheiro BACPAC for importado para uma instância do SQL Server, a nova base de dados também não é automaticamente encriptada.

A única exceção é quando exporta uma base de dados de e para a Base de Dados SQL. O TDE está ativado na nova base de dados, mas o ficheiro BACPAC ainda não está encriptado.

## <a name="manage-transparent-data-encryption"></a>Gerir encriptação de dados transparentes

# <a name="the-azure-portal"></a>[O portal do Azure](#tab/azure-portal)

Gerir o TDE no portal Azure.

Para configurar o TDE através do portal Azure, tem de estar ligado como Proprietário, Colaborador ou Gestor de Segurança da SQL.

Ative e desative o TDE no nível da base de dados. Para a Azure SQL Managed Instance utilize o Transact-SQL (T-SQL) para ligar e desligar o TDE numa base de dados. Para a Base de Dados Azure SQL e Azure Synapse, pode gerir o TDE para a base de dados no [portal Azure](https://portal.azure.com) depois de ter assinado com a conta administrador ou contribuinte do Azure. Encontre as definições de TDE na base de dados do utilizador. Por padrão, é utilizada encriptação de dados transparentes gerida pelo serviço. Um certificado TDE é gerado automaticamente para o servidor que contém a base de dados.

![Encriptação de dados transparentes gerida pelo serviço](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Definiu a tecla master TDE, conhecida como protetor TDE, ao nível do servidor ou da instância. Para utilizar o TDE com suporte BYOK e proteger as suas bases de dados com uma chave do Key Vault, abra as definições de TDE debaixo do seu servidor.

![Encriptação de dados transparente com suporte bring your own key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gerir o TDE utilizando o PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para configurar o TDE através do PowerShell, tem de estar ligado como Proprietário, Colaborador ou Gestor de Segurança da SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets para Azure SQL Database e Azure Synapse

Utilize os seguintes cmdlets para a Base de Dados Azure SQL e para a Azure Synapse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Permite ou desativa encriptação de dados transparente para uma base de dados.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado de encriptação de dados transparente para uma base de dados. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso da encriptação para uma base de dados. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave do Cofre chave a um servidor. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Recebe as chaves do Cofre chave para um servidor  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de encriptação de dados transparente para um servidor. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor de encriptação de dados transparente |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave do cofre de uma servidora. |
|  | |

> [!IMPORTANT]
> Para Azure SQL Managed Instance, utilize o comando T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) para ligar e desligar o TDE num nível de base de dados e verifique a amostra do [script PowerShell](transparent-data-encryption-byok-configure.md) para gerir o TDE num nível de instância.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Gerir o TDE utilizando o Transact-SQL.

Ligue-se à base de dados utilizando um login que seja um administrador ou membro da função **dbmanager** na base de dados principal.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (Base de dados Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) | DEFINIR ENCRIPTAÇÃO ON/OFF encripta ou desencripta uma base de dados |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de uma base de dados e as chaves de encriptação da base de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Devolve informações sobre o estado de encriptação de cada nó Azure Synapse e as suas chaves de encriptação de base de dados associadas |
|  | |

Não é possível mudar o protetor TDE para uma chave do Key Vault utilizando o Transact-SQL. Utilize o PowerShell ou o portal Azure.

# <a name="rest-api"></a>[API REST](#tab/azure-RESTAPI)

Gerir o TDE utilizando a API REST.

Para configurar o TDE através da API REST, tem de estar ligado como Proprietário, Colaborador ou Gestor de Segurança da SQL.
Utilize o seguinte conjunto de comandos para Azure SQL Database e Azure Synapse:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar servidor](/rest/api/sql/servers/createorupdate)|Adiciona uma identidade do Azure Ative Directory a um servidor. (usado para conceder acesso ao Cofre-Chave)|
|[Criar ou atualizar chave de servidor](/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave do Cofre chave a um servidor.|
|[Eliminar a tecla do servidor](/rest/api/sql/serverkeys/delete)|Remove uma chave do cofre de uma servidora. |
|[Obter chaves de servidor](/rest/api/sql/serverkeys/get)|Obtém uma chave de cofre específica de um servidor.|
|[Listar chaves de servidor por servidor](/rest/api/sql/serverkeys/listbyserver)|Recebe as chaves do Cofre de Chaves para um servidor. |
|[Criar ou atualizar protetor de encriptação](/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor TDE para um servidor.|
|[Obtenha protetor de encriptação](/rest/api/sql/encryptionprotectors/get)|Obtém o protetor TDE para um servidor.|
|[Listar protetores de encriptação por servidor](/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores TDE para um servidor. |
|[Criar ou atualizar configuração de encriptação de dados transparentes](/rest/api/sql/transparentdataencryptions/createorupdate)|Ativa ou desativa o TDE para uma base de dados.|
|[Obtenha configuração de encriptação de dados transparentes](/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração TDE para uma base de dados.|
|[Lista resultados de configuração de encriptação de dados transparentes](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado da encriptação de uma base de dados.|

## <a name="see-also"></a>Consulte também

- O SQL Server a funcionar numa máquina virtual Azure também pode utilizar uma chave assimétrica do Key Vault. Os passos de configuração são diferentes de usar uma chave assimétrica na Base de Dados SQL e na SQL Managed Instance. Para obter mais informações, consulte [a gestão de chaves extensíveis utilizando o Cofre da Chave Azure (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Para obter uma descrição geral do TDE, consulte [encriptação de dados transparentes](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre o TDE com o suporte BYOK para Azure SQL Database, Azure SQL Managed Instance e Azure Synapse, consulte [encriptação de dados transparente com suporte Bring Your Own Key](transparent-data-encryption-byok-overview.md).
- Para começar a utilizar o suporte TDE com o suporte bring your own key, consulte o guia de como fazer, Ligue a [encriptação de dados transparente utilizando a sua própria chave a partir do Key Vault](transparent-data-encryption-byok-configure.md).
- Para obter mais informações sobre o Key Vault, consulte [acesso seguro a um cofre de chaves.](../../key-vault/general/secure-your-key-vault.md)