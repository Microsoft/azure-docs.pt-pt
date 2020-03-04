---
title: Cargas de trabalho de contentores - Lote Azure
description: Aprenda a executar e dimensionar aplicações a partir de imagens de contentores no Lote Azure. Crie um conjunto de nódosos computacionais que suportem tarefas de contentores em execução.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254847"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicações de contentores em Lote Azure

O Azure Batch permite-lhe executar e escalar um grande número de trabalhos de computação em lotes no Azure. As tarefas do lote podem ser executadas diretamente em máquinas virtuais (nós) numa piscina de lote, mas também pode configurar uma piscina de lote para executar tarefas em recipientes compatíveis com docker nos nós. Este artigo mostra-lhe como criar um conjunto de nós de computação que suportam tarefas de contentores em execução e, em seguida, executar tarefas de contentores na piscina.

Você deve estar familiarizado com conceitos de recipientes e como criar uma piscina e trabalho de lote. Os exemplos de código usam os SDKs Batch .NET e Python. Também pode utilizar outros SDKs e ferramentas de lote, incluindo o portal Azure, para criar piscinas de lote ativadas por contentores e executar tarefas de contentores.

## <a name="why-use-containers"></a>Por que usar contentores?

A utilização de recipientes proporciona uma maneira fácil de executar tarefas do Lote sem ter de gerir um ambiente e dependências para executar aplicações. Os contentores implantam aplicações como unidades leves, portáteis e autossuficientes que podem funcionar em vários ambientes diferentes. Por exemplo, construir e testar um recipiente localmente, em seguida, fazer upload da imagem do recipiente para um registo em Azure ou em qualquer outro lugar. O modelo de implantação do recipiente garante que o ambiente de tempo de funcionamento da sua aplicação está sempre corretamente instalado e configurado onde quer que apresente a aplicação. As tarefas baseadas em contentores no Batch também podem tirar partido das funcionalidades de tarefas não contentores, incluindo pacotes de aplicações e gestão de ficheiros de recursos e ficheiros de saída.

## <a name="prerequisites"></a>Pré-requisitos

* **Versões SDK**: Os SDKs de lote suportam imagens de contentores a partir das seguintes versões:
    * Lote REST API versão 2017-09-01.6.0
    * Lote .NET SDK versão 8.0.0
    * Versão SDK de Lote Python 4.0
    * Lote Java SDK versão 3.0
    * Batch Node.js SDK versão 3.0

* **Contas**: Na subscrição do Azure, é necessário criar uma conta Batch e opcionalmente uma conta de Armazenamento Azure.

* **Uma imagem VM suportada**: Os contentores só são suportados em piscinas criadas com a Configuração da Máquina Virtual, a partir de imagens detalhadas na secção seguinte, "Imagens de máquinas virtuais suportadas". Se fornecer uma imagem personalizada, consulte as considerações na secção seguinte e os requisitos em [Utilizar uma imagem personalizada gerida para criar um conjunto de máquinas virtuais](batch-custom-images.md).

### <a name="limitations"></a>Limitações

* Lote fornece suporte RDMA apenas para contentores em funcionamento em piscinas linux

* Para cargas de trabalho de contentores windows, é aconselhável escolher um tamanho VM multicore para a sua piscina

## <a name="supported-virtual-machine-images"></a>Imagens de máquinas virtuais suportadas

Utilize uma das seguintes imagens suportadas pelo Windows ou Linux para criar uma piscina de nódeos computacionais VM para cargas de trabalho de contentores. Para obter mais informações sobre imagens do Marketplace compatíveis com o Batch, consulte a [lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Suporte para janelas

O lote suporta imagens do servidor do Windows que têm designações de suporte de contentores. Normalmente, estes nomes de sku de imagem são sufixos com `-with-containers` ou `-with-containers-smalldisk`. Além disso, [a API para listar todas as imagens suportadas no Batch](batch-linux-nodes.md#list-of-virtual-machine-images) irá denotar uma capacidade de `DockerCompatible` se a imagem suportar os contentores Docker.

Também pode criar imagens personalizadas a partir de VMs que executam o Docker no Windows.

### <a name="linux-support"></a>Apoio Técnico para Linux

Para as cargas de trabalho de contentores Linux, o Batch suporta atualmente as seguintes imagens linux publicadas pelo Microsoft Azure Batch no Azure Marketplace sem necessidade de uma imagem personalizada.

#### <a name="vm-sizes-without-rdma"></a>Tamanhos VM sem RDMA

- Editor: `microsoft-azure-batch`
  - Oferta: `centos-container`
  - Oferta: `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>Tamanhos VM com RDMA

- Editor: `microsoft-azure-batch`
  - Oferta: `centos-container-rdma`
  - Oferta: `ubuntu-server-container-rdma`

Estas imagens são suportadas apenas para utilização em piscinas de Lote Azure e são orientadas para a execução de contentores Docker. Apresentam:

* Um tempo de execução de contentor [Moby](https://github.com/moby/moby) pré-instalado

* Condutores de GPU da NVIDIA pré-instalados e tempo de execução de contentores NVIDIA, para agilizar a implantação em VMs da série N Azure

* Imagem pré-instalada/pré-configurada com suporte para tamanhos de VM RDMA de Infiniband para imagens com o sufixo de `-rdma`. Atualmente, estas imagens não suportam tamanhos vm SR-IOV IB/RDMA.

Também pode criar imagens personalizadas de VMs executando Docker numa das distribuições Linux que é compatível com o Batch. Se optar por fornecer a sua própria imagem personalizada de Linux, consulte as instruções em [Utilizar uma imagem personalizada gerida para criar uma piscina de máquinas virtuais](batch-custom-images.md).

Para suporte docker em uma imagem personalizada, instale [Docker Community Edition (CE)](https://www.docker.com/community-edition) ou [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Considerações adicionais para o uso de uma imagem personalizada de Linux:

* Para aproveitar o desempenho da GPU dos tamanhos da série Azure N ao utilizar uma imagem personalizada, pré-instalar os controladores NVIDIA. Além disso, precisa de instalar o Utilitário de Motor Docker para GPUs NVIDIA, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Para aceder à rede Azure RDMA, utilize um tamanho VM capaz de RDMA. Os controladores RDMA necessários são instalados nas imagens CentOS HPC e Ubuntu suportadas pelo Batch. Pode ser necessária uma configuração adicional para executar cargas de trabalho de MPI. Consulte [As instâncias de utilização de RDMA ou gpu ativadas em pool batch](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Configuração do recipiente para piscina de lote

Para permitir que uma piscina de lote execute as cargas de trabalho do contentor, deve especificar as definições de Configuração de [Contentores](/dotnet/api/microsoft.azure.batch.containerconfiguration) no objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) da piscina. (Este artigo fornece ligações à referência aAPI do Lote .NET. As configurações correspondentes estão no [Batch Python](/python/api/overview/azure/batch) API.)

Pode criar uma piscina ativada por contentores com ou sem imagens de contentores pré-rebuscadas, como mostram os seguintes exemplos. O processo de pull (ou prefetch) permite-lhe pré-carregar imagens de contentores do Docker Hub ou de outro registo de contentores na Internet. Para um melhor desempenho, utilize um registo de [contentores Azure](../container-registry/container-registry-intro.md) na mesma região que a conta Batch.

A vantagem de pré-rebuscar imagens de contentores é que quando as tarefas começam a funcionar pela primeira vez não têm de esperar que a imagem do recipiente descarregue. A configuração do recipiente puxa imagens do recipiente para os VMs quando a piscina é criada. As tarefas que funcionam na piscina podem então fazer referência à lista de imagens de contentores e opções de execução de contentores.


### <a name="pool-without-prefetched-container-images"></a>Piscina sem imagens de recipientes pré-rebuscados

Para configurar uma piscina ativada por recipientes sem imagens de recipientes pré-rebuscadas, defina `ContainerConfiguration` e `VirtualMachineConfiguration` objetos, como mostra o exemplo python seguinte. Este exemplo utiliza o Ubuntu Server para o conjunto de contentores Do Lote Azure imagem do Marketplace.


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


### <a name="prefetch-images-for-container-configuration"></a>Imagens de prefetch para configuração do recipiente

Para pré-rebuscar imagens de contentores na piscina, adicione a lista de imagens de contentores (`container_image_names`, em Python) ao `ContainerConfiguration`.

O exemplo básico de Python mostra como pré-obter uma imagem de recipiente Ubuntu padrão de [Docker Hub](https://hub.docker.com).

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


O C# exemplo que se segue pressupõe que pretende pré-obter uma imagem TensorFlow do [Docker Hub](https://hub.docker.com). Este exemplo inclui uma tarefa inicial que corre no anfitrião VM nos nós da piscina. Pode executar uma tarefa inicial no anfitrião, por exemplo, para montar um servidor de ficheiros que possa ser acedido a partir dos recipientes.

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


### <a name="prefetch-images-from-a-private-container-registry"></a>Imagens prefetch de um registo de contentores privados

Também pode pré-obter imagens de contentores autenticando-se num servidor de registo de contentores privado. No exemplo seguinte, os objetos `ContainerConfiguration` e `VirtualMachineConfiguration` prebuscam uma imagem privada do TensorFlow de um registo privado de contentores Azure. A referência da imagem é a mesma do exemplo anterior.

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

## <a name="container-settings-for-the-task"></a>Definições do recipiente para a tarefa

Para executar uma tarefa de contentor numa piscina ativada por contentores, especifique as definições específicas do recipiente. As definições incluem a imagem a utilizar, o registo e as opções de execução do contentor.

* Utilize a propriedade `ContainerSettings` das classes de tarefas para configurar as definições específicas do contentor. Estas definições são definidas pela classe [TaskContainerSettings.](/dotnet/api/microsoft.azure.batch.taskcontainersettings) Note que a opção `--rm` contentor não requer uma opção adicional de `--runtime`, uma vez que é cuidada pelo Batch.

* Se executar tarefas em imagens de contentores, a tarefa de [tarefa em nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e o gestor de [emprego](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) requerem definições de contentores. No entanto, a [tarefa de início,](/dotnet/api/microsoft.azure.batch.starttask)tarefa de preparação de [emprego,](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)e tarefa de [libertação](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) de emprego sem necessidade de configurações de contentores (isto é, podem funcionar dentro de um contexto de contentorou diretamente no nó).

### <a name="container-task-command-line"></a>Linha de comando de tarefa de contentor

Quando executa uma tarefa de contentor, o Batch utiliza automaticamente o [comando para criar](https://docs.docker.com/engine/reference/commandline/create/) um recipiente utilizando a imagem especificada na tarefa. O lote controla então a execução da tarefa no recipiente.

Tal como acontece com as tarefas não contentores, definiu uma linha de comando para uma tarefa de contentor. Uma vez que o Batch cria automaticamente o recipiente, a linha de comando apenas especifica o comando ou comandos que irão ser executados no recipiente.

Se a imagem do recipiente para uma tarefa de Lote estiver configurada com um script [DE ENTRADA,](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) pode definir a sua linha de comando para utilizar o PONTO DE ENTRADA predefinido ou sobrepor-se:

* Para utilizar o ponto de entrada predefinido da imagem do recipiente, delineie a linha de comando de tarefa para a cadeia vazia `""`.

* Para anular o ponto de entrada predefinido, ou se a imagem não tiver um PONTO DE ENTRADA, delineie uma linha de comando adequada para o recipiente, por exemplo, `/app/myapp` ou `/bin/sh -c python myscript.py`.

[ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) opcionais são argumentos adicionais que fornece ao comando `docker create` que o Batch utiliza para criar e executar o recipiente. Por exemplo, para definir um diretório de trabalho para o recipiente, definir a opção `--workdir <directory>`. Consulte o [estivador criar](https://docs.docker.com/engine/reference/commandline/create/) referência para opções adicionais.

### <a name="container-task-working-directory"></a>Diretório de trabalho de tarefas de contentores

Uma tarefa de contentor de lote executa num diretório de trabalho no recipiente que é muito semelhante ao lote de diretório configura do conjunto para uma tarefa regular (não contentor). Note que este diretório de trabalho é diferente do [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) se configurado na imagem, ou o diretório de trabalho do recipiente padrão (`C:\` num recipiente windows, ou `/` num recipiente Linux).

Para uma tarefa de contentor de lote:

* Todos os diretórios recursivamente abaixo do `AZ_BATCH_NODE_ROOT_DIR` no nó hospedeiro (a raiz dos diretórios do Lote Azure) são mapeados no recipiente
* Todas as variáveis do ambiente de tarefa são mapeadas no recipiente
* O diretório de trabalho de tarefas `AZ_BATCH_TASK_WORKING_DIR` no nó é definido da mesma forma que para uma tarefa regular e mapeado no recipiente.

Estes mapeamentos permitem-lhe trabalhar com tarefas de contentores da mesma forma que as tarefas não contentores. Por exemplo, instale aplicações utilizando pacotes de aplicações, aceda a ficheiros de recursos a partir do Armazenamento Azure, utilize as definições de ambiente de tarefas e persista ficheiros de saída de tarefas após a paragem do contentor.

### <a name="troubleshoot-container-tasks"></a>Tarefas de contentores de resolução de problemas

Se a sua tarefa de contentor não funcionar como esperado, poderá ter de obter informações sobre a configuração WORKDIR ou ENTRYPOINT da imagem do recipiente. Para ver a configuração, verifique o comando de inspeção de [imagem do estivador.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

Se necessário, ajuste as definições da tarefa do recipiente com base na imagem:

* Especifique um caminho absoluto na linha de comando de tarefa. Se o PONTO DE ENTRADA padrão da imagem for utilizado para a linha de comando de tarefa, certifique-se de que está definido um caminho absoluto.

* Nas opções de execução do contentor da tarefa, altere o diretório de trabalho para corresponder ao WORKDIR na imagem. Por exemplo, definir `--workdir /app`.

## <a name="container-task-examples"></a>Exemplos de tarefas de contentores

O seguinte snippet Python mostra uma linha de comando básica correndo em um recipiente criado a partir de uma imagem fictícia retirada de Docker Hub. Aqui, a opção de contentor `--rm` remove o recipiente após o acabamento da tarefa, e a opção `--workdir` define um diretório de trabalho. A linha de comando sobrepõe-se ao recipiente ENTRYPOINT com um simples comando de concha que escreve um pequeno ficheiro para o diretório de trabalho de tarefas no hospedeiro.

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

O C# exemplo que se segue mostra as definições básicas do recipiente para uma tarefa na nuvem:

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


## <a name="next-steps"></a>Passos seguintes

* Consulte também o kit de ferramentas [do Estaleiro de Lote](https://github.com/Azure/batch-shipyard) para facilitar a colocação de cargas de trabalho de contentores no Lote Azure através de [receitas do Estaleiro](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Para obter mais informações sobre a instalação e utilização do Docker CE no Linux, consulte a documentação do [Docker.](https://docs.docker.com/engine/installation/)

* Para obter mais informações sobre a utilização de imagens personalizadas, consulte [Utilize uma imagem personalizada gerida para criar um conjunto de máquinas virtuais.](batch-custom-images.md)

* Saiba mais sobre o [projeto Moby](https://mobyproject.org/), um quadro para a criação de sistemas baseados em contentores.
