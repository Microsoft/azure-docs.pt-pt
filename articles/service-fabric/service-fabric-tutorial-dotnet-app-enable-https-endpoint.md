---
title: Adicione um ponto final HTTPS usando Kestrel
description: Neste tutorial, vai aprender a adicionar um ponto final de HTTPS a um serviço Web de front-end de ASP.NET Core com o Kestrel e implementar a aplicação num cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: c675f8ece8369bcfc0055343221ac82aea59dec1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326240"
---
# <a name="tutorial-add-an-https-endpoint-to-an-aspnet-core-web-api-front-end-service-using-kestrel"></a>Tutorial: Adicionar um ponto final de HTTPS a um serviço de front-end de API Web ASP.NET Core com o Kestrel

Este tutorial é a terceira parte de uma série.  Vai aprender a ativar o HTTPS num serviço ASP.NET Core em execução no Service Fabric. Quando tiver terminado, terá uma aplicação de voto com um front-end Web ASP.NET Core com HTTPS ativado a escutar na porta 443. Se não quiser criar a aplicação de voto manualmente em [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md), pode [transferir o código de origem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) da aplicação concluída.

Na terceira parte da série, ficará a saber como:

> [!div class="checklist"]
> * Definir um ponto final de HTTPS no serviço
> * Configurar o Kestrel para utilizar HTTPS
> * Instale o certificado TLS/SSL nos nós de cluster remoto
> * Dar ao SERVIÇO DE REDE acesso à chave privada do certificado
> * Abrir a porta 443 no Balanceador de Carga do Azure
> * Implementar a aplicação num cluster remoto

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação .NET do Service Fabric](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Implementar a aplicação num cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Adicionar um ponto final HTTPS a um serviço de front-end ASP.NET Core
> * [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-monitoring-aspnet.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale a versão 16.5 do [Visual Studio 2019](https://www.visualstudio.com/) ou posteriormente com o **desenvolvimento do Azure** e ASP.NET e trabalhos **de desenvolvimento web.**
* [Instalar o Service Fabric SDK](service-fabric-get-started.md)

## <a name="obtain-a-certificate-or-create-a-self-signed-development-certificate"></a>Obter um certificado ou criar um certificado de desenvolvimento autoassinado

Para aplicações de produção, utilize um certificado de uma [autoridade de certificação (AC)](https://wikipedia.org/wiki/Certificate_authority). Para fins de desenvolvimento e teste, pode criar e utilizar um certificado autoassinado. O SDK do Service Fabric disponibiliza o script *CertSetup.ps1*, que cria um certificado autoassinado e o importa para o arquivo de certificados `Cert:\LocalMachine\My`. Abra um pedido de comando como administrador e executar o seguinte comando para criar um certificado com o sujeito "CN=mytestcert":

```powershell
PS C:\program files\microsoft sdks\service fabric\clustersetup\secure> .\CertSetup.ps1 -Install -CertSubjectName CN=mytestcert
```

Se já tiver um ficheiro PFX de certificado, execute o seguinte para importá-lo para o arquivo de certificados `Cert:\LocalMachine\My`:

```powershell

PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysslcertificate.pfx -CertStoreLocation Cert:\LocalMachine\My -Password (ConvertTo-SecureString "!Passw0rd321" -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

## <a name="define-an-https-endpoint-in-the-service-manifest"></a>Definir um ponto final de HTTPS no manifesto de serviço

Inicie o Visual Studio como **administrador** e abra a solução Voting. No Solution Explorer, abra *o VotingWeb/PackageRoot/ServiceManifest.xml*. O manifesto do serviço declara os pontos finais do serviço.  Localize a secção **Pontos finais** e edite o ponto final "ServiceEndpoint" existente.  Mude o nome para "EndpointHttps", defina o protocolo como *https*, o tipo como *Input* e a porta como a *443*.  Guarde as alterações.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para utilizar HTTPS

No Explorador de Soluções, abra o ficheiro *VotingWeb/VotingWeb.cs*.  Configure o Kestrel para utilizar HTTPS e procure o certificado no arquivo `Cert:\LocalMachine\My`. Utilize instruções para adicionar o seguinte:

```csharp
using System.Net;
using Microsoft.Extensions.Configuration;
using System.Security.Cryptography.X509Certificates;
```

Atualize `ServiceInstanceListener` para utilizar o ponto final *EndpointHttps* novo e escutar na porta 443. Quando configurar o anfitrião da Web para utilizar o servidor Kestrel, tem de configurar o Kestrel para escutar os endereços IPv6 em todas as interfaces de rede: `opt.Listen(IPAddress.IPv6Any, port, listenOptions => {...}`.

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(FindMatchingCertificateBySubject());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Adicione também o seguinte método para que esse Kestrel possa encontrar o certificado no arquivo `Cert:\LocalMachine\My` pelo requerente.  

Substitua " &lt; your_CN_value &gt; " por "mytestcert" se tiver criado um certificado auto-assinado com o comando PowerShell anterior, ou utilizar o CN do seu certificado.
Esteja ciente de que no caso de implantação local `localhost` é preferível usar "CN=localhost" para evitar exceções de autenticação.

```csharp
private X509Certificate2 FindMatchingCertificateBySubject(string subjectCommonName)
{
    using (var store = new X509Store(StoreName.My, StoreLocation.LocalMachine))
    {
        store.Open(OpenFlags.OpenExistingOnly | OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var matchingCerts = new X509Certificate2Collection();
    
    foreach (var enumeratedCert in certCollection)
    {
      if (StringComparer.OrdinalIgnoreCase.Equals(subjectCommonName, enumeratedCert.GetNameInfo(X509NameType.SimpleName, forIssuer: false))
        && DateTime.Now < enumeratedCert.NotAfter
        && DateTime.Now >= enumeratedCert.NotBefore)
        {
          matchingCerts.Add(enumeratedCert);
        }
    }

        if (matchingCerts.Count == 0)
    {
        throw new Exception($"Could not find a match for a certificate with subject 'CN={subjectCommonName}'.");
    }
        
        return matchingCerts[0];
    }
}


```

## <a name="grant-network-service-access-to-the-certificates-private-key"></a>Grant NETWORK ACESSO à chave privada do certificado

Num passo anterior, importou o certificado para o arquivo `Cert:\LocalMachine\My` no computador de desenvolvimento.  Agora, dê explicitamente à conta que gere o serviço (NETWORK SERVICE, por padrão) acesso à chave privada do certificado. Pode fazer este passo manualmente (utilizando a ferramenta certlm.msc), mas é melhor executar automaticamente um script PowerShell [configurando um script de arranque](service-fabric-run-script-at-service-startup.md) no **SetupEntryPoint** do manifesto de serviço.

>[!NOTE]
> O Service Fabric suporta a declaração de certificados de ponto final por impressão digital ou nome comum do sujeito. Nesse caso, o tempo de execução criará a ligação e a ACL a chave privada do certificado para a identidade que o serviço está funcionando como. O tempo de execução também monitorizará o certificado para alterações/renovações, e re-ACL a chave privada correspondente em conformidade.

### <a name="configure-the-service-setup-entry-point"></a>Configurar o ponto de entrada da configuração do serviço

No Solution Explorer, abra *o VotingWeb/PackageRoot/ServiceManifest.xml*.  Na secção **CodePackage**, adicione o nó **SetupEntryPoint** node e, em seguida, o nó **ExeHost**.  Em **ExeHost**, defina **Program** como "Setup.bat" e **WorkingFolder** como "CodePackage".  Quando inicia o serviço VotingWeb, o script Setup.bat é executado na pasta CodePackage antes de VotingWeb.exe ser iniciado.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>

    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Protocol="https" Name="EndpointHttps" Type="Input" Port="443" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="add-the-batch-and-powershell-setup-scripts"></a>Adicionar os scripts de batch e do PowerShell

Para executar o PowerShell no ponto **SetupEntryPoint**, pode executar PowerShell.exe num ficheiro de batch que aponte para um ficheiro do PowerShell. Primeiro, adicione o ficheiro de batch ao projeto do serviço.  No Explorador de Soluções, clique com o botão direito do rato em **VotingWeb** e selecione **Add** (Adicionar)->**New Item** (Novo Item) e adicione um ficheiro novo com o nome "Setup.bat".  Edite o ficheiro *Setup.bat* e adicione o seguinte comando:

```cmd
powershell.exe -ExecutionPolicy Bypass -Command ".\SetCertAccess.ps1"
```

Modifique as propriedades do ficheiro *Setup.bat* para definir **Copy to Output Directory** (Copiar para Diretório de Saída) como "Copy if newer" (“Copiar se for mais recente”).

![Definir as propriedades do ficheiro][image1]

No Explorador de Soluções, clique com o botão direito do rato em **VotingWeb** e selecione **Add**->**New Item** e adicione um ficheiro novo com o nome "SetCertAccess.ps1".  Edite o ficheiro *SetCertAccess.ps1* e adicione o seguinte script:

```powershell
$subject="mytestcert"
$userGroup="NETWORK SERVICE"

Write-Host "Checking permissions to certificate $subject.." -ForegroundColor DarkCyan

$cert = (gci Cert:\LocalMachine\My\ | where { $_.Subject.Contains($subject) })[-1]

if ($cert -eq $null)
{
    $message="Certificate with subject:"+$subject+" does not exist at Cert:\LocalMachine\My\"
    Write-Host $message -ForegroundColor Red
    exit 1;
}elseif($cert.HasPrivateKey -eq $false){
    $message="Certificate with subject:"+$subject+" does not have a private key"
    Write-Host $message -ForegroundColor Red
    exit 1;
}else
{
    $keyName=$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

    $keyPath = "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys\"
    $fullPath=$keyPath+$keyName
    $acl=(Get-Item $fullPath).GetAccessControl('Access')


    $hasPermissionsAlready = ($acl.Access | where {$_.IdentityReference.Value.Contains($userGroup.ToUpperInvariant()) -and $_.FileSystemRights -eq [System.Security.AccessControl.FileSystemRights]::FullControl}).Count -eq 1

    if ($hasPermissionsAlready){
        Write-Host "Account $userGroup already has permissions to certificate '$subject'." -ForegroundColor Green
        return $false;
    } else {
        Write-Host "Need add permissions to '$subject' certificate..." -ForegroundColor DarkYellow

        $permission=$userGroup,"Full","Allow"
        $accessRule=new-object System.Security.AccessControl.FileSystemAccessRule $permission
        $acl.AddAccessRule($accessRule)
        Set-Acl $fullPath $acl

        Write-Output "Permissions were added"

        return $true;
    }
}

```

Modifique as propriedades do ficheiro *SetCertAccess.ps1* para definir **Copiar para Diretório de Saída** como “Copiar se for mais recente”.

### <a name="run-the-setup-script-as-a-local-administrator"></a>Executar o script de configuração como administrador local

Por predefinição, o executável do ponto de entrada da configuração do serviço é executado com as mesmas credenciais do Service Fabric (normalmente, a conta NetworkService). *SetCertAccess.ps1* requer privilégios de administrador. No manifesto da aplicação, pode alterar as permissões de segurança para executar o script de arranque com uma conta de administrador local.

No Explorador de Soluções, abra *Voting/ApplicationPackageRoot/ApplicationManifest.xml*. Primeiro, crie uma secção **Principals** e adicione um utilizador novo (por exemplo, "SetupAdminUser"). Adicione a conta de utilizador SetupAdminUser ao grupo Administradores do sistema.
Depois, na secção VotingWebPkg **ServiceManifestImport**, configure uma política **RunAsPolicy** para aplicar o principal SetupAdminUser ao ponto de entrada da configuração. Esta política indica ao Service Fabric que o ficheiro Setup.bat é executado como SetupAdminUser (com privilégios de administrador).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

No Solution Explorer, selecione a aplicação **de voto** e desatrei a propriedade URL **de aplicação** para "https: \/ /localhost:443".

Guarde todos os ficheiros e prima F5 para executar a aplicação localmente.  Após a implementação da aplicação, um navegador web abre para https: \/ /localhost:443. Se estiver a utilizar um certificado autoassinado, verá um aviso se o PC não confiar na segurança deste site.  Avance para a página Web.

![Screenshot da aplicação Service Fabric Voting Sample que funciona numa janela do navegador com o URL https://localhost/ .][image2]

## <a name="install-certificate-on-cluster-nodes"></a>Instalar o certificado nos nós de cluster remoto

Antes de colocar a aplicação no Azure, instale o certificado na `Cert:\LocalMachine\My` loja de todos os nós de cluster remoto.  Os serviços podem mover-se para diferentes nós do cluster.  Quando o serviço Web de front-end é iniciado num nó de cluster, o script de arranque irá procurar o certificado e configurar as permissões de acesso.

Em primeiro lugar, exporte o certificado para um ficheiro PFX. Abra a aplicação certlm.msc e navegue para **Personal** > **Certificados Pessoais**.  Clique com o botão direito no certificado *de certificação e* selecione **All Tasks** > **Export**.

![Exportar o certificado][image4]

No assistente de exportação, escolha **Yes, export the private key** (Sim, exportar a chave privada) e escolha o formato Personal Information Exchange (PFX).  Exporte o ficheiro para *C:\Users\sfuser\votingappcert.pfx*.

Em seguida, instale o certificado no cluster remoto utilizando [estes scripts powershell fornecidos](./scripts/service-fabric-powershell-add-application-certificate.md).

> [!Warning]
> Para as aplicações de desenvolvimento e teste, basta um certificado autoassinado. Para aplicações de produção, utilize um certificado de uma [autoridade de certificação (AC)](https://wikipedia.org/wiki/Certificate_authority) em vez de um autoassinado.

## <a name="open-port-443-in-the-azure-load-balancer-and-virtual-network"></a>Porta aberta 443 no equilibrador de carga Azure e rede virtual

Abra a porta 443 no balanceador de carga, se ainda não estiver aberta.

```powershell
$probename = "AppPortProbe6"
$rulename="AppPortLBRule6"
$RGname="voting_RG"
$port=443

# Get the load balancer resource
$resource = Get-AzResource | Where {$_.ResourceGroupName –eq $RGname -and $_.ResourceType -eq "Microsoft.Network/loadBalancers"}
$slb = Get-AzLoadBalancer -Name $resource.Name -ResourceGroupName $RGname

# Add a new probe configuration to the load balancer
$slb | Add-AzLoadBalancerProbeConfig -Name $probename -Protocol Tcp -Port $port -IntervalInSeconds 15 -ProbeCount 2

# Add rule configuration to the load balancer
$probe = Get-AzLoadBalancerProbeConfig -Name $probename -LoadBalancer $slb
$slb | Add-AzLoadBalancerRuleConfig -Name $rulename -BackendAddressPool $slb.BackendAddressPools[0] -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -Probe $probe -Protocol Tcp -FrontendPort $port -BackendPort $port

# Set the goal state for the load balancer
$slb | Set-AzLoadBalancer
```

Faça o mesmo para a rede virtual associada.

```powershell
$rulename="allowAppPort$port"
$nsgname="voting-vnet-security"
$RGname="voting_RG"
$port=443

# Get the NSG resource
$nsg = Get-AzNetworkSecurityGroup -Name $nsgname -ResourceGroupName $RGname

# Add the inbound security rule.
$nsg | Add-AzNetworkSecurityRuleConfig -Name $rulename -Description "Allow app port" -Access Allow `
    -Protocol * -Direction Inbound -Priority 3891 -SourceAddressPrefix "*" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange $port

# Update the NSG.
$nsg | Set-AzNetworkSecurityGroup
```

## <a name="deploy-the-application-to-azure"></a>Implementar a aplicação no Azure

Guarde todos os ficheiros, mude de Debug para Release e prima F6 para recompilar.  No Explorador de Soluções, clique com o botão direito do rato em **Voting** e selecione **Publish** (Publicar). Selecione o ponto final da ligação do cluster criado em [Implementar uma aplicação num cluster](service-fabric-tutorial-deploy-app-to-party-cluster.md) ou selecione outro cluster.  Clique em **Publish** para publicar a aplicação no cluster remoto.

Quando a aplicação estiver implementada, abra um browser e navegue para `https://mycluster.region.cloudapp.azure.com:443` (atualize o URL com o ponto final da ligação do cluster). Se estiver a utilizar um certificado autoassinado, verá um aviso se o PC não confiar na segurança deste site.  Avance para a página Web.

![Screenshot da aplicação Service Fabric Voting Sample que funciona numa janela do navegador com o URL https://mycluster.region.cloudapp.azure.com:443 .][image3]

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Definir um ponto final de HTTPS no serviço
> * Configurar o Kestrel para utilizar HTTPS
> * Instale o certificado TLS/SSL nos nós de cluster remoto
> * Dar ao SERVIÇO DE REDE acesso à chave privada do certificado
> * Abrir a porta 443 no Balanceador de Carga do Azure
> * Implementar a aplicação num cluster remoto

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Configurar CI/CD com o Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

[image1]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/SetupBatProperties.png
[image2]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppLocal.png
[image3]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/VotingAppAzure.png
[image4]: ./media/service-fabric-tutorial-dotnet-app-enable-https-endpoint/ExportCert.png
