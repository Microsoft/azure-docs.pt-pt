---
title: Remover o protetor de TDE – PowerShell
description: Guia de instruções para responder a um protetor de TDE potencialmente comprometido para um banco de dados SQL do Azure ou data warehouse usando TDE com suporte a BYOK (traga sua própria chave).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: be187e34e3232c0755e2613ffffe0647da70079c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201667"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de Transparent Data Encryption (TDE) usando o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura
- Tem de ter o Azure PowerShell instalado e em funcionamento.
- Este guia de instruções pressupõe que você já está usando uma chave de Azure Key Vault como o protetor de TDE para um banco de dados SQL do Azure ou data warehouse. Consulte a [Encriptação transparente de dados com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para a instalação, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Descrição geral

Este guia de instruções descreve como responder a um protetor de TDE potencialmente comprometido para um banco de dados SQL do Azure ou data warehouse que está usando o TDE com chaves gerenciadas pelo cliente no suporte a BYOK (Azure Key Vault-Bring Your Own Key). Para saber mais sobre o suporte byok para TDE, consulte a [página de visão geral](transparent-data-encryption-byok-azure-sql.md).

Os procedimentos a seguir só devem ser feitos em casos extremos ou em ambientes de teste. Reveja cuidadosamente o guia como guiar, uma vez que a apagar protetores TDE ativamente utilizados do Cofre chave Azure pode resultar na perda de **dados**.

Se uma chave já suspeita de comprometimento, de modo que um serviço ou usuário tivesse acesso não autorizado à chave, é melhor excluir a chave.

Tenha em mente que uma vez que o protetor TDE é eliminado no Cofre chave, todas as ligações às bases de **dados encriptadas sob o servidor estão bloqueadas, e estas bases de dados ficam offline e são retiradas dentro**de 24 horas . Os backups antigos criptografados com a chave comprometida não são mais acessíveis.

As etapas a seguir descrevem como verificar as impressões digitais do protetor de TDE que ainda estão em uso por VLF (arquivos de log virtuais) de um determinado banco de dados.
A impressão digital do protetor de TDE atual do banco de dados e a ID do banco de dados podem ser encontradas executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

A consulta a seguir retorna o VLFs e o criptografador respectivas impressões digitais em uso. Cada impressão digital diferente refere-se a uma chave diferente em Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

O comando PowerShell **Get-AzureRmSqlServerKeyKey ** fornece a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a manter e quais as teclas a eliminar no AKV. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

O programa de teclas do **servidor az sql** de comando PowerShell fornece a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a manter e quais as teclas a eliminar no AKV. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

* * *

Este guia de instruções passa por duas abordagens dependendo do resultado desejado após a resposta a incidentes:

- Para manter as bases de dados Azure SQL / Data Warehouses **acessíveis**
- Para tornar **inacessíveis** as bases de dados Azure SQL / Data Warehouses

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos criptografados acessíveis

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Cofre chave.](/powershell/module/az.keyvault/add-azkeyvaultkey) Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, pois o controle de acesso é provisionado em um nível de cofre.

2. Adicione a nova chave ao servidor utilizando o [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [o Set-AzSqlServerTransparentTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets e atualize-o como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas atualizaram para o novo protetor TDE utilizando o [Get-AzSqlServerTransparentDataCryptOnCryptEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet.

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor de TDE se propague para todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Pegue uma [cópia da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) no Cofre chave.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Elimine a tecla comprometida do Cofre de Chaves utilizando o cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para o Cofre chave no futuro usando o cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para referência ao comando, consulte o cofre de [chaves Azure CLI](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Cofre chave.](/cli/azure/keyvault/key#az-keyvault-key-create) Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, pois o controle de acesso é provisionado em um nível de cofre.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor de TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Verifique se o servidor e as réplicas foram atualizados para o novo protetor de TDE.

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor de TDE se propague para todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Faça um backup da nova chave em Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Exclua a chave comprometida de Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar uma chave para Key Vault no futuro.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para tornar os recursos criptografados inacessíveis

1. Remova os bancos de dados que estão sendo criptografados pela chave potencialmente comprometida.

   O backup do banco de dados e dos arquivos de log é feito automaticamente, portanto, uma restauração pontual do banco de dados pode ser feita em qualquer ponto (desde que você forneça a chave). Os bancos de dados devem ser descartados antes da exclusão de um protetor de TDE ativo para evitar a possível perda de dados de até 10 minutos das transações mais recentes.

2. Faça backup do material da chave do protetor de TDE no Key Vault.
3. Remover a chave potencialmente comprometida de Key Vault

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Passos seguintes

- Aprenda a rodar o protetor TDE de um servidor para cumprir os requisitos de segurança: Rode o protetor de encriptação transparente de [dados utilizando powerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Inicie com bring your own key support for TDE: [Ligue tDE usando a sua própria chave do Key Vault usando powerShell](transparent-data-encryption-byok-azure-sql-configure.md)
