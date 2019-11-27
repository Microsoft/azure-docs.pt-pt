---
title: Habilitar TDE SQL com Azure Key Vault
description: Saiba como configurar um banco de dados SQL do Azure e data warehouse para começar a usar o Transparent Data Encryption (TDE) para criptografia em repouso usando o PowerShell ou a CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e60b83f8eb1e95733512180417bb2a1be679b0a0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422460"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e CLI: habilitar Transparent Data Encryption com chave gerenciada pelo cliente de Azure Key Vault

Este artigo explica como usar uma chave de Azure Key Vault para Transparent Data Encryption (TDE) em um banco de dados SQL ou data warehouse. Para saber mais sobre o TDE com suporte ao BYOK (Bring Your Own Key integração com o Azure Key Vault), visite [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para o PowerShell

- Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Ter um HSM (módulo de segurança de hardware) ou um repositório de chaves local para criar uma cópia local do material da chave do protetor de TDE.
- Você deve ter Azure PowerShell instalado e em execução.
- Crie um Azure Key Vault e uma chave para usar para TDE.
  - [Instruções para usar um HSM (módulo de segurança de hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [exclusão reversível](../key-vault/key-vault-ovw-soft-delete.md) e limpeza da proteção
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Nenhuma data de validade
   - Não desabilitado
   - Capaz de executar operações *Get*, *Wrap Key*, não *encapsular chave*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Para obter informações específicas sobre Key Vault, consulte [instruções do PowerShell do Key Vault](../key-vault/quick-create-powershell.md) e [como usar Key Vault exclusão reversível com o PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao seu servidor

Se você tiver um servidor existente, use o seguinte para adicionar uma identidade do Azure AD ao seu servidor:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Se você estiver criando um servidor, use o cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) com a marca-Identity para adicionar uma identidade do Azure ad durante a criação do servidor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões de Key Vault ao seu servidor

Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder ao servidor acesso ao cofre de chaves antes de usar uma chave a partir dele para TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave de Key Vault ao servidor e definir o protetor de TDE

- Use o cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) para recuperar a ID de chave do Key Vault
- Use o cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) para adicionar a chave do Key Vault ao servidor.
- Use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) para definir a chave como o protetor de TDE para todos os recursos do servidor.
- Use o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) para confirmar se o protetor de TDE foi configurado como pretendido.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

> [!TIP]
> Um KeyId de exemplo de Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Ativar TDE

Use o cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para ativar o TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Agora o banco de dados ou data warehouse tem TDE habilitado com uma chave de criptografia em Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar a atividade de criptografia e estado de criptografia

Use [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) para obter o estado de criptografia e [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) para verificar o progresso da criptografia para um banco de dados ou data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para instalar a interface de linha de comando necessária versão 2,0 ou posterior e conectar-se à sua assinatura do Azure, consulte [instalar e configurar a interface de linha de comando de plataforma cruzada do azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Para obter informações específicas sobre Key Vault, consulte [gerenciar Key Vault usando a cli 2,0](../key-vault/key-vault-manage-with-cli2.md) e [como usar Key Vault exclusão reversível com a CLI](../key-vault/key-vault-soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao seu servidor

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Mantenha a "PrincipalId" de criar o servidor, é a ID de objeto usada para atribuir permissões do cofre de chaves na próxima etapa

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões de Key Vault ao seu servidor

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Mantenha o URI de chave ou keyID da nova chave para a próxima etapa, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave de Key Vault ao servidor e definir o protetor de TDE

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

## <a name="turn-on-tde"></a>Ativar TDE

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Agora o banco de dados ou data warehouse tem TDE habilitado com uma chave de criptografia gerenciada pelo cliente no Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar a atividade de criptografia e estado de criptografia

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets do PowerShell útil

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Use o cmdlet [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) para desligar o TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Use o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para retornar a lista de chaves de Key Vault adicionadas ao servidor.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use o [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) para remover uma chave de Key Vault do servidor.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

- Para configurações gerais de banco de dados, consulte [AZ SQL](/cli/azure/sql).

- Para configurações de chave do cofre, consulte [AZ SQL Server Key](/cli/azure/sql/server/key).

- Para configurações de TDE, consulte [AZ SQL Server TDE-Key](/cli/azure/sql/server/tde-key) e [AZ SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Resolução de problemas

Se ocorrer um problema, verifique o seguinte:

- Se o cofre de chaves não puder ser encontrado, verifique se você está na assinatura correta.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Se a nova chave não puder ser adicionada ao servidor ou se a nova chave não puder ser atualizada como o protetor de TDE, verifique o seguinte:
   - A chave não deve ter uma data de expiração
   - A chave deve ter as operações *Get*, *Wrap Key*e *Unwrap Key* habilitadas.

## <a name="next-steps"></a>Passos seguintes

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- No caso de um risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
