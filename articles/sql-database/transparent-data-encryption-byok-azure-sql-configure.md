---
title: Habilitar TDE SQL com Azure Key Vault
description: Saiba como configurar um banco de dados SQL do Azure e data warehouse para começar a usar o Transparent Data Encryption (TDE) para criptografia em repouso usando o PowerShell ou a CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 87a9db7d320a7d5b35234899c59884bcf2bf4b60
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721681"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell e CLI: habilitar Transparent Data Encryption com chave gerenciada pelo cliente de Azure Key Vault

Este artigo explica como usar uma chave de Azure Key Vault para Transparent Data Encryption (TDE) em um banco de dados SQL ou data warehouse. Para saber mais sobre o TDE com integração do Cofre de Chaves Azure - Traga o suporte da sua própria chave (BYOK), visite [o TDE com chaves geridas pelo cliente no Cofre de Chaves Azure](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para o PowerShell

- Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Dispor de um módulo de segurança de hardware (HSM) ou de uma loja-chave local para criar uma cópia local do material-chave Do Protetor TDE.
- Você deve ter Azure PowerShell instalado e em execução.
- Crie um Azure Key Vault e uma chave para usar para TDE.
  - [Instruções para a utilização de um módulo de segurança de hardware (HSM) e key vault](../key-vault/key-vault-hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) e purga proteção
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Nenhuma data de validade
   - Não desabilitado
   - Capaz de realizar as operações chave de *embrulho,* *desembrulhar*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Para obter detalhes no Cofre de Chaves, consulte [as instruções da PowerShell do Key Vault](../key-vault/quick-create-powershell.md) e como utilizar o [Soft-delete key Vault com powerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao seu servidor

Se você tiver um servidor existente, use o seguinte para adicionar uma identidade do Azure AD ao seu servidor:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Se estiver a criar um servidor, utilize o cmdlet [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) com a etiqueta -Identidade para adicionar uma identidade Azure AD durante a criação do servidor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões de Key Vault ao seu servidor

Utilize o [cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para garantir o acesso ao servidor ao cofre da chave antes de utilizar uma chave para tDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave de Key Vault ao servidor e definir o protetor de TDE

- Use o cmdlet [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) para recuperar o ID chave do cofre chave
- Utilize o cmdlet [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) para adicionar a chave do Cofre chave ao servidor.
- Utilize o [Set-AzSqlServerTransparentTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet para definir a tecla como protetor TDE para todos os recursos do servidor.
- Utilize o [Get-AzSqlServerTransparentTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet para confirmar que o protetor TDE foi configurado como pretendido.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

> [!TIP]
> Um exemplo keyid de Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

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

Utilize o [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet para ligar o TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Agora o banco de dados ou data warehouse tem TDE habilitado com uma chave de criptografia em Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar a atividade de criptografia e estado de criptografia

Utilize o [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) para obter o estado de encriptação e a [Get-AzSqlDatabaseTransparentTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) para verificar o progresso da encriptação para uma base de dados ou armazém de dados.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para instalar a necessária interface de linha de comando 2.0 ou posteriormente e ligar-se à sua subscrição Azure, consulte Instalar e Configurar a Interface de [Linha de Comando de Plataforma Cruzada Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Para obter detalhes no Cofre de Chaves, consulte [Gerir o Cofre de Chaves utilizando cli 2.0](../key-vault/key-vault-manage-with-cli2.md) e como utilizar o [soft-delete key vault com CLI](../key-vault/key-vault-soft-delete-cli.md).

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
> Mantenha a chave URI ou keyID da nova chave para o próximo passo, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

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

- Utilize o [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet para desligar o TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Utilize o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para devolver a lista das teclas key vault adicionadas ao servidor.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Utilize o [Remove-AzSqlServerKeyVaultKeyKeypara](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) remover uma chave vault do servidor.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

- Para obter configurações gerais de base de dados, consulte [az sql](/cli/azure/sql).

- Para configurações da chave do cofre, consulte [a tecla do servidor az sql](/cli/azure/sql/server/key).

- Para as definições de TDE, consulte [az sql server tde-key](/cli/azure/sql/server/tde-key) e [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Resolução de Problemas

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
   - A chave deve ter *as*operações de get, *wrap key*e *desembrulhar* as operações de chave ativadas.

## <a name="next-steps"></a>Passos Seguintes

- Aprenda a rodar o Protetor TDE de um servidor para cumprir os requisitos de segurança: Rode o protetor de encriptação transparente de [dados utilizando powerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Em caso de risco de segurança, aprenda a remover um Protetor TDE potencialmente comprometido: [Remova uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
