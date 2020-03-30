---
title: Conta de armazenamento gerida pela Azure Key Vault - versão PowerShell
description: A funcionalidade de conta de armazenamento gerida proporciona uma integração perfeita, entre o Azure Key Vault e uma conta de armazenamento Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: cbd7bd034c5cbbdf5308ec660a96dc52a9a2b310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78200707"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Obter tokens de assinatura de acesso partilhado no código

Você pode gerir a sua conta de armazenamento com as [fichas](../storage/common/storage-dotnet-shared-access-signature-part-1.md) de assinatura de acesso partilhada no seu cofre chave. Este artigo fornece exemplos de código C# que requer uma ficha SAS e realiza operações com ele.  Para obter informações sobre como criar e armazenar tokens SAS, consulte Gerir as chaves da conta de [armazenamento com o Key Vault e o Azure CLI](key-vault-ovw-storage-keys.md) ou gerir as chaves da conta de armazenamento com key vault e [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código requer um token SAS do seu cofre chave, usa-o para criar uma nova conta de armazenamento, e cria um novo cliente de serviço Blob.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se o seu token de assinatura de acesso partilhado estiver prestes a expirar, pode obter o símbolo da assinatura de acesso partilhado do seu cofre de chaves e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passos seguintes
- Saiba como gerir as chaves da conta de [armazenamento com o Key Vault e o Azure CLI](key-vault-ovw-storage-keys.md) ou [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- Ver [amostras-chave](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) da conta de armazenamento gerida
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência de Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
