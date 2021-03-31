---
title: Controlador de volume de ficheiros Azure para tecido de serviço
description: O Service Fabric suporta a utilização de Ficheiros Azure para fazer cópias de segurança do seu contentor.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: a5125dbd88a2fe236196c427244f1311d9b73b9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86247698"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Controlador de volume de ficheiros Azure para tecido de serviço

O controlador de volume Azure Files é um [plugin de volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) que fornece volumes baseados em [Ficheiros Azure](../storage/files/storage-files-introduction.md) para recipientes Docker. É embalado como uma aplicação de Tecido de Serviço que pode ser implantada num cluster de Tecido de Serviço para fornecer volumes para outras aplicações de recipientes de tecido de serviço dentro do cluster.

> [!NOTE]
> A versão 6.5.661.9590 do plugin de volume Azure Files foi lançada para disponibilidade geral.
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão Windows do plugin de volume Azure Files funciona apenas na [versão 1709](/windows-server/get-started/whats-new-in-windows-server-1709)do Windows Server , [versão 1709 do Windows 10](/windows/whats-new/whats-new-windows-10-version-1709) ou apenas nos sistemas operativos posteriores.

* A versão Linux do plugin de volume Azure Files funciona em todas as versões do sistema operativo suportadas pelo Service Fabric.

* O plugin de volume Azure Files funciona apenas na versão 6.2 do Service Fabric e mais recente.

* Siga as instruções na documentação do [Azure Files](../storage/files/storage-how-to-create-file-share.md) para criar uma partilha de ficheiros para a aplicação do recipiente de tecido de serviço para utilizar como volume.

* Necessitará do [Powershell com o módulo de Tecido de Serviço](./service-fabric-get-started.md) ou [SFCTL](./service-fabric-cli.md) instalado.

* Se estiver a utilizar recipientes Hyper-V, é necessário adicionar os seguintes cortes na secção ClusterManifest (cluster local) ou fabricSettings no seu modelo de Gestor de Recursos Azure (cluster Azure) ou ClusterConfig.js(cluster autónomo).

No ClusterManifest, é necessário adicionar o seguinte na secção de Hospedagem. Neste exemplo, o nome do volume é **sfazurefile** e a porta que ouve no cluster é **19100**. Substitua-os pelos valores corretos para o seu cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Na secção de tecidosSettings no seu modelo de Gestor de Recursos Azure (para implementações Azure) ou ClusterConfig.js(para implementações autónomas), é necessário adicionar o seguinte corte. Mais uma vez, substitua o nome do volume e os valores da porta pelos seus.

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19100"
      }
    ]
  }
]
```

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Implementar uma aplicação de amostra usando o controlador de volume de ficheiros de tecido de serviço

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Utilização do Gestor de Recursos Azure através do script fornecido powershell (recomendado)

Se o seu cluster estiver baseado no Azure, recomendamos a implementação de aplicações para o mesmo utilizando o modelo de recursos de aplicação Azure Resource Manager para facilitar a utilização e para ajudar a mover-se para o modelo de manutenção da infraestrutura como código. Esta abordagem elimina a necessidade de acompanhar a versão da aplicação para o controlador de volume Azure Files. Também permite manter modelos separados do Gestor de Recursos Azure para cada SISTEMA suportado. O script pressupõe que está a implementar a versão mais recente da aplicação Azure Files e toma parâmetros para o tipo DE, ID de subscrição de cluster e grupo de recursos. Você pode baixar o script do site de [descarregamento](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)do Service Fabric . Note que isto define automaticamente o ListenPort, que é a porta onde o plugin de volume Azure Files ouve pedidos do daemon Docker, a 19100. Pode alterá-lo adicionando o parâmetro denominado "listenPort". Certifique-se de que a porta não entra em conflito com qualquer outra porta que o cluster ou as suas aplicações utilizem.
 

Comando de implementação do Gestor de Recursos Azure para o Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Comando de implementação do Gestor de Recursos Azure para o Linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Uma vez executado com sucesso o script, pode saltar para a [secção de configuração da sua aplicação.](#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Implantação manual para clusters autónomos

A aplicação Service Fabric que fornece os volumes dos seus contentores pode ser descarregada a partir do site de descarregamento do [Service Fabric.](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip) A aplicação pode ser implantada no cluster através de [PowerShell,](./service-fabric-deploy-remove-applications.md) [CLI](./service-fabric-application-lifecycle-sfctl.md) ou [FabricClient APIs](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Utilizando a linha de comando, altere o diretório para o diretório de raiz do pacote de aplicações descarregado.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Em seguida, copie o pacote de aplicações para a loja de imagens com os valores adequados para [ApplicationPackagePath] e [ImageStoreConnectionString]:

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Registar o tipo de candidatura

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Crie a aplicação, prestando muita atenção ao valor do parâmetro da aplicação **ListenPort.** Este valor é a porta onde o plugin de volume Azure Files ouve pedidos do daemon Docker. Certifique-se de que a porta fornecida à aplicação corresponde aos VolumePluginPorts no ClusterManifest e não entra em conflito com qualquer outra porta que o cluster ou as suas aplicações utilizem.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> O Datacenter 2016 do Windows Server não suporta mapeamento de suportes SMB para contentores ([Que só é suportado na versão 1709 do Windows Server](/virtualization/windowscontainers/manage-containers/container-storage)). Esta restrição impede o mapeamento do volume de rede e os controladores de volume Azure Files em versões com mais de 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implementar a aplicação num cluster de desenvolvimento local
Siga os passos 1-3 a partir do [acima.](#manual-deployment-for-standalone-clusters)

 A contagem de exemplos de serviço predefinidos para a aplicação de plugin de volume Azure Files é -1, o que significa que há uma instância do serviço implantado em cada nó no cluster. No entanto, ao implementar a aplicação de plugin de volume Azure Files num cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isto pode ser feito através do parâmetro de aplicação **InstanceCount.** Portanto, o comando para a criação da aplicação de plugin de volume Azure Files num cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configure as suas aplicações para utilizar o volume
O seguinte corte mostra como um volume baseado em Azure Files pode ser especificado no ficheiro manifesto de aplicação da sua aplicação. O elemento de interesse específico é a etiqueta **volume:**

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
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

O nome do controlador do plugin de volume Azure Files é **sfazurefile**. Este valor é definido para o atributo **Driver** do elemento marca **volume** no manifesto de aplicação.

Na etiqueta **volume** no corte acima, o plugin de volume Azure Files requer os seguintes atributos:
- **Fonte** - Este é o nome do volume. O utilizador pode escolher qualquer nome para o seu volume.
- **Destino** - Este atributo é o local para onde o volume está mapeado dentro do recipiente de funcionamento. Assim, o seu destino não pode ser um local que já existe dentro do seu contentor

Tal como mostrado nos elementos **DriverOption** no corte acima, o plugin de volume Azure Files suporta as seguintes opções do controlador:
- **shareName** - Nome da partilha de ficheiros Azure Files que fornece o volume para o recipiente.
- **armazenamentoAme** - Nome da conta de armazenamento Azure que contém a partilha de ficheiros Azure Files.
- **armazenamentoSCotakey** - Chave de acesso para a conta de armazenamento Azure que contém a partilha de ficheiros Azure Files.
- **armazenamentoAccountFQDN** - Nome de domínio associado à conta de armazenamento. Se o armazenamentoAccountFQDN não for especificado, o nome de domínio será formado utilizando o sufixo predefinido (.file.core.windows.net) com o armazémA contagem de dadosName.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Usando o seu próprio volume ou controlador de registo
O Service Fabric também permite o uso do seu próprio [volume](https://docs.docker.com/engine/extend/plugins_volume/) personalizado ou condutores [de registo.](https://docs.docker.com/engine/admin/logging/overview/) Se o controlador de volume/registo do Docker não estiver instalado no cluster, pode instalá-lo manualmente utilizando os protocolos RDP/SSH. Pode realizar a instalação com estes protocolos através de um script de arranque de [escala de máquina virtual](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou de um script [SetupEntryPoint](./service-fabric-application-model.md).

Um exemplo do script para instalar o controlador de [volume Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Nas suas aplicações, para utilizar o volume ou o controlador de registo que instalou, deverá especificar os valores adequados nos elementos **Volume** e **LogConfig** no **âmbito do ContainerHostPolicies** no seu manifesto de aplicação.

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

Ao especificar um plug-in de volume, o Tecido de Serviço cria automaticamente o volume utilizando os parâmetros especificados. A etiqueta **'Fonte'** para o elemento **Volume** é o nome do volume e a etiqueta **do controlador** especifica o plug-in do controlador de volume. A etiqueta **Destino** é o local para onde a **Fonte** está mapeada dentro do recipiente de funcionamento. Assim, o seu destino não pode ser um local que já exista dentro do seu contentor. As opções podem ser especificadas utilizando a etiqueta **DriverOption** da seguinte forma:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Os parâmetros de aplicação são suportados para volumes como mostrado no corte manifesto anterior (procure `MyStorageVar` uma utilização por exemplo).

Se for especificado um controlador de registo Docker, tem de colocar agentes (ou contentores) para manusear os registos do cluster. A etiqueta **DriverOption** pode ser utilizada para especificar opções para o controlador de registo.

## <a name="next-steps"></a>Passos seguintes
* Para ver amostras de recipientes, incluindo o controlador de volume, visite as [amostras](https://github.com/Azure-Samples/service-fabric-containers) do recipiente de tecido de serviço
* Para implantar contentores num cluster de tecido de serviço, consulte o artigo [Desdobre um recipiente no Tecido de Serviço](./service-fabric-get-started-containers.md)
