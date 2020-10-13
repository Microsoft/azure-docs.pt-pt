---
title: Tutorial - Criptografe e desencriptar bolhas usando cofre de chave Azure
titleSuffix: Azure Storage
description: Aprenda a encriptar e desencriptar uma bolha usando encriptação do lado do cliente com a Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ac69b442b06864fd45561b26909e1247674611a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89458387"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Tutorial - Criptografe e desencriptar bolhas usando cofre de chave Azure

Este tutorial cobre como fazer uso da encriptação de armazenamento do lado do cliente com a Azure Key Vault. Ele explica-lhe como encriptar e desencriptar uma bolha numa aplicação de consola usando estas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações gerais sobre o Cofre da Chave Azure, veja [o que é o Cofre da Chave Azure?](../../key-vault/general/overview.md)

Para obter informações gerais sobre encriptação do lado do cliente para o Armazenamento Azure, consulte [a Encriptação do Lado do Cliente e o Cofre da Chave Azure para o Armazenamento microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Uma conta de armazenamento Azure
* Estúdio Visual 2013 ou mais tarde
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Visão geral da encriptação do lado do cliente

Para obter uma visão geral da encriptação do lado do cliente para o armazenamento do Azure, consulte [a encriptação do lado do cliente e o cofre da chave Azure para o Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Aqui está uma breve descrição de como a encriptação do lado do cliente funciona:

1. O cliente Azure Storage SDK gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.
2. Os dados do cliente são encriptados usando este CEK.
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado em Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso ao KEK. Apenas invoca o algoritmo de embrulho chave que é fornecido pela Key Vault. Os clientes podem optar por utilizar fornecedores personalizados para embrulhar/desembrulhar se quiserem.
4. Os dados encriptados são então enviados para o serviço de Armazenamento Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de Chaves Azure

Para prosseguir com este tutorial, você precisa fazer os seguintes passos, que são delineados no tutorial [Quickstart: set and retrieve a secret from Azure Key Vault usando uma aplicação web .NET](../../key-vault/secrets/quick-create-net.md):

* Criar um cofre de chaves.
* Adicione uma chave ou segredo ao cofre da chave.
* Registe um requerimento no Azure Ative Directory.
* Autorizar a aplicação a utilizar a chave ou o segredo.

Tome nota do ClientID e do ClientSecret que foram gerados ao registar uma aplicação com o Azure Ative Directory.

Crie as duas chaves no cofre das chaves. Assumimos para o resto do tutorial que usou os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar uma aplicação de consola com pacotes e AppSettings

No Visual Studio, crie uma nova aplicação para consolas.

Adicione pacotes nuget necessários na consola package manager.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione AppSettings ao App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione as `using` seguintes diretivas e certifique-se de adicionar uma referência à System.Configuration ao projeto.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicione um método para obter um símbolo na sua aplicação de consola

O seguinte método é utilizado pelas classes Key Vault que precisam de autenticar para acesso ao cofre das chaves.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Aceda a Azure Storage e Key Vault no seu programa

No método Principal,, adicione o seguinte código.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modelos de objetos de abóbada de chave
> 
> É importante entender que existem na verdade dois modelos de objetos Key Vault para estar ciente: um é baseado no REST API (KeyVault namespace) e o outro é uma extensão para encriptação do lado do cliente.
> 
> O Cliente Key Vault interage com a API REST e compreende as chaves e segredos da JSON Web Keys para os dois tipos de coisas que estão contidas no Cofre de Chaves.
> 
> As extensões do cofre chave são classes que parecem especificamente criadas para encriptação do lado do cliente no Azure Storage. Eles contêm uma interface para chaves (IKey) e aulas baseadas no conceito de um Key Resolver. Existem duas implementações do IKey que precisa de saber: RSAKey e SymmetricKey. Agora coincidem com as coisas que estão contidas num Cofre-Chave, mas neste momento são classes independentes (por isso a Chave e o Segredo recuperados pelo Cliente-Chave do Cofre não implementam o IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Encriptar blob e carregar

Adicione o seguinte código para encriptar uma bolha e carregá-la para a sua conta de armazenamento Azure. O método **ResolveKeyAsync** que é usado devolve um IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Se olhar para o construtor BlobEncrypationPolicy, verá que pode aceitar uma chave e/ou um resolver. Esteja ciente de que neste momento não pode utilizar um resolver para encriptação porque não suporta atualmente uma chave padrão.

## <a name="decrypt-blob-and-download"></a>Desencriptar blob e baixar

Desencriptação é realmente quando se usa as aulas de Resolver faz sentido. O ID da chave utilizada para encriptação está associado à bolha nos seus metadados, pelo que não há razão para recuperar a chave e lembrar a associação entre a chave e a bolha. Só tens de ter a certeza que a chave permanece no Cofre das Chaves.   

A chave privada de uma chave RSA permanece no Cofre de Chaves, por isso, para desencriptação, a chave encriptada dos metadados blob que contém o CEK é enviada para o Cofre de Chaves para desencriptação.

Adicione o seguinte para desencriptar a bolha que acabou de carregar.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Existem outros tipos de resolver para facilitar a gestão das chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Use segredos do Cofre chave

A forma de usar um segredo com encriptação do lado do cliente é através da classe SymmetricKey porque um segredo é essencialmente uma chave simétrica. Mas, como referido acima, um segredo em Key Vault não mapeia exatamente para um SymmetricKey. Há algumas coisas a entender:

* A chave de uma Chave Simétrica tem de ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
* A chave de uma Chave Simétrica deve ser codificada base64.
* Um segredo do Cofre chave que será usado como Um SymmetricKey precisa de ter um tipo de conteúdo de "application/octet-stream" no Key Vault.

Aqui está um exemplo no PowerShell de criar um segredo em Key Vault que pode ser usado como uma Chave Simétrica.
Por favor, note que o valor codificado, $key, é apenas para fins de demonstração. No seu próprio código, vai querer gerar esta chave.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Na sua aplicação de consola, pode utilizar a mesma chamada de antes para recuperar este segredo como uma SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Já está! Divirta-se!

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do Microsoft Azure Storage com C#, consulte [a Microsoft Azure Storage Client Library para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obter mais informações sobre a API Blob REST, consulte [a API do Blob Service REST](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para obter as informações mais recentes sobre o Microsoft Azure Storage, aceda ao [Microsoft Azure Storage Team Blog](https://docs.microsoft.com/archive/blogs/windowsazurestorage/).
