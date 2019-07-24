---
title: Provisionar o pool do lote do Azure de uma imagem personalizada | Microsoft Docs
description: Crie um pool do lote de uma imagem personalizada para provisionar nós de computação que contêm o software e os dados de que você precisa para seu aplicativo. Imagens personalizadas são uma maneira eficiente de configurar nós de computação para executar suas cargas de trabalho do lote.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 54456ff48ca7104cc1ba10ddc47cec1bc364ddf6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323695"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Usar uma imagem personalizada para criar um pool de máquinas virtuais 

Ao criar um pool do lote do Azure usando a configuração de máquina virtual, você especifica uma imagem de VM que fornece o sistema operacional para cada nó de computação no pool. Você pode criar um pool de máquinas virtuais com uma imagem do Azure Marketplace com suporte ou com uma imagem personalizada (uma imagem de VM que você criou e configurou por conta própria). A imagem personalizada deve ser um recurso de *imagem gerenciada* na mesma assinatura e região do Azure que a conta do lote.

## <a name="benefits-of-custom-images"></a>Benefícios de imagens personalizadas

Ao fornecer uma imagem personalizada, você tem controle sobre a configuração do sistema operacional e o tipo de sistema operacional e discos de dados a serem usados. Sua imagem personalizada pode incluir aplicativos e dados de referência que se tornam disponíveis em todos os nós do pool do lote assim que eles são provisionados.

O uso de uma imagem personalizada poupa tempo na preparação dos nós de computação do pool para executar a carga de trabalho do lote. Embora você possa usar uma imagem do Azure Marketplace e instalar o software em cada nó de computação após o provisionamento, o uso de uma imagem personalizada pode ser mais eficiente.

O uso de uma imagem personalizada configurada para seu cenário pode fornecer várias vantagens:

- **Configure o sistema operacional (SO)** . Você pode personalizar a configuração do disco do sistema operacional da imagem. 
- **Pré-instalar aplicativos.** Pré-instale aplicativos no disco do sistema operacional, que é mais eficiente e menos propenso a erros do que instalar aplicativos depois de provisionar os nós de computação usando uma tarefa de início.
- **Economize o tempo de reinicialização nas VMs.** A instalação do aplicativo normalmente requer a reinicialização da VM, o que é demorado. Você pode economizar tempo de reinicialização Instalando aplicativos previamente. 
- **Copie quantidades muito grandes de dados uma vez.** Faça parte dos dados estáticos da imagem personalizada gerenciada copiando-os para os discos de dados de uma imagem gerenciada. Isso só precisa ser feito uma vez e disponibiliza os dados para cada nó do pool.
- **Opção de tipos de disco.** Você tem a opção de usar o armazenamento Premium para o disco do sistema operacional e o disco de dados.
- **Aumente os pools para grandes tamanhos.** Quando você usa uma imagem personalizada gerenciada para criar um pool, o pool pode crescer sem a necessidade de fazer cópias de VHDs de blob de imagem.

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerenciada**. Para criar um pool de máquinas virtuais usando uma imagem personalizada, você precisa ter ou criar um recurso de imagem gerenciada na mesma assinatura e região do Azure que a conta do lote. A imagem deve ser criada a partir de instantâneos do disco do sistema operacional da VM e, opcionalmente, dos discos de dados anexados. Para obter mais informações e etapas para preparar uma imagem gerenciada, consulte a seção a seguir.
  - Use uma imagem personalizada exclusiva para cada pool que você criar.
  - Para criar um pool com a imagem usando as APIs do lote, especifique a **ID de recurso** da imagem, que é do formulário `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Para usar o portal, use o **nome** da imagem.  
  - O recurso de imagem gerenciada deve existir durante o tempo de vida do pool para permitir a expansão e pode ser removido após a exclusão do pool.

- **Autenticação do Azure Active Directory (AAD)** . A API do cliente do lote deve usar a autenticação do AAD. O suporte do lote do Azure para o AAD está documentado em [autenticar soluções de serviço de lote com Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

No Azure, você pode preparar uma imagem gerenciada de instantâneos do sistema operacional e dos discos de dados de uma VM do Azure, de uma VM do Azure generalizada com discos gerenciados ou de um VHD local generalizado que você carrega. Para dimensionar os pools do lote de forma confiável com uma imagem personalizada, é recomendável criar uma imagem gerenciada usando *apenas* o primeiro método: usando instantâneos dos discos da VM. Consulte as etapas a seguir para preparar uma VM, tirar um instantâneo e criar uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se você estiver criando uma nova VM para a imagem, use uma imagem de terceiros do Azure Marketplace com suporte pelo lote como a imagem base para a imagem gerenciada. Somente imagens de terceiros podem ser usadas como uma imagem de base. Para obter uma lista completa de referências de imagem do Azure Marketplace com suporte pelo lote do Azure, consulte a [lista de SKUs de agente de nó de listas](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Você não pode usar uma imagem de terceiros que tenha licença adicional e termos de compra como sua imagem base. Para obter informações sobre essas imagens do Marketplace, consulte as [diretrizes](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) para VMs](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) do Linux ou [do Windows.


* Verifique se a VM foi criada com um disco gerenciado. Essa é a configuração de armazenamento padrão quando você cria uma VM.
* Não instale extensões do Azure, como a extensão de script personalizado, na VM. Se a imagem contiver uma extensão pré-instalada, o Azure poderá encontrar problemas ao implantar o pool do lote.
* Ao usar discos de dados anexados, você precisa montar e formatar os discos de dentro de uma VM para usá-los.
* Verifique se a imagem do sistema operacional base que você forneceu usa a unidade temporária padrão. O agente de nó de lote atualmente espera a unidade temporária padrão.
* Depois que a VM estiver em execução, conecte-se a ela via RDP (para Windows) ou SSH (para Linux). Instale qualquer software necessário ou copie os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo de VM

Um instantâneo é uma cópia completa e somente leitura de um VHD. Para criar um instantâneo do sistema operacional ou dos discos de dados de uma VM, você pode usar o portal do Azure ou as ferramentas de linha de comando. Para obter as etapas e opções para criar um instantâneo, consulte as diretrizes para VMs do [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou do [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) .

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem de um ou mais instantâneos

Para criar uma imagem gerenciada de um instantâneo, use as ferramentas de linha de comando do Azure, como o comando [AZ Image Create](/cli/azure/image) . Você pode criar uma imagem especificando um instantâneo de disco do sistema operacional e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um pool com base em uma imagem personalizada no portal

Depois de salvar a imagem personalizada e souber sua ID de recurso ou nome, crie um pool do lote a partir dessa imagem. As etapas a seguir mostram como criar um pool do portal do Azure.

> [!NOTE]
> Se você estiver criando o pool usando uma das APIs do lote, certifique-se de que a identidade usada para a autenticação do AAD tenha permissões para o recurso de imagem. Consulte [autenticar soluções de serviço de lote com Active Directory](batch-aad-auth.md).
>
> O recurso para a imagem gerenciada deve existir durante o tempo de vida do pool. Se o recurso subjacente for excluído, o pool não poderá ser dimensionado. 

1. No portal do Azure, navegue para a sua conta do Batch. Essa conta deve estar na mesma assinatura e região que o grupo de recursos que contém a imagem personalizada. 
2. Na janela **configurações** à esquerda, selecione o item de  menu pools.
3. Na janela **pools** , selecione o comando **Adicionar** .
4. Na janela **Adicionar pool** , selecione **imagem personalizada (Linux/Windows)** na lista suspensa **tipo de imagem** . Na lista suspensa **imagem de VM personalizada** , selecione o nome da imagem (forma abreviada da ID do recurso).
5. Selecione o Publicador/ **oferta/SKU** correto para sua imagem personalizada.
6. Especifique as configurações necessárias restantes, incluindo o **tamanho do nó**, os **nós dedicados de destino**e os **nós de baixa prioridade**, bem como as configurações opcionais desejadas.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server Datacenter 2016, a janela **Adicionar pool** aparece como mostrado abaixo:

    ![Adicionar pool de imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um pool existente é baseado em uma imagem personalizada, consulte a propriedade **sistema operacional** na seção Resumo de recursos da janela **pool** . Se o pool tiver sido criado a partir de uma imagem personalizada, ele será definido como **imagem de VM personalizada**.

Todas as imagens personalizadas associadas a um pool são exibidas na janela **Propriedades** do pool.

## <a name="considerations-for-large-pools"></a>Considerações para grandes pools

Se você planeja criar um pool com centenas de VMs ou mais usando uma imagem personalizada, é importante seguir as diretrizes anteriores para usar uma imagem criada a partir de um instantâneo de VM.

Observe também o seguinte:

- **Limites de tamanho** – o lote limita o tamanho do pool a 2500 nós de computação dedicados ou 1000 nós de baixa prioridade, quando você usa uma imagem personalizada.

  Se você usar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar vários pools, o total de nós de computação nos pools não poderá exceder os limites anteriores. Não é recomendável usar uma imagem ou seu instantâneo subjacente para mais de um único pool.

  Os limites podem ser reduzidos se você configurar o pool com [pools NAT de entrada](pool-endpoint-configuration.md).

- **Redimensionar tempo limite** – se o pool contiver um número fixo de nós (não dimensionamento automático), aumente a propriedade resizeTimeout do pool para um valor como 20-30 minutos. Se o pool não atingir seu tamanho de destino dentro do período de tempo limite, execute outra [operação](/rest/api/batchservice/pool/resize)de redimensionamento.

  Se você planejar um pool com mais de 300 nós de computação, talvez seja necessário redimensionar o pool várias vezes para alcançar o tamanho do destino.

## <a name="considerations-for-using-packer"></a>Considerações sobre o uso do Packr

Criar um recurso de imagem gerenciada diretamente com o Packer só pode ser feito com contas do lote no modo de assinatura do usuário. Para contas do modo de serviço de lote, você precisa primeiro criar um VHD e, em seguida, importar o VHD para um recurso de imagem gerenciada. Dependendo do modo de alocação do pool (assinatura do usuário ou serviço de lote), suas etapas para criar um recurso de imagem gerenciada irão variar.

Verifique se o recurso usado para criar a imagem gerenciada existe para os tempos de vida de qualquer pool que referencie a imagem personalizada. A falha em fazer isso pode resultar em falhas de alocação de pool e/ou em redimensionamento de falhas. 

Se a imagem ou o recurso subjacente for removido, você poderá receber um erro semelhante a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se isso acontecer, verifique se o recurso subjacente não foi removido.

Para obter mais informações sobre como usar o Packer para criar uma VM, consulte [criar uma imagem do Linux com](../virtual-machines/linux/build-image-with-packer.md) o packr ou [criar uma imagem do Windows com o packr](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma visão geral detalhada do lote, consulte [desenvolver soluções de computação paralela em larga escala com o lote](batch-api-basics.md).
