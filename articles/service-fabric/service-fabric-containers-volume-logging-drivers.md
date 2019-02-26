---
title: Controladores de Volume (pré-visualização) de ficheiros do Azure de recursos de infraestrutura do serviço | Documentos da Microsoft
description: O Service Fabric suporta ficheiros do Azure a utilizar para volumes de cópia de segurança do seu contentor. Isso está atualmente em pré-visualização.
services: service-fabric
documentationcenter: other
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: aljo, subramar
ms.openlocfilehash: 5d44904d6210dbc9520ae735605699b197f38bef
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804135"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Controlador de Volume de ficheiros do Azure do serviço Fabric (pré-visualização)
O plug-in de volume de ficheiros do Azure é um [Plug-in de volume do Docker](https://docs.docker.com/engine/extend/plugins_volume/) que fornece [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) com base em volumes para contentores do Docker. Este plug-in de volume do Docker é empacotado como uma aplicação do Service Fabric que pode ser implementada em clusters do Service Fabric. Seu objetivo é fornecer a ficheiros do Azure com base em volumes para outras aplicações de contentor do Service Fabric que são implementadas para o cluster.

> [!NOTE]
> Versão 6.4.571.9494 do plug-in de volume de ficheiros do Azure é uma versão de pré-lançamento que estão disponível neste documento. Como uma versão de pré-visualização, é **não** suportados para utilização em ambientes de produção.
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão do Windows do plug-in de volume de ficheiros do Azure funciona em [versão 1709 do Windows Server](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [com o Windows 10 versão 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou sistemas operativos posteriores apenas. A versão do Linux do plug-in de volume de ficheiros do Azure funciona em todas as versões de sistema operativo suportadas pelo Service Fabric.

* O plug-in de volume de ficheiros do Azure só funciona no Service Fabric versão 6.2 e versões mais recente.

* Siga as instruções no [documentação de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) para criar uma partilha de ficheiros para a aplicação de contentor do Service Fabric utilizar como o volume.

* Precisará [Powershell com o módulo do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) instalado.

* Se estiver a utilizar contentores de Hyper-v, os fragmentos seguintes tem de ser adicionada na secção de fabricSettings no seu modelo ARM (cluster do Azure) ou ClusterConfig.json (cluster autónomo) ou ClusterManifest (local cluster). Terá o nome do volume e a porta que o volume de escuta no cluster. 

Em ClusterManifest, a seguinte tem de ser adicionada na secção de alojamento. Neste exemplo, é o nome do volume **sfazurefile** e a porta de escuta para o cluster é **19300**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19300" />
</Section>
```

Na secção fabricSettings no seu modelo ARM (para implementações do Azure) ou ClusterConfig.json (para implementações de autónomo), o fragmento seguinte tem de ser adicionada. 

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19300"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Implementar a aplicação de serviço ficheiros do Azure Service Fabric

A aplicação do Service Fabric que fornece os volumes para os seus contentores pode ser baixada em seguintes [link](https://aka.ms/sfvolume6.4). O aplicativo pode ser implementado no cluster através de [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) ou [FabricClient APIs](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. Com a linha de comandos, altere o diretório para o diretório de raiz do pacote do aplicativo transferido.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copie o pacote de aplicação para o armazenamento de imagens, execute o comando abaixo com o valor apropriado para [ApplicationPackagePath] e [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registar o tipo de aplicação

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Criar a aplicação no comando para criar a aplicação abaixo, tenha em conta a **ListenPort** parametr aplikace. O valor especificado para este parâmetro de aplicação é a porta em que o plug-in de volume de ficheiros do Azure escuta pedidos do daemon do Docker. É importante certificar-se de que a porta fornecida para a aplicação não entram em conflito com qualquer outra porta que utilizam o cluster ou das suas aplicações.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter não suporta o mapeamento SMB monta para contentores ([que só é suportado no Windows Server versão 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Esta restrição impede que o mapeamento do volume de rede e controladores de volume de ficheiros do Azure nas versões mais antigas que 1709.
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implementar a aplicação num cluster de desenvolvimento local
A contagem de instâncias de serviço padrão para a aplicação de plug-in do volume de ficheiros do Azure é -1, o que significa que existe uma instância do serviço implementado em cada nó no cluster. No entanto, quando implementar a aplicação de plug-in do volume de ficheiros do Azure num cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isso pode ser feito através da **InstanceCount** parametr aplikace. Por conseguinte, o comando para implementar a aplicação de plug-in do volume de ficheiros do Azure num cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Configurar as suas aplicações utilizem o volume
O fragmento seguinte mostra como um volume de ficheiros do Azure com base pode ser especificado no manifesto da aplicação da sua aplicação. O elemento específico de interesse é o **Volume** etiqueta:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

O nome de controlador para o plug-in de volume de ficheiros do Azure é **sfazurefile**. Este valor está definido para o **Driver** atributo da **Volume** elemento no manifesto do aplicativo.

Na **Volume** elemento no fragmento acima, o plug-in de volume de ficheiros do Azure requer as seguintes tags:
- **Origem** -este é o nome do volume. O usuário pode escolher qualquer nome para o volume.
- **Destino** -esta etiqueta é a localização que o volume está mapeado para dentro do contentor em execução. Portanto, o seu destino não pode ser uma localização que já existe no seu contentor

Como mostra a **DriverOption** elementos no fragmento acima, o plug-in de volume de ficheiros do Azure suporta as seguintes opções de controlador:
- **shareName** -nome da partilha de ficheiros de ficheiros do Azure que fornece o volume para o contentor.
- **storageAccountName** – nome da conta de armazenamento do Azure que contém o ficheiro de ficheiros do Azure de partilhar.
- **storageAccountKey** -chave de acesso para a conta de armazenamento do Azure que contém a partilha de ficheiros de ficheiros do Azure.
- **storageAccountFQDN** -nome de domínio associado com a conta de armazenamento. Se não for especificado storageAccountFQDN, nome de domínio será formado utilizando o suffix(.file.core.windows.net) padrão com o storageAccountName.  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>Utilizar o seu próprio volume ou o driver de registo
Service Fabric também permite a utilização de seu próprio custom [volume](https://docs.docker.com/engine/extend/plugins_volume/) ou [registo](https://docs.docker.com/engine/admin/logging/overview/) drivers. Se o controlador de volume/registo de Docker não está instalado no cluster, pode instalá-lo manualmente utilizando os protocolos RDP/SSH. Pode efetuar a instalação com estes protocolos através de um [conjunto de dimensionamento de máquina virtual de script de inicialização](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou uma [SetupEntryPoint script](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model).

Um exemplo do script para instalar o [controladores de volume do Docker para o Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Em seus aplicativos, para utilizar o volume ou o driver de registo instalado, terá de especificar os valores adequados na **Volume** e **LogConfig** elementos em  **ContainerHostPolicies** no seu manifesto de aplicativo.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Ao especificar um plug-in de volume, o Service Fabric cria automaticamente o volume utilizando os parâmetros especificados. O **origem** etiqueta para o **Volume** elemento é o nome do volume e o **Driver** marca Especifica o plug-in de driver de volume. O **destino** marca é a localização que o **origem** é mapeado para dentro do contentor em execução. Assim, o seu destino não pode ser uma localização que já existe no seu contentor. Podem ser especificadas opções utilizando o **DriverOption** etiqueta da seguinte forma:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

São suportados parâmetros de aplicação para volumes, conforme mostrado no fragmento de manifesto anterior (procure `MyStorageVar` para obter um exemplo utilizar).

Se não for especificado um controlador de registo do Docker, tem de implementar agentes (ou contentores) para lidar com os registos do cluster. O **DriverOption** etiqueta pode ser utilizada para especificar as opções para o controlador de registo.

## <a name="next-steps"></a>Passos Seguintes
* Para ver os exemplos de contentor, incluindo o driver de volume, visite o [exemplos de contentor do Service Fabric](https://github.com/Azure-Samples/service-fabric-containers)
* Para implementar contentores no cluster do Service Fabric, consulte o artigo [implementar um contentor no Service Fabric](service-fabric-deploy-container.md)
