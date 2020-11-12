---
title: Executar Linux em nó de computação de máquina virtual
description: Aprenda a processar cargas de trabalho paralelas em piscinas de máquinas virtuais Linux em Azure Batch.
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 0a9c801a13af05f077b87f296992da7f50742e4b
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533502"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Provisão Linux nómada em piscinas de Lote

Pode utilizar o Azure Batch para executar cargas de trabalho paralelas de computação em máquinas virtuais Linux e Windows. Este artigo detalha como criar piscinas de nós de computação Linux no serviço Batch utilizando as bibliotecas de [clientes Batch Python](https://pypi.python.org/pypi/azure-batch) e [Batch .NET.](/dotnet/api/microsoft.azure.batch) 

## <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Quando cria um conjunto de nós computacional em Batch, tem duas opções para selecionar o tamanho do nó e o sistema operativo: Configuração de serviços de nuvem e configuração de máquina virtual. A maioria dos grupos de nós de computação do Windows utilizam [a Configuração dos Serviços cloud,](nodes-and-pools.md#cloud-services-configuration)que especifica que a piscina é composta por nós dos Azure Cloud Services. Estas piscinas fornecem apenas nós de computação Windows.

Em contraste, [a Configuração da Máquina Virtual](nodes-and-pools.md#virtual-machine-configuration) especifica que a piscina é composta por VMs Azure, que podem ser criados a partir de imagens Linux ou Windows. Quando criar uma piscina com Configuração de Máquina Virtual, tem de especificar um [tamanho de nó de computação disponível,](../virtual-machines/sizes.md)a referência de imagem da máquina virtual e o agente de nó de lote SKU (um programa que funciona em cada nó e fornece uma interface entre o nó e o serviço Batch) e a referência de imagem da máquina virtual que será instalada nos nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual

O serviço Batch utiliza [conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/overview.md) para fornecer nós de computação na Configuração da Máquina Virtual. Pode especificar uma imagem do [Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)ou [utilizar a Galeria de Imagens Partilhadas para preparar uma imagem personalizada.](batch-sig-images.md)

Quando criar uma referência de imagem de máquina virtual, deve especificar as seguintes propriedades:

| **Propriedade de referência de imagem** | **Exemplo** |
| --- | --- |
| Publisher |Canónico |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Versão |mais recente |

> [!TIP]
> Você pode saber mais sobre estas propriedades e como especificar imagens do Marketplace em [find Linux VM imagens no Azure Marketplace com o Azure CLI](../virtual-machines/linux/cli-ps-findimage.md). Note que nem todas as imagens do Marketplace são atualmente compatíveis com o Batch.

### <a name="node-agent-sku"></a>Agente de nó SKU

O [agente de nó batch](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) é um programa que funciona em cada nó na piscina e fornece a interface de comando e controlo entre o nó e o serviço Batch. Existem diferentes implementações do agente de nó, conhecido como SKUs, para diferentes sistemas operativos. Essencialmente, quando cria uma Configuração de Máquina Virtual, primeiro especifica a referência de imagem da máquina virtual e, em seguida, especifica o agente de nó para instalar na imagem. Normalmente, cada agente de nó SKU é compatível com múltiplas imagens de máquinas virtuais. Aqui estão alguns exemplos de skus agente de nó:

- lote.node.ubuntu 18.04
- lote.nó.centos 7
- lote.node.windows amd64

### <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquinas virtuais

Nem todas as imagens do Marketplace são compatíveis com os agentes de nó de lote atualmente disponíveis. Para listar todas as imagens de máquinas virtuais suportadas do Marketplace para o serviço Batch e o respetivo agente de nó SKUs, utilize [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .NET) ou a API correspondente em outro SDK de idioma.

## <a name="create-a-linux-pool-batch-python"></a>Criar uma piscina Linux: Batch Python

O seguinte corte de código mostra um exemplo de como usar a [Microsoft Azure Batch Client Library para Python](https://pypi.python.org/pypi/azure-batch) para criar um conjunto de nós de computação Ubuntu Server. Para mais detalhes sobre o módulo Batch Python, consulte a [documentação de referência.](/python/api/overview/azure/batch)

Este snippet cria uma [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explicitamente e especifica cada uma das suas propriedades (editora, oferta, SKU, versão). No código de produção, no entanto, recomendamos que utilize o método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para selecionar a partir das combinações SKU do agente de imagem e nó disponível no tempo de execução.

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
vm_size = "STANDARD_D2_V3"
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
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como mencionado anteriormente, recomendamos a utilização do método [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) para selecionar dinamicamente das combinações de imagem do agente de nó/marketplace atualmente suportados (em vez de criar uma [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explicitamente). O seguinte snippet Python mostra como usar este método.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Criar uma piscina Linux: Lote .NET

O seguinte corte de código mostra um exemplo de como usar a biblioteca de [clientes Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) para criar um conjunto de nós de computação Ubuntu Server. Para mais detalhes sobre o Lote .NET, consulte a [documentação de referência.](/dotnet/api/microsoft.azure.batch)

O seguinte código snippet utiliza o método [PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para selecionar a partir da lista de combinações SKU de imagem e agente de nó sonoro atualmente suportado. Esta técnica é recomendada, porque a lista de combinações suportadas pode mudar de vez em quando. Mais comummente, combinações suportadas são adicionadas.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

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

Embora o snippet anterior utilize o método [PoolOperations.istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) para listar e selecionar de combinações SKU de imagem e node suportadas (recomendado), também pode configurar explicitamente uma [ImageReference:](/dotnet/api/microsoft.azure.batch.imagereference)

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Ligue-se aos nosdes Linux usando SSH

Durante o desenvolvimento ou durante a resolução de problemas, poderá achar necessário entrar nos nós da sua piscina. Ao contrário dos nós de computação do Windows, não é possível utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) para ligar aos nós linux. Em vez disso, o serviço Batch permite o acesso SSH em cada nó para ligação remota.

O seguinte corte de código Python cria um utilizador em cada nó numa piscina, que é necessária para a ligação remota. Em seguida, imprime as informações de ligação secure shell (SSH) para cada nó.

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

Este código terá saída semelhante ao seguinte exemplo. Neste caso, a piscina contém quatro nós Linux.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma palavra-passe, pode especificar uma chave pública SSH quando cria um utilizador num nó. No Python SDK, utilize o parâmetro **ssh_public_key** no [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). Em .NET, utilize a propriedade [ComputeNodeUser.SshPublicKey.](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey)

## <a name="pricing"></a>Preços

O Azure Batch é construído com base nos Serviços Azure Cloud e na tecnologia Azure Virtual Machines. O próprio serviço Batch é oferecido sem custos, o que significa que é cobrado apenas pelos recursos computativos (e custos associados que implicam) que as suas soluções Batch consomem. Ao escolher **a Configuração virtual da máquina,** é cobrado com base na estrutura [de preços das Máquinas Virtuais.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Se implementar aplicações nos seus nós Batch utilizando [pacotes de aplicações,](batch-application-packages.md)também é cobrado pelos recursos de Armazenamento Azure que os seus pacotes de aplicação consomem.

## <a name="next-steps"></a>Passos seguintes

- Explore as [amostras](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) de código Python no [repositório gitHub de amostras de azure-lot](https://github.com/Azure/azure-batch-samples) para ver como realizar operações comuns do Batch, tais como piscina, trabalho e criação de tarefas. A [README](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) que acompanha as amostras python tem detalhes sobre como instalar as embalagens necessárias.
- Saiba como utilizar [VMs de baixa prioridade](batch-low-pri-vms.md) com o Batch.
