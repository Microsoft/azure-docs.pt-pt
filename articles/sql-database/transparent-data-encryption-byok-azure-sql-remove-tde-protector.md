---
title: PowerShell-remover um protetor de TDE-banco de dados SQL do Azure | Microsoft Docs
description: Guia de instruções para responder a um protetor de TDE potencialmente comprometido para um banco de dados SQL do Azure ou data warehouse usando TDE com suporte a BYOK (traga sua própria chave).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: dc117dd844a3a47cafa1b37170c95fe852bb82ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566062"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de Transparent Data Encryption (TDE) usando o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

- Você deve ter uma assinatura do Azure e ser um administrador nessa assinatura
- Você deve ter Azure PowerShell instalado e em execução. 
- Este guia de instruções pressupõe que você já está usando uma chave de Azure Key Vault como o protetor de TDE para um banco de dados SQL do Azure ou data warehouse. Consulte [Transparent Data Encryption com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

## <a name="overview"></a>Descrição geral

Este guia de instruções descreve como responder a um protetor de TDE potencialmente comprometido para um banco de dados SQL do Azure ou data warehouse que está usando o TDE com chaves gerenciadas pelo cliente no suporte a BYOK (Azure Key Vault-Bring Your Own Key). Para saber mais sobre o suporte do BYOK para TDE, consulte a [página Visão geral](transparent-data-encryption-byok-azure-sql.md). 

Os procedimentos a seguir só devem ser feitos em casos extremos ou em ambientes de teste. Examine cuidadosamente o guia de instruções, pois excluir os protetores de TDE usados ativamente de Azure Key Vault pode resultar em **perda de dados**. 

Se uma chave já suspeita de comprometimento, de modo que um serviço ou usuário tivesse acesso não autorizado à chave, é melhor excluir a chave.

Tenha em mente que, depois que o protetor de TDE for excluído no Key Vault, **todas as conexões com os bancos de dados criptografados no servidor serão bloqueadas e esses bancos de dados ficarão offline e serão descartados dentro de 24 horas**. Os backups antigos criptografados com a chave comprometida não são mais acessíveis.

As etapas a seguir descrevem como verificar as impressões digitais do protetor de TDE que ainda estão em uso por VLF (arquivos de log virtuais) de um determinado banco de dados. A impressão digital do protetor de TDE atual do banco de dados e a ID do banco de dados podem ser encontradas executando: SELECT [database_id],       [encryption_state], [encryptor_type],/*Key assimétrica significa akv, certificado significa chaves gerenciadas pelo serviço*/[encryptor_thumbprint], de [sys]. [ dm_database_encryption_keys] 
 
A consulta a seguir retorna o VLFs e o criptografador respectivas impressões digitais em uso. Cada impressão digital diferente refere-se a uma chave diferente em Azure Key Vault (AKV): Selecione * de sys. dm _db_log_info (database_id) 

O comando Get-AzureRmSqlServerKeyVaultKey do PowerShell fornece a impressão digital do protetor de TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves serão excluídas em AKV. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

Este guia de instruções passa por duas abordagens dependendo do resultado desejado após a resposta a incidentes:

- Para manter os bancos de dados SQL/data warehouses do Azure **acessíveis**
- Para tornar os bancos de dados SQL/data warehouses do Azure inacessíveis

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos criptografados acessíveis

1. Crie uma [nova chave no Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, pois o controle de acesso é provisionado em um nível de cofre.
2. Adicione a nova chave ao servidor usando os cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e atualize-o como o novo protetor TDE do servidor.

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

3. Verifique se o servidor e todas as réplicas foram atualizadas para o novo protetor TDE usando o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) . 

   >[!NOTE]
   > Pode levar alguns minutos para que o novo protetor de TDE se propague para todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faça um [backup da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) em Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Exclua a chave comprometida de Key Vault usando o cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) . 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Para restaurar uma chave para Key Vault no futuro usando o cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para tornar os recursos criptografados inacessíveis

1. Remova os bancos de dados que estão sendo criptografados pela chave potencialmente comprometida.

   O backup do banco de dados e dos arquivos de log é feito automaticamente, portanto, uma restauração pontual do banco de dados pode ser feita em qualquer ponto (desde que você forneça a chave). Os bancos de dados devem ser descartados antes da exclusão de um protetor de TDE ativo para evitar a possível perda de dados de até 10 minutos das transações mais recentes. 
2. Faça backup do material da chave do protetor de TDE no Key Vault.
3. Remover a chave potencialmente comprometida de Key Vault

## <a name="next-steps"></a>Passos Seguintes

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introdução ao suporte do Bring Your Own Key para TDE: [Ativar o TDE usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
