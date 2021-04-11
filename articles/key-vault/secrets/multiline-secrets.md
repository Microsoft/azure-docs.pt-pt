---
title: Armazenar um segredo multiline em Azure Key Vault
description: Tutorial mostrando como definir segredos multiline do Azure Key Vault usando Azure CLI e PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610287"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Armazenar um segredo de várias linhas no Cofre da Chave Azure

O [quickstart Azure CLI](quick-create-cli.md) e [o quickstart Azure PowerShell](quick-create-powershell.md) demonstram como armazenar um segredo de uma única linha.   Também pode utilizar o Key Vault para armazenar um segredo de várias linhas, como um ficheiro JSON ou uma chave privada RSA.

Os segredos multi-linha não podem ser transmitidos ao comando secreto Azure CLI [az keyvault](/cli/azure/keyvault/secret#az_keyvault_secret_set) ou ao cmdlet Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) através da linha de comando. Em vez disso, deve primeiro armazenar o segredo multi-linha como um ficheiro de texto. 

Por exemplo, pode criar um ficheiro de texto chamado "secretfile.txt" contendo as seguintes linhas:

```bash
This is my
multi-line
secret
```

Em seguida, pode passar este ficheiro para o comando secreto Azure CLI [az keyvault](/cli/azure/keyvault/secret#az_keyvault_secret_set) usando o `--file` parâmetro.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Com o Azure PowerShell, deve ler primeiro no ficheiro utilizando o cmdlet [Get-Content](/powershell/module/microsoft.powershell.management/get-content) e, em seguida, convertê-lo numa cadeia segura utilizando [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Por último, armazena o segredo utilizando o [cmdlet Set-AzKeyVaultSecret.](/powershell/module/az.keyvault/set-azkeyvaultsecret)

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

Em qualquer dos casos, pode visualizar o segredo armazenado utilizando o comando secreto de [az-keyvault](/cli/azure/keyvault/secret#az_keyvault_secret_show) Azure CLI ou o cmdlet Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret)

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

O segredo será devolvido com novidades embutidas:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Passos seguintes

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Veja o arranque rápido do [Azure CLI](quick-create-cli.md)
- Veja os [comandos Azure CLI az keyvault](/cli/azure/keyvault)
- Veja o arranque rápido do [Azure PowerShell](quick-create-powershell.md)
- Consulte os [cmdlets Azure PowerShell Az.KeyVault](/powershell/module/az.keyvault#key-vault)
