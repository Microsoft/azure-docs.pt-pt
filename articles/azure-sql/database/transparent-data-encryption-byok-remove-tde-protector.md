---
title: Remover o protetor TDE (PowerShell & o Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Aprenda a responder a um protetor TDE potencialmente comprometido para a Base de Dados Azure SQL ou Azure Synapse Analytics usando o suporte TDE com suporte Bring YOur Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 77f2312438f3f9db7aa4e0dc7cc0f672644a87c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617422"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remova um protetor de encriptação de dados transparente (TDE) usando o PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Este tópico descreve como responder a uma proteção TDE potencialmente comprometida para a Base de Dados Azure SQL ou Azure Synapse Analytics que está a usar o TDE com chaves geridas pelo cliente no suporte Azure Key Vault - Traga a sua própria chave (BYOK). Para saber mais sobre o suporte da BYOK para o TDE, consulte a [página geral.](transparent-data-encryption-byok-overview.md)

> [!CAUTION]
> Os procedimentos descritos neste artigo só devem ser efetuados em casos extremos ou em ambientes de ensaio. Reveja cuidadosamente os passos, uma vez que eliminar protetores TDE ativamente utilizados do Azure Key Vault resultará na **indisponibilidade da base de dados**.

Se alguma vez se suspeitar que uma chave está comprometida, de modo a que um serviço ou utilizador tenha acesso não autorizado à chave, o melhor é apagar a chave.

Tenha em mente que uma vez que o protetor TDE é eliminado no Cofre de Chaves, em até 10 minutos, todas as bases de dados encriptadas começarão a negar todas as ligações com a mensagem de erro correspondente e alterar o seu estado para [Inacessível](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

Este guia de como orientar passa por duas abordagens dependendo do resultado desejado após uma resposta comprometida do incidente:

- Para tornar **inacessíveis**as bases de dados em Azure SQL Database / Azure Synapse Analytics .
- Para tornar **inacessíveis**as bases de dados em Azure SQL Database / Azure Azure Synapse Analytics (anteriormente SQL Data Warehouse).

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma assinatura Azure e ser um administrador nessa subscrição
- Deve ter a Azure PowerShell instalada e a funcionar.
- Este guia assume que já está a utilizar uma chave do Azure Key Vault como protetor TDE para uma Base de Dados Azure SQL ou Azure Synapse (anteriormente SQL Data Warehouse). Consulte [encriptação de dados transparentes com suporte BYOK](transparent-data-encryption-byok-overview.md) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para a instalação, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>Verifique as impressões digitais do Protetor TDE

Os passos a seguir descrevem como verificar as impressões digitais do Protetor TDE ainda em uso por Ficheiros de Registo Virtual (VLF) de uma determinada base de dados.
A impressão digital do protetor TDE atual da base de dados, e o ID da base de dados podem ser encontrados executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

A seguinte consulta devolve os VLFs e o Protetor TDE respetivas impressões digitais em uso. Cada impressão digital diferente refere-se a chave diferente no Cofre da Chave Azure (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Em alternativa, pode utilizar o PowerShell ou o Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O comando PowerShell **Get-AzureRmSqlServerKeyVaultKey**   fornece a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a guardar e quais as teclas a eliminar em AKV. Apenas as chaves que já não são utilizadas pela base de dados podem ser eliminadas com segurança do Cofre da Chave Azure.

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

O programa de teclado do servidor de comando PowerShell **az sql**   fornece a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a guardar e quais as teclas a eliminar em AKV. Apenas as chaves que já não são utilizadas pela base de dados podem ser eliminadas com segurança do Cofre da Chave Azure.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Mantenha os recursos encriptados acessíveis

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Key Vault.](/powershell/module/az.keyvault/add-azkeyvaultkey) Certifique-se de que esta nova chave é criada num cofre separado do protetor TDE potencialmente comprometido, uma vez que o controlo de acesso é aprovisionado a um nível de abóbada.

2. Adicione a nova chave ao servidor utilizando o [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets e atualizá-lo como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas foram atualizados para o novo protetor TDE utilizando o [cmdlet Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor TDE se propague a todas as bases de dados e bases de dados secundárias sob o servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Pegue uma [cópia da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) em Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Elimine a chave comprometida do Key Vault utilizando o cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para o Cofre de Chaves no futuro usando o [cmdlet Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para obter referência de comando, consulte o [keyvault Azure CLI](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Key Vault.](/cli/azure/keyvault/key#az-keyvault-key-create) Certifique-se de que esta nova chave é criada num cofre separado do protetor TDE potencialmente comprometido, uma vez que o controlo de acesso é aprovisionado a um nível de abóbada.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor TDE do servidor.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas foram atualizadas para o novo protetor TDE.

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor TDE se propague a todas as bases de dados e bases de dados secundárias sob o servidor.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Pegue uma cópia da nova chave em Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Apague a chave comprometida do Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar uma chave para o Cofre chave no futuro.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Tornar os recursos encriptados inacessíveis

1. Largue as bases de dados que estão a ser encriptadas pela chave potencialmente comprometida.

   A base de dados e os ficheiros de registo são automaticamente apoiados, pelo que uma restauração pontual da base de dados pode ser feita em qualquer ponto (desde que forneça a chave). As bases de dados devem ser retiradas antes da eliminação de um protetor TDE ativo para evitar a perda de dados potenciais até 10 minutos das transações mais recentes.

2. Faça o reforço do material chave do protetor TDE no Cofre de Chaves.
3. Remova a chave potencialmente comprometida do Cofre de Chaves

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba como rodar o protetor TDE de um servidor para cumprir os requisitos de segurança: [Rode o protetor de encriptação de dados transparente utilizando o PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Começa com o suporte Bring Your Own Key para TDE: [Ligue o TDE utilizando a sua própria chave a partir do Key Vault usando o PowerShell](transparent-data-encryption-byok-configure.md)
