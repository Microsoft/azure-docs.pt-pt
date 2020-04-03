---
title: Tutorial - Criptografar e desencriptar bolhas usando o Cofre de Chaves Azure
titleSuffix: Azure Storage
description: Aprenda a encriptar e desencriptar uma bolha usando encriptação do lado do cliente com o Cofre de Chaves Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 3f4f1f5e163dfed9f356aed538d934d0e4258790
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618890"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Tutorial - Criptografar e desencriptar bolhas usando o Cofre de Chaves Azure

Este tutorial cobre como fazer uso da encriptação de armazenamento do lado do cliente com o Cofre chave Azure. Ele te explica como encriptar e desencriptar uma bolha numa aplicação de consola usando estas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](../../key-vault/key-vault-overview.md)

Para obter informações sobre encriptação do lado do cliente para o Armazenamento Azure, consulte a encriptação do lado do cliente e o [Cofre de Chaves Azure para o Armazenamento Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Uma conta de armazenamento azure
* Estúdio Visual 2013 ou mais tarde
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Visão geral da encriptação do lado do cliente

Para uma visão geral da encriptação do lado do cliente para o Armazenamento Azure, consulte [encriptação do lado do cliente e cofre de chaves Azure para armazenamento Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Aqui está uma breve descrição de como a encriptação do lado do cliente funciona:

1. O SDK, cliente de armazenamento azure, gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uso único.
2. Os dados do cliente são encriptados utilizando este CEK.
3. O CEK é então embrulhado (encriptado) utilizando a chave de encriptação da chave (KEK). O KEK é identificado por um identificador chave e pode ser um par de chaves assimétrica ou uma chave simétrica e pode ser gerido localmente ou armazenado no Cofre chave Azure. O próprio cliente de Armazenamento nunca tem acesso ao KEK. Apenas invoca o algoritmo de embrulho chave que é fornecido pela Key Vault. Os clientes podem optar por utilizar fornecedores personalizados para embrulhar/desembrulhar chaves, se assim o desejarem.
4. Os dados encriptados são depois enviados para o serviço de armazenamento Azure.

## <a name="set-up-your-azure-key-vault"></a>Instale o seu Cofre de Chave Azure

Para prosseguir com este tutorial, é necessário fazer os seguintes passos, que estão delineados no tutorial [Quickstart: Definir e recuperar um segredo do Azure Key Vault utilizando uma aplicação web .NET:](../../key-vault/quick-create-net.md)

* Criar um cofre de chaves.
* Adicione uma chave ou segredo ao cofre da chave.
* Registe uma candidatura com o Azure Ative Directory.
* Autorize o pedido a utilizar a chave ou o segredo.

Tome nota do ClientID e ClientSecret que foram gerados ao registar uma aplicação com o Diretório Ativo Azure.

Crie ambas as chaves no cofre da chave. Assumimos para o resto do tutorial que usou os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar uma aplicação de consola com pacotes e AppSettings

No Estúdio Visual, crie uma nova aplicação de consola.

Adicione os pacotes de nuget necessários na consola do Gestor de Pacotes.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione As Definições de Aplicações à App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione as `using` seguintes diretivas e certifique-se de adicionar uma referência ao System.Configuration ao projeto.

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

O método seguinte é usado pelas classes Key Vault que precisam autenticar para acesso ao seu cofre chave.

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

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Aceda ao Armazenamento Azure e cofre de chaves no seu programa

No método Principal, adicione o seguinte código.

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
> Modelos de objeto de cofre chave
> 
> É importante entender que existem na verdade dois modelos de objetos Key Vault para estar atento: um é baseado no REST API (KeyVault namespace) e o outro é uma extensão para encriptação do lado do cliente.
> 
> O Cliente Key Vault interage com a API REST e compreende as Chaves Web da JSON e os segredos para os dois tipos de coisas que estão contidas no Cofre chave.
> 
> As extensões do cofre chave são classes que parecem especificamente criadas para encriptação do lado do cliente no Armazenamento Azure. Contêm uma interface para teclas (IKey) e aulas baseadas no conceito de um Key Resolver. Há duas implementações do IKey que precisa de saber: RSAKey e SymmetricKey. Agora coincidem com as coisas que estão contidas num Cofre chave, mas neste momento são classes independentes (por isso a Chave e o Segredo recuperados pelo Cliente Key Vault não implementam o IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Criptografe blob e upload

Adicione o seguinte código para encriptar uma bolha e carregá-lo na sua conta de armazenamento Azure. O método **ResolveKeyAsync** que é utilizado devolve um IKey.

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
> Se olhar para o construtor BlobEncryptionPolicy, verá que pode aceitar uma chave e/ou um resolver. Esteja ciente de que, neste momento, não pode utilizar um resolver para encriptação porque não suporta atualmente uma chave predefinida.

## <a name="decrypt-blob-and-download"></a>Desencriptar blob e baixar

A desencriptação é realmente quando se usam as aulas resolver fazem sentido. A identificação da chave utilizada para encriptação está associada à bolha nos seus metadados, pelo que não há razão para recuperar a chave e lembrar-se da associação entre a chave e a bolha. Só tens de ter a certeza que a chave permanece no Cofre chave.   

A chave privada de uma chave RSA permanece no Cofre chave, por isso, para que a desencriptação ocorra, a Chave Encriptada dos metadados blob que contém o CEK é enviada para Key Vault para desencriptação.

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
> Existem alguns outros tipos de resolver para facilitar a gestão das chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Use segredos do cofre chave

A forma de usar um segredo com encriptação do lado do cliente é através da classe SymmetricKey porque um segredo é essencialmente uma chave simétrica. Mas, como referido acima, um segredo em Key Vault não mapeia exatamente para uma Chave Simétrica. Há algumas coisas para entender:

* A chave de uma Chave Simétrica tem de ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
* A chave de uma Chave Simétrica deve ser codificada pela Base64.
* Um segredo do Cofre chave que será usado como uma Chave Simétrica precisa de ter um tipo de conteúdo de "aplicação/fluxo de octeto" no Cofre chave.

Aqui está um exemplo na PowerShell de criar um segredo no Cofre chave que pode ser usado como simétricoChave.
Por favor, note que o valor codificado, $key, é apenas para fins de demonstração. No seu próprio código, vai querer gerar esta chave.

```csharp
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

Na aplicação da consola, pode utilizar a mesma chamada que antes para recuperar este segredo como um SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Já está. Divirta-se!

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do Armazenamento Microsoft Azure com C#, consulte a [Microsoft Azure Storage Client Library para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para mais informações sobre a API blob REST, consulte [Blob Service REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para obter as mais recentes informações sobre o Armazenamento Microsoft Azure, vá ao [Microsoft Azure Storage Team Blog](https://blogs.msdn.com/b/windowsazurestorage/).
