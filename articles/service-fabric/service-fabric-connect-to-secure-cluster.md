---
title: Ligar em segurança a um cluster de Azure Service Fabric | Documentos da Microsoft
description: Descreve como autenticar o acesso de cliente para um cluster do Service Fabric e como proteger a comunicação entre clientes e um cluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 759a539e-e5e6-4055-bff5-d38804656e10
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2019
ms.author: aljo
ms.openlocfilehash: 42c8fa15c6b1e7c98ae47180bec5cc61236a7c44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881390"
---
# <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro

Quando um cliente se liga a um nó de cluster do Service Fabric, o cliente pode ser uma comunicação autenticada e segura estabelecida com o Azure Active Directory (AAD) ou de segurança de certificado. Esta autenticação garante que apenas os utilizadores autorizados podem aceder ao cluster e aplicativos implantados e efetuar tarefas de gestão.  Certificado ou de segurança do AAD tem anteriormente habilitada no cluster quando o cluster foi criado.  Para obter mais informações sobre cenários de segurança do cluster, consulte [segurança de Cluster](service-fabric-cluster-security.md). Se estiver a ligar a um cluster protegido por certificados, [configurar o certificado de cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que estabelece ligação ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Ligar a um cluster seguro com a CLI do Azure Service Fabric (sfctl)

Existem algumas formas diferentes para ligar a um cluster seguro com a CLI do Service Fabric (sfctl). Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster. Se o certificado tem de autoridades de certificação (ACs), tem de especificar adicionalmente a CAs confiáveis.

Pode ligar a um cluster com o `sfctl cluster select` comando.

Certificados de cliente podem ser especificados em dois fashions diferentes, como um par de certificado e chave, ou como um único ficheiro PFX. Para palavra-passe protegidas ficheiros PEM, será solicitado automaticamente para introduzir a palavra-passe. Se tiver adquirido o certificado de cliente como um ficheiro PFX, comece por converter o ficheiro PFX para ficheiro PEM com o seguinte comando. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Se o ficheiro. pfx não estiver protegido de palavra-passe, utilize - passin pass: para o último parâmetro.

Para especificar o certificado de cliente como um ficheiro pem, especifique o caminho do ficheiro no `--pem` argumento. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Palavra-passe protegida pem ficheiros solicitará a palavra-passe antes de executar qualquer comando.

Para especificar um certificado, utilize par de chaves. o `--cert` e `--key` argumentos para especificar os caminhos de ficheiro para cada ficheiro correspondente.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Por vezes, certificados usados para proteger clusters de desenvolvimento ou teste falham de validação de certificado. Para ignorar a verificação de certificado, especifique o `--no-verify` opção. Por exemplo:

> [!WARNING]
> Não utilize o `no-verify` opção ao ligar a clusters do Service Fabric de produção.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Além disso, pode especificar caminhos para os diretórios de certificados de AC fidedignos ou certificados individuais. Para especificar estes caminhos, use o `--ca` argumento. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Depois de ligar, deverá conseguir [executar outros comandos de sfctl](service-fabric-cli.md) para interagir com o cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Ligar a um cluster com o PowerShell
Antes de executar operações num cluster através do PowerShell, primeiro estabelecer uma ligação ao cluster. A ligação de cluster é utilizada para todos os comandos seguintes na sessão do PowerShell especificada.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster inseguro

Para ligar a um cluster inseguro, forneça o endereço de ponto final de cluster para o **Connect-ServiceFabricCluster** comando:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligar a um cluster seguro com o Azure Active Directory

Para ligar a um cluster seguro que utiliza o Azure Active Directory para autorizar o acesso de administrador de cluster, forneça o thumbprint do certificado de cluster e utilizar o *AzureActiveDirectory* sinalizador.  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro a utilizar um certificado de cliente
Execute o seguinte comando do PowerShell para ligar a um cluster seguro que utiliza certificados de cliente para autorizar o acesso de administrador. 

#### <a name="connect-using-certificate-common-name"></a>Ligar com o nome comum do certificado
Forneça o nome comum do certificado de cluster e o nome comum do certificado de cliente que foram concedido permissões para a gestão de cluster. Os detalhes do certificado devem coincidir com um certificado em nós do cluster.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* é o nome comum do certificado do servidor instalado em nós do cluster. *FindValue* é o nome comum do certificado de cliente de administrador. Quando os parâmetros são preenchidos, o comando terá um aspeto semelhante ao seguinte exemplo:
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

#### <a name="connect-using-certificate-thumbprint"></a>Ligar com o thumbprint do certificado
Forneça o thumbprint do certificado de cluster e o thumbprint do certificado de cliente que foram concedido permissões para a gestão de cluster. Os detalhes do certificado devem coincidir com um certificado em nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é o thumbprint do certificado do servidor instalado em nós do cluster. *FindValue* é o thumbprint do certificado de cliente administrativo.  Quando os parâmetros são preenchidos, o comando terá um aspeto semelhante ao seguinte exemplo:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Ligar a um cluster seguro com o Windows Active Directory
Se o seu cluster autónomo é implementado através da segurança do AD, ligue ao cluster ao acrescentar o comutador "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Ligar a um cluster com as APIs FabricClient
O SDK do Service Fabric fornece a [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) classe a gestão de clusters. Para utilizar as APIs FabricClient, obtenha o pacote Microsoft.ServiceFabric NuGet.

### <a name="connect-to-an-unsecure-cluster"></a>Ligar a um cluster inseguro

Para ligar a um cluster não seguro remoto, crie uma instância de FabricClient e forneça o endereço de cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para o código que for executada a partir de um cluster, por exemplo, um serviço fiável, criar um FabricClient *sem* especificando o endereço do cluster. FabricClient liga-se para o gateway de gestão local no nó que o código está atualmente em execução, evitando um salto de rede adicional.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro a utilizar um certificado de cliente

Os nós do cluster tem de ter certificados válidos cujo nome comum ou nome DNS no SAN é exibido no [propriedade RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) definir na [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Este processo permite a autenticação mútua entre o cliente e os nós do cluster.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Ligar a um cluster seguro de forma interativa através do Azure Active Directory

O exemplo seguinte utiliza o Azure Active Directory para o certificado de identidade e o servidor de cliente para a identidade do servidor.

Uma janela de caixa de diálogo aparece automaticamente para o início de sessão interativa ao estabelecer ligação ao cluster.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Ligar a um cluster seguro de forma não interativa com o Azure Active Directory

O exemplo a seguir se baseia em ActiveDirectory, versão: 2.19.208020213.

Para obter mais informações sobre aquisição de token do AAD, consulte [ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Ligar a um cluster seguro sem conhecimento prévio de metadados com o Azure Active Directory

O exemplo seguinte utiliza a aquisição do token não interativa, mas a mesma abordagem pode ser utilizada para criar uma experiência de aquisição do token interativos personalizados. Os metadados do Azure Active Directory necessários para a aquisição do token é de leitura da configuração de cluster.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Ligar a um cluster seguro com o Service Fabric Explorer
Para alcançar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para um determinado cluster, aponte o browser para:

`http://<your-cluster-endpoint>:19080/Explorer`

O URL completo também está disponível no painel essentials do cluster do portal do Azure.

Para ligar a um cluster seguro no Windows ou OS X através de um browser, pode importar o certificado de cliente e o navegador solicitará que para o certificado a utilizar para ligar ao cluster.  Nas máquinas do Linux, o certificado terá de ser importados com as definições do browser avançado (cada browser tem mecanismos diferentes) e aponte-o para a localização do certificado no disco. Leia [configurar um certificado de cliente](#connectsecureclustersetupclientcert) para obter mais informações.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Ligar a um cluster seguro com o Azure Active Directory

Para ligar a um cluster que está protegido com o AAD, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticamente ser-lhe pedido para iniciar sessão com o AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Ligar a um cluster seguro a utilizar um certificado de cliente

Para ligar a um cluster que está protegido com certificados, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Automaticamente ser-lhe pedido para selecionar um certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto

Pelo menos dois certificados devem ser usados para proteger o cluster, um para o certificado de cluster e o servidor e outro para acesso de cliente.  Recomendamos que utilize também certificados secundários adicionais e certificados de acesso de cliente.  Para proteger a comunicação entre um cliente e um nó de cluster através da segurança do certificado, tem primeiro de obter e instalar o certificado de cliente. O certificado pode ser instalado no arquivo pessoal do (meu) do computador local ou o utilizador atual.  Também é necessário o thumbprint do certificado de servidor para que o cliente pode autenticar o cluster.

* No Windows: Faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado no seu arquivo pessoal, `Certificates - Current User\Personal\Certificates`. Em alternativa, pode utilizar o comando do PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Se for um certificado autoassinado, terá de importá-lo ao arquivo de "pessoas fidedignas" da sua máquina antes de poder utilizar este certificado para ligar a um cluster seguro.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* No Mac: Faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado na Keychain.

## <a name="next-steps"></a>Passos Seguintes

* [Processo de atualização do Cluster do Service Fabric e as expectativas da sua](service-fabric-cluster-upgrade.md)
* [Gestão das suas aplicações do Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introdução do modelo de estado de funcionamento do Service Fabric](service-fabric-health-introduction.md)
* [Segurança de aplicativos e RunAs](service-fabric-application-runas-security.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
