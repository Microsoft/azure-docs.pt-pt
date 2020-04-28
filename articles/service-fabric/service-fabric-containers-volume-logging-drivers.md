---
title: Condutor de volume de ficheiros Azure para tecido de serviço
description: Suportes de tecido de serviço utilizando Ficheiros Azure para fazer cópias de segurança a partir do seu recipiente.
ms.topic: conceptual
ms.date: 6/10/2018
ms.openlocfilehash: 514a0cb12359d58e38ebc30ae12cdb277757f2b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75750039"
---
# <a name="azure-files-volume-driver-for-service-fabric"></a>Condutor de volume de ficheiros Azure para tecido de serviço

O controlador de volume Azure Files é um [plugin](https://docs.docker.com/engine/extend/plugins_volume/) de volume Docker que fornece volumes baseados em [Ficheiros Azure](/azure/storage/files/storage-files-introduction) para recipientes Docker. É embalado como uma aplicação de Tecido de Serviço que pode ser implantada num cluster de Tecido de Serviço para fornecer volumes para outras aplicações de contentores de tecido de serviço dentro do cluster.

> [!NOTE]
> Versão 6.5.661.9590 do plugin de volume Dos Ficheiros Azure foi lançado para disponibilidade geral.
>

## <a name="prerequisites"></a>Pré-requisitos
* A versão Windows do plugin de volume Do Ficheiro Sino funciona na [versão 1709](/windows-server/get-started/whats-new-in-windows-server-1709)do Windows Server , [a versão 1709 do Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou apenas nos sistemas operativos.

* A versão Linux do plugin de volume Azure Files funciona em todas as versões do sistema operativo suportadas pelo Service Fabric.

* O plugin de volume Azure Files funciona apenas na versão 6.2 e mais recente do Tecido de Serviço.

* Siga as instruções na documentação dos [Ficheiros Azure](/azure/storage/files/storage-how-to-create-file-share) para criar uma partilha de ficheiros para a aplicação do recipiente service Fabric para utilizar como volume.

* Necessitará de [Powershell com o módulo de tecido de serviço](/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) instalado.

* Se estiver a utilizar recipientes Hyper-V, é necessário adicionar os seguintes cortes na secção ClusterManifest (cluster local) ou fabricSettings no seu modelo de Gestor de Recursos Azure (cluster Azure) ou ClusterConfig.json (cluster autónomo).

No ClusterManifest, é necessário adicionar o seguinte na secção de Hospedagem. Neste exemplo, o nome do volume é **sfazurefile** e a porta que ouve no cluster é **19100**. Substitua-os pelos valores corretos para o seu cluster.

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19100" />
</Section>
```

Na secção de definições de tecido no seu modelo de Gestor de Recursos Azure (para implementações Azure) ou ClusterConfig.json (para implementações autónomas), é necessário adicionar o seguinte corte. Mais uma vez, substitua o nome de volume e os valores da porta por si próprio.

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

## <a name="deploy-a-sample-application-using-service-fabric-azure-files-volume-driver"></a>Implementar uma aplicação de amostra utilizando o controlador de volume de ficheiros De Tecido de Serviço Azure

### <a name="using-azure-resource-manager-via-the-provided-powershell-script-recommended"></a>Utilização do Gestor de Recursos Azure através do script Powershell fornecido (recomendado)

Se o seu cluster estiver sediado no Azure, recomendamos a implementação de aplicações para o mesmo utilizando o modelo de recursos de aplicação do Gestor de Recursos Azure para facilitar a utilização e para ajudar a avançar para o modelo de manutenção da infraestrutura como código. Esta abordagem elimina a necessidade de acompanhar a versão da aplicação para o controlador de volume Do Azure Files. Também lhe permite manter modelos separados do Gestor de Recursos Azure para cada SISTEMA suportado. O script pressupõe que está a implementar a versão mais recente da aplicação Azure Files e tem parâmetros para o tipo OS, ID de subscrição de cluster e grupo de recursos. Pode descarregar o script a partir do site de [descarregamento](https://sfazfilevd.blob.core.windows.net/sfazfilevd/DeployAzureFilesVolumeDriver.zip)do Service Fabric . Note que isto configura automaticamente o ListenPort, que é a porta em que o plugin de volume dos Ficheiros Azure ouve pedidos do daemon do Docker, para 19100. Pode mudá-lo adicionando parâmetro chamado "listenPort". Certifique-se de que a porta não entra em conflito com qualquer outra porta que o cluster ou as suas aplicações usem.
 

Comando de implementação do Gestor de Recursos Azure para Windows:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -windows
```

Comando de implantação do Gestor de Recursos Azure para linux:
```powershell
.\DeployAzureFilesVolumeDriver.ps1 -subscriptionId [subscriptionId] -resourceGroupName [resourceGroupName] -clusterName [clusterName] -linux
```

Depois de executar com sucesso o script, pode saltar para a secção de configuração da [sua aplicação.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#configure-your-applications-to-use-the-volume)


### <a name="manual-deployment-for-standalone-clusters"></a>Implementação manual para clusters autónomos

A aplicação Service Fabric que fornece os volumes dos seus contentores pode ser descarregada a partir do site de [descarregamento](https://sfazfilevd.blob.core.windows.net/sfazfilevd/AzureFilesVolumePlugin.6.5.661.9590.zip)de Tecido de Serviço . A aplicação pode ser implantada para o cluster através de [PowerShell,](./service-fabric-deploy-remove-applications.md) [CLI](./service-fabric-application-lifecycle-sfctl.md) ou [FabricClient APIs](./service-fabric-deploy-remove-applications-fabricclient.md).

1. Utilizando a linha de comando, altere o diretório para o diretório raiz do pacote de aplicações descarregado.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Em seguida, copie o pacote de aplicação para a loja de imagens com os valores apropriados para [ApplicationPackagePath] e [ImageStoreConnectionString]:

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

4. Crie a aplicação, prestando muita atenção ao valor do parâmetro de aplicação **ListenPort.** Este valor é a porta em que o plugin de volume dos Ficheiros Azure ouve pedidos do daemon docker. Certifique-se de que a porta fornecida à aplicação corresponde às Portas De VolumePluginno do ClusterManifest e não entra em conflito com qualquer outra porta que o cluster ou as suas aplicações usem.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590   -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]
> 
> O Windows Server 2016 Datacenter não suporta mapeamento de suportes SMB para contentores ([que é suportado apenas na versão 1709](/virtualization/windowscontainers/manage-containers/container-storage)do Windows Server ). Esta restrição impede o mapeamento do volume de rede e os condutores de volume azure Files em versões com mais de 1709.

#### <a name="deploy-the-application-on-a-local-development-cluster"></a>Implementar a aplicação num cluster de desenvolvimento local
Siga os passos 1-3 a partir de [cima.](/azure/service-fabric/service-fabric-containers-volume-logging-drivers#manual-deployment-for-standalone-clusters)

 A contagem de funções predefinida para a aplicação plugin de volume Do Volume Azure Files é -1, o que significa que existe uma instância do serviço implantado em cada nó do cluster. No entanto, ao implementar a aplicação plugin de volume de ficheiros Azure num cluster de desenvolvimento local, a contagem de instâncias de serviço deve ser especificada como 1. Isto pode ser feito através do parâmetro de aplicação **InstanceCount.** Por isso, o comando para a criação da aplicação plugin de volume De ficheiros Azure num cluster de desenvolvimento local é:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.5.661.9590  -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.5.661.9590  --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```

## <a name="configure-your-applications-to-use-the-volume"></a>Configure as suas aplicações para utilizar o volume
O seguinte corte mostra como um volume baseado em Ficheiros Azure pode ser especificado no ficheiro manifesto de aplicação da sua aplicação. O elemento de interesse específico é a etiqueta **volume:**

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

O nome do condutor do plugin de volume Dos Ficheiros Azure é **sfazurefile**. Este valor é definido para o atributo do **Condutor** do elemento etiqueta **volume** no manifesto de aplicação.

Na etiqueta **volume** no corte acima, o plugin de volume dos Ficheiros Azure requer os seguintes atributos:
- **Fonte** - Este é o nome do volume. O utilizador pode escolher qualquer nome para o seu volume.
- **Destino** - Este atributo é o local para o local a que o volume está mapeado dentro do recipiente de corrida. Assim, o seu destino não pode ser um local que já existe dentro do seu contentor

Tal como indicado nos elementos **DriverOption** no corte acima, o plugin de volume Do Volume Azure Files suporta as seguintes opções de condutor:
- **shareName** - Nome da partilha de ficheiros Azure Files que fornece o volume para o recipiente.
- **storageAccountName** - Nome da conta de armazenamento Azure que contém a parte de ficheiro sondo do Azure Files.
- **armazenamentoAccountKey** - Chave de acesso para a conta de armazenamento Azure que contém a parte de ficheiro sonde Ficheiros Azure.
- **armazenamentoAccountFQDN** - Nome de domínio associado à conta de armazenamento. Se o armazenamentoAccountFQDN não for especificado, o nome de domínio será formado utilizando o sufixo predefinido (.file.core.windows.net) com o nome de conta de armazenamento.  

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

## <a name="using-your-own-volume-or-logging-driver"></a>Usando o seu próprio volume ou controlador de madeira
O Serviço Fabric também permite o uso do seu próprio [volume](https://docs.docker.com/engine/extend/plugins_volume/) personalizado ou [controladores de madeira.](https://docs.docker.com/engine/admin/logging/overview/) Se o controlador de volume/exploração não estiver instalado no cluster, pode instalá-lo manualmente utilizando os protocolos RDP/SSH. Pode executar a instalação com estes protocolos através de um [script de arranque de escala](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) de máquina virtual ou de um script De [ConfiguraçãoEntryPoint](/azure/service-fabric/service-fabric-application-model).

Um exemplo do script para instalar o controlador de [volume Docker para Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) é o seguinte:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Nas suas aplicações, para utilizar o volume ou controlador de registo instalado, terá de especificar os valores adequados nos elementos **Volume** e **LogConfig** no âmbito das Políticas de Anfitriões de **Contentores** no seu manifesto de aplicação.

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

Ao especificar um plug-in de volume, o Tecido de Serviço cria automaticamente o volume utilizando os parâmetros especificados. A etiqueta **Fonte** para o elemento **Volume** é o nome do volume e a etiqueta do **condutor** especifica o plug-in do volume do condutor. A etiqueta **Destino** é a localização para a a que a **Fonte** está mapeada dentro do recipiente de funcionamento. Assim, o seu destino não pode ser um local que já existe dentro do seu contentor. As opções podem ser especificadas utilizando a etiqueta **DriverOption** da seguinte forma:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Os parâmetros de aplicação são suportados para volumes como mostrado `MyStorageVar` no fragmento de manifesto anterior (procure uma utilização por exemplo).

Se for especificado um controlador de registo Docker, tem de colocar agentes (ou contentores) para manusear os registos do cluster. A etiqueta **DriverOption** pode ser utilizada para especificar opções para o controlador de registo.

## <a name="next-steps"></a>Passos seguintes
* Para ver amostras de recipientes, incluindo o condutor do volume, visite as [amostras](https://github.com/Azure-Samples/service-fabric-containers) do recipiente de tecido de serviço
* Para implantar contentores num cluster de tecido de serviço, consulte o artigo [Implementar um recipiente em Tecido de Serviço](service-fabric-deploy-container.md)
