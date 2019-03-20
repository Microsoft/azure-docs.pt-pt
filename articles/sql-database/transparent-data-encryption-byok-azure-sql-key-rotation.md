---
title: SQL Database do Azure PowerShell - protetor de TDE rodar - | Documentos da Microsoft
description: Saiba como rodar o protetor de encriptação de dados transparente (TDE) para um servidor SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 03/12/2019
ms.openlocfilehash: 760b292e75b4cc64b85eaf51ffad0521b721dabf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895656"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rodar o protetor de encriptação de dados transparente (TDE) com o PowerShell

Este artigo descreve a rotação de chaves para um servidor SQL do Azure com um protetor de TDE no Azure Key Vault. Girando meios de protetor de TDE de um Azure SQL server mudar para uma nova chave assimétrica que protege as bases de dados no servidor. Rotação de chaves é uma operação online e deverá demorar apenas alguns segundos para concluir, uma vez que isso só descriptografa e encripta novamente a chave de encriptação de dados da base de dados, não a base de dados.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Tem de ser retomado um SQL Data Warehouse em pausa antes de rotações de chave.
>

> [!IMPORTANT]
> **Fazer não eliminar** versões anteriores da chave depois de um rollover.  Quando as chaves estão a ser transferidas, alguns dados ainda são encriptados com as chaves anteriores, como cópias de segurança de base de dados mais antigas. 
>

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- Este guia de procedimentos pressupõe que já estiver a utilizar uma chave de Cofre de chaves do Azure como o protetor de TDE para uma base de dados do Azure SQL ou armazém de dados. Ver [encriptação de dados transparente com suporte BYOK](transparent-data-encryption-byok-azure-sql.md).
- Tem de ter o Azure PowerShell instalada e em execução.
- [Recomendável, mas opcional] Criar o material de chave para o protetor de TDE num módulo de segurança de hardware (HSM) ou armazenar a primeira chave local e importar o material de chave para o Azure Key Vault. Siga os [instruções para utilizar um módulo de segurança de hardware (HSM) e o Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

## <a name="manual-key-rotation"></a>Rotação de chaves manual

Manual rotação de chaves utiliza a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey adicionar](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), e [AzSqlServerTransparentDataEncryptionProtector conjunto](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets para adicionar um chave completamente nova, que poderia ser num novo nome de chave ou até mesmo outro Cofre de chaves. Usando essa abordagem suporta a adição a mesma chave aos cofres de chaves diferentes para suportar cenários de elevada disponibilidade e recuperação após desastre geográfico.

>[!NOTE]
>O comprimento combinado para o nome de Cofre de chaves e o nome da chave não pode exceder 94 carateres.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

## <a name="option-2-manual-rotation"></a>Opção 2: Rotação manual

Utiliza a opção o [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), e [AzSqlServerTransparentDataEncryptionProtector conjunto](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets para adicionar um completamente nova chave, que poderia ser num novo nome de chave ou até mesmo outro Cofre de chaves. 

>[!NOTE]
>O comprimento combinado para o nome de Cofre de chaves e o nome da chave não pode exceder 94 carateres.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell útil

- Para mudar o protetor de TDE de gerida pela Microsoft para o modo BYOK, utilize o [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para mudar o protetor de TDE do modo BYOK para gerida pela Microsoft, utilize o [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Passos Seguintes

- Em caso de um risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Introdução ao suporte de traga a sua própria chave para TDE e de integração Azure Key Vault: [Ativar o TDE com sua própria chave do Key Vault com o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
