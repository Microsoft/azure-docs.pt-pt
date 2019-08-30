---
title: Use a Galeria de imagens compartilhadas para criar um pool – lote do Azure | Microsoft Docs
description: Crie um pool do lote com a Galeria de imagens compartilhadas para provisionar imagens personalizadas para nós de computação que contêm o software e os dados de que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente de configurar nós de computação para executar suas cargas de trabalho do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: lahugh
ms.openlocfilehash: 3c2213c25a8fdc6d6545711bd2af9b94662ee609
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141831"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Usar a Galeria de imagens compartilhadas para criar um pool

Ao criar um pool do lote do Azure usando a configuração de máquina virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace com suporte ou criar uma imagem personalizada com a [Galeria de imagens](../virtual-machines/windows/shared-image-galleries.md)compartilhadas.

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

* **Uma imagem da Galeria de imagens compartilhada**. Para obter mais informações e etapas para preparar uma imagem compartilhada, consulte [criar uma galeria de imagens compartilhada com CLI do Azure](../virtual-machines/linux/shared-images.md) ou [criar uma galeria de imagens compartilhadas usando o portal do Azure](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> Sua imagem compartilhada deve estar na mesma assinatura que a conta do lote. Sua imagem compartilhada pode estar em regiões diferentes, contanto que ela tenha réplicas na mesma região que a sua conta do lote.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Criar um pool com base em uma imagem compartilhada usando o CLI do Azure

Para criar um pool a partir de sua imagem compartilhada usando o CLI do Azure, `az batch pool create` use o comando. Especifique a ID da imagem compartilhada no `--image` campo. Verifique se o tipo de sistema operacional e a SKU correspondem às versões especificadas por`--node-agent-sku-id`

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
1. Na seção **tipo de imagem** , selecione **Galeria de imagens**compartilhadas.
1. Conclua as seções restantes com informações sobre sua imagem gerenciada.
1. Selecione **OK**.

![Crie um pool com base em uma imagem compartilhada com o Portal.](media/batch-custom-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Considerações para grandes pools

Se você planeja criar um pool com centenas ou milhares de VMs ou mais usando uma imagem compartilhada, use as diretrizes a seguir.

* **Números de réplica da Galeria de imagens compartilhadas.**  Para cada pool com até 600 instâncias, recomendamos que você mantenha pelo menos uma réplica. Por exemplo, se você estiver criando um pool com 3000 VMs, deverá manter pelo menos 5 réplicas de sua imagem. Sempre sugerimos manter mais réplicas do que os requisitos mínimos para melhor desempenho.

* **Redimensionar tempo limite** Se o pool contiver um número fixo de nós (se não autoescala), aumente `resizeTimeout` a propriedade do pool dependendo do tamanho do pool. Para cada 1000 VMs, o tempo limite de redimensionamento recomendado é de pelo menos 15 minutos. Por exemplo, o tempo limite de redimensionamento recomendado para um pool com 2000 VMs é de pelo menos 30 minutos.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
