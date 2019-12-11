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
ms.date: 03/12/2019
ms.openlocfilehash: d4c9b926afe93f52946c5f1adf40835f72812f2a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995827"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de Transparent Data Encryption (TDE) usando o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura
- Você deve ter Azure PowerShell instalado e em execução.
- Este guia de instruções pressupõe que você já está usando uma chave de Azure Key Vault como o protetor de TDE para um banco de dados SQL do Azure ou data warehouse. Consulte [Transparent Data Encryption com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para instalação, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Visão geral

Este guia de instruções descreve como responder a um protetor de TDE potencialmente comprometido para um banco de dados SQL do Azure ou data warehouse que está usando o TDE com chaves gerenciadas pelo cliente no suporte a BYOK (Azure Key Vault-Bring Your Own Key). Para saber mais sobre o suporte do BYOK para TDE, consulte a [página Visão geral](transparent-data-encryption-byok-azure-sql.md).

Os procedimentos a seguir só devem ser feitos em casos extremos ou em ambientes de teste. Examine cuidadosamente o guia de instruções, pois excluir os protetores de TDE usados ativamente de Azure Key Vault pode resultar em **perda de dados**.

Se uma chave já suspeita de comprometimento, de modo que um serviço ou usuário tivesse acesso não autorizado à chave, é melhor excluir a chave.

Tenha em mente que, depois que o protetor de TDE for excluído no Key Vault, **todas as conexões com os bancos de dados criptografados no servidor serão bloqueadas e esses bancos de dados ficarão offline e serão descartados dentro de 24 horas**. Os backups antigos criptografados com a chave comprometida não são mais acessíveis.

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

O comando **Get-AzureRmSqlServerKeyVaultKey** do PowerShell fornece a impressão digital do protetor de TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves serão excluídas em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

O comando **AZ SQL Server Key show** fornece a impressão digital do protetor TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves excluir em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

* * *

Este guia de instruções passa por duas abordagens dependendo do resultado desejado após a resposta a incidentes:

- Para manter os bancos de dados SQL/data warehouses do Azure **acessíveis**
- Para tornar os bancos de dados SQL/data warehouses do Azure **inacessíveis**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos criptografados acessíveis

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, pois o controle de acesso é provisionado em um nível de cofre.

2. Adicione a nova chave ao servidor usando os cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e atualize-o como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Verifique se o servidor e todas as réplicas foram atualizadas para o novo protetor TDE usando o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor de TDE se propague para todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faça um [backup da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) em Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Exclua a chave comprometida de Key Vault usando o cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para Key Vault no futuro usando o cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para referência de comando, consulte o [CLI do Azure keyvault](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, pois o controle de acesso é provisionado em um nível de cofre.

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

## <a name="next-steps"></a>Passos seguintes

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introdução ao suporte de Bring Your Own Key para TDE: [ative o TDE usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
