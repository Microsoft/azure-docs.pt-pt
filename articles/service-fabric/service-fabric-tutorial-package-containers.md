---
title: Compactar e implementar contentores como uma aplicação do Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, saiba como gerar uma definição de aplicação do Azure Service Fabric com o Yeoman e como compactar a aplicação.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Contentores, Microsserviços, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b6353b2c720a188571a0f6a4e27a89a49c857173
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311582"
---
# <a name="tutorial-package-and-deploy-containers-as-a-service-fabric-application-using-yeoman"></a>Tutorial: Agrupar e implementar contentores como uma aplicação do Service Fabric com o Yeoman

Este tutorial é a segunda parte de uma série. Neste tutorial, é utilizada uma ferramenta de gerador de modelos (Yeoman) para gerar uma definição de aplicação do Service Fabric. Esta aplicação pode, depois, ser utilizada para implementar contentores no Service Fabric. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar o Yeoman
> * Criar um pacote de aplicação com o Yeoman
> * Configurar as definições no pacote da aplicação para utilização com contentores
> * Criar a aplicação
> * Implementar e executar a aplicação
> * Limpar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* Vão ser utilizadas as imagens de contentor enviadas para o Azure Container Registry que foram criadas na [Parte 1](service-fabric-tutorial-create-container-images.md) desta série de tutoriais.
* Vai ser [configurado](service-fabric-tutorial-create-container-images.md) o ambiente de desenvolvimento do Linux.

## <a name="install-yeoman"></a>Instalar o Yeoman

O Service Fabric proporciona ferramentas estruturais que ajudam a criar aplicações a partir do terminal com o gerador de modelos Yeoman. Siga os passos abaixo para confirmar que tem o gerador de modelos Yeoman.

1. Instale o nodejs e o NPM no computador. Os utilizadores de Mac OSX terão de utilizar o gestor de pacotes Homebrew

    ```bash
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
    nvm install node 
    ```
2. Instalar o gerador de modelos Yeoman no seu computador a partir do NPM

    ```bash
    npm install -g yo
    ```
3. Instalar o gerador de contentores Yeoman do Service Fabric

    ```bash 
    npm install -g generator-azuresfcontainer
    ```

## <a name="package-a-docker-image-container-with-yeoman"></a>Empacotar um contentor de imagens do Docker com o Yeoman

1. Para criar uma aplicação de contentor do Service Fabric, no diretório “container-tutorial” do repositório clonado, execute o comando seguinte.

    ```bash
    yo azuresfcontainer
    ```
2. Escreva "TestContainer" para dar um nome à aplicação
3. Escreva "azurevotefront" para dar um nome ao serviço de aplicação.
4. Indique o caminho da imagem do contentor no ACR para o repositório do front-end, como, por exemplo, “\<acrName>.azurecr.io/azure-vote-front:v1”. O campo \<acrName> tem de ser igual ao valor que foi utilizado no tutorial anterior.
5. Prima Enter para deixar a secção Comandos vazia.
6. Especifique uma contagem de instâncias de 1.

A imagem seguinte mostra a entrada e a saída da execução do comando yo:

```bash
? Name your application TestContainer
? Name of the application service: azurevotefront
? Input the Image Name: <acrName>.azurecr.io/azure-vote-front:v1
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/TestContainer/ApplicationManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/ServiceManifest.xml
   create TestContainer/TestContainer/azurevotefrontPkg/config/Settings.xml
   create TestContainer/TestContainer/azurevotefrontPkg/code/Dummy.txt
   create TestContainer/install.sh
   create TestContainer/uninstall.sh
```

Para adicionar outro serviço de contentores a uma aplicação já criada com o Yeoman, execute os seguintes passos:

1. Altere o diretório um nível para o diretório **TestContainer**, como, por exemplo, *./TestContainer*
2. Execute `yo azuresfcontainer:AddService`
3. Dê ao serviço o nome “azurevoteback”
4. Indique o caminho da imagem do contentor para Redis - “alpine:redis”
5. Prima Enter para deixar a secção Comandos vazia
6. Especifique uma contagem de instâncias de "1".

São apresentadas todas as entradas para adicionar o serviço utilizado:

```bash
? Name of the application service: azurevoteback
? Input the Image Name: alpine:redis
? Commands:
? Number of instances of guest container application: 1
   create TestContainer/azurevotebackPkg/ServiceManifest.xml
   create TestContainer/azurevotebackPkg/config/Settings.xml
   create TestContainer/azurevotebackPkg/code/Dummy.txt
```

Ao longo do resto do tutorial, vamos trabalhar no diretório **TestContainer**. Por exemplo, *./TestContainer/TestContainer*. O conteúdo deste diretório deve ser o seguinte.

```bash
$ ls
ApplicationManifest.xml azurevotefrontPkg azurevotebackPkg
```

## <a name="configure-the-application-manifest-with-credentials-for-azure-container-registry"></a>Configurar o manifesto da aplicação com credenciais para o Azure Container Registry

Para que o Service Fabric extraia as imagens de contentores do Azure Container Registry, temos de indicar as credenciais em **ApplicationManifest.xml**.

Inicie sessão na sua instância do ACR. Utilize o comando **az acr login** para concluir a operação. Forneça o nome exclusivo dado ao registo de contentor quando este foi criado.

```bash
az acr login --name <acrName>
```

O comando devolve a mensagem **Início de sessão com êxito** após a conclusão.

Em seguida, execute o comando seguinte para obter a palavra-passe do seu registo de contentor. O Service Fabric utiliza esta palavra-passe para se autenticar no ACR e extrair as imagens de contentores.

```bash
az acr credential show -n <acrName> --query passwords[0].value
```

Em **ApplicationManifest.xml**, adicione o fragmento de código no elemento **ServiceManifestImport** do serviço de front-end. Insira o **acrName** no campo **AccountName** e a palavra-passe devolvida com o comando anterior é utilizada no campo **Password**. É disponibilizado um **ApplicationManifest.xml** completo na parte final deste documento.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <RepositoryCredentials AccountName="<acrName>" Password="<password>" PasswordEncrypted="false"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar a comunicação e o mapeamento de portas de porta para anfitrião do contentor

### <a name="configure-communication-port"></a>Configurar a porta da comunicação

Configure um ponto final de HTTP de modo a que os clientes possam comunicar com o seu serviço. Abra o ficheiro *./TestContainer/azurevotefrontPkg/ServiceManifest.xml* e declare um recurso de ponto final no elemento **ServiceManifest**.  Adicione o protocolo, a porta e o nome. Neste tutorial, o serviço escuta na porta 80. O fragmento seguinte é colocado na etiqueta *ServiceManifest* do recurso.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```

Da mesma forma, modifique o Manifesto do Serviço do serviço de back-end. Abra *./TestContainer/azurevotebackPkg/ServiceManifest.xml* e declare um recurso de ponto final no elemento **ServiceManifest**. Neste tutorial, a predefinição de redis 6379 é mantida. O fragmento seguinte é colocado na etiqueta *ServiceManifest* do recurso.

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
            listen. Please note that if your service is partitioned, this port is shared with 
            replicas of different partitions that are placed in your code. -->
    <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Indicar **UriScheme** regista automaticamente o ponto final do contentor no serviço Nomenclatura do Service Fabric para deteção. É disponibilizado um ficheiro de exemplo completo ServiceManifest.xml do serviço de back-end na parte final deste artigo.

### <a name="map-container-ports-to-a-service"></a>Mapear portas de contentores para um serviço

Para expor os contentores no cluster, também temos de criar um enlace de porta em “ApplicationManifest.xml”. A política **PortBinding** referencia os **Pontos finais** que definimos nos ficheiros **ServiceManifest.xml**. Os pedidos recebidos para estes pontos finais são mapeados para as portas dos contentores que estão abertas e vinculadas aqui. No ficheiro **ApplicationManifest.xml**, adicione o seguinte código para vincular as portas 80 e 6379 aos pontos finais. É disponibilizado um **ApplicationManifest.xml** completo na parte final deste documento.

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
</ContainerHostPolicies>
```

```xml
<ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
</ContainerHostPolicies>
```

### <a name="add-a-dns-name-to-the-backend-service"></a>Adicionar um nome DNS ao serviço de back-end

Para que o Service Fabric atribua este nome DNS ao serviço de back-end, o nome tem de ser especificado em **ApplicationManifest.xml**. Adicione o atributo **ServiceDnsName** ao elemento **Service**, conforme mostrado:

```xml
<Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
  <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
    <SingletonPartition/>
  </StatelessService>
</Service>
```

O serviço de front-end lê uma variável de ambiente para saber o nome DNS da instância do Redis. Esta variável de ambiente já está definida no Dockerfile que foi utilizado para gerar a imagem do Docker e não é necessário realizar qualquer ação aqui.

```Dockerfile
ENV REDIS redisbackend.testapp
```

O fragmento de código seguinte ilustra de que forma é que o código Python do front-end escolhe a variável de ambiente descrita no Dockerfile. Não é necessário realizar nenhuma ação aqui.

```python
# Get DNS Name
redis_server = os.environ['REDIS']

# Connect to the Redis store
r = redis.StrictRedis(host=redis_server, port=6379, db=0)
```

Nesta fase do tutorial, o modelo de uma aplicação Service Package está disponível para implementação num cluster. No tutorial subsequente, esta aplicação é implementada e executada num cluster do Service Fabric.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Para implementar a aplicação no Azure, precisa de um cluster do Service Fabric para executar a aplicação. Os seguintes comandos criam um cluster de cinco nós no Azure.  Os comandos também criar um certificado autoassinado, adiciona-o para um cofre de chaves e transfere o certificado localmente como um ficheiro PEM. O novo certificado é utilizado para proteger o cluster quando implementa e é utilizado para autenticar clientes.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  
# The certificate is downloaded locally as a PEM file.
az sf cluster create --resource-group $ResourceGroupName --location $Location \ 
--certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject \ 
--cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName \ 
--vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> O serviço de front-end da Web está configurado para escutar tráfego de entrada na porta 80. Por predefinição, a porta 80 está aberta no seu cluster de VMs e o Balanceador de carga do Azure.
>

Para obter mais informações sobre como criar seu próprio cluster, consulte [criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

## <a name="build-and-deploy-the-application-to-the-cluster"></a>Criar e implementar a aplicação no cluster

Pode implementar a aplicação no cluster do Azure com a CLI do Service Fabric. Se a CLI do Service Fabric não estiver instalada no seu computador, siga [estas](service-fabric-get-started-linux.md#set-up-the-service-fabric-cli) instruções para instalá-la.

Ligue ao cluster do Service Fabric no Azure. Substitua o ponto final do exemplo pelo seu próprio. O ponto final tem de ser um URL completo semelhante ao seguinte.  O ficheiro PEM é o certificado autoassinado que foi criado anteriormente.

```bash
sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
```

Utilize o script de instalação disponibilizado no diretório **TestContainer** para copiar o pacote de aplicação para o arquivo de imagens do cluster, registar o tipo de aplicação e criar uma instância da mesma.

```bash
./install.sh
```

Abra um browser e navegue para o Service Fabric Explorer em http:\//containertestcluster.eastus.cloudapp.azure.com:19080/Explorer. Expanda o nó Aplicações e repare que há uma entrada para o tipo de aplicação e outra para a instância.

![Service Fabric Explorer][sfx]

Para ligar para a aplicação em execução, abra um browser e aceda ao url do cluster - por exemplo, http:\//containertestcluster.eastus.cloudapp.azure.com:80. Deverá ver a aplicação Voting na IU da Web.

![votingapp][votingapp]

## <a name="clean-up"></a>Limpeza

Utilize o script de desinstalação fornecido no modelo para eliminar a instância da aplicação do cluster local e anular o registo do tipo de aplicação. Este comando demora algum tempo a limpar a instância e o comando “install'sh” não pode ser executado imediatamente a seguir a este script.

```bash
./uninstall.sh
```

## <a name="examples-of-completed-manifests"></a>Exemplos de manifestos concluídos

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<ApplicationManifest ApplicationTypeName="TestContainerType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotefrontPkg" ServiceManifestVersion="1.0.0"/>
    <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myaccountname" Password="<password>" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="azurevotefrontTypeEndpoint"/>
    </ContainerHostPolicies>
        </Policies>
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="azurevotebackPkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
          <PortBinding ContainerPort="6379" EndpointRef="azurevotebackTypeEndpoint"/>
        </ContainerHostPolicies>
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="azurevotefront">
      <StatelessService ServiceTypeName="azurevotefrontType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
    <Service Name="azurevoteback" ServiceDnsName="redisbackend.testapp">
      <StatelessService ServiceTypeName="azurevotebackType" InstanceCount="1">
        <SingletonPartition/>
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

### <a name="front-end-servicemanifestxml"></a>ServiceManifest.xml do front-end

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotefrontPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotefrontType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>acrName.azurecr.io/azure-vote-front:v1</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotefrontTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>

 </ServiceManifest>
```

### <a name="redis-servicemanifestxml"></a>ServiceManifest.xml do Redis

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="azurevotebackPkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="azurevotebackType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>

   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>alpine:redis</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables>
      </EnvironmentVariables>
   </CodePackage>
     <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="azurevotebackTypeEndpoint" Port="6379" Protocol="tcp"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, foram agrupados vários contentores numa aplicação do Service Fabric com o Yeoman. Depois, a aplicação foi implementada e executada num cluster do Service Fabric. Foram efetuados os seguintes passos:

> [!div class="checklist"]
> * Instalar o Yeoman
> * Criar um pacote de aplicação com o Yeoman
> * Configurar as definições no pacote da aplicação para utilização com contentores
> * Criar a aplicação
> * Implementar e executar a aplicação
> * Limpar a aplicação

Avance para o próximo tutorial para saber mais sobre a ativação pós-falha e o dimensionamento da aplicação no Service Fabric.

> [!div class="nextstepaction"]
> [Saiba mais sobre a ativação pós-falha e o dimensionamento de aplicações (Learn about failover and scaling applications)](service-fabric-tutorial-containers-failover.md)

[votingapp]: ./media/service-fabric-tutorial-deploy-run-containers/votingapp.png
[sfx]: ./media/service-fabric-tutorial-deploy-run-containers/containerspackagetutorialsfx.png