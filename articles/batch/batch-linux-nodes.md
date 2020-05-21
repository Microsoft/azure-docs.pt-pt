---
title: Executar Linux em nódos de computação de máquina virtual
description: Aprenda a processar as suas cargas de trabalho paralelas em piscinas de máquinas virtuais Linux em Azure Batch.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd8a39556fb0aec0ddbf6c8e639281d7329228a4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726609"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Provision Linux computação nóde sintetizar em piscinas de lote

Pode utilizar o Azure Batch para executar cargas de trabalho paralelas de computação em máquinas virtuais Linux e Windows. Este artigo detalha como criar piscinas de nós de computação Linux no serviço Batch, utilizando as bibliotecas de clientes [Batch Python][py_batch_package] e [Batch .NET.][api_net]

> [!NOTE]
> Os pacotes de aplicações são suportados em todos os conjuntos do Batch criados após 5 de Julho de 2017. Só são suportados em conjuntos do Batch criados entre 10 de Março de 2016 e 5 de Julho de 2017 se o conjunto tiver sido criado com uma configuração de Serviço Cloud. Os conjuntos do Batch criados antes de 10 de Março de 2016 não suportam pacotes de aplicações. Para obter mais informações sobre a utilização de pacotes de aplicações para implementar as aplicações em nós do Batch, veja [Implementar aplicações em nós de computação com pacotes de aplicações do Batch](batch-application-packages.md).
>
>

## <a name="virtual-machine-configuration"></a>Configuração da máquina virtual
Ao criar um conjunto de nós computacionais em Batch, tem duas opções para selecionar o tamanho do nó e o sistema operativo: Configuração de Serviços em Nuvem e Configuração de Máquina Virtual.

A **Configuração de Serviços Cloud** fornece nós de computação do Windows *apenas*. Os tamanhos disponíveis do nó computacional estão listados em [Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md), e os sistemas operativos disponíveis estão listados nas [versões Do Os Do EO do Hóspede Azure e](../cloud-services/cloud-services-guestos-update-matrix.md)na matriz de compatibilidade sdk . Ao criar uma piscina que contenha nós azure Cloud Services, especifice o tamanho do nó e a família OS, que são descritas nos artigos anteriormente mencionados. Para piscinas de nós de computação windows, os Serviços cloud são mais utilizados.

**A Configuração virtual** da máquina fornece imagens linux e Windows para nós de computação. Os tamanhos disponíveis do nó computacional estão listados em [Tamanhos para máquinas virtuais em Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) e [Sizes para máquinas virtuais em Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Quando criar uma piscina que contenha nós de configuração de máquinavirtual, deve especificar o tamanho dos nós, a referência de imagem da máquina virtual e o agente de nó sku do Batch a instalar nos nós.

### <a name="virtual-machine-image-reference"></a>Referência de imagem de máquina virtual

O serviço Batch utiliza [conjuntos de escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) de máquina virtual para fornecer nódosos de computação na Configuração da Máquina Virtual. Pode especificar uma imagem do [Azure Marketplace,][vm_marketplace]ou fornecer uma imagem personalizada que tenha preparado. Para mais detalhes sobre imagens personalizadas, consulte [Criar uma piscina com a Galeria de Imagem Partilhada.](batch-sig-images.md)

Ao configurar uma referência de imagem virtual da máquina, especifice as propriedades da imagem da máquina virtual. As seguintes propriedades são necessárias quando cria uma referência de imagem de máquina virtual:

| **Propriedades de referência de imagem** | **Exemplo** |
| --- | --- |
| Publisher |Canónico |
| Oferta |UbuntuServer |
| SKU |18.04-LTS |
| Versão |mais recente |

> [!TIP]
> Você pode saber mais sobre estas propriedades e como listar imagens do Marketplace em [Navigate e selecionar imagens de máquinas virtuais Linux em Azure com CLI ou PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Note que nem todas as imagens do Marketplace são atualmente compatíveis com o Batch. Para mais informações, consulte o [agente do Nó SKU](#node-agent-sku).
>
>

### <a name="node-agent-sku"></a>Agente do nó SKU
O agente do nó batch é um programa que funciona em cada nó na piscina e fornece a interface de comando e controlo entre o nó e o serviço Batch. Existem diferentes implementações do agente do nó, conhecido como SKUs, para diferentes sistemas operativos. Essencialmente, quando cria uma Configuração virtual da máquina, primeiro especifica a referência de imagem da máquina virtual e, em seguida, especifica o agente do nó para instalar na imagem. Tipicamente, cada agente do nó SKU é compatível com múltiplas imagens de máquinas virtuais. Aqui estão alguns exemplos do agente do nó SKUs:

* lote.node.ubuntu 18.04
* lote.node.centos 7
* lote.node.janelas amd64

> [!IMPORTANT]
> Nem todas as imagens de máquinas virtuais que estão disponíveis no Mercado são compatíveis com os agentes do nó batch atualmente disponíveis. Utilize os SDKs de lote para listar o agente de nó disponível SKUs e as imagens de máquinas virtuais com as quais são compatíveis. Consulte a [Lista de Imagens](#list-of-virtual-machine-images) da Máquina Virtual mais tarde neste artigo para obter mais informações e exemplos de como recuperar uma lista de imagens válidas no tempo de execução.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Crie uma piscina linux: Batch Python
O seguinte código snippet mostra um exemplo de como usar a Biblioteca de Clientes do [Microsoft Azure Batch para python][py_batch_package] para criar uma piscina de nós computacionais Ubuntu Server. A documentação de referência para o módulo Batch Python pode ser encontrada no [pacote azure.batch][py_batch_docs] em Read the Docs.

Este snippet cria explicitamente uma [ImageReference][py_imagereference] e especifica cada uma das suas propriedades (editora, oferta, SKU, versão). No código de produção, no entanto, recomendamos que utilize o método [list_supported_images][py_list_supported_images] para determinar e selecionar a partir das combinações sku de imagem e nó disponíveis no tempo de execução.

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

Como mencionado anteriormente, recomendamos que, em vez de criar explicitamente a [ImageReference,][py_imagereference] utilize o método [list_supported_images][py_list_supported_images] para selecionar dinamicamente a partir das combinações de imagem de agente de nó/Marketplace atualmente suportadas. O seguinte excerto python mostra como usar este método.

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

## <a name="create-a-linux-pool-batch-net"></a>Crie uma piscina Linux: Batch .NET
O seguinte código de snippet mostra um exemplo de como usar a biblioteca de clientes [Batch .NET][nuget_batch_net] para criar uma piscina de nós computacionais Ubuntu Server. Pode encontrar a documentação de [referência do Lote .NET][api_net] no docs.microsoft.com.

O seguinte código de corte utiliza as [Operações de Pool][net_pool_ops]. [Método ListSupportEdImages][net_list_supported_images] para selecionar a partir da lista de combinações de imagem e agente de nó sKU suportadas atualmente. Esta técnica é desejável porque a lista de combinações suportadas pode mudar de vez em quando. Mais frequentemente, são adicionadas combinações apoiadas.

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

Embora o corte anterior utilize o [PoolOperations][net_pool_ops]. [Método ListSupportEdImages][net_list_supported_images] para listar e selecionar de forma dinâmica as combinações sKU de imagem e nó suportadas (recomendado), também pode configurar explicitamente uma [ImageReference:][net_imagereference]

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lista de imagens de máquinas virtuais
Para obter a lista de todas as imagens de máquinavirtual suportadas do Marketplace para o serviço Batch e seus agentes de nó correspondentes, por favor aproveite o [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch.NET) ou a API correspondente no respetivo SDK de idioma à sua escolha.

## <a name="connect-to-linux-nodes-using-ssh"></a>Ligue-se aos nódosos Linux usando SSH
Durante o desenvolvimento ou durante a resolução de problemas, poderá achar necessário iniciar sessão nos nós da sua piscina. Ao contrário dos nós computacionais do Windows, não é possível utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP) para ligar aos nós linux. Em vez disso, o serviço Batch permite o acesso sSH em cada nó para ligação remota.

O seguinte código Python snippet cria um utilizador em cada nó numa piscina, que é necessário para a ligação remota. Em seguida, imprime as informações de ligação da concha segura (SSH) para cada nó.

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

Aqui está a saída da amostra para o código anterior para uma piscina que contém quatro nós Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Em vez de uma palavra-passe, pode especificar uma chave pública SSH quando criar um utilizador num nó. No SDK Python, utilize o parâmetro **ssh_public_key** no [ComputeNodeUser][py_computenodeuser]. Em .NET, utilize o [ComputeNodeUser][net_computenodeuser]. [Propriedade SshPublicKey.][net_ssh_key]

## <a name="pricing"></a>Preços
O Azure Batch é construído com base na tecnologia Azure Cloud Services e Azure Virtual Machines. O próprio serviço De lote é oferecido sem custos, o que significa que você é cobrado apenas pelos recursos computacionais (e custos associados que implicam) que as suas soluções De Lote consomem. Ao escolher a **Configuração de Serviços cloud,** é cobrado com base na estrutura de preços dos [Serviços cloud.][cloud_services_pricing] Quando escolhe a Configuração da **Máquina Virtual,** é carregado com base na estrutura de preços das [Máquinas Virtuais.][vm_pricing]

Se implementar aplicações nos seus nós de Lote utilizando pacotes de [aplicações,](batch-application-packages.md)também é cobrado pelos recursos de Armazenamento Azure que os seus pacotes de aplicação consomem.

## <a name="next-steps"></a>Próximos passos

As [amostras][github_samples_py] de código Python no repositório [de amostras de lote azul][github_samples] no GitHub contêm scripts que mostram como realizar operações comuns do Lote, tais como pool, job, e criação de tarefas. A [README][github_py_readme] que acompanha as amostras python tem detalhes sobre como instalar os pacotes necessários.

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
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
