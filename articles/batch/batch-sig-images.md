---
title: Use a Galeria de Imagem Partilhada para criar uma piscina personalizada
description: Crie um pool de Lote com a Galeria de Imagem Partilhada para fornecer imagens personalizadas para calcular os nódosos que contêm o software e os dados que precisa para a sua aplicação. As imagens personalizadas são uma forma eficiente de configurar os nódosos de cálculo para executar as suas cargas de trabalho do Lote.
ms.topic: article
ms.date: 08/28/2019
ms.openlocfilehash: 1a26aaecc5da0ef348b720919b04d86f8fcfbc70
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82743582"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Use a Galeria de Imagem Partilhada para criar uma piscina personalizada

Quando cria uma piscina de Lote Azure utilizando a Configuração da Máquina Virtual, especifica uma imagem VM que fornece o sistema operativo para cada nó de cálculo na piscina. Você pode criar um conjunto de máquinas virtuais com uma imagem de Azure Marketplace suportada ou criar uma imagem personalizada com a [Galeria de Imagem Partilhada](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Benefícios da Galeria de Imagem Partilhada

Quando utiliza a Galeria de Imagem Partilhada para a sua imagem personalizada, tem controlo sobre o tipo e configuração do sistema operativo, bem como o tipo de discos de dados. A sua Imagem Partilhada pode incluir aplicações e dados de referência que ficam disponíveis em todos os nós do pool do Lote assim que forem aprovisionados.

Também pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se utiliza uma versão de imagem para criar um VM, a versão de imagem é usada para criar novos discos para o VM.

A utilização de uma Imagem Partilhada poupa tempo na preparação dos nódos de cálculo da sua piscina para executar a sua carga de trabalho do Lote. É possível usar uma imagem do Azure Marketplace e instalar software em cada nó de computação após o provisionamento, mas usar uma Imagem Partilhada é tipicamente mais eficiente. Além disso, pode especificar várias réplicas para a Imagem Partilhada para que quando criar piscinas com muitos VMs (mais de 600 VMs), você economizará tempo na criação de piscinas.

A utilização de uma Imagem Partilhada configurada para o seu cenário pode fornecer várias vantagens:

* **Use as mesmas imagens em todas as regiões.** Você pode criar réplicas de Imagem Partilhada em diferentes regiões para que todas as suas piscinas utilizem a mesma imagem.
* **Configure o sistema operativo (OS).** Pode personalizar a configuração do disco do sistema operativo da imagem.
* **Pré-instalação de aplicações.** As aplicações de pré-instalação no disco OS são mais eficientes e menos propensas a erros do que a instalação de aplicações após o fornecimento dos nós de computação com uma tarefa inicial.
* **Copie grandes quantidades de dados uma vez.** Faça parte da imagem partilhada gerida, copiando-a para os discos de dados de uma imagem gerida. Isto só precisa de ser feito uma vez e disponibiliza dados para cada nó da piscina.
* **Cultivar piscinas para tamanhos maiores.** Com a Galeria de Imagem Partilhada, pode criar piscinas maiores com as suas imagens personalizadas juntamente com mais réplicas de Imagem Partilhada.
* **Melhor desempenho do que imagem personalizada.** Utilizando imagens partilhadas, o tempo que a piscina leva para chegar ao estado estável é até 25% mais rápido, e a latência vm idle é até 30% mais curta.
* **Versão de imagem e agrupamento para uma gestão mais fácil.** A definição de agrupamento de imagem contém informações sobre o porquê da imagem ter sido criada, para que ser o SISTEMA de Identificação e informações sobre o uso da imagem. Agrupar imagens permite uma gestão de imagem mais fácil. Para mais informações, consulte definições de [imagem.](../virtual-machines/windows/shared-image-galleries.md#image-definitions)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Tens de autenticar usando o Azure AD. Se utilizar o auth de chave partilhada, terá um erro de autenticação.  

* **Uma conta do Azure Batch.** Para criar uma conta Batch, consulte o batch quickstarts utilizando o [portal Azure](quick-create-portal.md) ou [o Azure CLI](quick-create-cli.md).

* Uma imagem partilhada da Galeria de **Imagens.** Para criar uma Imagem Partilhada, é necessário ter ou criar um recurso de imagem gerido. A imagem deve ser criada a partir de instantâneos do disco osso do VM e opcionalmente dos seus discos de dados anexados. Para mais informações, consulte [Prepare uma imagem gerida](#prepare-a-managed-image).

> [!NOTE]
> A sua Imagem Partilhada deve estar na mesma subscrição que a conta Batch. A sua Imagem Partilhada pode estar em diferentes regiões desde que tenha réplicas na mesma região que a sua conta Batch.

## <a name="prepare-a-managed-image"></a>Preparar uma imagem gerida

Em Azure, pode preparar uma imagem gerida a partir de:

* Instantâneos de um Sistema operativo e discos de dados de um Azure VM
* Um Azure VM generalizado com discos geridos
* Um VHD generalizado no local carregado para a nuvem

Para escalar as piscinas de Lote de forma fiável com uma imagem personalizada, recomendamos a criação de uma imagem gerida usando *apenas* o primeiro método: utilizando instantâneos dos discos do VM. Veja os seguintes passos para preparar um VM, tire uma foto e crie uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar um VM

Se está a criar um novo VM para a imagem, utilize uma imagem de primeira parte do Azure Marketplace apoiada pelo Batch como imagem base para a sua imagem gerida. Apenas as imagens da primeira festa podem ser usadas como imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas pelo Azure Batch, consulte a operação do agente de [nóso lista SKUs.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Não pode usar uma imagem de terceiros que tenha licença adicional e termos de compra como imagem base. Para obter informações sobre estas imagens do Marketplace, consulte as orientações para Os VMs [linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

* Certifique-se de que o VM é criado com um disco gerido. Esta é a definição de armazenamento padrão quando cria um VM.
* Não instale extensões Azure, como a extensão do Script Personalizado, no VM. Se a imagem contiver uma extensão pré-instalada, o Azure poderá encontrar problemas ao implantar a piscina do Lote.
* Ao utilizar discos de dados anexados, é necessário montar e formatar os discos de dentro de um VM para os utilizar.
* Certifique-se de que a imagem base de OS que fornece utiliza a unidade temporária predefinida. O agente do nó batch espera atualmente a unidade temporária padrão.
* Uma vez que o VM esteja em execução, ligue-o via RDP (para Windows) ou SSH (para Linux). Instale qualquer software ou copiar dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo VM

Um instantâneo é uma cópia completa e só de leitura de um VHD. Para criar uma imagem instantânea de um Sistema operativo ou de dados de um VM, pode utilizar o portal Azure ou ferramentas de linha de comando. Para passos e opções para criar um instantâneo, consulte a orientação para [VMs Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem a partir de um ou mais instantâneos

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas de linha de comando Azure, como a [imagem az criar](/cli/azure/image) comando. Crie uma imagem especificando um instantâneo de disco OS e opcionalmente um ou mais instantâneos de disco de dados.

### <a name="create-a-shared-image-gallery"></a>Criar um Shared Image Gallery

Depois de ter criado com sucesso a sua imagem gerida, precisa de criar uma Galeria de Imagem Partilhada para disponibilizar a sua imagem personalizada. Para aprender a criar uma Galeria de Imagem Partilhada para as suas imagens, consulte Criar uma Galeria de [Imagem Partilhada com o Azure CLI](../virtual-machines/linux/shared-images.md) ou criar uma Galeria de Imagem Partilhada utilizando o portal [Azure.](../virtual-machines/linux/shared-images-portal.md)

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Crie uma piscina a partir de uma imagem partilhada usando o Azure CLI

Para criar uma piscina a partir da sua Imagem `az batch pool create` Partilhada utilizando o Azure CLI, utilize o comando. Especifique o `--image` ID de imagem partilhada no campo. Certifique-se de que o tipo OS e o SKU correspondem às versões especificadas por`--node-agent-sku-id`

> [!NOTE]
> Tens de autenticar usando o Azure AD. Se utilizar o auth de chave partilhada, terá um erro de autenticação.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Criar uma piscina a partir de uma imagem partilhada usando C #

Em alternativa, pode criar uma piscina a partir de uma Imagem Partilhada utilizando o C# SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Crie uma piscina a partir de uma imagem partilhada usando Python

Você também pode criar uma piscina a partir de uma Imagem Partilhada usando o Python SDK: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Crie uma piscina a partir de uma imagem partilhada usando o portal Azure

Utilize os seguintes passos para criar uma piscina a partir de uma Imagem Partilhada no portal Azure.

1. Abra o [portal Azure.](https://portal.azure.com)
1. Vá às **contas do Lote** e selecione a sua conta.
1. Selecione **Pools** **e,** em seguida, Adicione para criar uma nova piscina.
1. Na secção **Tipo de Imagem,** selecione **Galeria de Imagem Partilhada**.
1. Complete as restantes secções com informações sobre a sua imagem gerida.
1. Selecione **OK**.

![Crie uma piscina com uma imagem partilhada com o portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações para grandes piscinas

Se planeia criar uma piscina com centenas ou milhares de VMs ou mais usando uma Imagem Partilhada, utilize a seguinte orientação.

* **Números de réplica da Galeria de Imagem Partilhada.**  Para cada piscina com até 600 instâncias, recomendamos que guarde pelo menos uma réplica. Por exemplo, se estiver a criar uma piscina com 3000 VMs, deverá manter pelo menos 5 réplicas da sua imagem. Sempre sugerimos manter mais réplicas do que requisitos mínimos para um melhor desempenho.

* **Redimensionar o tempo.** Se a sua piscina contiver um número fixo de nós (se não fizer escala automática), aumente a `resizeTimeout` propriedade da piscina dependendo do tamanho da piscina. Para cada 1000 VMs, o tempo de redimensionação recomendado é de pelo menos 15 minutos. Por exemplo, o tempo de redimensionação recomendado para uma piscina com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral aprofundada do Lote, consulte Desenvolver soluções de [computação paralela em larga escala com lote](batch-api-basics.md).
