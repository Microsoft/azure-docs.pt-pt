---
title: Ligue-se de forma segura a um cluster de tecido de serviço Azure
description: Descreve como autenticar o acesso do cliente a um cluster de Tecido de Serviço e como garantir a comunicação entre clientes e um cluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587061"
---
# <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro

Quando um cliente se conecta a um nó de cluster de tecido de serviço, o cliente pode ser autenticado e seguro comunicação estabelecida usando a segurança do certificado ou o Diretório Ativo Azure (AAD). Esta autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e implementar aplicações e executar tarefas de gestão.  O certificado ou a segurança AAD devem ter sido previamente ativados no cluster quando o cluster foi criado.  Para obter mais informações sobre os cenários de segurança do cluster, consulte a [segurança do Cluster](service-fabric-cluster-security.md). Se estiver a ligar-se a um cluster protegido com certificados, [fixe o certificado de cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que se liga ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ligue-se a um cluster seguro utilizando o Tecido de Serviço Azure CLI (sfctl)

Existem algumas formas diferentes de se ligar a um cluster seguro usando o ClI de tecido de serviço (sfctl). Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster. Se o seu certificado tiver Autoridades de Certificado (AEm), precisa de especificar adicionalmente os AEm fidedignos.

Pode ligar-se a um cluster utilizando o comando `sfctl cluster select`.

Os certificados de cliente podem ser especificados em duas formas diferentes, seja como um par cert e chave, ou como um único ficheiro PFX. Para ficheiros PEM protegidos por palavra-passe, será solicitado automaticamente a introduzir a palavra-passe. Se obteve o certificado de cliente como ficheiro PFX, converta primeiro o ficheiro PFX num ficheiro PEM utilizando o seguinte comando. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Se o seu ficheiro .pfx não estiver protegido por palavra-passe, utilize passe -passina: para o último parâmetro.

Para especificar o certificado de cliente como ficheiro pem, especifique o caminho do ficheiro no argumento `--pem`. Por exemplo:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Os ficheiros pem protegidos por palavra-passe serão solicitados para a palavra-passe antes de executar qualquer comando.

Para especificar um par de chaves, utilize os argumentos `--cert` e `--key` para especificar os caminhos dos ficheiros para cada ficheiro respetivo.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Por vezes, os certificados utilizados para garantir o teste ou os clusters de dev falham na validação do certificado. Para contornar a verificação do certificado, especifique a opção `--no-verify`. Por exemplo:

> [!WARNING]
> Não utilize a opção `no-verify` ao ligar-se aos clusters de tecido de serviço de produção.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Além disso, pode especificar caminhos para diretórios de certificados ca confiáveis, ou certs individuais. Para especificar estes caminhos, use o argumento `--ca`. Por exemplo:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Depois de se ligar, deve ser capaz de [executar outros comandos de sfctl](service-fabric-cli.md) para interagir com o cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ligue-se a um cluster usando powerShell
Antes de efetuar operações num cluster através da PowerShell, estabeleça primeiro uma ligação ao cluster. A ligação de cluster é utilizada para todos os comandos subsequentes na sessão PowerShell dada.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster inseguro

Para se ligar a um cluster não seguro, forneça o endereço final do cluster ao comando **Connect-ServiceFabricCluster:**

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligue-se a um cluster seguro usando o Diretório Ativo Azure

Para se ligar a um cluster seguro que utiliza o Diretório Ativo Azure para autorizar o acesso ao administrador do cluster, forneça a impressão digital do certificado de cluster e utilize a bandeira *AzureActiveDirectory.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligue-se a um cluster seguro usando um certificado de cliente
Executar o seguinte comando PowerShell para ligar a um cluster seguro que utiliza certificados de cliente para autorizar o acesso do administrador. 

#### <a name="connect-using-certificate-common-name"></a>Conecte-se usando o nome comum do certificado
Forneça o nome comum do certificado de cluster e o nome comum do certificado de cliente que tenha sido concedida permissões para gestão de clusters. Os dados do certificado devem coincidir com um certificado nos nós do cluster.

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
Forneça a impressão digital do certificado de cluster e a impressão digital do certificado de cliente que foi concedida permissões para a gestão do cluster. Os dados do certificado devem coincidir com um certificado nos nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é a impressão digital do certificado de servidor instalado nos nós do cluster. *FindValue* é a impressão digital do certificado de cliente administrativo.  Quando os parâmetros são preenchidos, o comando parece ser o seguinte exemplo:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ligue-se a um cluster seguro usando o Diretório Ativo do Windows
Se o seu cluster autónomo for implantado utilizando a segurança da AD, ligue-se ao cluster através da adesão do interruptor "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ligue-se a um cluster usando as APIs FabricClient
O SDK de Tecido de Serviço fornece a classe [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para gestão de clusters. Para utilizar as APIs do FabricClient, obtenha o pacote Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster inseguro

Para se ligar a um cluster remoto não seguro, crie uma instância FabricClient e forneça o endereço de cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para um código que está a funcionar dentro de um cluster, por exemplo, num Serviço Fiável, crie um FabricClient *sem* especificar o endereço do cluster. O FabricClient liga-se ao portal de gestão local no nó em que o código está atualmente em execução, evitando um salto de rede extra.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligue-se a um cluster seguro usando um certificado de cliente

Os nós do cluster devem ter certificados válidos cujo nome comum ou nome DNS em SAN aparece na [propriedade RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) definida no [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Na sequência deste processo permite a autenticação mútua entre o cliente e os nós do cluster.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ligue-se a um cluster seguro utilizando o Diretório Ativo Azure

O exemplo seguinte utiliza o Diretório Ativo Azure para identidade do cliente e certificado de servidor para identidade do servidor.

Uma janela de diálogo aparece automaticamente para iniciar sessão interativa ao ligar-se ao cluster.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ligue-se a um cluster seguro sem interactivamente usando o Diretório Ativo Azure

O exemplo seguinte baseia-se no Microsoft.IdentityModel.Clients.ActiveDirectory, Versão: 2.19.208020213.

Para obter mais informações sobre a aquisição de token AAD, consulte [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ligue-se a um cluster seguro sem conhecimentos prévios de metadados utilizando o Diretório Ativo do Azure

O exemplo seguinte utiliza aquisição de token não interativo, mas a mesma abordagem pode ser usada para construir uma experiência de aquisição interativa personalizada. Os metadados do Azure Ative Directory necessários para a aquisição de fichas são lidos a partir da configuração do cluster.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ligue-se a um cluster seguro usando o Explorador de Tecido de Serviço
Para chegar ao [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para um determinado cluster, aponte o seu navegador para:

`http://<your-cluster-endpoint>:19080/Explorer`

O URL completo também está disponível no painel essencial do cluster do portal Azure.

Para se ligar a um cluster seguro no Windows ou NO Os X utilizando um browser, pode importar o certificado de cliente, e o navegador irá pedir-lhe para o certificado usar para a ligação ao cluster.  Nas máquinas Linux, o certificado terá de ser importado utilizando configurações avançadas do navegador (cada navegador tem mecanismos diferentes) e apontá-lo para a localização do certificado no disco. Leia [Configurar um certificado](#connectsecureclustersetupclientcert) de cliente para mais informações.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligue-se a um cluster seguro usando o Diretório Ativo Azure

Para se ligar a um cluster que está protegido com AAD, aponte o seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

É-lhe pedido automaticamente que se inscreva no AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligue-se a um cluster seguro usando um certificado de cliente

Para se ligar a um cluster que está protegido com certificados, aponte o seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

É-lhe solicitado automaticamente que selecione um certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto

Pelo menos dois certificados devem ser utilizados para a segurança do cluster, um para o cluster e certificado de servidor e outro para acesso ao cliente.  Recomendamos que também utilize certificados secundários adicionais e certificados de acesso ao cliente.  Para garantir a comunicação entre um cliente e um nó de cluster utilizando a segurança do certificado, primeiro precisa de obter e instalar o certificado de cliente. O certificado pode ser instalado na loja Pessoal (Minha) do computador local ou no utilizador atual.  Também precisa da impressão digital do certificado do servidor para que o cliente possa autenticar o cluster.

* No Windows: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado no seu arquivo pessoal, `Certificates - Current User\Personal\Certificates`. Em alternativa, pode utilizar o comando PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Se for um certificado auto-assinado, precisa de o importar para a loja "pessoas de confiança" da sua máquina antes de poder utilizar este certificado para se ligar a um cluster seguro.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* No Mac: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado na sua Keychain.

## <a name="next-steps"></a>Passos seguintes

* [Processo de upgrade do Cluster de Tecidode Serviço e expectativas de si](service-fabric-cluster-upgrade.md)
* [Gerir as suas aplicações de Tecido de Serviço no Estúdio Visual](service-fabric-manage-application-in-visual-studio.md)
* [Introdução do modelo de saúde de tecido de serviço](service-fabric-health-introduction.md)
* [Segurança de aplicações e RunAs](service-fabric-application-runas-security.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
