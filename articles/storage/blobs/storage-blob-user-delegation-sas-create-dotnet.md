---
title: Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia) – armazenamento do Azure
description: Saiba como criar uma SAS de delegação de usuário usando credenciais de Azure Active Directory no armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 59de768e75a88d7cfa5b68fa306d0e83f1aa0ba3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671333"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>Criar uma SAS de delegação de usuário para um contêiner ou BLOB com .NET (versão prévia)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar as credenciais do Azure Active Directory (AD do Azure) para criar uma SAS de delegação de usuário para um contêiner ou BLOB com a [biblioteca de cliente de armazenamento do Azure para .net](https://www.nuget.org/packages/Azure.Storage.Blobs).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-packages"></a>Instalar os pacotes de visualização

Os exemplos neste artigo usam a versão de visualização mais recente da biblioteca de cliente de armazenamento do Azure para armazenamento de BLOBs. Para instalar o pacote de visualização, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Os exemplos neste artigo também usam a versão de visualização mais recente da [biblioteca de cliente de identidade do Azure para .net](https://www.nuget.org/packages/Azure.Identity/) para autenticar com as credenciais do Azure AD. A biblioteca de cliente de identidade do Azure autentica uma entidade de segurança. A entidade de segurança autenticada pode criar a SAS de delegação de usuário. Para obter mais informações sobre a biblioteca de cliente de identidade do Azure, consulte [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

```
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para autenticar com as credenciais do Azure AD por meio da biblioteca de cliente de identidade do Azure, use uma entidade de serviço ou uma identidade gerenciada como a entidade de segurança, dependendo de onde o código está sendo executado. Se o seu código estiver sendo executado em um ambiente de desenvolvimento, use uma entidade de serviço para fins de teste. Se o seu código estiver em execução no Azure, use uma identidade gerenciada. Este artigo pressupõe que você está executando código do ambiente de desenvolvimento e mostra como usar uma entidade de serviço para criar a SAS de delegação de usuário.

Para criar uma entidade de serviço com CLI do Azure e atribuir uma função de RBAC, chame o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) . Forneça uma função de acesso de dados do armazenamento do Azure para atribuir à nova entidade de serviço. A função deve incluir a ação **Microsoft. Storage/storageAccounts/blobservices/generateUserDelegationKey** . Para obter mais informações sobre as funções internas fornecidas para o armazenamento do Azure, consulte [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

Além disso, forneça o escopo para a atribuição de função. A entidade de serviço criará a chave de delegação de usuário, que é uma operação executada no nível da conta de armazenamento, de modo que a atribuição de função deve ser delimitada no nível da conta de armazenamento, do grupo de recursos ou da assinatura. Para obter mais informações sobre permissões RBAC para criar uma SAS de delegação de usuário, consulte a seção **atribuir permissões com o RBAC** em [criar uma delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas).

Se você não tiver permissões suficientes para atribuir uma função à entidade de serviço, talvez seja necessário solicitar ao proprietário da conta ou ao administrador para executar a atribuição de função.

O exemplo a seguir usa o CLI do Azure para criar uma nova entidade de serviço e atribuir a função de **leitor de dados de blob de armazenamento** a ela com o escopo da conta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O comando `az ad sp create-for-rbac` retorna uma lista de propriedades da entidade de serviço no formato JSON. Copie esses valores para que você possa usá-los para criar as variáveis de ambiente necessárias na próxima etapa.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> As atribuições de função do RBAC podem levar alguns minutos para serem propagadas.

## <a name="set-environment-variables"></a>Definir variáveis de ambiente

A biblioteca de cliente de identidade do Azure lê valores de três variáveis de ambiente em tempo de execução para autenticar a entidade de serviço. A tabela a seguir descreve o valor a ser definido para cada variável de ambiente.

|Variável de ambiente|Value
|-|-
|`AZURE_CLIENT_ID`|A ID do aplicativo para a entidade de serviço
|`AZURE_TENANT_ID`|A ID de locatário do Azure AD da entidade de serviço
|`AZURE_CLIENT_SECRET`|A senha gerada para a entidade de serviço

> [!IMPORTANT]
> Depois de definir as variáveis de ambiente, feche e abra novamente a janela do console. Se você estiver usando o Visual Studio ou outro ambiente de desenvolvimento, talvez seja necessário reiniciar o ambiente de desenvolvimento para que ele registre as novas variáveis de ambiente.

## <a name="add-using-directives"></a>Adicionar com diretivas

Adicione as seguintes diretivas `using` ao seu código para usar as versões de visualização da identidade do Azure e das bibliotecas de cliente de armazenamento do Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="authenticate-the-service-principal"></a>Autenticar a entidade de serviço

Para autenticar a entidade de serviço, crie uma instância da classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . O Construtor `DefaultAzureCredential` lê as variáveis de ambiente que você criou anteriormente.

O trecho de código a seguir mostra como obter a credencial autenticada e usá-la para criar um cliente de serviço para o armazenamento de BLOBs

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Obter a chave de delegação do usuário

Cada SAS é assinada com uma chave. Para criar uma SAS de delegação de usuário, você deve primeiro solicitar uma chave de delegação de usuário, que é usada para assinar a SAS. A chave de delegação de usuário é análoga à chave de conta usada para assinar uma SAS de serviço ou uma SAS de conta, exceto que ela depende de suas credenciais do Azure AD. Quando um cliente solicita uma chave de delegação de usuário usando um token OAuth 2,0, o armazenamento do Azure retorna a chave de delegação de usuário em nome do usuário.

Quando tiver a chave de delegação de usuário, você poderá usar essa chave para criar qualquer número de assinaturas de acesso compartilhado de delegação de usuário, durante o tempo de vida da chave. A chave de delegação de usuário é independente do token 2,0 do OAuth usado para adquiri-la, portanto, o token não precisa ser renovado, contanto que a chave ainda seja válida. Você pode especificar que a chave seja válida por um período de até 7 dias.

Use um dos seguintes métodos para solicitar a chave de delegação do usuário:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

O trecho de código a seguir obtém a chave de delegação do usuário e grava suas propriedades:

```csharp
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>Criar o token SAS

O trecho de código a seguir mostra como criar um novo [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e fornecer os parâmetros para a SAS de delegação de usuário. O trecho de código chama o [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a cadeia de caracteres de token SAS. Por fim, o código cria o URI completo, incluindo o endereço de recurso e o token SAS.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Exemplo: Obter uma SAS de delegação de usuário

O seguinte método de exemplo mostra o código completo para autenticar a entidade de segurança e criar a SAS de delegação de usuário:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Exemplo: Ler um blob com uma SAS de delegação de usuário

O exemplo a seguir testa a SAS de delegação de usuário criada no exemplo anterior de um aplicativo cliente simulado. Se a SAS for válida, o aplicativo cliente poderá ler o conteúdo do blob. Se a SAS for inválida, por exemplo, se tiver expirado, o armazenamento do Azure retornará o código de erro 403 (proibido).

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Consulte também

- [Operação de obtenção de chave de delegação de usuário](/rest/api/storageservices/get-user-delegation-key)
- [Criar uma SAS de delegação de usuário (API REST)](/rest/api/storageservices/create-user-delegation-sas)
