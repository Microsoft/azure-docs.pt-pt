---
title: Criar e gerir uma máquina virtual Azure usando Java
description: Utilize o Java e o Azure Resource Manager para implementar uma máquina virtual e todos os seus recursos de apoio.
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/17/2017
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: 568760dcaebbad0e14405e14c5a5bf8f6cd18395
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197460"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Criar e gerir VMs do Windows em Azure utilizando a Java

[Uma Máquina Virtual Azure](overview.md) (VM) precisa de vários recursos de suporte Azure. Este artigo abrange a criação, gestão e eliminação de recursos VM usando Java. Saiba como:

> [!div class="checklist"]
> * Criar um projeto do Maven
> * Adicionar dependências
> * Criar credenciais
> * Criar recursos
> * Executar tarefas de gestão
> * Eliminar recursos
> * Executar a aplicação

Leva cerca de 20 minutos para fazer estes passos.

## <a name="create-a-maven-project"></a>Criar um projeto do Maven

1. Se ainda não o fez, instale [Java.](/azure/developer/java/fundamentals/java-jdk-long-term-support)
2. Instale [o Maven.](https://maven.apache.org/download.cgi)
3. Criar uma nova pasta e o projeto:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Adicionar dependências

1. Sob a `testAzureApp` pasta, abra o `pom.xml` ficheiro e adicione a configuração de construção ao &lt; projeto para permitir a &gt; construção da sua aplicação:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Adicione as dependências necessárias para aceder ao Azure Java SDK.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Guarde o ficheiro.

## <a name="create-credentials"></a>Criar credenciais

Antes de iniciar este passo, certifique-se de que tem acesso a um [diretor de serviço de Diretório Ativo.](../../active-directory/develop/howto-create-service-principal-portal.md) Você também deve registar o ID da aplicação, a chave de autenticação, e o ID do inquilino que você precisa em um passo posterior.

### <a name="create-the-authorization-file"></a>Criar o ficheiro de autorização

1. Crie um ficheiro com o nome `azureauth.properties` e adicione estas propriedades:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Substitua o **&lt; &gt; id de subscrição** pelo seu identificador de subscrição, **&lt; id &gt; de aplicação** pelo identificador de aplicação ative, **&lt; chave &gt; de autenticação** com a chave de aplicação e **&lt; id &gt; do inquilino** pelo identificador do inquilino.

2. Guarde o ficheiro.
3. Desaprove uma variável ambiental denominada AZURE_AUTH_LOCATION na sua concha com o caminho completo para o ficheiro de autenticação.

### <a name="create-the-management-client"></a>Criar o cliente de gestão

1. Abra o `App.java` ficheiro em baixo e `src\main\java\com\fabrikam` certifique-se de que esta declaração de pacote está no topo:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Nos termos da declaração do pacote, adicione estas declarações de importação:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Para criar as credenciais de Diretório Ativo que precisa de fazer pedidos, adicione este código ao método principal da classe App:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Criar recursos

### <a name="create-the-resource-group"></a>Criar o grupo de recursos

Todos os recursos devem ser contidos num [grupo de recursos.](../../azure-resource-manager/management/overview.md)

Para especificar os valores para a aplicação e criar o grupo de recursos, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Crie o conjunto de disponibilidade

[Os conjuntos de disponibilidade](tutorial-availability-sets.md) facilitam a manutenção das máquinas virtuais utilizadas pela sua aplicação.

Para criar o conjunto de disponibilidade, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

É necessário um [endereço IP público](../../virtual-network/public-ip-addresses.md) para comunicar com a máquina virtual.

Para criar o endereço IP público para a máquina virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina virtual deve estar numa sub-rede de uma [rede Virtual.](../../virtual-network/virtual-networks-overview.md)

Para criar uma sub-rede e uma rede virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual precisa de uma interface de rede para comunicar na rede virtual.

Para criar uma interface de rede, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual

Agora que criou todos os recursos de apoio, pode criar uma máquina virtual.

Para criar a máquina virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Este tutorial cria uma máquina virtual que executa uma versão do sistema operativo Windows Server. Para saber mais sobre a seleção de outras imagens, consulte [a Navigate e selecione imagens de máquinas virtuais Azure com o Windows PowerShell e o Azure CLI](../linux/cli-ps-findimage.md).
> 
>

Se quiser utilizar um disco existente em vez de uma imagem de mercado, utilize este código: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Executar tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar código para automatizar tarefas repetitivas ou complexas.

Quando precisas de fazer alguma coisa com o VM, precisas de ter uma instância. Adicione este código ao bloco de tentativa do método principal:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Obtenha informações sobre o VM

Para obter informações sobre a máquina virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>Parar a VM

Pode parar uma máquina virtual e manter todas as suas definições, mas continuar a ser carregada por ela, ou pode parar uma máquina virtual e translocar a máquina. Quando uma máquina virtual é negociada, todos os recursos associados a ela também são translocados e a faturação termina para ela.

Para parar a máquina virtual sem a fazer negóciolocado, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Se quiser negociar a máquina virtual, altere a chamada PowerOff para este código:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Iniciar a VM

Para iniciar a máquina virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>Redimensionar o VM

Muitos aspetos da implementação devem ser considerados ao decidir sobre um tamanho para a sua máquina virtual. Para mais informações, consulte os [tamanhos de VM.](../sizes.md)  

Para alterar o tamanho da máquina virtual, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adicionar um disco de dados à VM

Para adicionar um disco de dados à máquina virtual de 2 GB de tamanho, tem um LUN de 0, e um tipo de cache de ReadWrite, adicione este código ao bloco de tentativa no método principal:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Eliminar recursos

Por ser cobrado pelos recursos utilizados no Azure, é sempre uma boa prática apagar recursos que já não são necessários. Se pretender eliminar as máquinas virtuais e todos os recursos de suporte, tudo o que tem de fazer é eliminar o grupo de recursos.

1. Para eliminar o grupo de recursos, adicione este código ao bloco de tentativa no método principal:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Guarde o ficheiro .java App.

## <a name="run-the-application"></a>Executar a aplicação

Deve levar cerca de cinco minutos para que esta aplicação da consola corra completamente do início ao fim.

1. Para executar a aplicação, utilize este comando Maven:

    ```
    mvn compile exec:java
    ```

2. Antes de pressionar **Enter** para começar a apagar recursos, pode demorar alguns minutos a verificar a criação dos recursos no portal Azure. Clique no estado de implementação para ver informações sobre a implementação.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a utilização das [bibliotecas Azure para Java.](/java/azure/java-sdk-azure-overview)