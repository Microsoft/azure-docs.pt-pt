---
title: Fornecer uma piscina personalizada a partir de uma imagem gerida
description: Crie um pool de Lote a partir de um recurso de imagem gerido para fornecer nódos de computação com o software e dados para a sua aplicação.
ms.topic: article
ms.date: 09/16/2019
ms.openlocfilehash: b08c6a609516bcebaca64cf1c186d75887b098e3
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780212"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Use uma imagem gerida para criar uma piscina de máquinas virtuais

Para criar uma imagem personalizada para as máquinas virtuais do seu Pool Batch (VMs), pode utilizar a Galeria de [Imagem Partilhada,](batch-sig-images.md)ou um recurso de *imagem gerido.*

> [!TIP]
> Na maioria dos casos, deve criar imagens personalizadas utilizando a Galeria de Imagem Partilhada. Ao utilizar a Galeria de Imagem Partilhada, pode fornecer piscinas mais rapidamente, escalar quantidades maiores de VMs e ter uma maior fiabilidade ao fornecer VMs. Para saber mais, consulte [Use a Galeria de Imagem Partilhada para criar uma piscina personalizada.](batch-sig-images.md)

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerido.** Para criar um conjunto de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerido na mesma subscrição Azure e região como a conta Batch. A imagem deve ser criada a partir de instantâneos do disco osso do VM e opcionalmente dos seus discos de dados anexados. Para obter mais informações e passos para preparar uma imagem gerida, consulte a secção seguinte.
  - Use uma imagem personalizada única para cada piscina que cria.
  - Para criar uma piscina com a imagem utilizando as APIs do lote, especifique o ID de **recurso** da imagem, que é do formulário `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` . Para utilizar o portal, use o **nome** da imagem.  
  - O recurso de imagem gerido deve existir durante toda a vida útil da piscina para permitir a escala e pode ser removido após a eliminação da piscina.

- Autenticação do **Diretório Ativo Azure (AAD).** A API do cliente Batch deve utilizar a autenticação AAD. O suporte do Lote Azure para AAD está documentado em soluções de [serviço Authenticate Batch com Diretório Ativo](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

Em Azure, pode preparar uma imagem gerida a partir de:

- Instantâneos de um Sistema operativo e discos de dados de um Azure VM
- Um Azure VM generalizado com discos geridos
- Um VHD generalizado no local carregado para a nuvem

Para escalar as piscinas de Lote de forma fiável com uma imagem personalizada, recomendamos a criação de uma imagem gerida usando *apenas* o primeiro método: utilizando instantâneos dos discos do VM. Veja os seguintes passos para preparar um VM, tire uma foto e crie uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar um VM

Se está a criar um novo VM para a imagem, utilize uma imagem de primeira parte do Azure Marketplace apoiada pelo Batch como imagem base para a sua imagem gerida. Apenas as imagens da primeira festa podem ser usadas como imagem base. Para obter uma lista completa de referências de imagem do Azure Marketplace suportadas pelo Azure Batch, consulte a operação do agente de [nóso lista SKUs.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Não pode usar uma imagem de terceiros que tenha licença adicional e termos de compra como imagem base. Para obter informações sobre estas imagens do Marketplace, consulte as orientações para Os VMs [linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

- Certifique-se de que o VM é criado com um disco gerido. Esta é a definição de armazenamento padrão quando cria um VM.
- Não instale extensões Azure, como a extensão do Script Personalizado, no VM. Se a imagem contiver uma extensão pré-instalada, o Azure poderá encontrar problemas ao implantar a piscina do Lote.
- Ao utilizar discos de dados anexados, é necessário montar e formatar os discos de dentro de um VM para os utilizar.
- Certifique-se de que a imagem base de OS que fornece utiliza a unidade temporária predefinida. O agente do nó batch espera atualmente a unidade temporária padrão.
- Uma vez que o VM esteja em execução, ligue-o via RDP (para Windows) ou SSH (para Linux). Instale qualquer software ou copiar dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo VM

Um instantâneo é uma cópia completa e só de leitura de um VHD. Para criar uma imagem instantânea de um Sistema operativo ou de dados de um VM, pode utilizar o portal Azure ou ferramentas de linha de comando. Para passos e opções para criar um instantâneo, consulte a orientação para [VMs Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem a partir de um ou mais instantâneos

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas de linha de comando Azure, como a [imagem az criar](/cli/azure/image) comando. Pode criar uma imagem especificando um instantâneo de disco OS e opcionalmente um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Crie uma piscina a partir de uma imagem personalizada no portal

Depois de ter guardado a sua imagem personalizada e conhecer o seu ID de recurso ou nome, crie uma piscina de Lote a partir dessa imagem. Os seguintes passos mostram-lhe como criar uma piscina a partir do portal Azure.

> [!NOTE]
> Se estiver a criar a piscina utilizando uma das APIs do Lote, certifique-se de que a identidade que utiliza para autenticação AAD tem permissões para o recurso de imagem. Consulte soluções de [serviço Authenticate Batch com Diretório Ativo](batch-aad-auth.md).
>
> O recurso para a imagem gerida deve existir durante toda a vida útil da piscina. Se o recurso subjacente for eliminado, a piscina não pode ser dimensionada.

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta deve estar na mesma subscrição e região que o grupo de recursos que contém a imagem personalizada.
2. Na janela **Definições** à esquerda, selecione o item do menu **Pools.**
3. Na janela **Pools,** selecione o comando **Adicionar.**
4. Na janela **Add Pool,** selecione **Custom Image (Linux/Windows)** a partir do dropdown do **Tipo de Imagem.** A partir da queda de **imagem Custom VM,** selecione o nome de imagem (forma curta do ID do recurso).
5. Selecione o **Editor/Oferta/Sku** correto para a sua imagem personalizada.
6. Especifique as definições restantes, incluindo o tamanho do **nó,** **os nós dedicados**ao alvo e **os nós de baixa prioridade,** bem como quaisquer configurações opcionais desejadas.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Add Pool** aparece como mostrado abaixo:

    ![Adicione piscina a partir da imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se uma piscina existente é baseada numa imagem personalizada, consulte a propriedade do **Sistema Operativo** na secção de resumo de recursos da janela **pool.** Se a piscina foi criada a partir de uma imagem personalizada, está definida para **Imagem VM Personalizada**.

Todas as imagens personalizadas associadas a uma piscina são exibidas na janela **Propriedades** da piscina.

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

- Para uma visão geral aprofundada do Lote, consulte o fluxo de [trabalho e os recursos](batch-service-workflow-features.md)do serviço batch .
