---
title: 'Tutorial: Encriptar e desencriptar blobs no armazenamento do Azure com o Azure Key Vault | Documentos da Microsoft'
description: Como encriptar e desencriptar um blob com encriptação do lado do cliente para o armazenamento do Microsoft Azure com o Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: d7c740133911689c6d3f8e29c2cb20aa8873f0c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788001"
---
# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>Tutorial: Encriptar e desencriptar blobs no armazenamento do Microsoft Azure com o Azure Key Vault

## <a name="introduction"></a>Introdução
Este tutorial abrange como tornar a utilização de encriptação de armazenamento do lado do cliente com o Azure Key Vault. Ele descreve como encriptar e desencriptar um blob numa aplicação de consola com essas tecnologias.

**Tempo estimado para concluir:** 20 minutos

Para obter informações gerais sobre o Azure Key Vault, consulte [o que é o Azure Key Vault?](../../key-vault/key-vault-whatis.md).

Para obter informações gerais sobre a encriptação do lado do cliente do armazenamento do Azure, consulte [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, deve ter o seguinte:

* Uma conta de armazenamento do Azure
* Visual Studio 2013 ou posterior
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Descrição geral da encriptação do lado do cliente

Para uma descrição geral da encriptação do lado do cliente do armazenamento do Azure, consulte [encriptação do lado do cliente e o Azure Key Vault para o armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Eis uma breve descrição de como funciona a encriptação do lado do cliente:

1. O SDK do cliente de armazenamento do Azure gera uma chave de encriptação de conteúdo (CEK), que é uma chave simétrica de uma único uso.
2. Dados do cliente são encriptados utilizando este CEK.
3. O CEK, em seguida, é encapsulado (encriptada) com a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada no Azure Key Vault. O próprio cliente de armazenamento nunca tem acesso para a KEK. Ele simplesmente invoca o algoritmo de chave de encapsulamento de aplicações fornecido pelo Cofre de chaves. Os clientes podem optar por utilizar o provedores personalizados para a chave de encapsulamento de aplicações/abertura se desejarem.
4. Os dados encriptados, em seguida, são carregados para o serviço de armazenamento do Azure.

## <a name="set-up-your-azure-key-vault"></a>Configurar o seu Cofre de chaves do Azure

Para continuar com este tutorial, terá de efetuar os passos seguintes, que são descritos no tutorial [início rápido: Definir e obter um segredo no Azure Key Vault ao utilizar uma aplicação web .NET](../../key-vault/quick-create-net.md):

* Criar um cofre de chaves.
* Adicione uma chave ou segredo ao Cofre de chaves.
* Registe uma aplicação com o Azure Active Directory.
* Autorize a aplicação a utilizar a chave ou segredo.

Tome nota do ID de cliente e ClientSecret que foram gerados quando o registo de aplicações no Azure Active Directory.

Crie ambas as chaves no Cofre de chaves. Partimos do princípio para o resto do tutorial que utiliza os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar uma aplicação de consola com pacotes e AppSettings

No Visual Studio, crie uma nova aplicação de consola.

Adicione pacotes de nuget necessárias na consola do Gestor de pacotes.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione AppSettings para o App. config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione o seguinte `using` diretivas e certifique-se de adicionar uma referência a System ao projeto.

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

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicionar um método para obter um token para a sua aplicação de consola

O método seguinte é utilizado por classes de Cofre de chaves que tem de autenticar para o acesso ao seu Cofre de chaves.

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

## <a name="access-storage-and-key-vault-in-your-program"></a>Armazenamento de acesso e o Cofre de chaves em seu programa

No método Main (), adicione o seguinte código.

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
> Modelos de objeto do Cofre de chaves
> 
> É importante compreender o que há, na verdade, duas Key Vault modelos de objeto estar atento: uma baseia-se a API de REST (espaço de nomes do Cofre de chaves) e o outro é uma extensão para a encriptação do lado do cliente.
> 
> O cliente do Cofre de chave interage com a API REST e compreende JSON Web chaves e segredos para os dois tipos de coisas que estão contidos no Cofre de chaves.
> 
> As extensões de Cofre de chave são classes que parecem criadas especificamente para a encriptação do lado do cliente no armazenamento do Azure. Contêm uma interface de chaves (IKey) e classes com base no conceito de um resolvedor de chave. Existem duas implementações de IKey que precisa saber: RSAKey e SymmetricKey. Agora que eles aconteçam coincidir com as coisas que estão contidas num cofre de chaves, mas neste momento são classes independentes (para que a chave e o segredo obtido pelo cliente do Cofre de chave não implementam IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Encriptar BLOBs e carregar

Adicione o seguinte código para encriptar um blob e carregue-o à sua conta de armazenamento do Azure. O **ResolveKeyAsync** método usado retorna uma IKey.

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
> Se examinar o construtor de BlobEncryptionPolicy, verá que ele pode aceitar uma chave de e/ou de um resolvedor. Lembre-se de que neste momento não é possível utilizar um resolvedor para a encriptação porque faz atualmente não suporta uma chave predefinida.

## <a name="decrypt-blob-and-download"></a>Desencriptar BLOBs e transferir

Desencriptação é, realmente, quando usando as classes do resolvedor fazem sentido. O ID da chave utilizada para encriptação é associado ao blob nos respetivos metadados, portanto, não há nenhuma razão para obter a chave e lembre-se a associação entre a chave e BLOBs. Terá de certificar-se de que a chave permanece no Cofre de chaves.   

A chave privada de uma chave RSA permanece no Key Vault, para que para a descriptografia ocorrer, a chave encriptada dos metadados do blob que contém o CEK sejam enviada para o Key Vault para desencriptação.

Adicione o seguinte para desencriptar o blob que acabou de carregar.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Existem alguns outros tipos de resoluções para facilitar a gestão de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Utilizar segredos do Key Vault

A forma de usar um segredo com a encriptação do lado do cliente é por meio da classe SymmetricKey porque um segredo é essencialmente uma chave simétrica. No entanto, conforme observado anteriormente, um segredo no Key Vault não é mapeado exatamente a um SymmetricKey. Existem alguns aspetos a compreender:

* A chave num SymmetricKey tem de ser um comprimento fixo: 128, 192, 256, 384 ou 512 bits.
* A chave num SymmetricKey deve ser codificada em Base64.
* Um segredo do Key Vault que será utilizado como um SymmetricKey tem de ter um tipo de conteúdo de "application/octet-stream" no Key Vault.

Eis um exemplo do PowerShell de criação de um segredo no Cofre de chaves que podem ser utilizadas como um SymmetricKey.
Tenha em atenção que o valor hard-coded, $key, destina-se apenas a fins de demonstração. No seu próprio código vai querer gerar esta chave.

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

Na sua aplicação de consola, pode utilizar a mesma chamada como antes de obter este segredo como um SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
Já está. Divirta-se!

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o armazenamento do Microsoft Azure com c#, veja [biblioteca do cliente de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obter mais informações sobre a API de REST do Blob, veja [API de REST do serviço Blob](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para obter as informações mais recentes no armazenamento do Microsoft Azure, vá para o [blogue da equipa do Microsoft Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/).