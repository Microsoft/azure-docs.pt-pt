---
title: Utilize a Galeria de Imagens Partilhadas para criar uma piscina de imagens personalizada
description: As piscinas de imagem personalizadas são uma forma eficiente de configurar os nós computacional para executar as cargas de trabalho do Lote.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f0ba6270e6b6b4fcd258d8f5b3668931706f95b5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888356"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>Utilize a Galeria de Imagens Partilhadas para criar uma piscina de imagens personalizada

Quando cria um pool Azure Batch utilizando a Configuração da Máquina Virtual, especifica uma imagem VM que fornece o sistema operativo para cada nó de computação na piscina. Pode criar um conjunto de máquinas virtuais com uma imagem Azure Marketplace suportada ou criar uma imagem personalizada com uma [imagem da Galeria de Imagens Partilhada.](../virtual-machines/windows/shared-image-galleries.md)

## <a name="benefits-of-the-shared-image-gallery"></a>Benefícios da Galeria de Imagens Partilhadas

Quando utiliza a Galeria de Imagens Partilhada para a sua imagem personalizada, tem controlo sobre o tipo e configuração do sistema operativo, bem como o tipo de discos de dados. A sua Imagem Partilhada pode incluir aplicações e dados de referência que ficam disponíveis em todos os nós do pool batch assim que são a provisionados.

Também pode ter várias versões de uma imagem necessária para o seu ambiente. Quando se utiliza uma versão de imagem para criar um VM, a versão de imagem é usada para criar novos discos para o VM.

A utilização de uma imagem partilhada poupa tempo na preparação dos nós de computação da sua piscina para executar a carga de trabalho do Lote. É possível utilizar uma imagem do Azure Marketplace e instalar software em cada nó de computação após o provisionamento, mas usar uma Imagem Partilhada é tipicamente mais eficiente. Além disso, pode especificar várias réplicas para a Imagem Partilhada para que quando criar piscinas com muitos VMs (mais de 600 VMs), economize tempo na criação da piscina.

A utilização de uma Imagem Partilhada configurada para o seu cenário pode proporcionar várias vantagens:

- **Use as mesmas imagens em todas as regiões.** Pode criar réplicas de Imagem Partilhada em diferentes regiões para que todas as suas piscinas utilizem a mesma imagem.
- **Configure o sistema operativo (OS).** Pode personalizar a configuração do disco do sistema operativo da imagem.
- **Pré-instalar aplicações.** A pré-instalação de aplicações no disco OS é mais eficiente e menos propensa a erros do que a instalação de aplicações depois de ter previsto os nós computacionais com uma tarefa inicial.
- **Copie grandes quantidades de dados uma vez.** Faça parte dos dados estáticos da imagem partilhada gerida copiando-a para os discos de dados de uma imagem gerida. Isto só precisa ser feito uma vez e disponibilizar dados para cada nó da piscina.
- **Cresça piscinas para tamanhos maiores.** Com a Galeria de Imagens Partilhadas, pode criar piscinas maiores com as suas imagens personalizadas juntamente com mais réplicas de Imagem Partilhada.
- **Melhor desempenho do que usar apenas uma imagem gerida como uma imagem personalizada.** Para um pool de imagem personalizado image shared Image, o tempo para alcançar o estado estável é até 25% mais rápido, e a latência ociosa VM é até 30% mais curta.
- **Imagem de versão e agrupamento para uma gestão mais fácil.** A definição de agrupamento de imagens contém informações sobre o porquê da imagem ter sido criada, para que é o SISTEMA e informação sobre a utilização da imagem. Agrupar imagens permite uma gestão de imagem mais fácil. Para obter mais informações, consulte [as definições de imagem.](../virtual-machines/windows/shared-image-galleries.md#image-definitions)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> Você precisa autenticar usando Azure AD. Se utilizar o auth de tecla partilhada, obterá um erro de autenticação.  

- **Uma conta do Azure Batch.** Para criar uma conta Batch, consulte os quickstarts do Lote utilizando o [portal Azure](quick-create-portal.md) ou [O Azure CLI](quick-create-cli.md).

- **Uma imagem da Galeria de Imagens Partilhada.** Para criar uma Imagem Partilhada, é necessário ter ou criar um recurso de imagem gerido. A imagem deve ser criada a partir de instantâneos do disco de oss do VM e opcionalmente dos seus discos de dados anexados.

> [!NOTE]
> Se a Imagem Partilhada não estiver na mesma subscrição que a conta Batch, deverá [registar o fornecedor de recursos ch Microsoft.Bat](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) para essa subscrição. As duas assinaturas devem estar no mesmo inquilino da AD Azure.
>
> A imagem pode estar numa região diferente, desde que tenha réplicas na mesma região que a sua conta Batch.

Se utilizar uma aplicação AD Azure para criar um pool de imagens personalizado com uma imagem da Galeria de Imagens Partilhada, essa aplicação deve ter recebido um [papel incorporado Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) que lhe dá acesso à Imagem Partilhada. Pode conceder este acesso no portal Azure navegando na Imagem Partilhada, selecionando o **controlo de acesso (IAM)** e adicionando uma atribuição de funções para a aplicação.

## <a name="prepare-a-shared-image"></a>Preparar uma imagem partilhada

Em Azure, pode preparar uma imagem partilhada a partir de uma imagem gerida, que pode ser criada a partir de:

- Instantâneos de um sistema operativo EZure VM e discos de dados
- Um VM Azure generalizado com discos geridos
- Um VHD generalizado no local enviado para a nuvem

> [!NOTE]
> Atualmente, o Batch apenas suporta imagens partilhadas generalizadas. Não é possível criar um pool de imagens personalizado a partir de uma imagem partilhada especializada neste momento.

Os passos a seguir mostram como preparar um VM, tirar uma fotografia e criar uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar um VM

Se estiver a criar um novo VM para a imagem, utilize uma primeira imagem do Azure Marketplace suportada por Batch como a imagem base para a sua imagem gerida. Apenas as primeiras imagens de festas podem ser usadas como imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas por Azure Batch, consulte a operação [skus](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) do agente de nó de lista.

> [!NOTE]
> Não pode usar uma imagem de terceiros que tenha licença e termos de compra adicionais como a sua imagem base. Para obter informações sobre estas imagens do Marketplace, consulte as orientações para [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) VMs.

- Certifique-se de que o VM é criado com um disco gerido. Esta é a definição de armazenamento predefinido quando cria um VM.
- Não instale extensões Azure, como a extensão de Script Personalizado, no VM. Se a imagem contiver uma extensão pré-instalada, o Azure pode encontrar problemas ao implantar o pool do Lote.
- Ao utilizar discos de dados anexados, é necessário montar e formatar os discos a partir de um VM para os utilizar.
- Certifique-se de que a imagem de SO base que fornece utiliza a unidade temporária predefinido. O agente de nó de lote espera atualmente a unidade temporária predefinido.
- Uma vez em funcionamento o VM, ligue-o através de RDP (para Windows) ou SSH (para Linux). Instale qualquer software necessário ou copie os dados pretendidos.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo VM

Uma foto é uma cópia completa e só de leitura de um VHD. Para criar uma imagem instantânea do SISTEMA ou discos de dados de um VM, pode utilizar o portal Azure ou as ferramentas de linha de comando. Para obter passos e opções para criar uma imagem instantânea, consulte as orientações para Os VMs [do Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [do Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem a partir de um ou mais instantâneos

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas da linha de comando Azure, como a [imagem az criar](/cli/azure/image) comando. Crie uma imagem especificando um instantâneo de disco de SO e opcionalmente um ou mais instantâneos de disco de dados.

### <a name="create-a-shared-image-gallery"></a>Criar um Shared Image Gallery

Uma vez criada com sucesso a sua imagem gerida, precisa de criar uma Galeria de Imagens Partilhada para disponibilizar a sua imagem personalizada. Para aprender a criar uma Galeria de Imagens Partilhadas para as suas imagens, consulte [Criar uma Galeria de Imagens Partilhadas com Azure CLI](../virtual-machines/shared-images-cli.md) ou Criar uma Galeria de [Imagens Partilhadas utilizando o portal Azure.](../virtual-machines/linux/shared-images-portal.md)

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Criar uma piscina a partir de uma imagem partilhada usando o Azure CLI

Para criar uma piscina a partir da sua Imagem Partilhada utilizando o CLI Azure, utilize o `az batch pool create` comando. Especifique o ID de imagem partilhado no `--image` campo. Certifique-se de que o tipo de SO e SKU correspondem às versões especificadas por `--node-agent-sku-id`

> [!NOTE]
> Você precisa autenticar usando Azure AD. Se utilizar o auth de tecla partilhada, obterá um erro de autenticação.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Criar uma piscina a partir de uma imagem partilhada usando C #

Em alternativa, pode criar uma piscina a partir de uma imagem partilhada utilizando o C# SDK.

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

Também pode criar uma piscina a partir de uma imagem partilhada utilizando o Python SDK: 

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Criar uma piscina a partir de uma imagem partilhada usando o portal Azure

Use os seguintes passos para criar uma piscina a partir de uma imagem partilhada no portal Azure.

1. Abra o [portal do Azure](https://portal.azure.com).
1. Vá às **contas do Batch** e selecione a sua conta.
1. Selecione **Pools** e, em seguida, **Adicione** para criar uma nova piscina.
1. Na secção **Tipo de Imagem,** selecione **Galeria de Imagens Partilhadas.**
1. Complete as restantes secções com informações sobre a sua imagem gerida.
1. Selecione **OK**.

![Crie uma piscina com uma imagem partilhada com o portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações para grandes piscinas

Se planeia criar uma piscina com centenas ou milhares de VMs ou mais usando uma Imagem Partilhada, utilize as seguintes orientações.

- **Números de réplica da Galeria de Imagens Partilhadas.**  Para cada piscina com até 300 casos, recomendamos que guarde pelo menos uma réplica. Por exemplo, se estiver a criar uma piscina com 3000 VMs, deve manter pelo menos 10 réplicas da sua imagem. Sugerimos sempre manter mais réplicas do que requisitos mínimos para um melhor desempenho.

- **Redimensionar o tempo limite.** Se a sua piscina contiver um número fixo de nós (se não for auto-escalado), aumente a `resizeTimeout` propriedade da piscina dependendo do tamanho da piscina. Para cada 1000 VMs, o tempo limite recomendado de redimensionar é de pelo menos 15 minutos. Por exemplo, o tempo limite recomendado para uma piscina com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral aprofundada do Lote, consulte o fluxo de trabalho e os recursos do [serviço Batch.](batch-service-workflow-features.md)
- Conheça a [Galeria de Imagens Partilhadas.](../virtual-machines/windows/shared-image-galleries.md)
