---
title: Criar uma aplicação de contentor do Azure Service Fabric | Microsoft Docs
description: Crie a sua primeira aplicação de contentor do Windows no Azure Service Fabric. Crie uma imagem do Docker com a sua aplicação Python, envie-a para um registo de contentor e crie e implemente uma aplicação de contentor do Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/25/2019
ms.author: aljo
ms.openlocfilehash: 4133379ff7c1c0a64bd2d9aefdafdd5cdb530491
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57875073"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>Criar a sua primeira aplicação de contentor do Service Fabric no Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Para executar uma aplicação existente num contentor do Windows num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este artigo explica como criar uma imagem do Docker que contém um Python [Flask](http://flask.pocoo.org/) web application e implantá-lo para um cluster do Service Fabric em execução no seu computador local. Também vai partilhar a sua aplicação contentorizada através do [Azure Container Registry](/azure/container-registry/). Este artigo pressupõe uma compreensão básica do Docker. Para saber mais sobre o Docker, leia a [Descrição Geral do Docker](https://docs.docker.com/engine/understanding-docker/).

> [!NOTE]
> Este artigo aplica-se para um ambiente de desenvolvimento do Windows.  O tempo de execução do cluster do Service Fabric e o tempo de execução do Docker tem de ser executado no mesmo SO.  Não é possível executar contentores do Windows num cluster do Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um computador de programação com:
  * Visual Studio 2015 ou Visual Studio 2017.
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started.md).
  *  Docker para Windows. [Obtenha o Docker CE para Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique com o botão direito do rato no ícone de tabuleiro e selecione **Mudar para os contentores do Windows**. Este passo é necessário para executar imagens do Docker baseadas no Windows.

* Um cluster do Windows com três ou mais nós em execução no Windows Server com contentores. 

  Neste artigo, a versão (compilação) do Windows Server com contentores em execução em nós do cluster tem de corresponder no computador de desenvolvimento. Isto acontece porque criar a imagem do docker no seu computador de desenvolvimento e existem restrições de compatibilidade entre versões do contentor do sistema operacional e o SO do host no qual está implementada. Para obter mais informações, consulte [contentor do Windows Server SO e o host de compatibilidade do SO](#windows-server-container-os-and-host-os-compatibility). 
  
Para determinar a versão do Windows Server com contentores, precisa para o seu cluster, execute o `ver` comando num prompt de comando do Windows no computador de desenvolvimento:

* Se a versão contém *x.x.14323.x*, em seguida, selecione *WindowsServer 2016 Datacenter com contentores* para o sistema operativo quando [criação de um cluster](service-fabric-cluster-creation-via-portal.md).
  * Se a versão contém *x.x.16299.x*, em seguida, selecione *WindowsServerSemiAnnual Datacenter-Core-1709-com-contentores* para o sistema operativo quando [criar um cluster](service-fabric-cluster-creation-via-portal.md).

* Um registo no Azure Container Registry - [Criar um registo de contentor](../container-registry/container-registry-get-started-portal.md) na sua subscrição do Azure.

> [!NOTE]
> A implementação de contentores para um cluster do Service Fabric em execução no Windows 10 é suportada.  Ver [este artigo](service-fabric-how-to-debug-windows-containers.md) para obter informações sobre como configurar o Windows 10 para executar contentores do Windows.
>   

> [!NOTE]
> Versões de Service Fabric 6.2 e versões posteriores dão suporte a implementação de contentores para clusters em execução no Windows Server versão 1709.  
> 

## <a name="define-the-docker-container"></a>Definir o contentor do Docker

Crie uma imagem com base na [imagem de Python](https://hub.docker.com/_/python/) que se encontra no Docker Hub.

Especifique o contentor do Docker num Dockerfile. O Dockerfile consiste em instruções para configurar o ambiente no interior do contentor, para carregar a aplicação que pretende executar e para mapear as portas. O Dockerfile é a entrada para o comando `docker build`, o que cria a imagem.

Crie um diretório vazio e crie o ficheiro *Dockerfile* (sem extensão de ficheiro). Adicione o seguinte ao *Dockerfile* e guarde as alterações:

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para obter mais informações.

## <a name="create-a-basic-web-application"></a>Criar uma aplicação Web básica
Crie uma aplicação web Flask que escute na porta 80 e que devolva `Hello World!`. No mesmo diretório, crie o ficheiro *requirements.txt*. Adicione o seguinte e guarde as alterações:
```
Flask
```

Crie também o ficheiro *app.py* e adicione o seguinte fragmento de código:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello():

    return 'Hello World!'

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>Criar a imagem
Execute o comando `docker build` para criar a imagem que executa a sua aplicação Web. Abra uma janela do PowerShell e navegue para o diretório que contém o Dockerfile. Execute o seguinte comando:

```
docker build -t helloworldapp .
```

Este comando cria a nova imagem através das instruções fornecidas no seu Dockerfile e dá à imagem o nome (identificação -t) `helloworldapp`. Para criar uma imagem de contentor, a imagem de base começa por ser transferida do Hub do Docker ao qual aplicação está adicionada. 

Quando o comando de criação concluir, execute o comando `docker images` para ver informações sobre a nova imagem:

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente
Verifique a sua imagem localmente antes de a enviar para o registo de contentor. 

Execute a aplicação:

```
docker run -d --name my-web-site helloworldapp
```

*nome* dá um nome ao contentor em execução (em vez do ID do contentor).

Assim que o contentor for iniciado, localize o respetivo endereço IP para que possa ligar-se ao seu contentor em execução a partir de um browser:
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

Se esse comando não devolver nada, execute o seguinte comando e inspecione o elemento **NetworkSettings**->**Networks** para o endereço IP:
```
docker inspect my-web-site
```

Ligue-se ao contentor em execução. Abra um browser que aponte para o endereço IP devolvido, como, por exemplo, "<http://172.31.194.61>". Deverá ver o cabeçalho "Hello World!" apresentado no browser.

Para parar o contentor, execute:

```
docker stop my-web-site
```

Elimine o contentor do seu computador de programação:

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registo de contentor

Depois de confirmar que o contentor é executado no seu computador de programação, envie a imagem para o seu registo no Azure Container Registry.

Execute ``docker login`` para iniciar sessão no registo de contentores com as suas [credenciais do registo](../container-registry/container-registry-authentication.md).

O exemplo seguinte transmite o ID e a palavra-passe de um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) do Azure Active Directory. Por exemplo, poderá ter atribuído um principal de serviço ao seu registo no âmbito de um cenário de automatização. Em alternativa, pode iniciar sessão com o nome de utilizador e a palavra-passe do registo.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

O comando seguinte cria uma etiqueta, ou alias, da imagem, com um caminho completamente qualificado para o seu registo. Este exemplo coloca a imagem no espaço de nomes ```samples``` para evitar sobrepovoar a raiz do registo.

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Envie a imagem para o seu registo de contentor:

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>Criar o serviço do contentor no Visual Studio
O SDK e as ferramentas do Service Fabric fornecem um modelo de serviço para o ajudar a criar uma aplicação de contentor.

1. Inicie o Visual Studio. Selecione **Ficheiro** > **Novo** > **Projeto**.
2. Selecione **Aplicação do Service Fabric**, dê-lhe o nome "MyFirstContainer" e clique em **OK**.
3. Selecione **Contentor** na lista de **modelos de serviço**.
4. Em **Nome da Imagem**, introduza "myregistry.azurecr.io/samples/helloworldapp", a imagem que enviou para o repositório do seu contentor.
5. Dê um nome ao serviço e clique em **OK**.

## <a name="configure-communication"></a>Configurar a comunicação
O serviço de contentor precisa de um ponto final para comunicação. Adicione um elemento `Endpoint` com o protocolo, a porta e o tipo ao ficheiro ServiceManifest.xml. Neste exemplo, é utilizada uma porta fixa 8081. Se não especificar uma porta, é escolhida uma porta aleatória do intervalo de portas da aplicação. 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> Pontos finais adicionais para um serviço podem ser adicionados ao declarar os elementos de ponto final adicionais com valores de propriedade aplicável. Cada porta só pode declarar um valor de protocolo.

Ao definir um ponto final, o Service Fabric publica o ponto final no serviço de Nomes. Este contentor pode ser resolvido por outros serviços em execução no cluster. Também pode realizar comunicação de contentor para contentor através do [proxy inverso](service-fabric-reverseproxy.md). Para realizar a comunicação, forneça a porta de escuta HTTP do proxy inverso e o nome dos serviços com os quais quer comunicar como variáveis de ambiente.

O serviço está a escutar uma porta específica (8081 neste exemplo). Quando implementa a aplicação num cluster do Azure, tanto o cluster como a aplicação são executados atrás de um balanceador de carga do Azure. A porta da aplicação tem de ser aberta no balanceador de carga do Azure para que o tráfego de entrada possa chegar ao serviço.  Pode abrir esta porta no balanceador de carga do Azure com um [script do PowerShell](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) ou no [portal do Azure](https://portal.azure.com).

## <a name="configure-and-set-environment-variables"></a>Configurar e definir variáveis de ambiente
Pode especificar as variáveis de ambiente para cada pacote do código no manifesto do serviço. Esta funcionalidade está disponível para todos os serviços, independentemente de estarem implementados como contentores ou processos ou como executáveis convidados. Pode substituir os valores das variáveis de ambiente no manifesto de aplicação ou especificá-los durante a implementação como parâmetros de aplicação.

O seguinte fragmento XML do manifesto do serviço mostra um exemplo de como especificar variáveis de ambiente para um pacote do código:
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

Estas variáveis de ambiente podem ser substituídas no manifesto de aplicação:

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>Configurar o mapeamento de portas porta-a-anfitrião e deteção de contentor para contentor
Configure uma porta de anfitrião utilizada para comunicar com o contentor. O enlace da porta mapeia a porta em que o serviço está a escutar no interior do contentor para uma porta no anfitrião. Adicione um elemento `PortBinding` no elemento `ContainerHostPolicies` do ficheiro ApplicationManifest.xml. Neste artigo, `ContainerPort` é 80 (o contentor expõe a porta 80, conforme especificado no Dockerfile) e `EndpointRef` é "Guest1TypeEndpoint" (o ponto final definido anteriormente no manifesto do serviço). Os pedidos de entrada para o serviço na porta 8081 são mapeados para a porta 80 no contentor.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> PortBindings adicionais para um serviço podem ser adicionados ao declarar elementos PortBinding adicionais com valores de propriedade aplicável.

## <a name="configure-container-registry-authentication"></a>Configurar a autenticação do registo de contentor

Configure a autenticação do registo de contentor ao adicionar `RepositoryCredentials` a `ContainerHostPolicies` do ficheiro ApplicationManifest.xml. Adicione a conta e a palavra-passe do registo de contentor myregistry.azurecr.io, o que permite ao serviço transferir a imagem de contentor a partir do repositório.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Recomendamos que encripte a palavra-passe do repositório através de um certificado de encriptação que é implementado em todos os nós do cluster. Quando o Service Fabric implementa o pacote de serviço no cluster, o certificado de encriptação é utilizado para desencriptar o texto de cifra. O cmdlet Invoke-ServiceFabricEncryptText é utilizado para criar o texto de cifra para a palavra-passe, que é adicionado ao ficheiro ApplicationManifest.xml.

O script seguinte cria um novo certificado autoassinado e exporta-a para um ficheiro PFX. O certificado é importado para um cofre de chaves existente e, em seguida, implementado no cluster do Service Fabric.

```powershell
# Variables.
$certpwd = ConvertTo-SecureString -String "Pa$$word321!" -Force -AsPlainText
$filepath = "C:\MyCertificates\dataenciphermentcert.pfx"
$subjectname = "dataencipherment"
$vaultname = "mykeyvault"
$certificateName = "dataenciphermentcert"
$groupname="myclustergroup"
$clustername = "mycluster"

$subscriptionId = "subscription ID"

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Create a self signed cert, export to PFX file.
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject $subjectname -Provider 'Microsoft Enhanced Cryptographic Provider v1.0' `
| Export-PfxCertificate -FilePath $filepath -Password $certpwd

# Import the certificate to an existing key vault. The key vault must be enabled for deployment.
$cer = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certificateName -FilePath $filepath -Password $certpwd

Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $groupname -EnabledForDeployment

# Add the certificate to all the VMs in the cluster.
Add-AzureRmServiceFabricApplicationCertificate -ResourceGroupName $groupname -Name $clustername -SecretIdentifier $cer.SecretId
```
Encripte a palavra-passe através do cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps).

```powershell
$text = "=P==/==/=8=/=+u4lyOB=+=nWzEeRfF="
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint $cer.Thumbprint -Text $text -StoreLocation Local -StoreName My
```

Substitua a palavra-passe pelo texto de cifra devolvido pelo cmdlet [Invoke-ServiceFabricEncryptText](/powershell/module/servicefabric/Invoke-ServiceFabricEncryptText?view=azureservicefabricps) e defina `PasswordEncrypted` como "verdadeiro".

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

### <a name="configure-cluster-wide-credentials"></a>Configurar as credenciais de todo o cluster

Começando com o tempo de execução 6.3, Service Fabric permite-lhe configurar as credenciais de todo o cluster que podem ser utilizadas como credenciais do padrão repositório pelos aplicativos.

Pode ativar ou desativar a funcionalidade ao adicionar o `UseDefaultRepositoryCredentials` para o atributo `ContainerHostPolicies` em applicationmanifest. XML com um `true` ou `false` valor.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

O Service Fabric, em seguida, utiliza as credenciais de repositório predefinidas que pode ser especificada em ClusterManifest sob o `Hosting` secção.  Se `UseDefaultRepositoryCredentials` é `true`, Service Fabric lê os seguintes valores do ClusterManifest:

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (string)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (cadeia)---suportada ao iniciar com o tempo de execução 6.4

Eis um exemplo de como pode adicionar dentro do `Hosting` seção no arquivo ClusterManifestTemplate.json. Para obter mais informações, consulte [definições do cluster de alteração do Azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [segredos da aplicação de gerir o Azure Service Fabric](service-fabric-application-secret-management.md)

```json
      {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
```

## <a name="configure-isolation-mode"></a>Configurar o modo de isolamento
O Windows suporta dois modos de isolamento para contentores: processo e Hyper-V. No modo de isolamento de processo, todos os contentores em execução no mesmo computador anfitrião partilham o kernel com o anfitrião. No modo de isolamento de Hyper-V, os kernels estão isolados entre cada contentor de Hyper-V e o anfitrião do contentor. O modo de isolamento está especificado no elemento `ContainerHostPolicies` no ficheiro de manifesto de aplicação. Os modos de isolamento que pode especificar são `process`, `hyperv` e `default`. A predefinição é o modo de isolamento do processo em anfitriões do Windows Server. Modo de isolamento de Hyper-V apenas é suportado em anfitriões do Windows 10, pelo que o contentor é executado no modo de isolamento de Hyper-V, independentemente de sua definição do modo de isolamento. O fragmento seguinte mostra como o modo de isolamento é especificado no ficheiro de manifesto de aplicação.

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > O modo de isolamento de Hyper-v está disponível nos SKUs do Azure Ev3 e Dv3, que têm suporte para virtualização aninhada. 
   >
   >

## <a name="configure-resource-governance"></a>Configurar a governação de recursos
A [governação de recursos](service-fabric-resource-governance.md) restringe os recursos que o contentor pode utilizar no anfitrião. O elemento `ResourceGovernancePolicy`, que está especificado no manifesto de aplicação, é utilizado para declarar os limites de recursos para um pacote do código do serviço. Limites de recursos podem ser definidas para os seguintes recursos: Memória, MemorySwap, CpuShares (peso relativo de CPU), MemoryReservationInMB, BlkioWeight (relativo de blockio). Neste exemplo, o Guest1Pkg do pacote de serviço obtém um núcleo nos nós de cluster onde está colocado. Os limites de memória são absolutos, pelo que o pacote do código está limitado a 1024 MB de memória (e uma reserva de garantia com o mesmo valor). Os pacotes do código (contentores ou processos) não conseguem alocar mais memória para além deste limite. Tentar fazê-lo resulta numa exceção de memória esgotada. Para que a imposição dos limites de recursos funcione, todos os pacotes do código dentro de um pacote de serviço devem ter limites de memória especificados.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>Configurar docker HEALTHCHECK 

A partir da versão v6.1, o Service Fabric integra automaticamente eventos [HEALTHCHECK do docker](https://docs.docker.com/engine/reference/builder/#healthcheck) no respetivo relatório de estado de funcionamento do sistema. Isto significa que, se o seu contentor tiver **HEALTHCHECK** ativado, o Service Fabric comunicará o estado de funcionamento sempre que o estado de funcionamento do contentor for alterado, conforme comunicado pelo Docker. Quando o *health_status* for *bom estado de funcionamento* é apresentado no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) um relatório com o estado de funcionamento **OK** e é apresentado **AVISO** se o *health_status* for *mau estado de funcionamento*. A instrução **HEALTHCHECK** que aponta para a verificação atual que é efetuada para monitorizar o estado de funcionamento do contentor tem de estar presente no dockerfile utilizado ao gerar a imagem de contentor. 

![HealthCheckHealthy][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

Pode configurar o comportamento de **HEALTHCHECK** para cada contentor, especificando as opções **HealthConfig** como parte de **ContainerHostPolicies** em ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true" RestartContainerOnUnhealthyDockerHealthStatus="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Por predefinição, *IncludeDockerHealthStatusInSystemHealthReport* está definido como **verdadeiro** e *RestartContainerOnUnhealthyDockerHealthStatus* está definido como  **falso**. Se *RestartContainerOnUnhealthyDockerHealthStatus* estiver definido como **verdadeiro**, um contentor que esteja a comunicar repetidamente um mau estado de funcionamento é reiniciado (possivelmente nos outros nós).

Se pretender desativar a integração de **HEALTHCHECK** em todo o cluster do Service Fabric, terá de definir [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) como **falso**.

## <a name="deploy-the-container-application"></a>Implementar a aplicação de contentor
Guarde todas as alterações e crie a aplicação. Para publicar a sua aplicação, clique com o botão direito do rato em **MyFirstContainer**, no Explorador de Soluções, e selecione **Publicar**.

No **Ponto Final da Ligação**, introduza o ponto final de gestão para o cluster. Por exemplo, "containercluster.westus2.cloudapp.azure.com:19000". Pode encontrar o ponto final de ligação do cliente no separador Descrição Geral do seu cluster no [portal do Azure](https://portal.azure.com).

Clique em **Publicar**.

O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web utilizada para inspecionar e gerir aplicações e nós num cluster do Service Fabric. Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implementação da aplicação. A aplicação implementada, mas fica em estado de erro até que a imagem é transferida em nós de cluster (o que podem demorar algum tempo, consoante o tamanho da imagem): ![Error][1]

A aplicação está pronta quando estiver a ser ```Ready``` Estado: ![Pronto][2]

Abra um browser e navegue para http://containercluster.westus2.cloudapp.azure.com:8081. Deverá ver o cabeçalho "Hello World!" apresentado no browser.

## <a name="clean-up"></a>Limpeza

Continua a incorrer em custos enquanto o cluster está em execução, pelo que deve considerar [eliminar o seu cluster](service-fabric-cluster-delete.md).

Depois de enviar a imagem para o registo de contentor, pode eliminar a imagem local do seu computador de programação:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Contentor do Windows Server SO e o host de compatibilidade do SO

Contentores do Windows Server não são compatíveis entre todas as versões de um sistema operacional host. Por exemplo:
 
- Contentores do Windows Server criados com a versão 1709 do Windows Server não funcionam num anfitrião com o Windows Server versão 2016. 
- Contentores do Windows Server criados com o Windows Server 2016 funcionam no modo de isolamento de Hyper-v apenas num anfitrião com o Windows Server versão 1709. 
- Com contentores do Windows Server criados com o Windows Server 2016, poderá ser necessário para se certificar de que a revisão do contentor de sistema operacional e o sistema operacional host são os mesmos quando em execução no modo de isolamento do processo num anfitrião com o Windows Server 2016.
 
Para obter mais informações, consulte [compatibilidade de versões do contentor Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility).

Considere a compatibilidade do sistema operacional host e o contentor do sistema operacional ao criar e implementar contentores no cluster do Service Fabric. Por exemplo:

- Certifique-se de que implementa contentores com um sistema operacional compatível com o SO em nós do cluster.
- Certifique-se de que o modo de isolamento especificado para a sua aplicação de contentor é consistente com suporte para o contentor de sistema operacional no nó onde está a ser implementado.
- Considere como as atualizações de SO para os nós de cluster ou contentores podem afetar seus de compatibilidade. 

Recomendamos as seguintes práticas para se certificar de que a contentores são implementados corretamente no seu cluster do Service Fabric:

- Utilize a marcação de imagem explícita com as imagens do Docker para especificar a versão do SO do Windows Server que é criado um contentor a partir. 
- Uso [SO marcação](#specify-os-build-specific-container-images) no seu ficheiro de manifesto de aplicação para se certificar de que seu aplicativo é compatível em diferentes versões do Windows Server e as atualizações.

> [!NOTE]
> Com o Service Fabric versão 6.2 e versões posterior, pode implementar contentores com base no Windows Server 2016 localmente num anfitrião com o Windows 10. No Windows 10, pode executar os contentores no modo de isolamento de Hyper-V, independentemente do modo de isolamento definido no manifesto do aplicativo. Para obter mais informações, consulte [modo de isolamento de configurar](#configure-isolation-mode).   
>
 
## <a name="specify-os-build-specific-container-images"></a>Indicar imagens de contentor específicas da compilação de SO 

Contentores do Windows Server podem não ser compatíveis entre diferentes versões do sistema operacional. Por exemplo, contentores do Windows Server criados com o Windows Server 2016 não funcionam no Windows Server versão 1709 no modo de isolamento do processo. Por conseguinte, se nós de cluster forem atualizados para a versão mais recente, os serviços de contentores criados com as versões anteriores do sistema operacional poderão falhar. Para contornar isso com a versão 6.1 do tempo de execução e mais recente, o Service Fabric suporta a especificação de várias imagens de sistema operacional por contentor e marcá-los com as versões de compilação do sistema operacional no manifesto do aplicativo. Pode obter a versão de compilação do SO, executando `winver` num prompt de comando do Windows. Antes de atualizar o SO nos nós, atualize os manifestos da aplicação e especifique a substituições de imagem por versão de SO. O fragmento seguinte mostra como especificar várias imagens de contentor no manifesto da aplicação, **ApplicationManifest.xml**:


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
A versão de compilação do Windows Server 2016 é 14393 e a da versão 1709 do Windows Server é 16299. O manifesto de serviço continua a especificar apenas uma imagem por serviço de contentores, conforme a imagem abaixo:

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > As funcionalidades de identificação da versão de compilação do SO só estão disponíveis para o Service Fabric no Windows
   >

Se o SO subjacente na VM for a compilação 16299 (versão 1709), o Service Fabric escolhe a imagem de contentor correspondente a essa versão do Windows Server. Se também for fornecida uma imagem de contentor não identificada juntamente com as imagens de contentor identificadas no manifesto da aplicação, o Service Fabric trata-a como uma imagem que funciona em todas as versões. Identificar as imagens de contentor explicitamente para evitar problemas durante as atualizações.

A imagem de contentor não marcada irá funcionar como uma substituição da imagem que fornece o ServiceManifest. Portanto, a imagem "myregistry.azurecr.io/samples/helloworldappDefault" irá substituir o ImageName "myregistry.azurecr.io/samples/helloworldapp" no ServiceManifest.

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo da aplicação do Service Fabric e dos manifestos do serviço
Seguem-se os manifestos completos do serviço e da aplicação utilizados neste artigo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurar o intervalo de tempo antes do contentor ser forçado a terminar

Pode configurar um intervalo de tempo para o tempo de execução para aguardar antes do contentor ser removido após a eliminação do serviço (ou uma mudança para outro nó) ser iniciada. Configurar o intervalo de tempo envia o comando `docker stop <time in seconds>` para o contentor.  Para obter mais detalhes, veja [paragem do docker](https://docs.docker.com/engine/reference/commandline/stop/). O intervalo de tempo de espera é especificado na secção `Hosting`. O fragmento do manifesto do cluster seguinte mostra como definir o intervalo de espera:

```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```
O intervalo de tempo predefinido está definido para 10 segundos. Uma vez que esta configuração é dinâmica, uma configuração apenas de atualização no cluster atualiza tempo limite. 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurar o tempo de execução para remover as imagens do contentor não utilizadas

Pode configurar o cluster do Service Fabric para remover as imagens do contentor não utilizadas do nó. Esta configuração permite que o espaço em disco seja recapturado se existirem demasiadas imagens do contentor no nó. Para ativar esta funcionalidade, atualize o [alojamento](service-fabric-cluster-fabric-settings.md#hosting) secção manifesto do cluster, conforme mostrado no seguinte fragmento: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
} 
```

Para imagens que não devem ser eliminadas, pode especificá-las no parâmetro `ContainerImagesToSkip`.  


## <a name="configure-container-image-download-time"></a>Configurar o tempo de transferência de imagem de contentor

O tempo de execução do Service Fabric aloca 20 minutos para transferir e extrair imagens de contentor, que funciona para a maioria das imagens de contentor. Para imagens de grandes dimensões ou quando a ligação de rede for lenta, poderá ser necessário aumentar o tempo de espera antes de abortar a transferência e extração de imagens. Este limite de tempo é definido utilizando o atributo **ContainerImageDownloadTimeout** na secção **Alojamento** do manifesto do cluster, conforme mostrado no seguinte fragmento:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Definir a política de retenção de contentores

Para ajudar a diagnosticar falhas no arranque de contentores, o Service Fabric (versão 6.1 ou superior) suporta a retenção de contentores que terminaram ou cujo arranque falhou. Esta política pode ser definida no ficheiro **ApplicationManifest.xml**, conforme mostrado no fragmento seguinte:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

A definição **ContainersRetentionCount** especifica o número de contentores que vão ser retidos quando falham. Se não for especificado um valor negativo, todos os contentores com falhas serão mantidos. Se o atributo **ContainersRetentionCount** não for especificado, não serão retidos contentores. O atributo **ContainersRetentionCount** também suporta Parâmetros de Aplicação, para que os utilizadores possam especificar valores diferentes para clusters de teste e produção. Utilize restrições de posicionamento para segmentar o serviço de contentores para um nó particular ao utilizar esta funcionalidade para impedir a passagem do serviço de contentores para outros nós. Quaisquer contentores retidos que utilizem esta funcionalidade têm de ser removidos manualmente.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Iniciar o daemon do Docker com argumentos personalizados

Com a versão e posterior 6.2 do runtime do Service Fabric, pode iniciar o daemon do Docker com argumentos personalizados. Quando os argumentos personalizados são especificados, o Service Fabric não passa outros argumentos ao motor do docker, exceto o argumento `--pidfile`. Por conseguinte, `--pidfile` não deve ser passado como argumento. Além disso, o argumento deve continuar a ter o daemon do docker a escutar o pipe de nome predefinido no Windows (ou o socket de domínio unix no Linux) para o Service Fabric comunicar com o Daemon. Os argumentos personalizados passam pelo manifesto do cluster na secção **Alojamento**, em **ContainerServiceArguments** como mostrado no seguinte fragmento: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre como executar [contentores no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Deploy a .NET application in a container](service-fabric-host-app-in-a-container.md) (Implementar uma aplicação .NET num contentor).
* Saiba mais sobre o [ciclo de vida das aplicações](service-fabric-application-lifecycle.md) do Service Fabric.
* Veja os [exemplos de códigos de contentor do Service Fabric ](https://github.com/Azure-Samples/service-fabric-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
