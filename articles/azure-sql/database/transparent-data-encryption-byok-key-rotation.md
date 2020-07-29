---
title: Girar protetor TDE (PowerShell & o Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Saiba como rodar o protetor transparente de encriptação de dados (TDE) para um servidor em Azure usado pela Azure SQL Database e Azure Synapse Analytics utilizando o PowerShell e o Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: b2729975db154fbaf4569afc5aa9b5eaab358146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321360"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Rode o protetor transparente de encriptação de dados (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Este artigo descreve a rotação da chave para um [servidor](logical-servers.md) usando um protetor TDE do Azure Key Vault. Rodar o Protetor TDE lógico para um servidor significa mudar para uma nova chave assimétrica que protege as bases de dados do servidor. A rotação da chave é uma operação online e deve levar apenas alguns segundos para ser concluída, porque isto apenas desencripta e reencripta a chave de encriptação de dados da base de dados, e não toda a base de dados.

Este guia discute duas opções para rodar o protetor TDE no servidor.

> [!NOTE]
> Uma piscina SQL Azure Synapse Analytics pausada deve ser retomada antes das rotações das chaves.

> [!IMPORTANT]
> Não elimine as versões anteriores da chave após um capotamento. Quando as chaves são enroladas, alguns dados ainda são encriptados com as teclas anteriores, como cópias de segurança de bases de dados mais antigas.

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de como guiar pressupõe que já está a usar uma chave do Azure Key Vault como protetor TDE para Azure SQL Database ou Azure Synapse Analytics. Consulte [encriptação de dados transparente com suporte BYOK](transparent-data-encryption-byok-overview.md).
- Deve ter a Azure PowerShell instalada e a funcionar.
- [Recomendado, mas opcional] Crie o material chave para o protetor TDE num módulo de segurança de hardware (HSM) ou numa loja de chaves local primeiro, e importe o material chave para o Cofre da Chave Azure. Siga as [instruções para utilizar um módulo de segurança de hardware (HSM) e Um Cofre chave](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda está suportado, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de erros até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre a sua compatibilidade, consulte [a introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

Para a instalação, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotação manual da chave

A rotação manual da chave utiliza os seguintes comandos para adicionar uma chave completamente nova, que pode estar sob um novo nome-chave ou até mesmo outro cofre chave. A utilização desta abordagem suporta a adição da mesma chave a diferentes cofres-chave para suportar cenários de alta disponibilidade e geo-dr.

> [!NOTE]
> O comprimento combinado para o nome do cofre e nome chave não pode exceder 94 caracteres.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey,](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

Utilize a [tecla az keyvault create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server key create](/cli/azure/sql/server/key#az-sql-server-key-create), e [az sql server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) commands.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Modo protetor Switch TDE

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Para mudar o protetor TDE do modo BYOK gerido pela Microsoft, utilize o [cmdlet Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para mudar o protetor TDE do modo BYOK para o gerido pela Microsoft, utilize o [cmdlet Do Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[O Azure CLI](#tab/azure-cli)

Os exemplos a seguir usam [o conjunto de teclas de tde do servidor az sql](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Para mudar o protetor TDE do modo BYOK gerido pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Para mudar o protetor TDE do modo BYOK para o gerido pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Próximos passos

- Em caso de risco de segurança, aprenda a remover um protetor TDE potencialmente comprometido: [Remova uma chave potencialmente comprometida](transparent-data-encryption-byok-remove-tde-protector.md).

- Começa com a integração do Azure Key Vault e traz o suporte da sua própria chave para o TDE: [Ligue o TDE utilizando a sua própria chave a partir do Key Vault utilizando o PowerShell](transparent-data-encryption-byok-configure.md).
