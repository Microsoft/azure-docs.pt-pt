---
title: Remover protetor TDE - PowerShell
description: Como orientar para responder a um protetor TDE potencialmente comprometido para uma Base de Dados SQL Azure ou Data Warehouse usando o suporte TDE com bring YOur Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 64f3f3c1a4fa656edc7163f6c8ee0ad60f117be6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116575"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remova um protetor transparente de encriptação de dados (TDE) utilizando powerShell

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma assinatura Azure e ser um administrador nessa subscrição
- Tem de ter o Azure PowerShell instalado e em funcionamento.
- Este guia de como orientar assume que já está a usar uma chave do Cofre de Chaves Azure como protetor TDE para uma Base de Dados SQL Azure ou Armazém de Dados. Consulte a [Encriptação transparente de dados com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para a instalação, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Descrição geral

Este guia descreve como responder a um protetor TDE potencialmente comprometido para uma Base de Dados SQL Azure ou Armazém de Dados que está a usar O TDE com chaves geridas pelo cliente no cofre de chaves Azure - Traga a sua própria chave (BYOK). Para saber mais sobre o suporte byok para TDE, consulte a [página de visão geral](transparent-data-encryption-byok-azure-sql.md).

Os seguintes procedimentos só devem ser feitos em casos extremos ou em ambientes de ensaio. Reveja cuidadosamente o guia como orientar, uma vez que a apagamento de protetores TDE ativamente utilizados do Cofre chave Azure resultará na **indisponibilidade**da base de dados .

Se alguma vez se suspeita que uma chave esteja comprometida, de modo a que um serviço ou utilizador tenha acesso não autorizado à chave, o melhor é apagar a chave.

Tenha em mente que uma vez eliminado o protetor TDE no Cofre-Chave, em até 10 minutos todas as bases de dados encriptadas começarão a negar todas as ligações com a mensagem de erro correspondente e mudar o seu estado para [Inacessível](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

Os seguintes passos descrevem como verificar as impressões digitais do Protetor TDE ainda em uso por Ficheiros de Registo Virtual (VLF) de uma determinada base de dados.
A impressão digital do atual protetor TDE da base de dados, e o ID da base de dados podem ser encontrados executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

A seguinte consulta devolve os VLFs e as respetivas impressões digitais do encriptador em uso. Cada impressão digital diferente refere-se a diferentes teclas no Cofre de Chaves Azure (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O comando PowerShell **Get-AzureRmSqlServerKeyKeyfornece** a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a manter e quais as teclas a eliminar no AKV. Apenas as chaves que já não são utilizadas pela base de dados podem ser eliminadas com segurança do Cofre de Chaves Azure.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

O programa de teclas do servidor powerShell **az sql**fornece a impressão digital do Protetor TDE utilizado na consulta, para que possa ver quais as teclas a manter e quais as teclas a eliminar no AKV. Apenas as chaves que já não são utilizadas pela base de dados podem ser eliminadas com segurança do Cofre de Chaves Azure.

* * *

Este guia como orientar passa por duas abordagens dependendo do resultado desejado após a resposta do incidente:

- Para manter as bases de dados Azure SQL / Data Warehouses **acessíveis**
- Para tornar **inacessíveis** as bases de dados Azure SQL / Data Warehouses

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos encriptados acessíveis

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Cofre chave.](/powershell/module/az.keyvault/add-azkeyvaultkey) Certifique-se de que esta nova chave é criada num cofre separado do protetor TDE potencialmente comprometido, uma vez que o controlo de acesso é provisionado a um nível de cofre.

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
   > Pode levar alguns minutos para que o novo protetor TDE se propague a todas as bases de dados e bases de dados secundárias no servidor.

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para referência ao comando, consulte o cofre de [chaves Azure CLI](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Cofre chave.](/cli/azure/keyvault/key#az-keyvault-key-create) Certifique-se de que esta nova chave é criada num cofre separado do protetor TDE potencialmente comprometido, uma vez que o controlo de acesso é provisionado a um nível de cofre.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor TDE do servidor.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas foram atualizadas para o novo protetor TDE.

   > [!NOTE]
   > Pode levar alguns minutos para que o novo protetor TDE se propague a todas as bases de dados e bases de dados secundárias no servidor.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Pegue uma cópia da nova chave no Cofre chave.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Apague a chave comprometida do Cofre chave.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar a chave do Cofre chave no futuro.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para tornar os recursos encriptados inacessíveis

1. Largue as bases de dados que estão a ser encriptadas pela chave potencialmente comprometida.

   A base de dados e os ficheiros de registo são automaticamente apoiados, pelo que um restabelecimento pontual da base de dados pode ser feito em qualquer ponto (desde que forneça a chave). As bases de dados devem ser retiradas antes da eliminação de um protetor TDE ativo para evitar uma potencial perda de dados até 10 minutos das transações mais recentes.

2. Volte a apoiar o material chave do protetor TDE no Cofre chave.
3. Remova a chave potencialmente comprometida do Cofre chave

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Passos seguintes

- Aprenda a rodar o protetor TDE de um servidor para cumprir os requisitos de segurança: Rode o protetor de encriptação transparente de [dados utilizando powerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Inicie com bring your own key support for TDE: [Ligue tDE usando a sua própria chave do Key Vault usando powerShell](transparent-data-encryption-byok-azure-sql-configure.md)
