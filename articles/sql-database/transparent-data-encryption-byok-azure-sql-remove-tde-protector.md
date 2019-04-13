---
title: SQL Database do Azure PowerShell - remover um protetor de TDE - | Documentos da Microsoft
description: Guia de procedimentos para responder a um protetor de TDE potencialmente comprometido, para uma base de dados do Azure SQL ou armazém de dados utilizando a TDE com suporte de Bring YOur Own Key (BYOK).
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
ms.openlocfilehash: 51cdd43e62bd511da55978bbac3215200c3a8e01
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528276"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de encriptação de dados transparente (TDE) com o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Tem de ter uma subscrição do Azure e de ser um administrador da subscrição
- Tem de ter o Azure PowerShell instalada e em execução. 
- Este guia de procedimentos pressupõe que já estiver a utilizar uma chave de Cofre de chaves do Azure como o protetor de TDE para uma base de dados do Azure SQL ou armazém de dados. Ver [encriptação de dados transparente com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

## <a name="overview"></a>Descrição geral

Este guia de procedimentos descreve como responder a um protetor de TDE potencialmente comprometido, para uma base de dados do Azure SQL ou armazém de dados que está a utilizar o TDE com chaves geridas pelo cliente no Azure Key Vault - suporte de Bring Your Own Key (BYOK). Para saber mais sobre o suporte BYOK para TDE, consulte a [página de descrição geral](transparent-data-encryption-byok-azure-sql.md). 

Os procedimentos seguintes só devem ser feitos em casos extremos, ou em ambientes de teste. Reveja o guia de instruções com atenção, como a eliminar a TDE utilizado ativamente os componentes de proteção do Azure Key Vault podem resultar em **perda de dados**. 

Se uma chave nunca é suspeito for comprometida, de modo a que um serviço ou o utilizador tinha acesso não autorizado à chave, é melhor eliminar a chave.

Lembre-se de que uma vez o protetor de TDE é eliminado no Cofre de chaves **todas as ligações às bases de dados encriptados sob o servidor são bloqueadas, e esses bancos de dados ficam offline e são removidos dentro de 24 horas**. Cópias de segurança antigas encriptadas com a chave comprometida já não estão acessíveis.

Os passos seguintes descrevem como verificar os thumbprints de Protetor de TDE ainda em utilização pelo Virtual registo de ficheiros (VLF) de um determinado banco de dados. O thumbprint do protetor de TDE atual da base de dados, e o ID de base de dados pode ser encontrado ao executar: SELECIONE [database_id],       [encryption_state], [encryptor_type], /*chave assimétrica significa AKV, certificado significa chaves geridas pelo serviço*/ [encryptor_thumbprint,] de [sys]. [ dm_database_encryption_keys] 
 
A seguinte consulta devolve os VLFs e o encriptador thumbprints respectivos em utilização. Cada outra impressão digital refere-se a chave diferente no Cofre de chaves do Azure (AKV): SELECT * FROM sys.dm_db_log_info (database_id) 

O comando do PowerShell Get-AzureRmSqlServerKeyVaultKey fornece o thumbprint de Protetor TDE utilizado na consulta, pode ver as chaves para manter e as chaves para eliminar na AKV. Chaves só já não é utilizadas pela base de dados podem ser eliminadas em segurança do Azure Key Vault.

Este guia de procedimentos é feito por duas abordagens consoante o resultado pretendido após a resposta a incidentes:

- Para manter as bases de dados SQL do Azure / dados os depósitos **acessível**
- Para tornar as bases de dados SQL do Azure / dados os depósitos **inacessível**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos encriptados acessível

1. Criar uma [nova chave no Cofre de chaves](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que esta chave nova é criada num cofre de chaves separado do protetor de TDE potencialmente comprometido, uma vez que o controlo de acesso é aprovisionado num nível de cofre.
2. Adicionar a nova chave para o servidor utilizando o [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [conjunto AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets e atualizá-lo como protetor de TDE novo do servidor.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Certifique-se o servidor e as réplicas tem atualizado para o novo protetor de TDE com o [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet. 

   >[!NOTE]
   > Pode demorar alguns minutos para que o novo protetor de TDE a ser propagada para todas as bases de dados e bases de dados secundárias sob o servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Efetuar uma [cópia de segurança da chave nova](/powershell/module/az.keyvault/backup-azkeyvaultkey) no Cofre de chaves.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Eliminar a chave de comprometido do Cofre de chave com o [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Para restaurar uma chave para o Cofre de chaves no futuro com o [restauro AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para disponibilizar recursos encriptados inacessível

1. Remova bases de dados que estão a ser encriptadas pela chave potencialmente comprometida.

   Os base de dados e arquivos de log são automaticamente uma cópia de segurança, para que um ponto anterior no tempo restauro da base de dados pode ser feito em qualquer momento (desde que forneça a chave). As bases de dados tem de ser removidos antes da eliminação de um protetor de TDE Active Directory para impedir a potencial perda de dados de até 10 minutos das transações mais recentes. 
2. Criar cópias de segurança o material de chave do protetor de TDE no Cofre de chaves.
3. Remover a chave potencialmente comprometida do Key Vault

## <a name="next-steps"></a>Passos Seguintes

- Saiba como rodar o protetor de TDE de um servidor para atender aos requisitos de segurança: [Rodar para a encriptação de dados transparente protetor através do PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Comece com traga a sua própria chave de suporte para TDE: [Ativar o TDE com sua própria chave do Key Vault com o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
