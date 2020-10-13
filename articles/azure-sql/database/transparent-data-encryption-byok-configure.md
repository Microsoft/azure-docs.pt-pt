---
title: Ativar SQL TDE com cofre de chave Azure
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Aprenda a configurar uma Base de Dados Azure SQL e Azure Synapse Analytics para começar a usar encriptação de dados transparentes (TDE) para encriptação em repouso utilizando o PowerShell ou o Azure CLI.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e2cdf7d5213f1667b0b588cc5bfa9f105245b6b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619122"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e o Azure CLI: Ativar a encriptação de dados transparente com a chave gerida pelo cliente a partir do Cofre da Chave Azure
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este artigo percorre como usar uma chave do Cofre de Chaves Azure para encriptação de dados transparentes (TDE) na Base de Dados Azure SQL ou Azure Synapse Analytics (anteriormente SQL Data Warehouse). Para saber mais sobre o TDE com a integração do Azure Key Vault - Bring Your Own Key (BYOK) Support, visite [o TDE com chaves geridas pelo cliente no Cofre da Chave Azure](transparent-data-encryption-byok-overview.md).

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

- Você deve ter uma subscrição Azure e ser um administrador nessa subscrição.
- [Recomendado, mas opcional] Tenha um módulo de segurança de hardware (HSM) ou loja de chaves local para criar uma cópia local do material chave TDE Protetor.
- Deve ter a Azure PowerShell instalada e a funcionar.
- Crie um cofre e chave de chave Azure para usar para TDE.
  - [Instruções para a utilização de um módulo de segurança de hardware (HSM) e cofre de chaves](../../key-vault/keys/hsm-protected-keys.md)
    - O cofre-chave deve ter a seguinte propriedade a ser utilizada para tDE:
  - [proteção para eliminar](../../key-vault/general/soft-delete-overview.md) e limpar soft-delete
- A chave deve ter os seguintes atributos a utilizar para o TDE:
  - Sem data de validade
  - Não incapacitado
  - Capaz de executar *obter,* *embrulhar a chave,* *desembrulhar* as operações-chave

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Para obter detalhes no Cofre de Chaves, consulte [as instruções powerShell do Key Vault](../../key-vault/secrets/quick-create-powershell.md) e Como utilizar o Key Vault [soft-delete com PowerShell](../../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda está suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Atribua uma identidade do Azure Ative Directory (Azure AD) ao seu servidor

Se tiver um [servidor](logical-servers.md)existente, utilize o seguinte para adicionar uma identidade do Azure Ative Directory (Azure AD) ao seu servidor:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Se estiver a criar um servidor, utilize o cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) com a etiqueta -Identidade para adicionar uma identidade AD Azure durante a criação do servidor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceda permissões do Cofre chave ao seu servidor

Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para garantir o acesso do servidor ao cofre antes de utilizar uma chave para o TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicione a chave do cofre ao servidor e desaprote o Protetor TDE

- Use o [cmdlet Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) para recuperar o ID da chave do cofre de chaves
- Utilize o [cmdlet Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) para adicionar a chave do Cofre de Chaves ao servidor.
- Utilize o [cmdlet Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) para definir a chave como protetor TDE para todos os recursos do servidor.
- Utilize o [cmdlet Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) para confirmar que o protetor TDE foi configurado como pretendido.

> [!NOTE]
> O comprimento combinado para o nome do cofre e nome chave não pode exceder 94 caracteres.

> [!TIP]
> Um exemplo KeyId do Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Ligue o TDE

Utilize o [cmdlet Set-AzSqlDataTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para ligar o TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Agora a base de dados ou armazém de dados tem TDE ativado com uma chave de encriptação no Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verifique o estado de encriptação e a atividade de encriptação

Utilize o [Get-AzSqlDatabaseTransparentDataEncrycryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) para obter o estado de encriptação e o [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) para verificar o progresso da encriptação de uma base de dados ou armazém de dados.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para instalar a versão necessária do Azure CLI (versão 2.0 ou posterior) e ligar-se à sua subscrição Azure, consulte [instalar e configurar a Interface Command-Line Interface 2.0 da Plataforma Cruzada Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)

Para obter detalhes no Key Vault, consulte [Gerir o Cofre de Chaves utilizando o CLI 2.0](../../key-vault/general/manage-with-cli2.md) e como utilizar o Key Vault [soft-delete com o CLI](../../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribua uma identidade AD Azure ao seu servidor

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Mantenha o "principalID" de criar o servidor, é o id de objeto usado para atribuir permissões de cofre chave no passo seguinte

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceda permissões do Cofre chave ao seu servidor

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Mantenha a chave URI ou keyID da nova chave para o próximo passo, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicione a chave do cofre ao servidor e desaprote o Protetor TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> O comprimento combinado para o nome do cofre e nome chave não pode exceder 94 caracteres.

## <a name="turn-on-tde"></a>Ligue o TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Agora, a base de dados ou armazém de dados tem O TDE ativado com uma chave de encriptação gerida pelo cliente no Cofre da Chave Azure.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verifique o estado de encriptação e a atividade de encriptação

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Utilize o [cmdlet Set-AzSqlDataTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para desligar o TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Utilize o [cmdlet Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para devolver a lista de chaves do Cofre de Chaves adicionadas ao servidor.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Utilize o [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) para remover uma chave do cofre do servidor.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

- Para as definições gerais da base de dados, consulte [az sql](/cli/azure/sql).

- Para as definições das chaves do cofre, consulte [a tecla do servidor az sql](/cli/azure/sql/server/key).

- Para as definições de TDE, consulte [a chave de tde do servidor az sql](/cli/azure/sql/server/tde-key) e [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Resolução de problemas

Verifique se ocorre um problema:

- Se o cofre não for encontrado, certifique-se de que está na subscrição certa.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Se a nova chave não puder ser adicionada ao servidor, ou se a nova chave não puder ser atualizada como protetor TDE, verifique o seguinte:
   - A chave não deve ter uma data de validade
   - A chave deve ter a *chave get*, *wrap key*, e *desembrulhar* as operações das chaves ativadas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como rodar o Protetor TDE de um servidor para cumprir os requisitos de segurança: [Rode o protetor de encriptação de dados transparente utilizando o PowerShell](transparent-data-encryption-byok-key-rotation.md).
- Em caso de risco de segurança, aprenda a remover um Protetor TDE potencialmente comprometido: [Remova uma chave potencialmente comprometida](transparent-data-encryption-byok-remove-tde-protector.md).
