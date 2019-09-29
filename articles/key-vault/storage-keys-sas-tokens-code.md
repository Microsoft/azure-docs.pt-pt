---
title: Conta de armazenamento gerenciado Azure Key Vault-versão do PowerShell
description: O recurso de conta de armazenamento gerenciado fornece uma integração direta, entre Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 75561c643c2b8881e4b390be885b77f2fff4971d
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673462"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Obter tokens de assinatura de acesso partilhado no código

Você pode gerenciar sua conta de armazenamento com os [tokens de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) em seu cofre de chaves. Este artigo fornece exemplos de C# código que busca um token SAS e executa operações com ele.  Para obter informações sobre como criar e armazenar tokens SAS, consulte [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](key-vault-ovw-storage-keys.md) ou [gerenciar chaves de conta de armazenamento com Key Vault e Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código busca um token SAS do cofre de chaves, usa-o para criar uma nova conta de armazenamento e cria um novo cliente de serviço BLOB.  

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

Se o token de assinatura de acesso compartilhado estiver prestes a expirar, você poderá buscar o token de assinatura de acesso compartilhado do cofre de chaves e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passos seguintes
- Saiba como [gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure](key-vault-ovw-storage-keys.md) ou [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- Consulte [exemplos de chave da conta de armazenamento gerenciado](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Sobre chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- [Referência do Key Vault PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
