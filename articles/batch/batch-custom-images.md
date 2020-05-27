---
title: Fornecer uma piscina personalizada a partir de uma imagem gerida
description: Crie um pool de Lote a partir de um recurso de imagem gerido para fornecer nódos de computação com o software e dados para a sua aplicação.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: fbb336ff9d3d53cc53004c577e291afdba7702f6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847995"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Use uma imagem gerida para criar uma piscina de máquinas virtuais

Para criar uma imagem personalizada para as máquinas virtuais da sua piscina de Lote (VMs), pode utilizar uma imagem gerida para criar uma Galeria de [Imagem Partilhada](batch-sig-images.md). A utilização de apenas uma imagem gerida também é suportada, mas apenas para versões API até 2019-08-01.

> [!IMPORTANT]
> Na maioria dos casos, deve criar imagens personalizadas utilizando a Galeria de Imagem Partilhada. Ao utilizar a Galeria de Imagem Partilhada, pode fornecer piscinas mais rapidamente, escalar quantidades maiores de VMs e ter uma maior fiabilidade ao fornecer VMs. Para saber mais, consulte [Use a Galeria de Imagem Partilhada para criar uma piscina personalizada.](batch-sig-images.md)

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerido.** Para criar um conjunto de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerido na mesma subscrição Azure e região como a conta Batch. A imagem deve ser criada a partir de instantâneos do disco osso do VM e opcionalmente dos seus discos de dados anexados.
  - Use uma imagem personalizada única para cada piscina que cria.
  - Para criar uma piscina com a imagem utilizando as APIs do lote, especifique o ID de **recurso** da imagem, que é do formulário `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - O recurso de imagem gerido deve existir durante toda a vida útil da piscina para permitir a escala e pode ser removido após a eliminação da piscina.

- Autenticação do **Diretório Ativo Azure (Azure AD).** A API do cliente Batch deve utilizar a autenticação Azure AD. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

Em Azure, pode preparar uma imagem gerida a partir de:

- Instantâneos de um Sistema operativo e discos de dados de um Azure VM
- Um Azure VM generalizado com discos geridos
- Um VHD generalizado no local carregado para a nuvem

Para escalar as piscinas de Lote de forma fiável com uma imagem gerida, recomendamos a criação da imagem gerida usando *apenas* o primeiro método: utilizando instantâneos dos discos do VM. Os seguintes passos mostram como preparar um VM, tirar uma foto e criar uma imagem gerida a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar um VM

Se está a criar um novo VM para a imagem, utilize uma imagem de primeira parte do Azure Marketplace apoiada pelo Batch como imagem base para a sua imagem gerida. Apenas as imagens da primeira festa podem ser usadas como imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas pelo Azure Batch, consulte a operação do agente de [nóso lista SKUs.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Não pode usar uma imagem de terceiros que tenha licença adicional e termos de compra como imagem base. Para obter informações sobre estas imagens do Marketplace, consulte as orientações para Os VMs [linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) ou [Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms)

- Certifique-se de que o VM é criado com um disco gerido. Esta é a definição de armazenamento padrão quando cria um VM.
- Não instale extensões Azure, como a extensão do Script Personalizado, no VM. Se a imagem contiver uma extensão pré-instalada, o Azure poderá encontrar problemas ao implantar a piscina do Lote.
- Ao utilizar discos de dados anexados, é necessário montar e formatar os discos de dentro de um VM para os utilizar.
- Certifique-se de que a imagem base de OS que fornece utiliza a unidade temporária predefinida. O agente do nó batch espera atualmente a unidade temporária padrão.
- Uma vez que o VM esteja em execução, ligue-o via RDP (para Windows) ou SSH (para Linux). Instale qualquer software ou copiar dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo VM

Um instantâneo é uma cópia completa e só de leitura de um VHD. Para criar uma imagem instantânea de um Sistema operativo ou de dados de um VM, pode utilizar o portal Azure ou ferramentas de linha de comando. Para passos e opções para criar um instantâneo, consulte a orientação para [VMs Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem a partir de um ou mais instantâneos

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas de linha de comando Azure, como a [imagem az criar](/cli/azure/image) comando. Pode criar uma imagem especificando um instantâneo de disco OS e opcionalmente um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image"></a>Criar uma piscina a partir de uma imagem personalizada

Depois de ter encontrado o ID de recurso da sua imagem gerida, crie uma piscina de imagem personalizada a partir dessa imagem. Os seguintes passos mostram-lhe como criar uma piscina de imagem personalizada utilizando o Serviço de Lote ou a Gestão de Lotes.

> [!NOTE]
> Certifique-se de que a identidade que utiliza para autenticação adato azure tem permissões para o recurso de imagem. Consulte soluções de [serviço Authenticate Batch com Diretório Ativo](batch-aad-auth.md).
>
> O recurso para a imagem gerida deve existir durante toda a vida útil da piscina. Se o recurso subjacente for eliminado, a piscina não pode ser dimensionada.

### <a name="batch-service-net-sdk"></a>Serviço de Lote .NET SDK

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
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image definition name}");
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
```

### <a name="batch-management-rest-api"></a>API REST de Gestão de Lotes

URI da API REST

```http
 PUT https://management.azure.com/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Batch/batchAccounts/{account name}/pools/{pool name}?api-version=2020-03-01
```

Corpo do Pedido

```json
 {
   "properties": {
     "vmSize": "{VM size}",
     "deploymentConfiguration": {
       "virtualMachineConfiguration": {
         "imageReference": {
           "id": "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/images/{image name}"
         },
         "nodeAgentSkuId": "{Node Agent SKU ID}"
       }
     }
   }
 }
```

## <a name="considerations-for-large-pools"></a>Considerações para grandes piscinas

Se planeia criar uma piscina com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir a orientação anterior para usar uma imagem criada a partir de um instantâneo VM.

Note também as seguintes considerações:

- **Limites** de tamanho - O lote limita o tamanho da piscina a 2500 nós de computação dedicados, ou 1000 nós de baixa prioridade, quando se utiliza uma imagem personalizada.

  Se utilizar a mesma imagem (ou múltiplas imagens com base no mesmo instantâneo subjacente) para criar várias piscinas, os nós totais de computação nas piscinas não podem exceder os limites anteriores. Não recomendamos usar uma imagem ou o seu instantâneo subjacente para mais do que uma única piscina.

  Os limites podem ser reduzidos se configurar a piscina com [piscinas NAT de entrada](pool-endpoint-configuration.md).

- **Redimensionar** o tempo limite - Se a sua piscina contiver um número fixo de nós (não escala automática), aumente a propriedade redimensionada Timeout da piscina para um valor como 20-30 minutos. Se a sua piscina não atingir o seu tamanho-alvo dentro do período de tempo, execute outra operação de [redimensionação](/rest/api/batchservice/pool/resize).

  Se você planeja uma piscina com mais de 300 nós de computação, você pode precisar redimensionar a piscina várias vezes para alcançar o tamanho do alvo.
  
Ao utilizar a Galeria de [Imagem Partilhada,](batch-sig-images.md)pode criar piscinas maiores com as suas imagens personalizadas juntamente com mais réplicas de Imagem Partilhada. Utilizando imagens partilhadas, o tempo que a piscina leva para chegar ao estado estável é até 25% mais rápido, e a latência vm idle é até 30% mais curta.

## <a name="considerations-for-using-packer"></a>Considerações para a utilização de Packer

A criação de um recurso de imagem gerido diretamente com o Packer só pode ser feita com as contas do modo de subscrição do utilizador. Para as contas do modo de serviço do Lote, você precisa criar um VHD primeiro, em seguida, importar o VHD para um recurso de imagem gerido. Dependendo do modo de alocação de piscina (subscrição do utilizador ou serviço de Lote), os seus passos para criar um recurso de imagem gerido variarão.

Certifique-se de que o recurso utilizado para criar a imagem gerida existe para a vida útil de qualquer piscina que refira a imagem personalizada. Se não o fizer, pode resultar em falhas na atribuição de piscinas e/ou falhas de redimensionação.

Se a imagem ou o recurso subjacente forremovido, poderá obter um erro semelhante ao: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Se tiver este erro, certifique-se de que o recurso subjacente não foi removido.

Para obter mais informações sobre a utilização do Packer para criar um VM, consulte [Construir uma imagem Linux com packer](../virtual-machines/linux/build-image-with-packer.md) ou construir uma imagem do Windows com [packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar a Galeria de [Imagem Partilhada](batch-sig-images.md) para criar uma piscina personalizada.
- Para uma visão geral aprofundada do Lote, consulte o fluxo de [trabalho e os recursos](batch-service-workflow-features.md)do serviço batch .
