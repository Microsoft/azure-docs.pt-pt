---
title: Use uma imagem gerida para criar uma piscina de imagem personalizada
description: Crie um conjunto de imagens personalizadas Batch a partir de uma imagem gerida até provisão de nós computacional com o software e dados para a sua aplicação.
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 45bf0f8b3cb335b7025ff06189bf6bc4e0a896ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85851286"
---
# <a name="use-a-managed-image-to-create-a-custom-image-pool"></a>Use uma imagem gerida para criar uma piscina de imagem personalizada

Para criar uma piscina de imagens personalizada para as máquinas virtuais (VMs) da sua piscina de Lote, pode utilizar uma imagem gerida para criar uma [imagem da Galeria de Imagens Partilhada.](batch-sig-images.md) A utilização de apenas uma imagem gerida também é suportada, mas apenas para versões API até e incluindo 2019-08-01. 

> [!IMPORTANT]
> Na maioria dos casos, deverá criar imagens personalizadas utilizando a Galeria de Imagens Partilhadas. Ao utilizar a Galeria de Imagens Partilhadas, pode providenciar piscinas mais rapidamente, escalar maiores quantidades de VMs e melhorar a fiabilidade ao a provisionar VMs. Para saber mais, consulte [a Galeria de Imagens Partilhadas para criar uma piscina personalizada.](batch-sig-images.md)

Este tópico explica como criar um pool de imagem personalizado usando apenas uma imagem gerida.

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerido.** Para criar um conjunto de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerido na mesma subscrição e região Azure que a conta Batch. A imagem deve ser criada a partir de instantâneos do disco de oss do VM e opcionalmente dos seus discos de dados anexados.
  - Use uma imagem personalizada única para cada piscina que criar.
  - Para criar uma piscina com a imagem utilizando as APIs do lote, especifique o **ID** de recurso da imagem, que é do formulário `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` .
  - O recurso de imagem gerido deve existir durante o tempo de vida útil da piscina para permitir a escala e pode ser removido após a cobertura da piscina.

- **Autenticação do Azure Ative Directory (Azure AD).** A API do cliente Batch deve utilizar a autenticação AZure AD. O suporte do Azure Batch para o Azure AD está documentado em [Autenticar soluções de serviço do Batch com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-managed-image"></a>Preparar uma imagem gerida

Em Azure, pode preparar uma imagem gerida a partir de:

- Instantâneos de um sistema operativo EZure VM e discos de dados
- Um VM Azure generalizado com discos geridos
- Um VHD generalizado no local enviado para a nuvem

Para escalar os pools do Batch de forma fiável com uma imagem gerida, recomendamos a criação da imagem gerida usando *apenas* o primeiro método: utilizando instantâneos dos discos do VM. Os passos seguintes mostram como preparar um VM, tirar uma foto e criar uma imagem gerida a partir do instantâneo.

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

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas da linha de comando Azure, como a [imagem az criar](/cli/azure/image) comando. Pode criar uma imagem especificando um instantâneo de disco de SO e opcionalmente um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-managed-image"></a>Criar uma piscina a partir de uma imagem gerida

Assim que tiver encontrado o ID de recurso da sua imagem gerida, crie uma piscina de imagem personalizada a partir dessa imagem. Os passos a seguir mostram-lhe como criar um pool de imagens personalizado utilizando o Serviço de Lote ou a Gestão de Lotes.

> [!NOTE]
> Certifique-se de que a identidade que utiliza para a autenticação Azure AD tem permissões para o recurso de imagem. Consulte [soluções de serviço Authenticate Batch com Diretório Ativo](batch-aad-auth.md).
>
> O recurso para a imagem gerida deve existir durante toda a vida da piscina. Se o recurso subjacente for eliminado, o pool não pode ser dimensionado.

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

- **Limites de tamanho** - O lote limita o tamanho da piscina a 2500 nós de computação dedicados, ou 1000 nós de baixa prioridade, quando utiliza uma imagem personalizada.

  Se utilizar a mesma imagem (ou múltiplas imagens com base no mesmo instantâneo subjacente) para criar várias piscinas, os nós de computação total nas piscinas não podem exceder os limites anteriores. Não recomendamos a utilização de uma imagem ou do seu instantâneo subjacente para mais do que uma única piscina.

  Os limites podem ser reduzidos se configurar a piscina com [piscinas NAT de entrada](pool-endpoint-configuration.md).

- **Redimensione o tempo limite** - Se a sua piscina contiver um número fixo de nós (não de escala automática), aumente a propriedade de redimensioneTimeout da piscina para um valor como 20-30 minutos. Se a sua piscina não atingir o seu tamanho-alvo dentro do período de tempo limite, efetue outra [operação de redimensionação](/rest/api/batchservice/pool/resize).

  Se você planejar uma piscina com mais de 300 nós de computação, você pode precisar de redimensionar a piscina várias vezes para alcançar o tamanho do alvo.
  
Ao utilizar a Galeria de [Imagens Partilhadas,](batch-sig-images.md)pode criar piscinas maiores com as suas imagens personalizadas juntamente com mais réplicas de Imagem Partilhada. Usando imagens partilhadas, o tempo que a piscina leva para chegar ao estado estável é até 25% mais rápido, e a latência ociosa VM é até 30% mais curta.

## <a name="considerations-for-using-packer"></a>Considerações para a utilização do Packer

A criação de um recurso de imagem gerido diretamente com o Packer só pode ser feita com contas de lote do modo de subscrição do utilizador. Para as contas do modo de serviço Batch, é necessário criar primeiro um VHD e, em seguida, importar o VHD para um recurso de imagem gerido. Dependendo do modo de atribuição da piscina (subscrição do utilizador ou serviço Batch), os seus passos para criar um recurso de imagem gerido variarão.

Certifique-se de que o recurso utilizado para criar a imagem gerida existe para as vidas de qualquer piscina que se refere à imagem personalizada. Se não o fizer, pode resultar em falhas de atribuição de piscinas e/ou redimensionar falhas.

Se a imagem ou o recurso subjacente for removido, poderá obter um erro semelhante a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` . Se obter este erro, certifique-se de que o recurso subjacente não foi removido.

Para obter mais informações sobre a utilização do Packer para criar um VM, consulte [construir uma imagem Linux com Packer](../virtual-machines/linux/build-image-with-packer.md) ou construir uma imagem do Windows com o [Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como usar a Galeria de [Imagens Partilhadas](batch-sig-images.md) para criar uma piscina personalizada.
- Para obter uma visão geral aprofundada do Lote, consulte o fluxo de trabalho e os recursos do [serviço Batch.](batch-service-workflow-features.md)
