---
title: Executar o Linux em nós de computação de máquina virtual – lote do Azure | Microsoft Docs
description: Saiba como processar suas cargas de trabalho de computação paralela em pools de máquinas virtuais do Linux no lote do Azure.
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: af67fb2d35344ec6134a107a553fa48661dc0770
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095100"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Provisionar nós de computação do Linux em pools do lote

Você pode usar o lote do Azure para executar cargas de trabalho de computação paralelas em máquinas virtuais do Linux e do Windows. Este artigo fornece detalhes sobre como criar pools de nós de computação do Linux no serviço de lote usando as bibliotecas [][api_net] de cliente do batch e do lote do [lote][py_batch_package] .

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações. Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuração de máquina virtual
Ao criar um pool de nós de computação no lote, você tem duas opções das quais selecionar o tamanho do nó e o sistema operacional: Configuração de serviços de nuvem e configuração de máquina virtual.

A **Configuração de Serviços Cloud** fornece nós de computação do Windows *apenas*. Os tamanhos de nó de computação disponíveis são listados em [tamanhos para serviços de nuvem](../cloud-services/cloud-services-sizes-specs.md), e os sistemas operacionais disponíveis são listados na [matriz de compatibilidade do SDK e versões do SO convidado do Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Ao criar um pool que contém nós dos serviços de nuvem do Azure, você especifica o tamanho do nó e a família do sistema operacional, que são descritos nos artigos mencionados anteriormente. Para pools de nós de computação do Windows, os serviços de nuvem são usados com mais frequência.

A **configuração de máquina virtual** fornece imagens do Linux e do Windows para nós de computação. Os tamanhos de nó de computação disponíveis são listados em [tamanhos para máquinas virtuais no Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [tamanhos para máquinas virtuais no Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Ao criar um pool que contém nós de configuração de máquina virtual, você deve especificar o tamanho dos nós, a referência de imagem de máquina virtual e a SKU do agente de nó de lote a ser instalada nos nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual
O serviço de lote usa conjuntos de dimensionamento de [máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) para fornecer nós de computação na configuração de máquina virtual. Você pode especificar uma imagem do [Azure Marketplace][vm_marketplace]ou fornecer uma imagem personalizada que você preparou. Para obter mais detalhes sobre imagens personalizadas, consulte [criar um pool com uma imagem personalizada](batch-custom-images.md).

Ao configurar uma referência de imagem de máquina virtual, você especifica as propriedades da imagem de máquina virtual. As propriedades a seguir são necessárias quando você cria uma referência de imagem de máquina virtual:

| **Propriedades de referência de imagem** | **Exemplo** |
| --- | --- |
| Fabricante |Canónico |
| Oferta |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> Você pode saber mais sobre essas propriedades e como listar imagens do Marketplace em [navegar e selecionar imagens de máquinas virtuais do Linux no Azure com a CLI ou o PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Observe que nem todas as imagens do Marketplace são atualmente compatíveis com o lote. Para obter mais informações, consulte [SKU do agente do nó](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>SKU do agente do nó
O agente de nó de lote é um programa executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço de lote. Há diferentes implementações do agente de nó, conhecidas como SKUs, para sistemas operacionais diferentes. Essencialmente, ao criar uma configuração de máquina virtual, você primeiro especifica a referência de imagem de máquina virtual e, em seguida, especifica o agente de nó a ser instalado na imagem. Normalmente, cada SKU de agente de nó é compatível com várias imagens de máquina virtual. Aqui estão alguns exemplos de SKUs de agente do nó:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* lote. Node. Windows AMD64

> [!IMPORTANT]
> Nem todas as imagens de máquina virtual disponíveis no Marketplace são compatíveis com os agentes de nó de lote disponíveis no momento. Use os SDKs do lote para listar as SKUs do agente de nó disponíveis e as imagens de máquina virtual com as quais eles são compatíveis. Consulte a [lista de imagens de máquina virtual](#list-of-virtual-machine-images) mais adiante neste artigo para obter mais informações e exemplos de como recuperar uma lista de imagens válidas em tempo de execução.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Criar um pool do Linux: Batch Python
O trecho de código a seguir mostra um exemplo de como usar a [biblioteca de cliente lote do Microsoft Azure para Python][py_batch_package] para criar um pool de nós de computação do Ubuntu Server. A documentação de referência para o módulo python do lote pode ser encontrada no [pacote Azure. batch][py_batch_docs] em ler os documentos.

Esse trecho de código cria um [ImageReference][py_imagereference] explicitamente e especifica cada uma de suas propriedades (editor, oferta, SKU, versão). No código de produção, no entanto, recomendamos que você use o método [list_node_agent_skus][py_list_skus] para determinar e selecionar entre as combinações de SKU do agente de nó e de imagem disponíveis em tempo de execução.

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
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como mencionado anteriormente, recomendamos que, em vez de criar o [ImageReference][py_imagereference] explicitamente, você use o método [list_node_agent_skus][py_list_skus] para selecionar dinamicamente das combinações de imagem do agente do nó/Marketplace com suporte no momento. O trecho de código Python a seguir mostra como usar esse método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Criar um pool do Linux: .NET do Batch
O trecho de código a seguir mostra um exemplo de como usar a biblioteca de cliente [.net do lote][nuget_batch_net] para criar um pool de nós de computação do Ubuntu Server. Você pode encontrar a [documentação de referência do .net do lote][api_net] em docs.Microsoft.com.

O trecho de código a seguir usa o [PoolOperations][net_pool_ops]. O método [ListNodeAgentSkus][net_list_skus] para selecionar na lista de combinações de SKU de agente de nó e imagem do Marketplace com suporte no momento. Essa técnica é desejável porque a lista de combinações com suporte pode mudar de tempos em tempos. Mais comumente, as combinações com suporte são adicionadas.

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

Embora o trecho de código anterior use o [PoolOperations][net_pool_ops]. Método [ListNodeAgentSkus][net_list_skus] para listar dinamicamente e selecionar as combinações de SKU de agente de imagem e nó com suporte (recomendado), você também pode configurar um [ImageReference][net_imagereference] explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquina virtual
A tabela a seguir lista as imagens de máquina virtual do Marketplace que são compatíveis com os agentes de nó do lote disponíveis quando este artigo foi atualizado pela última vez. É importante observar que essa lista não é definitiva porque as imagens e os agentes de nó podem ser adicionados ou removidos a qualquer momento. Recomendamos que seus aplicativos e serviços do lote sempre usem [list_node_agent_skus][py_list_skus] (Python) ou [ListNodeAgentSkus][net_list_skus] (.net do lote) para determinar e selecionar entre os SKUs disponíveis no momento.

> [!WARNING]
> A lista a seguir pode ser alterada a qualquer momento. Sempre use os métodos de **SKU do agente de nó de lista** disponíveis nas APIs do lote para listar as SKUs de máquina virtual e de agente de nó compatíveis quando você executar os trabalhos do lote.
>
>

| **Publicador** | **Oferta** | **SKU da imagem** | **Versão** | **ID do SKU do agente do nó** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| lote | rendering-centos73 | tratamento | latest | batch.node.centos 7 |
| lote | rendering-windows2016 | tratamento | latest | lote. Node. Windows AMD64 |
| Canónico | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| Canónico | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| credativ | Debian | 9 | latest | batch.node.debian 9 |
| credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | lote. Node. Windows AMD64 |
| microsoft-azure-batch | CentOS-contêiner | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | CentOS-contêiner-RDMA | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-LTS | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-LTS | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-datacenter | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2016-datacenter-smalldisk | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | lote. Node. Windows AMD64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | lote. Node. Windows AMD64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | lote. Node. openSUSE 42,1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>Conectar-se a nós do Linux usando SSH
Durante o desenvolvimento ou durante a solução de problemas, talvez você ache necessário entrar nos nós do pool. Diferentemente dos nós de computação do Windows, você não pode usar protocolo RDP (RDP) para se conectar a nós do Linux. Em vez disso, o serviço de lote permite o acesso SSH em cada nó para conexão remota.

O seguinte trecho de código Python cria um usuário em cada nó em um pool, que é necessário para a conexão remota. Em seguida, ele imprime as informações de conexão SSH (Secure Shell) para cada nó.

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

Aqui está o exemplo de saída para o código anterior para um pool que contém quatro nós do Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma senha, você pode especificar uma chave pública SSH ao criar um usuário em um nó. No SDK do Python, use o parâmetro **ssh_public_key** em [ComputeNodeUser][py_computenodeuser]. No .NET, use o [ComputeNodeUser][net_computenodeuser]. Propriedade [SshPublicKey][net_ssh_key] .

## <a name="pricing"></a>Preços
O lote do Azure foi criado nos serviços de nuvem do Azure e na tecnologia de máquinas virtuais do Azure. O serviço de lote em si é oferecido sem custo, o que significa que você é cobrado apenas pelos recursos de computação que suas soluções de lote consomem. Quando você escolhe **configuração de serviços de nuvem**, você é cobrado com base na estrutura de [preços dos serviços de nuvem][cloud_services_pricing] . Ao escolher a **configuração de máquina virtual**, você será cobrado com base na estrutura de [preços de máquinas virtuais][vm_pricing] . 

Se você implantar aplicativos em seus nós do lote usando [pacotes de aplicativos](batch-application-packages.md), também será cobrado pelos recursos de armazenamento do Azure consumidos por seus pacotes de aplicativos. Em geral, os custos de armazenamento do Azure são mínimos. 

## <a name="next-steps"></a>Passos Seguintes

Os [exemplos de código do Python][github_samples_py] no repositório [Azure-batch-Samples][github_samples] no GitHub contêm scripts que mostram como executar operações comuns em lote, como pool, trabalho e criação de tarefas. O [Leiame][github_py_readme] que acompanha os exemplos do Python tem detalhes sobre como instalar os pacotes necessários.

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
