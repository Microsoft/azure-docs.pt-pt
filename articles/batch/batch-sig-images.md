---
title: Use a Galeria de imagens compartilhadas para criar um pool personalizado – lote do Azure | Microsoft Docs
description: Crie um pool do lote com a Galeria de imagens compartilhadas para provisionar imagens personalizadas para nós de computação que contêm o software e os dados de que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente de configurar nós de computação para executar suas cargas de trabalho do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: lahugh
ms.openlocfilehash: c3f5155c7d1576657f36445562b5b425148a838a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348951"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Usar a Galeria de imagens compartilhadas para criar um pool personalizado

Ao criar um pool do lote do Azure usando a configuração de máquina virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace com suporte ou criar uma imagem personalizada com a [Galeria de imagens compartilhadas](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Benefícios da Galeria de imagens compartilhadas

Ao usar a Galeria de imagens compartilhadas para sua imagem personalizada, você tem controle sobre o tipo e a configuração do sistema operacional, bem como o tipo de discos de dados. Sua imagem compartilhada pode incluir aplicativos e dados de referência que se tornam disponíveis em todos os nós do pool do lote assim que eles são provisionados.

Você também pode ter várias versões de uma imagem conforme necessário para seu ambiente. Quando você usa uma versão de imagem para criar uma VM, a versão da imagem é usada para criar novos discos para a VM.

O uso de uma imagem compartilhada poupa tempo na preparação dos nós de computação do pool para executar a carga de trabalho do lote. É possível usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, mas usar uma imagem compartilhada normalmente é mais eficiente. Além disso, você pode especificar várias réplicas para a imagem compartilhada para que, ao criar pools com várias VMs (mais de 600 VMs), Economize tempo na criação do pool.

O uso de uma imagem compartilhada configurada para seu cenário pode fornecer várias vantagens:

* **Use as mesmas imagens em todas as regiões.** Você pode criar réplicas de imagens compartilhadas em diferentes regiões para que todos os seus pools usem a mesma imagem.
* **Configure o sistema operacional (SO).** Você pode personalizar a configuração do disco do sistema operacional da imagem.
* **Pré-instalar aplicativos.** A pré-instalação de aplicativos no disco do sistema operacional é mais eficiente e menos propenso a erros do que instalar aplicativos depois de provisionar os nós de computação com uma tarefa inicial.
* **Copie grandes quantidades de dados uma vez.** Torne dados estáticos parte da imagem compartilhada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza os dados para cada nó do pool.
* **Aumente os pools para tamanhos maiores.** Com a Galeria de imagens compartilhadas, você pode criar pools maiores com suas imagens personalizadas juntamente com mais réplicas de imagem compartilhadas.
* **Melhor desempenho do que a imagem personalizada.** Usando imagens compartilhadas, o tempo necessário para o pool alcançar o estado estacionário é de até 25% mais rápido e a latência de ociosidade da VM é de até 30% mais curta.
* **Controle de versão e agrupamento de imagem para facilitar o gerenciamento.** A definição de agrupamento de imagens contém informações sobre por que a imagem foi criada, em qual sistema operacional ele é e informações sobre como usar a imagem. O agrupamento de imagens permite um gerenciamento mais fácil de imagens. Para obter mais informações, consulte [definições de imagem](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Pré-requisitos

* **Uma conta do Azure Batch.** Para criar uma conta do lote, consulte os guias de início rápido do lote usando o [portal do Azure](quick-create-portal.md) ou [CLI do Azure](quick-create-cli.md).

* **Uma imagem da Galeria de imagens compartilhada**. Para criar uma imagem compartilhada, você precisa ter ou criar um recurso de imagem gerenciada. A imagem deve ser criada a partir de instantâneos do disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações, consulte [preparar uma imagem gerenciada](#prepare-a-managed-image).

> [!NOTE]
> Sua imagem compartilhada deve estar na mesma assinatura que a conta do lote. Sua imagem compartilhada pode estar em regiões diferentes, contanto que ela tenha réplicas na mesma região que a sua conta do lote.

## <a name="prepare-a-managed-image"></a>Preparar uma imagem gerenciada

No Azure, você pode preparar uma imagem gerenciada de:

* Instantâneos de um sistema operacional e discos de dados de uma VM do Azure
* Uma VM do Azure generalizada com discos gerenciados
* Um VHD local generalizado carregado para a nuvem

Para dimensionar os pools do lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos dos discos da VM. Consulte as etapas a seguir para preparar uma VM, tirar um instantâneo e criar uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem de terceiros do Azure Marketplace com suporte pelo lote como a imagem base para a imagem gerenciada. Somente imagens de terceiros podem ser usadas como uma imagem de base. Para obter uma lista completa de referências de imagem do Azure Marketplace com suporte pelo lote do Azure, consulte a [lista de SKUs de agente de nó de listas](/rest/api/batchservice/account/listnodeagentskus) .

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenha licença adicional e termos de compra como sua imagem base. Para obter informações sobre essas imagens do Marketplace, consulte as [diretrizes](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) para VMs [do Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) do Linux ou .

* Verifique se a VM foi criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
* Não instale extensões do Azure, como a extensão de script personalizado, na VM. Se a imagem contiver uma extensão pré-instalada, o Azure poderá encontrar problemas ao implantar o pool do lote.
* Ao usar discos de dados anexados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
* Verifique se a imagem do sistema operacional base que você forneceu usa a unidade temporária padrão. O agente de nó de lote atualmente espera a unidade temporária padrão.
* Depois que a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale qualquer software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo de VM

Um instantâneo é uma cópia completa e somente leitura de um VHD. Para criar um instantâneo do sistema operacional ou dos discos de dados de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para obter as etapas e opções para criar um instantâneo, consulte as diretrizes para VMs do [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou do [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use as ferramentas de linha de comando do Azure, como o comando [AZ Image Create](/cli/azure/image) . Crie uma imagem especificando um instantâneo de disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

### <a name="create-a-shared-image-gallery"></a>Criar uma galeria de imagens compartilhadas

Depois de criar a imagem gerenciada com êxito, você precisará criar uma galeria de imagens compartilhada para disponibilizar sua imagem personalizada. Para saber como criar uma galeria de imagens compartilhada para suas imagens, consulte [criar uma galeria de imagens compartilhadas com CLI do Azure](../virtual-machines/linux/shared-images.md) ou [criar uma galeria de imagens compartilhadas usando o portal do Azure](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Criar um pool com base em uma imagem compartilhada usando o CLI do Azure

Para criar um pool a partir de sua imagem compartilhada usando o CLI do Azure, use o comando `az batch pool create`. Especifique a ID da imagem compartilhada no campo `--image`. Verifique se o tipo de sistema operacional e a SKU correspondem às versões especificadas por `--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Criar um pool com base em uma imagem compartilhada usandoC#

Como alternativa, você pode criar um pool de uma imagem compartilhada usando o C# SDK.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Criar um pool com base em uma imagem compartilhada usando o portal do Azure

Use as etapas a seguir para criar um pool de uma imagem compartilhada no portal do Azure.

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Vá para **contas do lote** e selecione sua conta.
1. Selecione **pools** e, em seguida, **Adicionar** para criar um novo pool.
1. Na seção **tipo de imagem** , selecione **Galeria de imagens compartilhadas**.
1. Conclua as seções restantes com informações sobre sua imagem gerenciada.
1. Selecione **OK**.

![Crie um pool com base em uma imagem compartilhada com o Portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações para grandes pools

Se você planeja criar um pool com centenas ou milhares de VMs ou mais usando uma imagem compartilhada, use as diretrizes a seguir.

* **Números de réplica da Galeria de imagens compartilhadas.**  Para cada pool com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando um pool com 3000 VMs, deverá manter pelo menos 5 réplicas de sua imagem. Sempre sugerimos manter mais réplicas do que os requisitos mínimos para melhor desempenho.

* **Redimensionar tempo limite.** Se o pool contiver um número fixo de nós (se não autoescala), aumente a propriedade `resizeTimeout` do pool, dependendo do tamanho do pool. Para cada 1000 VMs, o tempo limite de redimensionamento recomendado é de pelo menos 15 minutos. Por exemplo, o tempo limite de redimensionamento recomendado para um pool com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
