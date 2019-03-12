---
title: Nós – Azure Batch de computação do Linux de execução na máquina virtual | Documentos da Microsoft
description: Saiba como processar as suas cargas de trabalho de computação paralelas em conjuntos de máquinas virtuais do Linux no Azure Batch.
services: batch
documentationcenter: python
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e228e73283685988247c8d419ba0a97b8c7b2974
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546970"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Aprovisionar nós de computação do Linux em conjuntos do Batch

Pode utilizar o Azure Batch para executar cargas de trabalho de computação paralelas em máquinas virtuais Linux e Windows. Este artigo fornece detalhes sobre como criar conjuntos de nós de computação do Linux no serviço Batch com ambos os [Python do Batch] [ py_batch_package] e [.NET do Batch] [ api_net]bibliotecas de cliente.

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações. Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuração da máquina virtual
Quando cria um conjunto de nós de computação no Batch, tem duas opções a partir do qual pode selecionar o tamanho de nó e o sistema operativo: Configuração de serviços cloud e a configuração da Máquina Virtual.

A **Configuração de Serviços Cloud** fornece nós de computação do Windows *apenas*. Tamanhos de nó de computação disponíveis estão listados na [tamanhos para os serviços Cloud](../cloud-services/cloud-services-sizes-specs.md), e sistemas operativos disponíveis estão listados na [versões de SO convidado do Azure e matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria um conjunto que contém nós de serviços Cloud do Azure, especifique o tamanho do nó e a família de SO, que são descritas nos artigos mencionados anteriormente. Para nós de computação de agrupamentos do Windows, serviços em nuvem é geralmente utilizado.

**Configuração da máquina virtual** fornece imagens de Linux e Windows para nós de computação. Tamanhos de nó de computação disponíveis estão listados na [tamanhos de máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [tamanhos de máquinas virtuais no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Quando cria um conjunto que contém nós de configuração da Máquina Virtual, tem de especificar o tamanho de nós, a referência de imagem de máquina virtual e o agente de nó do Batch SKU para ser instalado em nós.

### <a name="virtual-machine-image-reference"></a>Referência da imagem de máquina virtual
O serviço do Batch utiliza [conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para fornecer nós de computação na configuração da Máquina Virtual. Pode especificar uma imagem a partir da [do Azure Marketplace][vm_marketplace], ou fornecer uma imagem personalizada que está preparado. Para obter mais detalhes sobre as imagens personalizadas, consulte [criar um conjunto com uma imagem personalizada](batch-custom-images.md).

Quando configurar uma referência de imagem de máquina virtual, especifica as propriedades da imagem de máquina virtual. As seguintes propriedades são necessárias quando cria uma referência de imagem de máquina virtual:

| **Propriedades de referência da imagem** | **Exemplo** |
| --- | --- |
| Publicador |Canonical |
| Oferta |UbuntuServer |
| SKU |14.04.4-LTS |
| Versão |mais recente |

> [!TIP]
> Pode saber mais sobre essas propriedades e como listar as imagens do Marketplace no [navegar e selecionadas imagens de máquinas virtuais do Linux no Azure com a CLI ou o PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Tenha em atenção que nem todas as imagens do Marketplace são atualmente compatíveis com o Batch. Para obter mais informações, consulte [SKU do agente de nó](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU do agente de nó
O agente de nó do Batch é um programa que é executado em cada nó no conjunto e fornece a interface de comando e controlo entre o nó e o serviço Batch. Existem diferentes implementações do agente de nó, conhecido como SKUs, para sistemas operativos diferentes. Essencialmente, quando cria uma configuração de Máquina Virtual, primeiro tem de especificar a referência de imagem de máquina virtual e, em seguida, especificar o agente de nó para instalar na imagem. Normalmente, cada agente de nó, SKU é compatível com várias imagens de máquina virtual. Aqui estão alguns exemplos das SKUs de agente de nó:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.Windows amd64

> [!IMPORTANT]
> Nem todas as imagens de máquinas virtuais que estão disponíveis no Marketplace são compatíveis com os agentes de nó de Batch está disponíveis. Utilize os SDKs do Batch para listar os SKUs de agente de nó disponíveis e as imagens de máquina virtual com a qual eles são compatíveis. Consulte a [imagens de lista de máquinas virtuais](#list-of-virtual-machine-images) mais adiante neste artigo para obter mais informações e exemplos de como obter uma lista de imagens válidas no tempo de execução.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Crie um conjunto do Linux: Batch Python
O fragmento de código seguinte mostra um exemplo de como utilizar o [biblioteca de clientes do Microsoft Azure Batch para Python] [ py_batch_package] para criar um conjunto do Ubuntu Server nós de computação. Documentação de referência para o módulo de Python do Batch pode ser encontrada em [azure.batch pacote] [ py_batch_docs] na leitura dos Docs.

Este fragmento cria um [ImageReference] [ py_imagereference] explicitamente e especifica cada uma das suas propriedades (publicador, oferta, SKU, versão). No código de produção, no entanto, recomendamos que utilize o [list_node_agent_skus] [ py_list_skus] método para determinar e selecione as disponíveis imagem nó agente SKU combinações e em tempo de execução.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como mencionado anteriormente, recomendamos que em vez de criar o [ImageReference] [ py_imagereference] explicitamente, utilize o [list_node_agent_skus] [ py_list_skus] método para selecionar dinamicamente as combinações de imagem do Marketplace/agente de nó atualmente suportados. O fragmento de Python seguinte mostra como utilizar este método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Crie um conjunto do Linux: .NET do Batch
O fragmento de código seguinte mostra um exemplo de como utilizar o [.NET do Batch] [ nuget_batch_net] biblioteca de cliente para criar um conjunto do Ubuntu Server nós de computação. Pode encontrar os [documentação de referência de .NET do Batch] [ api_net] no docs.microsoft.com.

O seguinte código trecho de código utiliza a [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] Marketplace imagem e o nó o agente SKU combinações suportadas de método para selecionar a partir da lista de atualmente. Essa técnica é desejável porque a lista de combinações suportadas pode ser alterada ocasionalmente. Normalmente, são adicionadas combinações suportadas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Embora o fragmento anterior utiliza o [PoolOperations][net_pool_ops].[ ListNodeAgentSkus] [ net_list_skus] método dinamicamente, listar e selecionar a partir de imagem e o nó de agente SKU combinações suportadas (recomendadas), também pode configurar um [ImageReference] [ net_imagereference] explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquinas virtuais
A tabela seguinte lista as imagens de máquina virtual do Marketplace que são compatíveis com os agentes de nó do Batch disponíveis quando este artigo foi atualizado pela última vez. É importante observar que esta lista não é definitiva porque imagens e os agentes de nó podem ser adicionadas ou removidas em qualquer altura. Recomendamos que seus serviços e aplicações de Batch sempre a usar [list_node_agent_skus] [ py_list_skus] (Python) ou [ListNodeAgentSkus] [ net_list_skus] ( .NET do batch) para determinar e selecione os SKUs disponíveis atualmente.

> [!WARNING]
> A lista a seguir podem ser alterados em qualquer altura. Utilize sempre a **SKU do agente de nó lista** métodos disponíveis nas APIs de Batch para listar a máquina virtual compatível e os SKUs de agente de nó, ao executar as tarefas de lote.
>
>

| **Publicador** | **Oferta** | **SKU de imagem** | **Versão** | **ID de SKU do agente de nó** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| batch | rendering-centos73 | composição | mais recente | batch.node.centos 7 |
| batch | rendering-windows2016 | composição | mais recente | batch.node.Windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | mais recente | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | mais recente | batch.node.ubuntu 14.04 |
| credativ | Debian | 9 | mais recente | batch.node.debian 9 |
| credativ | Debian | 8 | mais recente | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | mais recente | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | mais recente | batch.node.Windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | mais recente | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | mais recente | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | mais recente | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | mais recente | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | mais recente | batch.node.Windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2 SP1 smalldisk | mais recente | batch.node.Windows amd64 |
| OpenLogic | CentOS | 7.4 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | mais recente | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | mais recente | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | mais recente | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Ligar a nós do Linux através de SSH
Durante o desenvolvimento ou durante a resolução de problemas, pode ser necessário para iniciar sessão para os nós do conjunto. Ao contrário de nós de computação do Windows, é possível utilizar o protocolo RDP (Remote Desktop) para ligar a nós do Linux. Em vez disso, o serviço Batch permite o acesso SSH em cada nó de ligação remota.

O fragmento de código de Python seguinte cria um utilizador em cada nó num conjunto, que é necessário para a ligação remota. Em seguida, imprime as informações de ligação de secure shell (SSH) para cada nó.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Eis a saída de exemplo para o código anterior para um conjunto que contém quatro nós do Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma palavra-passe, pode especificar uma chave pública SSH ao criar um utilizador num nó. Utilizar o SDK do Python, o **ssh_public_key** parâmetro na [ComputeNodeUser][py_computenodeuser]. No .NET, utilize o [ComputeNodeUser][net_computenodeuser].[ SshPublicKey] [ net_ssh_key] propriedade.

## <a name="pricing"></a>Preços
O Azure Batch baseia-se na tecnologia de serviços Cloud do Azure e máquinas virtuais do Azure. O serviço de Batch em si é disponibilizado sem custos, o que significa que lhe são cobrados apenas os recursos de computação que consumam as soluções do Batch. Quando escolhe **configuração de serviços Cloud**, é cobrado com base no [preços de serviços Cloud] [ cloud_services_pricing] estrutura. Quando escolhe **configuração da Máquina Virtual**, é cobrado com base no [preços das máquinas virtuais] [ vm_pricing] estrutura. 

Se implementar aplicações em nós do Batch usando [pacotes de aplicações](batch-application-packages.md), também é cobradas para os recursos de armazenamento do Azure que consomem pacotes de aplicativos. Em geral, os custos de armazenamento do Azure são mínimos. 

## <a name="next-steps"></a>Passos Seguintes

O [exemplos de código Python] [ github_samples_py] no [azure-batch-samples] [ github_samples] repositório no GitHub contêm scripts que lhe mostram como realizar operações comuns em lote, como o agrupamento, a tarefa e a criação da tarefa. O [Leia-me] [ github_py_readme] que acompanha o Python exemplos inclui detalhes sobre como instalar os pacotes necessários.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
