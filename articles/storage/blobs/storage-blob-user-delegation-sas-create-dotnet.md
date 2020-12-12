---
title: Utilize .NET para criar uma delegação de utilizador SAS para um recipiente ou bolha
titleSuffix: Azure Storage
description: Saiba como criar uma delegação de utilizador SAS com credenciais de Diretório Azure Ative utilizando a biblioteca de clientes .NET para armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 8667fc3c5224b1f75a9beeb95b6e1261c768c14d
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347919"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net"></a>Crie uma delegação de utilizador SAS para um recipiente ou bolha com .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como utilizar credenciais do Azure Ative Directory (Azure AD) para criar uma delegação de utilizador SAS para um recipiente ou blob com a biblioteca de clientes Azure Storage para a versão 12 da NET.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Atribuir funções da Azure para acesso aos dados

Quando um diretor de segurança da AZure AD tenta aceder aos dados blob, esse diretor de segurança deve ter permissões para o recurso. Se o principal de segurança é uma identidade gerida em Azure ou uma conta de utilizador Azure AD que executa código no ambiente de desenvolvimento, o principal de segurança deve ser atribuído a uma função Azure que concede acesso a dados blob no Azure Storage. Para obter informações sobre a atribuição de permissões através do Azure RBAC, consulte a secção intitulada **Atribuir funções Azure para direitos de acesso** em Acesso autorizado a [bolhas e filas Azure utilizando o Azure Ative Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Para saber mais sobre como autenticar com a biblioteca de clientes Azure Identity a partir do Azure Storage, consulte a secção intitulada **Autenticação com a biblioteca de Identidade Azure** em [Autorização de acesso a blobs e filas com O Diretório Ativo Azure e identidades geridas para Recursos Azure.](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)

## <a name="get-an-authenticated-token-credential"></a>Obtenha uma credencial simbólica autenticada

Para obter uma credencial simbólica que o seu código pode usar para autorizar pedidos para o Azure Storage, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential)

O seguinte corte de código mostra como obter a credencial de token autenticado e usá-lo para criar um cliente de serviço para armazenamento Blob:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obtenha a chave da delegação de utilizadores

Todos os SAS são assinados com uma chave. Para criar uma delegação de utilizador SAS, deve primeiro solicitar uma chave de delegação de utilizador, que é depois utilizada para assinar o SAS. A chave da delegação do utilizador é análoga à chave de conta utilizada para assinar um serviço SAS ou uma conta SAS, exceto que se baseia nas suas credenciais AZure AD. Quando um cliente solicita uma chave de delegação de utilizador utilizando um token OAuth 2.0, o Azure Storage devolve a chave da delegação do utilizador em nome do utilizador.

Uma vez que tenha a chave da delegação do utilizador, pode usar essa chave para criar qualquer número de assinaturas de acesso partilhadas da delegação do utilizador, ao longo do tempo de vida da chave. A chave da delegação do utilizador é independente do símbolo OAuth 2.0 utilizado para adquiri-lo, pelo que o símbolo não precisa de ser renovado enquanto a chave ainda for válida. Pode especificar que a chave é válida por um período de até 7 dias.

Utilize um dos seguintes métodos para solicitar a chave da delegação do utilizador:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

O seguinte corte de código obtém a chave da delegação do utilizador e escreve as suas propriedades:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Obtenha uma delegação de usuário SAS para uma bolha

O seguinte exemplo de código mostra o código completo para autenticar o principal de segurança e criar a delegação de utilizador SAS para uma bolha:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

O exemplo a seguir testa a delegação de utilizador SAS criada no exemplo anterior a partir de uma aplicação de cliente simulada. Se o SAS for válido, a aplicação do cliente é capaz de ler o conteúdo da bolha. Se o SAS for inválido, por exemplo, se tiver expirado, o Azure Storage devolve o código de erro 403 (Proibido).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Obtenha uma delegação de usuário SAS para um recipiente

O seguinte exemplo de código mostra como gerar uma delegação de utilizador SAS para um recipiente:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

O exemplo a seguir testa a delegação de utilizador SAS criada no exemplo anterior a partir de uma aplicação de cliente simulada. Se o SAS for válido, a aplicação do cliente é capaz de ler o conteúdo da bolha. Se o SAS for inválido, por exemplo, se tiver expirado, o Azure Storage devolve o código de erro 403 (Proibido).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ver também

- [Conceder acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md)
- [Obtenha a operação chave da delegação de utilizadores](/rest/api/storageservices/get-user-delegation-key)
- [Criar uma delegação de utilizadores SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
