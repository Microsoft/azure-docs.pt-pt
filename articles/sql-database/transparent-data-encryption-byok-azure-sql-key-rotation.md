---
title: Protetor TDE rotativo - PowerShell
description: Aprenda a rodar o protetor transparente de encriptação de dados (TDE) para um servidor Azure SQL.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067174"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Rode o protetor transparente de encriptação de dados (TDE) utilizando powerShell

Este artigo descreve a rotação da chave para um servidor Azure SQL utilizando um protetor TDE do Cofre chave Azure. Rodar o protetor TDE do servidor Azure SQL significa mudar para uma nova chave assimétrica que protege as bases de dados do servidor. A rotação da chave é uma operação online e deve demorar apenas alguns segundos a ser concluída, porque isto apenas desencripta e encripta a chave de encriptação de dados da base de dados, e não toda a base de dados.

Este guia discute duas opções para rodar o protetor TDE no servidor.

> [!NOTE]
> Um armazém de dados SQL pausado deve ser retomado antes das rotações das teclas.

> [!IMPORTANT]
> Não elimine versões anteriores da chave após um capotamento. Quando as chaves são repostas, alguns dados ainda são encriptados com as teclas anteriores, tais como cópias de dados mais antigas.

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de como orientar assume que já está a usar uma chave do Cofre de Chaves Azure como protetor TDE para uma Base de Dados SQL Azure ou Armazém de Dados. Consulte [encriptação transparente de dados com suporte BYOK](transparent-data-encryption-byok-azure-sql.md).
- Tem de ter o Azure PowerShell instalado e em funcionamento.
- [Recomendado, mas opcional] Crie primeiro o material-chave para o protetor TDE num módulo de segurança de hardware (HSM) ou numa loja de chaves local e importe primeiro o material-chave para o Cofre chave Azure. Siga as instruções para utilizar um módulo de segurança de [hardware (HSM) e key vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, veja [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager (RM) ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para mais informações sobre a sua compatibilidade, consulte [A introdução do novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para a instalação, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotação manual da chave

A rotação manual da tecla utiliza os seguintes comandos para adicionar uma chave completamente nova, que pode estar sob um novo nome chave ou até mesmo outro cofre chave. A utilização desta abordagem suporta a adição da mesma chave a diferentes cofres-chave para suportar cenários de alta disponibilidade e geo-dr.

> [!NOTE]
> O comprimento combinado para o nome do cofre chave e o nome chave não podem exceder 94 caracteres.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilize o [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [Set-AzSqlServerTransparentDataCryptOnProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilize a [chave az keyvault create](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server criação](/cli/azure/sql/server/key#az-sql-server-key-create), e comandos conjuntos [de teclas tde-chave do servidor az sql.](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets powerShell úteis

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Para mudar o protetor TDE do modo BYOK gerido pela Microsoft, utilize o [set-AzSqlServerTransparentTransparentDataCryptOnProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para mudar o protetor TDE do modo BYOK para gerido pela Microsoft, utilize o [Set-AzSqlServerTransparentTransparentDataCryptOnProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Os exemplos seguintes utilizam o conjunto de [teclas tde-key do servidor az sql](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Para mudar o protetor TDE do modo BYOK gerido pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Para mudar o protetor TDE do modo BYOK para gerido pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Passos seguintes

- Em caso de risco de segurança, aprenda a remover um protetor TDE potencialmente comprometido: [Remova uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Inicie com a integração do Cofre de Chaves Azure e traga o seu próprio suporte de chave para TDE: Ligue o [TDE usando a sua própria chave do Key Vault usando powerShell](transparent-data-encryption-byok-azure-sql-configure.md)
