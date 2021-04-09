---
title: Cargas de trabalho de contentor
description: Aprenda a executar e escalar aplicativos a partir de imagens de contentores em Azure Batch. Crie um conjunto de nós computativos que suportem tarefas de contentores em funcionamento.
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91843452"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicações de contentores em Azure Batch

O Azure Batch permite-lhe executar e escalar um grande número de trabalhos de computação em lotes em Azure. As tarefas de lote podem ser executadas diretamente em máquinas virtuais (nós) numa piscina de Lote, mas também pode configurar um pool de Lote para executar tarefas em recipientes compatíveis com Docker nos nós. Este artigo mostra-lhe como criar um conjunto de nós de computação que suportam tarefas de contentores em execução e, em seguida, executar tarefas de contentores na piscina.

Os exemplos de código aqui utilizam os SDKs Batch .NET e Python. Também pode utilizar outros SDKs e ferramentas de lote, incluindo o portal Azure, para criar piscinas de lote ativadas por contentores e executar tarefas de contentores.

## <a name="why-use-containers"></a>Por que usar recipientes?

A utilização de recipientes proporciona uma forma fácil de executar tarefas de Lote sem ter de gerir um ambiente e dependências para executar aplicações. Os contentores implementam aplicações como unidades leves, portáteis e autossuficientes que podem funcionar em vários ambientes diferentes. Por exemplo, construa e teste um recipiente localmente e, em seguida, carrede a imagem do recipiente para um registo em Azure ou em qualquer outro lugar. O modelo de implantação do contentor garante que o ambiente de funcionamento da sua aplicação está sempre corretamente instalado e configurado onde quer que se hospeda a aplicação. As tarefas baseadas em contentores no Batch também podem tirar partido das funcionalidades de tarefas não contentores, incluindo pacotes de aplicações e gestão de ficheiros de recursos e ficheiros de saída.

## <a name="prerequisites"></a>Pré-requisitos

Você deve estar familiarizado com os conceitos de recipiente e como criar uma piscina e trabalho batch.

- **Versões SDK**: Os SDKs do lote suportam imagens de contentores a partir das seguintes versões:
  - Lote REST Versão API 2017-09-01.6.0
  - Lote .NET SDK versão 8.0.0
  - Lote Python SDK versão 4.0
  - Lote Java SDK versão 3.0
  - Lote Node.js versão SDK 3.0

- **Contas**: Na sua subscrição Azure, precisa de criar uma conta Batch e opcionalmente uma conta de Armazenamento Azure.

- **Uma imagem VM suportada**: Os recipientes só são suportados em piscinas criadas com a Configuração da Máquina Virtual, a partir de imagens detalhadas na secção seguinte, "Imagens de máquina virtual suportadas". Se fornecer uma imagem personalizada, consulte as considerações na secção seguinte e os requisitos na [Utilização de uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md).

Tenha em mente as seguintes limitações:

- O lote fornece suporte RDMA apenas para contentores que andam em piscinas Linux.

- Para a carga de trabalho do contentor do Windows, recomendamos a escolha de um tamanho VM multicore para a sua piscina.

## <a name="supported-virtual-machine-images"></a>Imagens de máquinas virtuais suportadas

Utilize uma das seguintes imagens suportadas do Windows ou Linux para criar um conjunto de nós de computação VM para cargas de trabalho de contentores. Para obter mais informações sobre imagens do Marketplace compatíveis com o Batch, consulte [lista de imagens de máquinas virtuais.](batch-linux-nodes.md#list-of-virtual-machine-images)

### <a name="windows-support"></a>Suporte ao Windows

O lote suporta imagens do servidor Windows que têm designações de suporte a contentores. Tipicamente, estes nomes de sku de imagem são sufixados com `-with-containers` ou `-with-containers-smalldisk` . Além disso, [a API para listar todas as imagens suportadas em Batch](batch-linux-nodes.md#list-of-virtual-machine-images) irá denotar uma capacidade se a imagem suportar os `DockerCompatible` recipientes Docker.

Também pode criar imagens personalizadas a partir de VMs que executam Docker no Windows.

### <a name="linux-support"></a>Apoio Técnico para Linux

Para as cargas de trabalho dos contentores Linux, o Batch suporta atualmente as seguintes imagens linux publicadas pela Microsoft Azure Batch no Azure Marketplace sem a necessidade de uma imagem personalizada.

#### <a name="vm-sizes-without-rdma"></a>Tamanhos VM sem RDMA

- Editor: `microsoft-azure-batch`
  - Oferta: `centos-container`
  - Oferta: `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Tamanhos VM com RDMA

- Editor: `microsoft-azure-batch`
  - Oferta: `centos-container-rdma`
  - Oferta: `ubuntu-server-container-rdma`

Estas imagens são suportadas apenas para serem utilizadas nas piscinas do Azure Batch e destinam-se à execução do contentor Docker. Apresentam:

- Um tempo de funcionatório de contentor [Moby](https://github.com/moby/moby) pré-instalado compatível com Docker

- Controladores GPU pré-instalados da NVIDIA e tempo de funcionamento do contentor NVIDIA, para dinamizar a implantação em VMs da série Azure N

- Imagem pré-instalada/pré-configurada com suporte para tamanhos VM Infiniband RDMA para imagens com o sufixo de `-rdma` . Atualmente, estas imagens não suportam tamanhos SR-IOV IB/RDMA VM.

Também pode criar imagens personalizadas a partir de VMs que executam Docker numa das distribuições linux que é compatível com o Batch. Se optar por fornecer a sua própria imagem personalizada do Linux, consulte as instruções em [Utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais.](batch-custom-images.md)

Para suporte docker em uma imagem personalizada, instale [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Considerações adicionais para a utilização de uma imagem linux personalizada:

- Para tirar partido do desempenho da GPU dos tamanhos da série Azure N ao utilizar uma imagem personalizada, pré-instalar os controladores NVIDIA. Além disso, você precisa instalar o Utilitário de Motor Docker para GPUs NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

- Para aceder à rede Azure RDMA, utilize um tamanho VM capaz de RDMA. Os controladores RDMA necessários são instalados nas imagens CentOS HPC e Ubuntu suportadas por Batch. Pode ser necessária uma configuração adicional para executar cargas de trabalho de MPI. Consulte [a utilização de casos com capacidade de RDMA ou GPU na piscina batch](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Configuração do recipiente para piscina de lote

Para permitir que uma piscina de lote possa executar cargas de trabalho de [contentores,](/dotnet/api/microsoft.azure.batch.containerconfiguration) deve especificar as definições de Configuração de Contentores no objeto virtual de [configuração](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) da piscina. (Este artigo fornece ligações à referência API do Lote .NET. As definições correspondentes estão no [Lote Python](/python/api/overview/azure/batch) API.)

Pode criar uma piscina ativada por contentores com ou sem imagens de recipientes pré-gravadas, como mostram os seguintes exemplos. O processo de puxar (ou prefetch) permite-lhe pré-carregar imagens de contentores do Docker Hub ou de outro registo de contentores na Internet. Para obter um melhor desempenho, utilize um [registo de contentores Azure](../container-registry/container-registry-intro.md) na mesma região que a conta Batch.

A vantagem de pré-gravar imagens de contentores é que quando as tarefas começam a funcionar pela primeira vez não têm de esperar que a imagem do contentor descarregue. A configuração do recipiente puxa as imagens do contentor para os VMs quando a piscina é criada. As tarefas que funcionam na piscina podem então referenciar a lista de imagens de contentores e opções de execução do contentor.

### <a name="pool-without-prefetched-container-images"></a>Piscina sem imagens de contentores pré-rebuscados

Para configurar uma piscina ativada por contentores sem imagens de recipientes pré-gravadas, defina `ContainerConfiguration` e `VirtualMachineConfiguration` os objetos como mostrado nos seguintes exemplos. Estes exemplos utilizam a imagem do Ubuntu Server para o Azure Batch pools do Marketplace.

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

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>Imagens prefetch para configuração do recipiente

Para pré-prender imagens de contentores na piscina, adicione a lista de imagens de contentores `container_image_names` (, em Python) ao `ContainerConfiguration` .

O exemplo básico de Python mostra como preterrear uma imagem padrão do recipiente Ubuntu do [Docker Hub](https://hub.docker.com).

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

O exemplo C# a seguir pressupõe que pretende prefetuar uma imagem tensorFlow do [Docker Hub](https://hub.docker.com). Este exemplo inclui uma tarefa inicial que funciona no anfitrião VM nos nós da piscina. Pode executar uma tarefa inicial no anfitrião, por exemplo, para montar um servidor de ficheiros que possa ser acedido a partir dos contentores.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>Imagens prefetch de um registo de contentores privados

Também pode pré-prender imagens de contentores autenticando-se num servidor de registo de contentores privados. Nos exemplos seguintes, os `ContainerConfiguration` `VirtualMachineConfiguration` e objetos prefamem uma imagem privada do TensorFlow a partir de um registo privado de contentores Azure. A referência de imagem é a mesma que no exemplo anterior.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Configurações do contentor para a tarefa

Para executar uma tarefa de contentor numa piscina ativada por contentores, especifique as definições específicas do contentor. As definições incluem a imagem a utilizar, o registo e as opções de execução do contentor.

- Utilize a `ContainerSettings` propriedade das classes de tarefas para configurar configurações específicas do contentor. Estas definições são definidas pela classe [TaskContainerSettings.](/dotnet/api/microsoft.azure.batch.taskcontainersettings) Note que a `--rm` opção do recipiente não requer uma `--runtime` opção adicional, uma vez que é cuidada pelo Batch.

- Se executar tarefas em imagens de contentores, a [tarefa](/dotnet/api/microsoft.azure.batch.cloudtask) de cloud e [de gestor de emprego](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) requer configurações de contentores. No entanto, a [tarefa inicial](/dotnet/api/microsoft.azure.batch.starttask), [a tarefa de preparação](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)do trabalho e a tarefa de [libertação](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) de emprego não requerem configurações de contentores (isto é, podem funcionar dentro de um contexto de contentor ou diretamente no nó).

- Para o Windows, as tarefas devem ser executadas com [o ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) definido para `admin` . 

- Para o Linux, o Batch mapeará a permissão do utilizador/grupo para o recipiente. Se o acesso a qualquer pasta dentro do recipiente necessitar de permissão do Administrador, poderá ter de executar a tarefa como âmbito de piscina com nível de elevação de administração. Isto garantirá que o Batch executa a tarefa como raiz no contexto do recipiente. Caso contrário, um utilizador não administrativo pode não ter acesso a essas pastas.

- Para piscinas de contentores com hardware ativado pela GPU, o Batch ativará automaticamente a GPU para tarefas de contentores, pelo que não deve incluir o `–gpus` argumento.

### <a name="container-task-command-line"></a>Linha de comando de tarefa de contentor

Quando executa uma tarefa de contentor, o Batch utiliza automaticamente o comando de criar o [estivador](https://docs.docker.com/engine/reference/commandline/create/) para criar um recipiente utilizando a imagem especificada na tarefa. Em seguida, o lote controla a execução da tarefa no recipiente.

Tal como acontece com as tarefas de lote não contentor, define uma linha de comando para uma tarefa de contentor. Como o Batch cria automaticamente o recipiente, a linha de comando apenas especifica o comando ou comandos que irão funcionar no contentor.

Se a imagem do recipiente para uma tarefa de Lote estiver configurada com um script [DE PONTO DE ENTRADA,](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) pode definir a sua linha de comando para utilizar o PONTO de ENTRADA predefinido ou sobrerigem-lo:

- Para utilizar o PONTO de ENTRADA predefinido da imagem do recipiente, deite a linha de comando de tarefa para a cadeia vazia `""` .

- Para anular o PONTO DE ENTRADA predefinido, ou se a imagem não tiver um PONTO DE ENTRADA, deite uma linha de comando adequada para o recipiente, por exemplo, `/app/myapp` ou `/bin/sh -c python myscript.py` .

Opcionais [DeOps de Contentores](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) são argumentos adicionais que fornece ao comando que o `docker create` Batch usa para criar e executar o recipiente. Por exemplo, para definir um diretório de trabalho para o recipiente, desate a `--workdir <directory>` opção. Consulte o [estivador para criar](https://docs.docker.com/engine/reference/commandline/create/) referência para opções adicionais.

### <a name="container-task-working-directory"></a>Diretório de trabalho de tarefa de contentor

Uma tarefa de contentor batch executa num diretório de trabalho no contentor muito semelhante ao diretório que o Batche configura para uma tarefa regular (não contentor). Note que este diretório de trabalho é diferente do [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) se configurado na imagem, ou no diretório de trabalho do contentor predefinido `C:\`  (num recipiente Windows ou `/` num recipiente Linux).

Para uma tarefa de contentor de lote:

- Todos os diretórios recursivamente abaixo `AZ_BATCH_NODE_ROOT_DIR` do nó de anfitrião (raiz de Azure Batch diretórios) são mapeados no recipiente
- Todas as variáveis do ambiente de tarefa são mapeadas no recipiente
- O diretório de trabalho da tarefa `AZ_BATCH_TASK_WORKING_DIR` no nó é definido da mesma forma que para uma tarefa regular e mapeado no recipiente.

Estes mapeamentos permitem-lhe trabalhar com tarefas de contentores da mesma forma que tarefas não contentores. Por exemplo, instale aplicações utilizando pacotes de aplicações, aceda aos ficheiros de recursos do Azure Storage, utilize definições de ambiente de tarefas e persista ficheiros de saída de tarefas após a paragem do contentor.

### <a name="troubleshoot-container-tasks"></a>Tarefas de contentor de resolução de problemas

Se a tarefa do seu contentor não funcionar como esperado, poderá ter de obter informações sobre a configuração WORKDIR ou ENTRYPOINT da imagem do recipiente. Para ver a configuração, verifique o comando de inspeção da imagem do [estivador.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

Se necessário, ajuste as definições da tarefa do recipiente com base na imagem:

- Especifique um caminho absoluto na linha de comando de tarefa. Se o PONTO DE ENTRADA predefinido da imagem for utilizado para a linha de comando de tarefa, certifique-se de que é definido um caminho absoluto.
- Nas opções de funcionamento do contentor da tarefa, altere o diretório de trabalho para corresponder ao WORKDIR na imagem. Por exemplo, definir `--workdir /app` .

## <a name="container-task-examples"></a>Exemplos de tarefas do contentor

O seguinte snippet Python mostra uma linha de comando básica correndo em um recipiente criado a partir de uma imagem fictícia retirada de Docker Hub. Aqui, a opção do `--rm` recipiente remove o recipiente após o fim da tarefa e a `--workdir` opção define um diretório de trabalho. A linha de comando sobrepõe-se ao ponto de entrada do recipiente com um simples comando de concha que escreve um pequeno ficheiro para o diretório de trabalho da tarefa no anfitrião.

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

O exemplo C# a seguir mostra as definições básicas do recipiente para uma tarefa em nuvem:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Passos seguintes

- Para facilitar a implantação das cargas de trabalho dos contentores no Azure Batch através [das receitas do Estaleiro,](https://github.com/Azure/batch-shipyard/tree/master/recipes)consulte o conjunto de ferramentas [do Estaleiro batch.](https://github.com/Azure/batch-shipyard)
- Para obter informações sobre a instalação e utilização do Docker CE no Linux, consulte a documentação do [Docker.](https://docs.docker.com/engine/installation/)
- Saiba como [utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais.](batch-custom-images.md)
- Saiba mais sobre o [projeto Moby,](https://mobyproject.org/)uma estrutura para a criação de sistemas baseados em contentores.
