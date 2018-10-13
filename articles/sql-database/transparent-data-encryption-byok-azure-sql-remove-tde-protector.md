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
ms.date: 08/07/2017
ms.openlocfilehash: 6675a68222e09be9a092ad21ee318a53a0a39ca5
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311308"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de encriptação de dados transparente (TDE) com o PowerShell
## <a name="prerequisites"></a>Pré-requisitos
- Tem de ter uma subscrição do Azure e de ser um administrador da subscrição
- Tem de ter o Azure PowerShell versão 4.2.0 ou mais recente instalado e em execução. 
- Este guia de procedimentos pressupõe que já estiver a utilizar uma chave de Cofre de chaves do Azure como o protetor de TDE para uma base de dados do Azure SQL ou armazém de dados. Ver [encriptação de dados transparente com suporte BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

## <a name="overview"></a>Descrição geral
Este guia de procedimentos descreve como responder a um protetor de TDE potencialmente comprometido, para uma base de dados do Azure SQL ou armazém de dados que está a utilizar o TDE com suporte de Bring Your Own Key (BYOK). Para saber mais sobre o suporte BYOK para TDE, consulte a [página de descrição geral](transparent-data-encryption-byok-azure-sql.md). 

Os procedimentos seguintes só devem ser feitos em casos extremos, ou em ambientes de teste. Reveja o guia de instruções com atenção, como a eliminar a TDE utilizado ativamente os componentes de proteção do Azure Key Vault podem resultar em **perda de dados**. 

Se uma chave nunca é suspeito for comprometida, de modo a que um serviço ou o utilizador tinha acesso não autorizado à chave, é melhor eliminar a chave.

Lembre-se de que uma vez o protetor de TDE é eliminado no Cofre de chaves **todas as ligações às bases de dados encriptados sob o servidor são bloqueadas, e esses bancos de dados ficam offline e são removidos dentro de 24 horas**. Cópias de segurança antigas encriptadas com a chave comprometida já não estão acessíveis.

Este guia de procedimentos é feito por duas abordagens consoante o resultado pretendido após a resposta a incidentes:
- Para manter as bases de dados SQL do Azure / dados os depósitos **acessível**
- Para tornar as bases de dados SQL do Azure / dados os depósitos **inacessível**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos encriptados acessível
1. Criar uma [nova chave no Cofre de chaves](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Certifique-se de que esta chave nova é criada num cofre de chaves separado do protetor de TDE potencialmente comprometido, uma vez que o controlo de acesso é aprovisionado num nível de cofre. 
2. Adicionar a nova chave para o servidor utilizando o [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) e [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) cmdlets e atualizá-lo como protetor de TDE novo do servidor.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Certifique-se o servidor e as réplicas tem atualizado para o novo protetor de TDE com o [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) cmdlet. 

   >[!NOTE]
   > Pode demorar alguns minutos para que o novo protetor de TDE a ser propagada para todas as bases de dados e bases de dados secundárias sob o servidor.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Efetuar uma [cópia de segurança da chave nova](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) no Cofre de chaves.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Eliminar a chave de comprometido do Cofre de chave com o [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) cmdlet. 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Para restaurar uma chave para o Cofre de chaves no futuro com o [restauro-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) cmdlet:
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para disponibilizar recursos encriptados inacessível
1. Remova bases de dados que estão a ser encriptadas pela chave potencialmente comprometida.
Os base de dados e arquivos de log são automaticamente uma cópia de segurança, para que um ponto anterior no tempo restauro da base de dados pode ser feito em qualquer momento (desde que forneça a chave). As bases de dados tem de ser removidos antes da eliminação de um protetor de TDE Active Directory para impedir a potencial perda de dados de até 10 minutos das transações mais recentes. 
2. Criar cópias de segurança o material de chave do protetor de TDE no Cofre de chaves.
3. Remover a chave potencialmente comprometida do Key Vault

## <a name="next-steps"></a>Passos Seguintes

- Saiba como rodar o protetor de TDE de um servidor para atender aos requisitos de segurança: [rodar a encriptação de dados transparente protetor através do PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- Introdução ao traga a sua própria chave de suporte para TDE: [ativar TDE com sua própria chave do Key Vault com o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
