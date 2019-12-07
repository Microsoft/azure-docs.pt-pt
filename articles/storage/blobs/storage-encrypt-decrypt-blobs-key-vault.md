---
title: Tutorial – criptografar e descriptografar BLOBs usando Azure Key Vault
titleSuffix: Azure Storage
description: Saiba como criptografar e descriptografar um BLOB usando a criptografia do lado do cliente com o Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c83e56a47f4b212a5612cb9e6965ce8e73228dcb
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892894"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Tutorial – criptografar e descriptografar BLOBs usando Azure Key Vault

Este tutorial aborda como fazer uso da criptografia de armazenamento do lado do cliente com o Azure Key Vault. Ele orienta você sobre como criptografar e descriptografar um blob em um aplicativo de console usando essas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações gerais sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../../key-vault/key-vault-overview.md).

Para obter informações gerais sobre a criptografia do cliente para o armazenamento do Azure, consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Uma conta de armazenamento do Azure
* Visual Studio 2013 ou posterior
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Visão geral da criptografia do lado do cliente

Para obter uma visão geral da criptografia do lado do cliente para o armazenamento do Azure, consulte [criptografia do lado do cliente e Azure Key Vault para armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Aqui está uma breve descrição de como funciona a criptografia do lado do cliente:

1. O SDK do cliente de armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo), que é uma chave simétrica de uso único.
2. Os dados do cliente são criptografados usando esse CEK.
3. O CEK é então encapsulado (criptografado) usando a chave de criptografia de chave (KEK). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciado localmente ou armazenado em Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso ao KEK. Ele simplesmente invoca o algoritmo de encapsulamento de chaves que é fornecido pelo Key Vault. Os clientes podem optar por usar provedores personalizados para encapsulamento/desencapsulamento de chave, se desejarem.
4. Os dados criptografados são então carregados no serviço de armazenamento do Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar seu Azure Key Vault

Para continuar com este tutorial, você precisa executar as etapas a seguir, que são descritas no guia de [início rápido do tutorial: definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .net](../../key-vault/quick-create-net.md):

* Criar um cofre de chaves.
* Adicione uma chave ou um segredo ao cofre de chaves.
* Registrar um aplicativo com Azure Active Directory.
* Autorize o aplicativo a usar a chave ou o segredo.

Anote os ClientID e ClientSecret que foram gerados ao registrar um aplicativo com Azure Active Directory.

Crie as duas chaves no cofre de chaves. Supomos que, para o restante do tutorial, você tenha usado os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar um aplicativo de console com pacotes e AppSettings

No Visual Studio, crie um novo aplicativo de console.

Adicione os pacotes NuGet necessários no console do Gerenciador de pacotes.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione AppSettings ao app. config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione as seguintes diretivas de `using` e certifique-se de adicionar uma referência a System. Configuration ao projeto.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicionar um método para obter um token para seu aplicativo de console

O método a seguir é usado por Key Vault classes que precisam se autenticar para acessar o cofre de chaves.

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

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Acessar o armazenamento do Azure e Key Vault em seu programa

No método Main (), adicione o código a seguir.

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey");
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modelos de objeto Key Vault
> 
> É importante entender que há, na verdade, dois Key Vault modelos de objeto a serem cientes: um é baseado na API REST (namespace do keyvault) e o outro é uma extensão para a criptografia do lado do cliente.
> 
> O cliente do Key Vault interage com a API REST e entende chaves da Web JSON e segredos para os dois tipos de coisas contidos no Key Vault.
> 
> As extensões de Key Vault são classes que parecem criadas especificamente para criptografia do lado do cliente no armazenamento do Azure. Eles contêm uma interface para chaves (IKey) e classes com base no conceito de um resolvedor de chave. Há duas implementações de IKey que você precisa saber: RSAKey e SymmetricKey. Agora, eles acontecem coincidir com as coisas contidas em um Key Vault, mas nesse ponto eles são classes independentes (portanto, a chave e o segredo recuperados pelo cliente Key Vault não implementam IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Criptografar BLOB e carregar

Adicione o código a seguir para criptografar um blob e carregá-lo em sua conta de armazenamento do Azure. O método **ResolveKeyAsync** usado retorna um iKey.

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
> Se você observar o Construtor BlobEncryptionPolicy, verá que ele pode aceitar uma chave e/ou um resolvedor. Lembre-se de que agora não é possível usar um resolvedor para criptografia porque ele atualmente não oferece suporte a uma chave padrão.

## <a name="decrypt-blob-and-download"></a>Descriptografar BLOB e baixar

A descriptografia é realmente ao usar as classes de resolvedor faz sentido. A ID da chave usada para criptografia é associada ao blob em seus metadados, portanto, não há motivo para você recuperar a chave e lembrar a associação entre a chave e o blob. Você só precisa certificar-se de que a chave permaneça em Key Vault.   

A chave privada de uma chave RSA permanece em Key Vault, portanto, para que a descriptografia ocorra, a chave criptografada dos metadados de BLOB que contém o CEK é enviada para Key Vault para descriptografia.

Adicione o seguinte para descriptografar o blob que você acabou de carregar.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Há alguns outros tipos de resolvedores para facilitar o gerenciamento de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Usar segredos de Key Vault

A maneira de usar um segredo com a criptografia do lado do cliente é por meio da classe SymmetricKey porque um segredo é essencialmente uma chave simétrica. Mas, como observado acima, um segredo em Key Vault não é mapeado exatamente para um SymmetricKey. Há algumas coisas a serem compreendidas:

* A chave em um SymmetricKey deve ter um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
* A chave em um SymmetricKey deve ser codificada em base64.
* Um segredo Key Vault que será usado como um SymmetricKey precisa ter um tipo de conteúdo de "application/octet-stream" em Key Vault.

Veja um exemplo no PowerShell de criação de um segredo no Key Vault que pode ser usado como um SymmetricKey.
Observe que o valor embutido em código, $key, é apenas para fins de demonstração. Em seu próprio código, você desejará gerar essa chave.

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

No aplicativo de console, você pode usar a mesma chamada de antes para recuperar esse segredo como um SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

E já está. Divirta-se!

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como usar C#armazenamento do Microsoft Azure com o, consulte [armazenamento do Microsoft Azure biblioteca de cliente para .net](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obter mais informações sobre a API REST do blob, consulte [API REST do serviço blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para obter as informações mais recentes sobre Armazenamento do Microsoft Azure, acesse o [blog da equipe do armazenamento do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/).
