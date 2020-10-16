---
title: Executar Linux em nó de computação de máquina virtual
description: Aprenda a processar as suas cargas de trabalho paralelas em piscinas de máquinas virtuais Linux em Azure Batch.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: 6cd8a13d67c1ce169e6c9300f7ee017fd3a59d96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89146339"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Provisão Linux nómada em piscinas de Lote

Pode utilizar o Azure Batch para executar cargas de trabalho paralelas de computação em máquinas virtuais Linux e Windows. Este artigo detalha como criar piscinas de nós de computação Linux no serviço Batch utilizando as bibliotecas de [clientes Batch Python][py_batch_package] e [Batch .NET.][api_net]

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações. Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuração de máquina virtual
Quando cria um conjunto de nós computacional em Batch, tem duas opções para selecionar o tamanho do nó e o sistema operativo: Configuração de serviços de nuvem e configuração de máquina virtual.

A **Configuração de Serviços Cloud** fornece nós de computação do Windows *apenas*. Os tamanhos dos nóns de computação disponíveis estão listados em [Tamanhos para Serviços em Nuvem,](../cloud-services/cloud-services-sizes-specs.md)e os sistemas operativos disponíveis estão listados nas [versões Azure Guest OS e na matriz de compatibilidade SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Quando cria uma piscina que contém nós Azure Cloud Services, especifica o tamanho do nó e a família OS, que são descritas nos artigos anteriormente mencionados. Para piscinas de nós de computação Windows, os Serviços cloud são mais utilizados.

**A Configuração de Máquinas Virtuais** fornece imagens linux e Windows para nós computacional. Os tamanhos dos nóns de computação disponíveis estão listados em [Tamanhos para máquinas virtuais em Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [tamanhos para máquinas virtuais em Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Quando criar uma piscina que contenha nós de configuração de máquina virtual, deve especificar o tamanho dos nós, a referência de imagem da máquina virtual e o agente de nó de lote SKU a ser instalado nos nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual

O serviço Batch utiliza [conjuntos de escala de máquina virtual](../virtual-machine-scale-sets/overview.md) para fornecer nós de computação na Configuração da Máquina Virtual. Pode especificar uma imagem do [Azure Marketplace][vm_marketplace]ou fornecer uma imagem personalizada que preparou. Para obter mais detalhes sobre imagens personalizadas, consulte [Criar uma piscina com a Galeria de Imagens Partilhadas.](batch-sig-images.md)

Ao configurar uma referência de imagem de máquina virtual, especifica as propriedades da imagem da máquina virtual. São necessárias as seguintes propriedades quando cria uma referência de imagem de máquina virtual:

| **Propriedades de referência de imagem** | **Exemplo** |
| --- | --- |
| Publisher |Canónico |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Versão |mais recente |

> [!TIP]
> Você pode saber mais sobre estas propriedades e como listar imagens do Marketplace em [Navigate e selecionar imagens de máquinas virtuais Linux em Azure com CLI ou PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Note que nem todas as imagens do Marketplace são atualmente compatíveis com o Batch. Para mais informações, consulte [o agente de nó SKU.](#node-agent-sku)
>
>

### <a name="node-agent-sku"></a>Agente de nó SKU
O agente de nó batch é um programa que funciona em cada nó na piscina e fornece a interface de comando e controlo entre o nó e o serviço Batch. Existem diferentes implementações do agente de nó, conhecido como SKUs, para diferentes sistemas operativos. Essencialmente, quando cria uma Configuração de Máquina Virtual, primeiro especifica a referência de imagem da máquina virtual e, em seguida, especifica o agente de nó para instalar na imagem. Normalmente, cada agente de nó SKU é compatível com múltiplas imagens de máquinas virtuais. Aqui estão alguns exemplos de skus agente de nó:

* lote.node.ubuntu 18.04
* lote.nó.centos 7
* lote.node.windows amd64

> [!IMPORTANT]
> Nem todas as imagens de máquinas virtuais disponíveis no Mercado são compatíveis com os agentes de nó de lote atualmente disponíveis. Utilize os SDKs do lote para listar o agente de nó disponível SKUs e as imagens de máquina virtual com as quais são compatíveis. Consulte a [Lista de imagens da Máquina Virtual](#list-of-virtual-machine-images) mais tarde neste artigo para obter mais informações e exemplos de como recuperar uma lista de imagens válidas no tempo de execução.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Criar uma piscina Linux: Batch Python
O seguinte corte de código mostra um exemplo de como usar a [Microsoft Azure Batch Client Library para Python][py_batch_package] para criar um conjunto de nós de computação Ubuntu Server. A documentação de referência para o módulo Batch Python pode ser encontrada no [ pacote chazure.bat][py_batch_docs] na Leitura dos Docs.

Este snippet cria uma [ImageReference][py_imagereference] explicitamente e especifica cada uma das suas propriedades (editora, oferta, SKU, versão). No código de produção, no entanto, recomendamos que utilize o método [list_supported_images][py_list_supported_images] para determinar e selecionar a partir das combinações SKU do agente de imagem e nó disponível no tempo de execução.

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
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Como mencionado anteriormente, recomendamos que em vez de criar explicitamente a [ImageReference,][py_imagereference] utilize o método [list_supported_images][py_list_supported_images] para selecionar dinamicamente a partir das combinações de imagem do agente de nó/marketplace atualmente suportado. O seguinte snippet Python mostra como usar este método.

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
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Criar uma piscina Linux: Lote .NET
O seguinte corte de código mostra um exemplo de como usar a biblioteca de [clientes Batch .NET][nuget_batch_net] para criar um conjunto de nós de computação Ubuntu Server. Pode encontrar a documentação de [referência do Lote .NET][api_net] no docs.microsoft.com.

O seguinte código snippet utiliza as [Cooperativas Pool][net_pool_ops]. [Método ListSupportedImages][net_list_supported_images] para selecionar a partir da lista de combinações SKU de imagem e agente de nó sku suportados atualmente. Esta técnica é desejável porque a lista de combinações suportadas pode mudar de vez em quando. Mais comummente, combinações suportadas são adicionadas.

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

Embora o corte anterior utilize as [Cooperativas Pool.][net_pool_ops] O método [ListSupportedImages][net_list_supported_images] para listar e selecionar a partir de combinações SKU de imagem e agente de nó suportado (recomendado), também pode configurar explicitamente uma [ImageReference:][net_imagereference]

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquinas virtuais
Para obter a lista de todas as imagens de máquinas virtuais suportadas do Marketplace para o serviço Batch e seus agentes de nó correspondentes, aproveite a [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) ou a API correspondente no respetivo SDK de língua que escolher.

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

Aqui está a saída da amostra para o código anterior para uma piscina que contém quatro nóns Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma palavra-passe, pode especificar uma chave pública SSH quando cria um utilizador num nó. No Python SDK, utilize o parâmetro **ssh_public_key** no [ComputeNodeUser][py_computenodeuser]. Em .NET, utilize o [ComputeNodeUser][net_computenodeuser]. [Propriedade SshPublicKey.][net_ssh_key]

## <a name="pricing"></a>Preços
O Azure Batch é construído com base nos Serviços Azure Cloud e na tecnologia Azure Virtual Machines. O próprio serviço Batch é oferecido sem custos, o que significa que é cobrado apenas pelos recursos computativos (e custos associados que implicam) que as suas soluções Batch consomem. Ao escolher **a Configuração dos Serviços em Nuvem,** é cobrado com base na estrutura [de preços dos Serviços cloud.][cloud_services_pricing] Ao escolher **a Configuração virtual da máquina,** é cobrado com base na estrutura [de preços das Máquinas Virtuais.][vm_pricing]

Se implementar aplicações nos seus nós Batch utilizando [pacotes de aplicações,](batch-application-packages.md)também é cobrado pelos recursos de Armazenamento Azure que os seus pacotes de aplicação consomem.

## <a name="next-steps"></a>Passos seguintes

As [amostras][github_samples_py] de código Python no repositório [de amostras de azure-batch][github_samples] no GitHub contêm scripts que mostram como realizar operações comuns do Batch, tais como piscina, trabalho e criação de tarefas. A [README][github_py_readme] que acompanha as amostras python tem detalhes sobre como instalar as embalagens necessárias.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_mgmt]: /dotnet/api/overview/azure/batch
[api_rest]: /rest/api/batchservice/
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser
[net_imagereference]: /dotnet/api/microsoft.azure.batch.imagereference
[net_list_supported_images]: /dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: /rest/api/batchservice/pool/add
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: /python/api/azure-batch/azure.batch.operations.AccountOperations
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
