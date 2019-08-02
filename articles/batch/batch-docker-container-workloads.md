---
title: Cargas de trabalho do contêiner – lote do Azure | Microsoft Docs
description: Saiba como executar aplicativos de imagens de contêiner no lote do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: edf4ce2be451672ecbd4f732c3110617dc122ca0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323589"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicativos de contêiner no lote do Azure

O lote do Azure permite que você execute e dimensione um grande número de trabalhos de computação em lotes no Azure. As tarefas do lote podem ser executadas diretamente em máquinas virtuais (nós) em um pool do lote, mas você também pode configurar um pool do lote para executar tarefas em contêineres compatíveis com o Docker nos nós. Este artigo mostra como criar um pool de nós de computação que dão suporte a tarefas de contêiner em execução e, em seguida, executar tarefas de contêiner no pool. 

Você deve estar familiarizado com os conceitos de contêiner e como criar um pool e um trabalho do lote. Os exemplos de código usam os SDKs .NET e Python do lote. Você também pode usar outros SDKs e ferramentas do lote, incluindo o portal do Azure, para criar pools de lote habilitados para contêiner e para executar tarefas de contêiner.

## <a name="why-use-containers"></a>Por que usar contêineres?

O uso de contêineres fornece uma maneira fácil de executar tarefas em lote sem precisar gerenciar um ambiente e dependências para executar aplicativos. Os contêineres implantam aplicativos como unidades leves, portáteis e autônomas que podem ser executadas em vários ambientes diferentes. Por exemplo, crie e teste um contêiner localmente e, em seguida, carregue a imagem de contêiner em um registro no Azure ou em outro lugar. O modelo de implantação de contêiner garante que o ambiente de tempo de execução do seu aplicativo sempre seja corretamente instalado e configurado sempre que você hospedar o aplicativo. As tarefas baseadas em contêiner no lote também podem aproveitar os recursos de tarefas que não são do contêiner, incluindo pacotes de aplicativos e gerenciamento de arquivos de recursos e arquivos de saída. 

## <a name="prerequisites"></a>Pré-requisitos

* **Versões do SDK**: Os SDKs do lote dão suporte a imagens de contêiner a partir das seguintes versões:
    * API REST do lote versão 2017 -09-01.6.0
    * SDK do .NET do lote versão 8.0.0
    * SDK do Python do lote versão 4,0
    * SDK do Java do lote versão 3,0
    * SDK do node. js do lote versão 3,0

* **Contas**: Em sua assinatura do Azure, você precisa criar uma conta do lote e, opcionalmente, uma conta de armazenamento do Azure.

* **Uma imagem de VM com suporte**: Os contêineres têm suporte apenas em pools criados com a configuração de máquina virtual, de imagens detalhadas na seção a seguir, "imagens de máquina virtual com suporte". Se você fornecer uma imagem personalizada, consulte as considerações na seção a seguir e os requisitos em [usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md). 

### <a name="limitations"></a>Limitações

* O lote fornece suporte a RDMA somente para contêineres em execução em pools do Linux

* Para cargas de trabalho de contêiner do Windows, é recomendável escolher um tamanho de VM de vários núcleos para seu pool

## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual com suporte

Use uma das seguintes imagens do Windows ou do Linux com suporte para criar um pool de nós de computação da VM para cargas de trabalho de contêiner. Para obter mais informações sobre imagens do Marketplace que são compatíveis com o lote, consulte [lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images). 

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho de contêiner do Windows, o lote dá suporte atualmente à imagem do **Windows Server 2016 datacenter com contêineres** no Azure Marketplace. Somente as imagens de contêiner do Docker têm suporte no Windows.

Você também pode criar imagens personalizadas de VMs que executam o Docker no Windows.

### <a name="linux-images"></a>Imagens do Linux

Para cargas de trabalho de contêiner do Linux, o lote atualmente dá suporte às seguintes imagens do Linux publicadas por Lote do Microsoft Azure no Azure Marketplace:

* **CentOS para pools de contêiner do lote do Azure**

* **CentOS (com drivers RDMA) para pools de contêineres do lote do Azure**

* **Servidor Ubuntu para pools de contêiner do lote do Azure**

* **Servidor Ubuntu (com drivers RDMA) para pools de contêineres do lote do Azure**

Essas imagens só têm suporte para uso em pools do lote do Azure. Eles apresentam:

* Um tempo de execução de contêiner [Moby](https://github.com/moby/moby) pré-instalado 

* Drivers NVIDIA GPU pré-instalados para simplificar a implantação nas VMs da série N do Azure

* Sua escolha de imagens com ou sem drivers RDMA pré-instalados. Esses drivers permitem que nós de pool acessem a rede RDMA do Azure quando implantados em tamanhos de VM compatíveis com RDMA. 

Você também pode criar imagens personalizadas de VMs que executam o Docker em uma das distribuições do Linux que são compatíveis com o lote. Se você optar por fornecer sua própria imagem personalizada do Linux, consulte as instruções em [usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

Para suporte do Docker em uma imagem personalizada, instale o [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou o [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Considerações adicionais para usar uma imagem personalizada do Linux:

* Para aproveitar o desempenho da GPU dos tamanhos da série N do Azure ao usar uma imagem personalizada, pré-instale os drivers NVIDIA. Além disso, você precisa instalar o utilitário do mecanismo do Docker para GPUs NVIDIA, o [Docker NVIDIA](https://github.com/NVIDIA/nvidia-docker).

* Para acessar a rede RDMA do Azure, use um tamanho de VM compatível com RDMA. Os drivers RDMA necessários são instalados nas imagens CentOS HPC e Ubuntu com suporte do lote. Pode ser necessária configuração adicional para executar cargas de trabalho MPI. Consulte [usar instâncias compatíveis com RDMA ou habilitadas para GPU no pool do lote](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuração de contêiner para pool do lote

Para habilitar um pool do lote para executar cargas de trabalho de contêiner, você deve especificar as configurações de [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) no objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) do pool. (Este artigo fornece links para a referência da API .NET do lote. As configurações correspondentes estão na API do [Python do lote](/python/api/azure.batch) .)

Você pode criar um pool habilitado para contêiner com ou sem imagens de contêiner de pré-busca, conforme mostrado nos exemplos a seguir. O processo de pull (ou prefetch) permite carregar previamente as imagens de contêiner do Hub do Docker ou de outro registro de contêiner na Internet. Para obter o melhor desempenho, use um [registro de contêiner do Azure](../container-registry/container-registry-intro.md) na mesma região que a conta do lote.

A vantagem da pré-busca de imagens de contêiner é que, quando as tarefas começam a ser executadas, eles não precisam aguardar a imagem de contêiner ser baixada. A configuração do contêiner extrai imagens de contêiner para as VMs quando o pool é criado. As tarefas que são executadas no pool podem fazer referência à lista de imagens de contêiner e opções de execução de contêiner.


### <a name="pool-without-prefetched-container-images"></a>Pool sem imagens de contêiner de pré-busca

Para configurar um pool habilitado para contêiner sem imagens de contêiner de pré-busca, defina `ContainerConfiguration` e `VirtualMachineConfiguration` objetos, conforme mostrado no exemplo de Python a seguir. Este exemplo usa o servidor Ubuntu para a imagem de pools de contêineres do lote do Azure do Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Imagens de pré-busca para configuração de contêiner

Para realizar a pré-busca de imagens de contêiner no pool, adicione a lista de`container_image_names`imagens de contêiner (, em `ContainerConfiguration`Python) ao. 

O exemplo de Python básico a seguir mostra como fazer uma pré-busca de uma imagem de contêiner do Ubuntu padrão do [Hub](https://hub.docker.com)do Docker.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

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


O exemplo C# a seguir pressupõe que você deseja prefetch de uma imagem TensorFlow do [Hub](https://hub.docker.com)do Docker. Este exemplo inclui uma tarefa inicial que é executada no host da VM nos nós do pool. Você pode executar uma tarefa de inicialização no host, por exemplo, para montar um servidor de arquivos que pode ser acessado por meio dos contêineres.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Imagens de pré-busca de um registro de contêiner privado

Você também pode fazer prefetch de imagens de contêiner Autenticando em um servidor de registro de contêiner privado. No exemplo a seguir, os `ContainerConfiguration` objetos `VirtualMachineConfiguration` e buscam uma imagem TensorFlow privada de um registro de contêiner do Azure privado. A referência de imagem é a mesma do exemplo anterior.

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

## <a name="container-settings-for-the-task"></a>Configurações de contêiner para a tarefa

Para executar uma tarefa de contêiner em um pool habilitado para contêiner, especifique as configurações específicas do contêiner. As configurações incluem a imagem a ser usada, o registro e as opções de execução do contêiner.

* Use a `ContainerSettings` propriedade das classes de tarefa para definir configurações específicas de contêiner. Essas configurações são definidas pela classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) .

* Se você executar tarefas em imagens de contêiner, a tarefa de [nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e o [Gerenciador de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigirão configurações de contêiner. No entanto, a tarefa de [início](/dotnet/api/microsoft.azure.batch.starttask), a tarefa de [preparação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)e a tarefa de [liberação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não exigem configurações de contêiner (ou seja, elas podem ser executadas dentro de um contexto de contêiner ou diretamente no nó).

### <a name="container-task-command-line"></a>Linha de comando da tarefa de contêiner

Quando você executa uma tarefa de contêiner, o lote usa automaticamente o comando [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) para criar um contêiner usando a imagem especificada na tarefa. Em seguida, o lote controla a execução da tarefa no contêiner. 

Assim como acontece com as tarefas de lote que não são de contêiner, você define uma linha de comando para uma tarefa de contêiner. Como o lote cria automaticamente o contêiner, a linha de comando especifica apenas o comando ou comandos que serão executados no contêiner.

Se a imagem de contêiner de uma tarefa de lote for configurada com um script de [ponto de entrada](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) , você poderá definir a linha de comando para usar o ponto de entrada padrão ou substituí-lo: 

* Para usar o ponto de entrada padrão da imagem de contêiner, defina a linha de comando da tarefa `""`como a cadeia de caracteres vazia.

* Para substituir o ponto de entrada padrão, ou se a imagem não tiver um ponto de entrada, defina uma linha de comando apropriada para o `/app/myapp` contêiner `/bin/sh -c python myscript.py`, por exemplo, ou.

[ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) opcionais são argumentos adicionais que você fornece `docker create` ao comando que o lote usa para criar e executar o contêiner. Por exemplo, para definir um diretório de trabalho para o contêiner, defina `--workdir <directory>` a opção. Consulte a referência de [criação](https://docs.docker.com/engine/reference/commandline/create/) do Docker para obter opções adicionais.

### <a name="container-task-working-directory"></a>Diretório de trabalho da tarefa do contêiner

Uma tarefa de contêiner de lote é executada em um diretório de trabalho no contêiner que é muito semelhante ao do diretório é configurado para uma tarefa regular (não contêiner). Observe que esse diretório de trabalho é diferente do [WorkDir](https://docs.docker.com/engine/reference/builder/#workdir) se estiver configurado na imagem ou do diretório de trabalho do contêiner padrão`C:\` (em um contêiner do Windows `/` ou em um contêiner do Linux). 

Para uma tarefa de contêiner de lote:

* Todos os diretórios recursivamente abaixo `AZ_BATCH_NODE_ROOT_DIR` do no nó do host (a raiz dos diretórios do lote do Azure) são mapeados para o contêiner
* Todas as variáveis de ambiente de tarefa são mapeadas para o contêiner
* O diretório `AZ_BATCH_TASK_WORKING_DIR` de trabalho da tarefa no nó é definido da mesma forma que uma tarefa regular e é mapeado para o contêiner. 

Esses mapeamentos permitem que você trabalhe com tarefas de contêiner praticamente da mesma forma que as tarefas que não são do contêiner. Por exemplo, instale aplicativos usando pacotes de aplicativos, acesse arquivos de recursos do armazenamento do Azure, use configurações de ambiente de tarefas e mantenha arquivos de saída de tarefas depois que o contêiner for interrompido.

### <a name="troubleshoot-container-tasks"></a>Solucionar problemas de tarefas de contêiner

Se a tarefa de contêiner não for executada conforme o esperado, talvez seja necessário obter informações sobre a configuração de WORKDIR ou ENTRYPOINT da imagem de contêiner. Para ver a configuração, execute o comando [Docker Image eninspecione](https://docs.docker.com/engine/reference/commandline/image_inspect/) . 

Se necessário, ajuste as configurações da tarefa de contêiner com base na imagem:

* Especifique um caminho absoluto na linha de comando da tarefa. Se o ponto de entrada padrão da imagem for usado para a linha de comando da tarefa, verifique se um caminho absoluto está definido.

* Nas opções de execução de contêiner da tarefa, altere o diretório de trabalho para corresponder ao WORKDIR na imagem. Por exemplo, defina `--workdir /app`.

## <a name="container-task-examples"></a>Exemplos de tarefas de contêiner

O seguinte trecho do Python mostra uma linha de comando básica em execução em um contêiner criado com base em uma imagem fictícia extraída do Hub do Docker. Aqui, a `--rm` opção de contêiner remove o contêiner após a conclusão da tarefa e `--workdir` a opção define um diretório de trabalho. A linha de comando substitui o ponto de entrada do contêiner por um comando simples do shell que grava um arquivo pequeno no diretório de trabalho da tarefa no host. 

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

O exemplo C# a seguir mostra as configurações básicas de contêiner para uma tarefa de nuvem:

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

* Consulte também o [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit para facilitar a implantação de cargas de trabalho de contêiner no lote do Azure por meio de [receitas Shipyards](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Para obter mais informações sobre como instalar e usar o Docker CE no Linux [](https://docs.docker.com/engine/installation/) , consulte a documentação do Docker.

* Para obter mais informações sobre como usar imagens personalizadas, consulte [usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

* Saiba mais sobre o [projeto Moby](https://mobyproject.org/), uma estrutura para a criação de sistemas baseados em contêiner.