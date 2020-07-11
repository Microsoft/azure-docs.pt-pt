---
title: Buscar fichas de assinatura de acesso partilhado em código Cofre da Chave Azure
description: A funcionalidade de conta de armazenamento gerida proporciona uma integração perfeita, entre o Azure Key Vault e uma conta de armazenamento Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: e429115ce2624685c413ae252229964feee70137
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232598"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Obter tokens de assinatura de acesso partilhado no código

Pode gerir a sua conta de armazenamento com fichas de acesso partilhado (SAS) armazenadas no cofre da chave. Para obter mais informações, consulte [Grant acesso limitado aos recursos de armazenamento Azure usando SAS](../../storage/common/storage-sas-overview.md).

Este artigo fornece exemplos de código .NET que requer um token SAS e executa operações com ele. Para obter informações sobre como criar e armazenar fichas SAS, consulte as chaves da conta de armazenamento com o Key Vault e as teclas de conta de armazenamento [Azure CLI](overview-storage-keys.md) ou [Manage com Key Vault e Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código recolhe um token SAS do seu cofre chave, usa-o para criar uma nova conta de armazenamento e cria um novo cliente de serviço Blob.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Se o seu token de assinatura de acesso partilhado estiver prestes a expirar, pode obter o token de assinatura de acesso partilhado do seu cofre de chave e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Passos seguintes
- Saiba como [conceder acesso limitado aos recursos de armazenamento Azure utilizando SAS.](../../storage/common/storage-sas-overview.md)
- Saiba como [gerir as chaves da conta de armazenamento com o Key Vault e o Azure CLI](overview-storage-keys.md) ou [a Azure PowerShell](overview-storage-keys-powershell.md).
- Ver [amostras-chave da conta de armazenamento gerida](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
