---
title: Ligue-se de forma segura a um cluster de tecido de serviço Azure
description: Descreve como autenticar o acesso do cliente a um cluster de Tecidos de Serviço e como garantir a comunicação entre clientes e um cluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a669f7400eb2daf2d71f6caed41560020699c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791126"
---
# <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro

Quando um cliente se conecta a um nó de cluster de Tecido de Serviço, o cliente pode ser autenticado e comunicar seguro estabelecido usando a segurança do certificado ou o Azure Ative Directory (AAD). Esta autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e aplicações implementadas e executar tarefas de gestão.  A segurança do certificado ou da AAD deve ter sido previamente ativada no cluster quando o cluster foi criado.  Para obter mais informações sobre cenários de segurança do cluster, consulte [a segurança do Cluster](service-fabric-cluster-security.md). Se estiver a ligar-se a um cluster protegido com certificados, [crie o certificado de cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que se conecta ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ligue-se a um cluster seguro utilizando o CLI do tecido de serviço Azure (sfctl)

Existem algumas maneiras diferentes de se ligar a um cluster seguro usando o CLI de tecido de serviço (sfctl). Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster. Se o seu certificado tiver Autoridades de Certificados (AC), precisa especificar adicionalmente os CAs fidedignos.

Pode ligar-se a um cluster utilizando o `sfctl cluster select` comando.

Os certificados de cliente podem ser especificados em duas modas diferentes, quer como um cert e um par de chaves, quer como um único ficheiro PFX. Para ficheiros PEM protegidos por palavra-passe, será solicitado automaticamente para introduzir a palavra-passe. Se obteve o certificado de cliente como ficheiro PFX, primeiro converta o ficheiro PFX num ficheiro PEM utilizando o seguinte comando. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Se o seu ficheiro .pfx não estiver protegido por palavra-passe, utilize o passe -passin pass: para o último parâmetro.

Para especificar o certificado do cliente como ficheiro pem, especifique o caminho do ficheiro no `--pem` argumento. Por exemplo:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Os ficheiros pem protegidos por palavra-passe solicitarão a palavra-passe antes de executar qualquer comando.

Para especificar um cert, o par de chaves usa os `--cert` e os argumentos para `--key` especificar os caminhos de ficheiro para cada ficheiro respetivo.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Por vezes, os certificados utilizados para garantir a validação do certificado de teste ou dev. Para contornar a verificação do certificado, especifique a `--no-verify` opção. Por exemplo:

> [!WARNING]
> Não utilize a `no-verify` opção quando ligar aos agrupamentos de tecidos de serviço de produção.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Além disso, você pode especificar caminhos para diretórios de certificados ca confiáveis, ou certificados individuais. Para especificar estes caminhos, use o `--ca` argumento. Por exemplo:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Depois de se ligar, deverá ser capaz [de executar outros comandos sfctl](service-fabric-cli.md) para interagir com o cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ligue-se a um cluster usando PowerShell
Antes de realizar operações num cluster através do PowerShell, primeiro estabeleça uma ligação ao cluster. A ligação de cluster é utilizada para todos os comandos subsequentes na sessão PowerShell dada.

### <a name="connect-to-an-unsecure-cluster"></a>Conecte-se a um cluster inseguro

Para ligar a um cluster não seguro, forneça o endereço final do cluster ao comando **Connect-ServiceFabricCluster:**

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligue-se a um cluster seguro usando o Azure Ative Directory

Para ligar a um cluster seguro que utiliza o Azure Ative Directory para autorizar o acesso ao administrador de cluster, forneça a impressão digital do certificado de cluster e utilize a bandeira *AzureActiveDirectory.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conecte-se a um cluster seguro usando um certificado de cliente
Executar o seguinte comando PowerShell para ligar a um cluster seguro que usa certificados de cliente para autorizar o acesso do administrador. 

#### <a name="connect-using-certificate-common-name"></a>Conecte-se usando o nome comum do certificado
Fornecer o nome comum do certificado de cluster e o nome comum do certificado de cliente a quem foi concedida permissão para gestão de clusters. Os dados do certificado devem corresponder a um certificado nos nós do cluster.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* é o nome comum do certificado de servidor instalado nos nós do cluster. *FindValue* é o nome comum do certificado de cliente administrativo. Quando os parâmetros são preenchidos, o comando parece ser o seguinte exemplo:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Conecte-se usando a impressão digital do certificado
Forneça a impressão digital do certificado de cluster e a impressão digital do certificado de cliente a quem foi concedida permissão para gestão do cluster. Os dados do certificado devem corresponder a um certificado nos nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é a impressão digital do certificado do servidor instalado nos nós do cluster. *FindValue* é a impressão digital do certificado de cliente administrativo.  Quando os parâmetros são preenchidos, o comando parece ser o seguinte exemplo:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ligue-se a um cluster seguro usando o Windows Ative Directory
Se o seu cluster autónomo for implantado utilizando a segurança AD, ligue-se ao cluster através da colocação do interruptor "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ligue-se a um cluster usando as APIs doCliente de Tecidos
A Service Fabric SDK fornece a classe [FabricClient](/dotnet/api/system.fabric.fabricclient) para gestão de clusters. Para utilizar as APIs DoNcico De Tecido, obtenha o pacote Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Conecte-se a um cluster inseguro

Para ligar a um cluster remoto não seguro, crie uma instância FabricClient e forneça o endereço de cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para um código que esteja a funcionar a partir de um cluster, por exemplo, num Serviço Fiável, crie um FabricClient *sem* especificar o endereço de cluster. O FabricClient liga-se ao portal de gestão local no nó onde o código está atualmente em funcionamento, evitando um salto de rede extra.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conecte-se a um cluster seguro usando um certificado de cliente

Os nós do cluster devem ter certificados válidos cujo nome comum ou nome DNS em SAN apareça na [propriedade RemoteCommonNames](/dotnet/api/system.fabric.x509credentials) definida no [FabricClient](/dotnet/api/system.fabric.fabricclient). Após este processo permite a autenticação mútua entre o cliente e os nós de cluster.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ligue-se a um cluster seguro interativamente usando o Azure Ative Directory

O exemplo a seguir utiliza o Azure Ative Directory para a identidade do cliente e certificado de servidor para a identidade do servidor.

Uma janela de diálogo aparece automaticamente para o insente interativo ao ligar-se ao cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ligue-se a um cluster seguro não interativamente usando o Azure Ative Directory

O exemplo a seguir baseia-se no Microsoft.IdentityModel.Clients.ActiveDirectory, Versão: 2.19.208020213.

Para obter mais informações sobre a aquisição de fichas AAD, consulte [Microsoft.IdentityModel.Clients.ActiveDirectory](/dotnet/api/microsoft.identitymodel.clients.activedirectory).

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Conecte-se a um cluster seguro sem conhecimentos de metadados prévios usando o Azure Ative Directory

O exemplo a seguir utiliza a aquisição de token não interativo, mas a mesma abordagem pode ser usada para construir uma experiência de aquisição de tokens interativos personalizada. Os metadados do Diretório Ativo Azure necessários para a aquisição de fichas são lidos a partir da configuração do cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Conecte-se a um cluster seguro usando o Explorador de Tecidos de Serviço
Para chegar ao [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para obter um determinado cluster, aponte o seu navegador para:

`http://<your-cluster-endpoint>:19080/Explorer`

O URL completo também está disponível no painel essencial do cluster do portal Azure.

Para se ligar a um cluster seguro no Windows ou NO X usando um browser, pode importar o certificado do cliente, e o navegador irá pedir-lhe que o certificado use para se conectar ao cluster.  Nas máquinas Linux, o certificado terá de ser importado utilizando configurações avançadas do navegador (cada navegador tem mecanismos diferentes) e apontá-lo para a localização do certificado no disco. Leia [Configurar um certificado de cliente](#connectsecureclustersetupclientcert) para mais informações.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligue-se a um cluster seguro usando o Azure Ative Directory

Para se ligar a um cluster que está seguro com a AAD, aponte o seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

É-lhe automaticamente solicitado que assine com a AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conecte-se a um cluster seguro usando um certificado de cliente

Para se ligar a um cluster que está protegido com certificados, aponte o seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

É-lhe automaticamente solicitado que selecione um certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto

Pelo menos dois certificados devem ser utilizados para assegurar o cluster, um para o certificado de cluster e servidor e outro para acesso ao cliente.  Recomendamos que utilize também certificados secundários adicionais e certificados de acesso ao cliente.  Para garantir a comunicação entre um cliente e um nó de cluster utilizando a segurança do certificado, primeiro precisa de obter e instalar o certificado do cliente. O certificado pode ser instalado na loja Pessoal (My) do computador local ou no utilizador atual.  Também precisa da impressão digital do certificado do servidor para que o cliente possa autenticar o cluster.

* No Windows: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado no seu arquivo pessoal, `Certificates - Current User\Personal\Certificates`. Em alternativa, pode utilizar o comando PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Se for um certificado auto-assinado, tem de o importar para a loja de "pessoas de confiança" da sua máquina antes de poder utilizar este certificado para se ligar a um cluster seguro.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* No Mac: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado na sua Keychain.

## <a name="next-steps"></a>Passos seguintes

* [Processo de upgrade do Cluster de Tecido de Serviço e expectativas de si](service-fabric-cluster-upgrade.md)
* [Gerir as suas aplicações de Tecido de Serviço em Estúdio Visual](service-fabric-manage-application-in-visual-studio.md)
* [Introdução do modelo de saúde do tecido de serviço](service-fabric-health-introduction.md)
* [Segurança de Aplicações e RunAs](service-fabric-application-runas-security.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
