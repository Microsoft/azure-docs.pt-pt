---
title: Cargas de trabalho do contentor - Azure Batch | Documentos da Microsoft
description: Saiba como executar aplicações a partir de imagens de contentor no Azure Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 51037e66ec649fc275a746c9f5316b91d82e186a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776276"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicações de contentor no Azure Batch

O Azure Batch permite-lhe executar e dimensionar um grande número de tarefas no Azure de computação do batch. Tarefas do batch podem ser executado diretamente em máquinas virtuais (nós) num conjunto do Batch, mas também pode definir um conjunto do Batch para executar tarefas em contentores compatíveis com o Docker em nós. Este artigo mostra-lhe como criar um conjunto de nós de computação que suporta tarefas de contentor em execução e, em seguida, executar tarefas de contentor no conjunto. 

Deve estar familiarizado com conceitos de contentor e como criar um conjunto do Batch e a tarefa. Os exemplos de código utilizam o .NET do Batch e SDKs de Python. Também pode utilizar outros SDKs do Batch e ferramentas, incluindo o portal do Azure, para criar os conjuntos do Batch habilitados no contentor e para executar tarefas de contentor.

## <a name="why-use-containers"></a>Porquê utilizar contentores?

Através de contentores fornece uma forma fácil de executar tarefas do Batch sem ter de gerir um ambiente e dependências para executar aplicações. Contentores de implantar aplicativos, como unidades de simples, portáteis e autossuficientes que podem ser executadas em vários ambientes diferentes. Por exemplo, criar e testar localmente um contentor, em seguida, carregue a imagem de contentor para um registo no Azure ou noutro local. O modelo de implementação do contentor garante que o ambiente de tempo de execução do seu aplicativo está sempre corretamente instalado e configurado onde quer alojar a aplicação. Tarefas baseadas em contentores no Batch também podem tirar partido das funcionalidades das tarefas de fora do contentor, incluindo pacotes de aplicativos e gerenciamento de arquivos de recursos e ficheiros de saída. 

## <a name="prerequisites"></a>Pré-requisitos

* **Versões do SDK**: Os SDKs do Batch suporta imagens de contentor a partir as seguintes versões:
    * API de REST do batch versão 2017-09-01.6.0
    * SDK de .NET do batch versão 8.0.0
    * SDK de Python do batch versão 4.0
    * Batch Java SDK versão 3.0
    * SDK de node. js do batch versão 3.0

* **Contas**: Na sua subscrição do Azure, terá de criar uma conta do Batch e, opcionalmente, uma conta de armazenamento do Azure.

* **Uma imagem de VM suportada**: Apenas são suportados contentores nos agrupamentos criados com a configuração de Máquina Virtual, a partir de imagens detalhadas na secção seguinte, "suportada imagens da máquina virtual". Se fornecer uma imagem personalizada, veja as considerações na seção a seguir e os requisitos na [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md). 

### <a name="limitations"></a>Limitações

* O batch fornece suporte RDMA, apenas para contentores em execução em conjuntos Linux

* Para cargas de trabalho de contentor Windows, é recomendado para escolher um tamanho de VM com vários núcleos para o seu conjunto

## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual suportadas

Utilize um dos seguintes suportada Windows ou nós para cargas de trabalho do contentor de computação de imagens do Linux para criar um conjunto de VM. Para obter mais informações sobre imagens do Marketplace que são compatíveis com o Batch, consulte [lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho do Windows contentor, o Batch atualmente suporta o **Windows Server 2016 Datacenter com contentores** imagem no Azure Marketplace. Apenas imagens de contentor do Docker são suportadas no Windows.

Também pode criar imagens personalizadas a partir de VMs a executar o Docker no Windows.

### <a name="linux-images"></a>Imagens do Linux

Para cargas de trabalho de contentor Linux, o Batch atualmente suporta as seguintes imagens de Linux, publicadas pela Microsoft Azure Batch no Azure Marketplace:

* **CentOS para conjuntos de contentor do Azure Batch**

* **CentOS (com controladores RDMA) para conjuntos de contentor do Azure Batch**

* **Ubuntu Server para conjuntos de contentor do Azure Batch**

* **Ubuntu Server (com controladores RDMA) para conjuntos de contentor do Azure Batch**

Estas imagens só são suportadas para utilização em conjuntos do Azure Batch. Estes infográficos utilizam:

* Um pré-instaladas [Moby](https://github.com/moby/moby) tempo de execução do contentor 

* Controladores de NVIDIA GPU pré-instalado, para simplificar a implantação em VMs de série N do Azure

* À sua escolha de imagens com ou sem os drivers RDMA previamente instalados. Estes controladores permitem que nós de conjunto acessar a rede de RDMA do Azure quando implantado em tamanhos de VM com capacidade RDMA. 

Também pode criar imagens personalizadas a partir de VMs em execução Docker das distribuições de Linux que é compatível com o Batch. Se optar por fornecer sua própria imagem personalizada do Linux, veja as instruções em [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md).

Para obter suporte do Docker numa imagem personalizada, instale [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Considerações adicionais para utilizar uma imagem personalizada do Linux:

* Para tirar partido do desempenho de GPU de tamanhos de série N do Azure ao utilizar uma imagem personalizada, previamente instalar controladores NVIDIA. Além disso, tem de instalar o utilitário de motor do Docker para GPUs da NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Para aceder à rede de RDMA do Azure, utilize um tamanho de VM com capacidade RDMA. Controladores RDMA necessários são instalados no CentOS HPC e imagens do Ubuntu suportadas pelo Batch. Configuração adicional pode ser necessário para executar cargas de trabalho MPI. Ver [utilização com capacidade RDMA ou ativadas para GPU instâncias num conjunto do Batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuração de contentor para o conjunto do Batch

Para ativar um conjunto do Batch executar cargas de trabalho do contentor, tem de especificar [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) definições do conjunto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) objeto. (Este artigo fornece ligações para a referência de API .NET do Batch. As definições correspondentes estão no [Batch Python](/python/api/azure.batch) API.)

Pode criar um conjunto de habilitados no contentor com ou sem imagens de contentor pré-obtidos, conforme mostrado nos exemplos a seguir. O processo de extração (ou obtenção prévia) permite-lhe pré-carregar imagens de contentor do Docker Hub ou de outro registo de contentor na Internet. Para obter melhor desempenho, utilize um [do Azure container registry](../container-registry/container-registry-intro.md) na mesma região que a conta do Batch.

A vantagem de pré-busca imagens de contentor é que quando tarefas primeiro iniciar a execução não precisam aguardar que a imagem de contentor transferir. A configuração do contentor transmite a imagens de contentor para as VMs quando o conjunto for criado. Tarefas que são executadas no conjunto, em seguida, podem referenciar a lista de imagens de contentor e opções de execução do contentor.


### <a name="pool-without-prefetched-container-images"></a>Conjunto sem imagens de contentor pré-obtidos

Para configurar um conjunto preparado para contentor sem imagens de contentor pré-obtidos, definir `ContainerConfiguration` e `VirtualMachineConfiguration` objetos como mostrado no seguinte exemplo de Python. Este exemplo utiliza o servidor Ubuntu para a imagem de conjuntos de contentor do Azure Batch no Marketplace.


```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            container_configuration=container_conf,
            node_agent_sku_id='batch.node.ubuntu 16.04'),
        vm_size='STANDARD_D1_V2',
        target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Obtenção prévia de imagens para a configuração de contentor

Para obtenção prévia de imagens de contentor no conjunto, adicione a lista de imagens de contentor (`container_image_names`, no Python) para o `ContainerConfiguration`. 

O exemplo de Python básico seguinte mostra como obtenção prévia de uma imagem de contentor do Ubuntu padrão da [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


O seguinte C# exemplo assume que deseja obtenção prévia de uma imagem do TensorFlow partir [Docker Hub](https://hub.docker.com). Este exemplo inclui uma tarefa de início é executado no anfitrião da VM em nós do conjunto. Pode executar uma tarefa de início no anfitrião, por exemplo, para montar um servidor de ficheiros que pode ser acedido a partir dos contentores.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Obtenção prévia de imagens do registo de contentor privado

Pode também obtenção prévia de imagens de contentor ao autenticar a um servidor de registo de contentor privado. No exemplo a seguir, o `ContainerConfiguration` e `VirtualMachineConfiguration` objetos obtenção prévia de uma imagem privada do TensorFlow partir de um registo de contentores do Azure privado. A referência da imagem é o mesmo do exemplo anterior.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Definições de contentor para a tarefa

Para executar uma tarefa de contentor num agrupamento habilitados no contentor, especifique as definições específicas do contentor. As definições incluem a imagem a utilizar, registo e opções de execução do contentor.

* Utilize o `ContainerSettings` propriedade as classes de tarefas para configurar definições específicas do contentor. Estas definições são definidas pelos [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) classe.

* Se executar tarefas em imagens de contentor, o [tarefas na cloud](/dotnet/api/microsoft.azure.batch.cloudtask) e [tarefa do Gestor](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigir definições de contentor. No entanto, o [tarefa de início](/dotnet/api/microsoft.azure.batch.starttask), [tarefa de preparação](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask), e [tarefa de libertação](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não necessitam de definições de contentor (ou seja, eles podem ser executados dentro de um contexto de contentor ou diretamente no nó).

### <a name="container-task-command-line"></a>Linha de comandos de tarefas de contentor

Quando executa uma tarefa de contentor, Batch utiliza automaticamente o [docker criar](https://docs.docker.com/engine/reference/commandline/create/) comando para criar um contentor utilizando a imagem especificada na tarefa. Batch, em seguida, controla a execução da tarefa no contentor. 

Como com tarefas de lote de fora do contentor, defina uma linha de comandos para uma tarefa de contentor. Uma vez que o Batch cria automaticamente o contentor, a linha de comandos especifica apenas o comando ou comandos que serão executados no contentor.

Se a imagem de contentor para uma tarefa do Batch está configurada com um [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) script, pode definir sua linha de comandos para utilizar a predefinição de ponto de entrada ou substituí-la: 

* Para utilizar a predefinição de ponto de entrada da imagem de contentor, defina a linha de comandos de tarefas para a cadeia vazia `""`.

* Para substituir a predefinição de ponto de entrada, ou se a imagem não tiver um ponto de entrada, definir uma linha de comandos adequada para o contentor, por exemplo, `/app/myapp` ou `/bin/sh -c python myscript.py`.

Opcional [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) são argumentos adicionais que indicar para o `docker create` que utiliza o Batch para criar e executar o contentor de comando. Por exemplo, para definir um diretório de trabalho para o contentor, defina o `--workdir <directory>` opção. Consulte a [docker criar](https://docs.docker.com/engine/reference/commandline/create/) referência para obter opções adicionais.

### <a name="container-task-working-directory"></a>Diretório de trabalho do contentor

Uma tarefa de contentor do Batch é executado num diretório de trabalho no contentor que é muito semelhante para o diretório de que batch configura uma tarefa (fora do contentor) regulares. Tenha em atenção que este diretório de trabalho é diferente do [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) se configurado na imagem ou o diretório de trabalho de contentor predefinido (`C:\` num contêiner de Windows, ou `/` no contentor do Linux). 

Para uma tarefa de contentor do Batch:

* Todos os diretórios recursivamente abaixo o `AZ_BATCH_NODE_ROOT_DIR` no anfitrião do nó (a raiz de diretórios do Azure Batch) são mapeados para o contentor
* Todas as variáveis de ambiente de tarefas são mapeadas para o contentor
* Diretório de trabalho `AZ_BATCH_TASK_WORKING_DIR` no nó é definir as mesmas usadas para uma tarefa regular e mapeado para o contentor. 

Estes mapeamentos permitem-lhe trabalhar com tarefas de contentor em quase da mesma forma como as tarefas de fora do contentor. Por exemplo, instalar aplicações com pacotes de aplicações, aceder aos ficheiros de recursos do armazenamento do Azure, utilize as definições de ambiente de tarefa e manter os ficheiros de saída da tarefa depois do contentor para.

### <a name="troubleshoot-container-tasks"></a>Resolver problemas relacionados com tarefas de contentor

Se a sua tarefa do contentor não é executado como esperado, poderá ter obter informações sobre a configuração WORKDIR ou ponto de entrada da imagem do contentor. Para ver a configuração, execute o [inspecionar a imagem do docker](https://docs.docker.com/engine/reference/commandline/image_inspect/) comando. 

Se for necessário, ajuste as definições da tarefa de contentor com base na imagem:

* Especifique um caminho absoluto na linha de comandos de tarefas. Se o padrão da imagem ponto de entrada é utilizado para a linha de comandos de tarefas, certifique-se de que um caminho absoluto está definido.

* Em Opções de execução do contentor da tarefa, altere o diretório de trabalho para fazer corresponder o WORKDIR na imagem. Por exemplo, definir `--workdir /app`.

## <a name="container-task-examples"></a>Exemplos de tarefas do contentor

O fragmento de Python seguinte mostra uma linha de comandos básicos em execução num contentor criado a partir de uma imagem fictícia obtida do Docker Hub. Aqui, o `--rm` opção container remove o contentor, depois de concluída a tarefa e o `--workdir` opção define um diretório de trabalho. A linha de comandos substitui o ponto de entrada de contentor com um comando de shell simples que escreve um pequeno ficheiro para o diretório de trabalho de tarefas no anfitrião. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage', 
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)

```

O exemplo do c# seguinte mostra as definições de contêiner básico para uma tarefa de cloud:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Passos Seguintes

* Consulte também os [Batch Shipyard](https://github.com/Azure/batch-shipyard) Kit de ferramentas para facilitar a implementação de cargas de trabalho de contentor no Azure Batch através de [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Para obter mais informações sobre como instalar e utilizar o Docker CE no Linux, consulte a [Docker](https://docs.docker.com/engine/installation/) documentação.

* Para obter mais informações sobre como utilizar imagens personalizadas, consulte [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md).

* Saiba mais sobre o [Moby projeto](https://mobyproject.org/), uma estrutura para criar sistemas baseados no contentor.