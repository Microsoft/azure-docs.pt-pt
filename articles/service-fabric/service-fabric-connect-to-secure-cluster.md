---
title: Conectar-se com segurança a um cluster de Service Fabric do Azure
description: Descreve como autenticar o acesso de cliente a um Cluster Service Fabric e como proteger a comunicação entre clientes e um cluster.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 89d9f67ba1a202b3830df7a5b960c6ef01091bf2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458267"
---
# <a name="connect-to-a-secure-cluster"></a>Ligar a um cluster seguro

Quando um cliente se conecta a um nó de Cluster Service Fabric, o cliente pode ser autenticado e proteger a comunicação estabelecida usando o AAD (segurança de certificado ou Azure Active Directory). Essa autenticação garante que somente usuários autorizados possam acessar o cluster e aplicativos implantados e executar tarefas de gerenciamento.  O certificado ou a segurança do AAD deve ter sido habilitada anteriormente no cluster quando o cluster foi criado.  Para obter mais informações sobre cenários de segurança de cluster, consulte [segurança de cluster](service-fabric-cluster-security.md). Se você estiver se conectando a um cluster protegido com certificados, [Configure o certificado do cliente](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) no computador que se conecta ao cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Conectar-se a um cluster seguro usando a CLI do Azure Service Fabric (sfctl)

Há algumas maneiras diferentes de se conectar a um cluster seguro usando a CLI do Service Fabric (sfctl). Quando utilizar um certificado de cliente para autenticação, os detalhes do certificado têm de coincidir com um certificado implementado nos nós do cluster. Se o certificado tiver autoridades de certificação (CAs), você precisará especificar também as CAs confiáveis.

Você pode se conectar a um cluster usando o comando `sfctl cluster select`.

Certificados de cliente podem ser especificados de duas maneiras diferentes, como um certificado e um par de chaves, ou como um único arquivo PFX. Para arquivos PEM protegidos por senha, você será solicitado a inserir a senha automaticamente. Se você tiver obtido o certificado do cliente como um arquivo PFX, primeiro converta o arquivo PFX em um arquivo PEM usando o comando a seguir. 

```bash
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Se seu arquivo. pfx não for protegido por senha, use-passe Pass: para o último parâmetro.

Para especificar o certificado do cliente como um arquivo PEM, especifique o caminho do arquivo no argumento `--pem`. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Os arquivos PEM protegidos por senha solicitarão a senha antes de executar qualquer comando.

Para especificar um certificado, o par de chaves usa os argumentos `--cert` e `--key` para especificar os caminhos de arquivo para cada arquivo respectivo.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Às vezes, os certificados usados para proteger os clusters de teste ou desenvolvimento falham na validação do certificado. Para ignorar a verificação de certificado, especifique a opção de `--no-verify`. Por exemplo:

> [!WARNING]
> Não use a opção `no-verify` ao se conectar a clusters de Service Fabric de produção.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Além disso, você pode especificar caminhos para diretórios de certificados de autoridade de certificação confiáveis ou certificados individuais. Para especificar esses caminhos, use o argumento `--ca`. Por exemplo:

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Depois de se conectar, você poderá [executar outros comandos do sfctl](service-fabric-cli.md) para interagir com o cluster.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Conectar-se a um cluster usando o PowerShell
Antes de executar operações em um cluster por meio do PowerShell, primeiro estabeleça uma conexão com o cluster. A conexão de cluster é usada para todos os comandos subsequentes na sessão do PowerShell especificada.

### <a name="connect-to-an-unsecure-cluster"></a>Conectar-se a um cluster não seguro

Para se conectar a um cluster não seguro, forneça o endereço do ponto de extremidade do cluster para o comando **Connect-ServiceFabricCluster** :

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conectar-se a um cluster seguro usando o Azure Active Directory

Para se conectar a um cluster seguro que usa Azure Active Directory para autorizar o acesso de administrador de cluster, forneça a impressão digital do certificado de cluster e use o sinalizador *AzureActiveDirectory* .  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente
Execute o seguinte comando do PowerShell para se conectar a um cluster seguro que usa certificados de cliente para autorizar o acesso de administrador. 

#### <a name="connect-using-certificate-common-name"></a>Conectar usando o nome comum do certificado
Forneça o nome comum do certificado de cluster e o nome comum do certificado de cliente que recebeu permissões para o gerenciamento de cluster. Os detalhes do certificado devem corresponder a um certificado nos nós do cluster.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* é o nome comum do certificado do servidor instalado nos nós do cluster. *FindValue* é o nome comum do certificado do cliente de administrador. Quando os parâmetros são preenchidos, o comando é semelhante ao exemplo a seguir:
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

#### <a name="connect-using-certificate-thumbprint"></a>Conectar usando a impressão digital do certificado
Forneça a impressão digital do certificado do cluster e a impressão digital do certificado do cliente que recebeu permissões para o gerenciamento de cluster. Os detalhes do certificado devem corresponder a um certificado nos nós do cluster.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* é a impressão digital do certificado do servidor instalado nos nós do cluster. *FindValue* é a impressão digital do certificado do cliente de administrador.  Quando os parâmetros são preenchidos, o comando é semelhante ao exemplo a seguir:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Conectar-se a um cluster seguro usando o Windows Active Directory
Se o cluster autônomo for implantado usando a segurança do AD, conecte-se ao cluster acrescentando o comutador "WindowsCredential".

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Conectar-se a um cluster usando as APIs do FabricClient
O SDK do Service Fabric fornece a classe [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) para o gerenciamento de cluster. Para usar as APIs do FabricClient, obtenha o pacote NuGet do Microsoft. Fabric.

### <a name="connect-to-an-unsecure-cluster"></a>Conectar-se a um cluster não seguro

Para se conectar a um cluster remoto não seguro, crie uma instância do FabricClient e forneça o endereço do cluster:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Para o código que está sendo executado de dentro de um cluster, por exemplo, em um serviço confiável, crie um FabricClient *sem* especificar o endereço do cluster. FabricClient conecta-se ao gateway de gerenciamento local no nó em que o código está sendo executado, evitando um salto extra de rede.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente

Os nós no cluster devem ter certificados válidos cujo nome comum ou nome DNS em SAN apareçam na [Propriedade RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) definida em [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Seguir esse processo habilita a autenticação mútua entre o cliente e os nós de cluster.

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

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Conectar-se a um cluster seguro interativamente usando o Azure Active Directory

O exemplo a seguir usa Azure Active Directory para identidade do cliente e certificado do servidor para a identidade do servidor.

Uma janela de caixa de diálogo automaticamente é exibida para entrada interativa quando se conecta ao cluster.

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

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Conectar-se a um cluster seguro de forma não interativa usando Azure Active Directory

O exemplo a seguir se baseia em Microsoft. IdentityModel. clients. ActiveDirectory, Version: 2.19.208020213.

Para obter mais informações sobre a aquisição de token do AAD, consulte [Microsoft. IdentityModel. clients. ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx).

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

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Conectar-se a um cluster seguro sem conhecimento de metadados anterior usando Azure Active Directory

O exemplo a seguir usa a aquisição de token não interativa, mas a mesma abordagem pode ser usada para criar uma experiência de aquisição de token interativo personalizada. Os metadados de Azure Active Directory necessários para a aquisição de token são lidos na configuração do cluster.

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

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Conectar-se a um cluster seguro usando o Service Fabric Explorer
Para alcançar [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) para um determinado cluster, aponte seu navegador para:

`http://<your-cluster-endpoint>:19080/Explorer`

A URL completa também está disponível no painel do Essentials do cluster do portal do Azure.

Para se conectar a um cluster seguro no Windows ou no OS X usando um navegador, você pode importar o certificado do cliente e o navegador solicitará que o certificado seja usado para conexão com o cluster.  Em computadores Linux, o certificado terá de ser importado usando configurações avançadas do navegador (cada navegador tem mecanismos diferentes) e o apontará para o local do certificado no disco. Leia [configurar um certificado do cliente](#connectsecureclustersetupclientcert) para obter mais informações.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Conectar-se a um cluster seguro usando o Azure Active Directory

Para se conectar a um cluster protegido com o AAD, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Você será automaticamente solicitado a entrar com o AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Conectar-se a um cluster seguro usando um certificado de cliente

Para se conectar a um cluster protegido com certificados, aponte seu navegador para:

`https://<your-cluster-endpoint>:19080/Explorer`

Você será automaticamente solicitado a selecionar um certificado de cliente.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Configurar um certificado de cliente no computador remoto

Pelo menos dois certificados devem ser usados para proteger o cluster, um para o cluster e o certificado do servidor e outro para acesso de cliente.  Recomendamos que você também use certificados secundários adicionais e certificados de acesso para cliente.  Para proteger a comunicação entre um cliente e um nó de cluster usando a segurança do certificado, primeiro você precisa obter e instalar o certificado do cliente. O certificado pode ser instalado no repositório pessoal (meu) do computador local ou no usuário atual.  Você também precisa da impressão digital do certificado do servidor para que o cliente possa autenticar o cluster.

* No Windows: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado no seu arquivo pessoal, `Certificates - Current User\Personal\Certificates`. Como alternativa, você pode usar o comando do PowerShell:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Se for um certificado autoassinado, você precisará importá-lo para o repositório "pessoas confiáveis" do seu computador antes de poder usar esse certificado para se conectar a um cluster seguro.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* No Mac: faça duplo clique no ficheiro PFX e siga as instruções para instalar o certificado na sua Keychain.

## <a name="next-steps"></a>Passos seguintes

* [Service Fabric processo de atualização de cluster e as expectativas de você](service-fabric-cluster-upgrade.md)
* [Gerenciando seus aplicativos Service Fabric no Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introdução ao modelo de integridade Service Fabric](service-fabric-health-introduction.md)
* [Segurança do aplicativo e RunAs](service-fabric-application-runas-security.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)
