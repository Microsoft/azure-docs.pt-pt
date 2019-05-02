---
title: Aprovisionar conjunto do Azure Batch a partir de uma imagem personalizada | Documentos da Microsoft
description: Crie um lote de nós que contêm o software e os dados que precisa para a sua aplicação de computação do conjunto a partir de uma imagem personalizada para aprovisionar. Imagens personalizadas são uma forma eficiente para configurar nós de computação para executar as cargas de trabalho do Batch.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/15/2019
ms.author: lahugh
ms.openlocfilehash: 886dea0e53519870aaa27dea721a9eb78515cf86
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64706323"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Utilizar uma imagem personalizada para criar um conjunto de máquinas virtuais 

Quando cria um conjunto do Azure Batch através da configuração de Máquina Virtual, especifique uma imagem de VM que fornece o sistema operativo para cada nó de computação no conjunto. Pode criar um conjunto de máquinas virtuais com uma imagem suportados do Azure Marketplace ou com uma imagem personalizada (uma imagem de VM ter criado e configurado por conta própria). A imagem personalizada tem de ser um *imagem gerida* recursos na mesma subscrição do Azure e a região que a conta do Batch.

## <a name="benefits-of-custom-images"></a>Benefícios de imagens personalizadas

Quando fornecer uma imagem personalizada, tem controle sobre a configuração do sistema operativo e o tipo de sistema operativo e discos de dados a serem utilizados. Sua imagem personalizada pode incluir aplicações e dados de referência que ficam disponíveis em todos os nós do conjunto de Batch assim que terem sido aprovisionados.

Com uma imagem personalizada poupa tempo na preparação de nós de computação do seu agrupamento para executar a sua carga de trabalho do Batch. Apesar de poder utilizar uma imagem do Azure Marketplace e instalar software em cada nó de computação após o aprovisionamento, com uma imagem personalizada pode ser mais eficiente.

Com uma imagem personalizada configurada para o seu cenário, pode fornecer várias vantagens:

- **Configurar o sistema operacional (SO)**. Pode personalizar a configuração do disco do sistema operativo da imagem. 
- **Pré-instalação aplicações.** Pré-instale aplicações no disco do SO, o que é mais eficiente e menos propenso a erros de instalação de aplicativos depois de aprovisionar nós de computação através de uma tarefa de início.
- **Economizar tempo de reinício de VMs.** Instalação de aplicativos normalmente requer a reinicialização da VM, que é um processo demorado. Pode salvar a hora de reinício através da pré-instalação de aplicativos. 
- **Copie grandes quantidades de dados uma vez.** Fazer parte de dados estáticos da imagem personalizada gerida através de cópia para discos de dados de uma imagem gerida. Isso só precisa ser feito uma vez e disponibiliza a cada nó do conjunto de dados.
- **Escolha de tipos de disco.** Tem a opção de usar o armazenamento premium para o disco do SO e o disco de dados.
- **Aumente a conjuntos de tamanhos grandes.** Quando utiliza uma imagem personalizada gerida para criar um conjunto, o conjunto pode crescer sem necessidade de fazer cópias do blob de imagem VHDs.

## <a name="prerequisites"></a>Pré-requisitos

- **Um recurso de imagem gerida**. Para criar um conjunto de máquinas virtuais utilizando uma imagem personalizada, terá de ter ou criar um recurso de imagem gerida na mesma subscrição do Azure e a região que a conta do Batch. A imagem deve ser criada a partir de instantâneos do disco do SO da VM e, opcionalmente, seus discos de dados anexados. Para obter mais informações e passos para preparar uma imagem gerida, consulte a secção seguinte.
  - Utilize uma imagem personalizada individual para cada conjunto de que criar.
  - Para criar um conjunto com a imagem usando as APIs do Batch, especifique a **ID de recurso** da imagem, que é o formato `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Para utilizar o portal, utilize o **nome** da imagem.  
  - O recurso de imagem gerida deve existir durante a vida útil do conjunto para permitir que o vertical e pode ser removido depois do conjunto ser eliminado.

- **Autenticação do Azure Active Directory (AAD)**. A API do cliente do Batch tem de utilizar autenticação do AAD. Suporte do Azure Batch para AAD está documentado no [soluções de serviço do Batch de autenticar com o Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Preparar uma imagem personalizada

No Azure pode preparar uma imagem gerida a partir de instantâneos do SO de uma VM do Azure e discos de dados, a partir de uma VM generalizada do Azure com discos geridos ou a partir de um VHD generalizado no local que carrega. Para dimensionar conjuntos do Batch com confiança com uma imagem personalizada, recomendamos que crie uma imagem gerida utilizando *apenas* o primeiro método: utilizando instantâneos de discos da VM. Consulte os seguintes passos para preparar uma VM, tire um instantâneo e criar uma imagem a partir do instantâneo.

### <a name="prepare-a-vm"></a>Preparar uma VM

Se estiver a criar uma nova VM para a imagem, utilize uma primeira imagem do Azure Marketplace terceiros suportada pelo Batch, como a imagem base para a sua imagem gerida. Apenas as primeiras imagens de terceiros podem ser utilizadas como uma imagem base. Para obter uma lista completa das referências de imagens do Azure Marketplace suportado pelo Azure Batch, consulte a [SKUs de agente de nó de lista](/rest/api/batchservice/account/listnodeagentskus) operação.

> [!NOTE]
> Não é possível utilizar uma imagem de terceiros que tenha licenças adicionais e os termos de compra que sua imagem base. Para obter informações sobre estas imagens do Marketplace, consulte a documentação de orientação para [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) ou [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VMs.


* Certifique-se de que a VM é criada com um disco gerido. Esta é a predefinição de armazenamento ao criar uma VM.
* Não instale extensões do Azure, como a extensão de Script personalizado na VM. Se a imagem tiver uma extensão pré-instalada, o Azure pode encontrar problemas ao implementar o conjunto do Batch.
* Quando utilizar discos de dados anexados, tem de montar e formatar os discos a partir de uma VM para utilizá-los.
* Certifique-se de que a imagem do SO base que indicar utiliza a unidade temp predefinida. O agente de nó do Batch espera, atualmente, a unidade temp predefinida.
* Quando a VM estiver em execução, ligue ao mesmo através de RDP (para Windows) ou SSH (para Linux). Instalar qualquer software necessário ou copiar os dados desejados.  

### <a name="create-a-vm-snapshot"></a>Criar um instantâneo VM

Um instantâneo é uma cópia completa, só de leitura de um VHD. Para criar um instantâneo de SO de uma VM ou discos de dados, pode utilizar o portal do Azure ou ferramentas da linha de comandos. Para passos e opções para criar um instantâneo, veja as orientações para [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) ou [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VMs.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Criar uma imagem a partir de instantâneos de um ou mais

Para criar uma imagem gerida a partir de um instantâneo, utilize ferramentas de linha de comandos do Azure como o [criar imagem de az](/cli/azure/image) comando. Pode criar uma imagem, especificando um instantâneo do disco de SO e, opcionalmente, um ou mais instantâneos de disco de dados.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Criar um agrupamento de uma imagem personalizada no portal

Depois de guardar sua imagem personalizada e sabe o ID de recurso ou o nome, crie um conjunto do Batch a partir dessa imagem. Os passos seguintes mostram como criar um conjunto a partir do portal do Azure.

> [!NOTE]
> Se estiver a criar o conjunto utilizar uma das APIs do Batch, certifique-se de que a identidade utilizada para autenticação de AAD tem permissões para o recurso de imagem. Ver [soluções de serviço do Batch de autenticar com o Active Directory](batch-aad-auth.md).
>
> O recurso para a imagem gerida tem de existir durante o ciclo de vida do conjunto. Se o recurso subjacente é eliminado, o conjunto não pode ser ampliado. 

1. No portal do Azure, navegue para a sua conta do Batch. Esta conta tem de ser na mesma subscrição e região que o grupo de recursos que contém a imagem personalizada. 
2. Na **definições** janela à esquerda, selecione a **conjuntos** item de menu.
3. Na **agrupamentos** janela, selecione a **Add** comando.
4. Sobre o **adicionar conjunto** janela, selecione **imagem personalizada (Linux/Windows)** do **tipo de imagem** lista pendente. Partir do **imagem de VM personalizada** lista pendente, selecione o nome de imagem (ditada breve do ID do recurso).
5. Selecione o correto **Editor/oferta/Sku** para sua imagem personalizada.
6. Especifique as restantes definições, incluindo o **tamanho de nó**, **nós dedicados de destino**, e **nós de prioridade de baixa**, bem como qualquer pretendido definições opcionais.

    Por exemplo, para uma imagem personalizada do Microsoft Windows Server 2016 de Datacenter, o **adicionar conjunto** é apresentada a janela conforme mostrado abaixo:

    ![Adicionar conjunto de imagem personalizada do Windows](media/batch-custom-images/add-pool-custom-image.png)
  
Para verificar se um conjunto existente se baseia numa imagem personalizada, consulte a **sistema operativo** na seção de resumo de recursos da **conjunto** janela. Se o conjunto foi criado a partir de uma imagem personalizada, ele é definido como **imagem de VM personalizada**.

Todas as imagens personalizadas associadas um conjunto são apresentadas do conjunto **propriedades** janela.

## <a name="considerations-for-large-pools"></a>Considerações sobre a grandes pools

Se planeja criar um conjunto com centenas de VMs ou mais com uma imagem personalizada, é importante seguir as orientações anteriores para utilizar uma imagem criada a partir de um instantâneo VM.

Tenha também em atenção o seguinte:

- **Limites de tamanho** -Batch limita o tamanho do conjunto a 2500 nós de computação dedicados ou nós de baixa prioridade de 1000, quando utilizar uma imagem personalizada.

  Se utilizar a mesma imagem (ou várias imagens com base no mesmo instantâneo subjacente) para criar múltiplos conjuntos, nós de computação total nos agrupamentos de não podem exceder os limites anteriores. Não é recomendado utilizar uma imagem ou o instantâneo subjacente durante mais de um único conjunto.

  Limites podem ser reduzidos se configurar o conjunto com [conjuntos de NAT de entrada](pool-endpoint-configuration.md).

- **Tempo limite de redimensionamento** - se o conjunto tiver fixa aumentar o número de nós (dimensionamento automático não), a propriedade resizeTimeout do conjunto para um valor como 20 a 30 minutos. Se o seu conjunto não chega ao seu tamanho de destino dentro do período de tempo limite, executar outra [redimensionar operação](/rest/api/batchservice/pool/resize).

  Se pretender um conjunto com mais de 300 nós de computação, precisará de redimensionar o conjunto de várias vezes para alcançar o tamanho de destino.

## <a name="considerations-for-using-packer"></a>Considerações sobre como utilizar o Packer

Criação de um recurso de imagem gerida diretamente com o Packer só pode ser feito com contas de Batch de modo de subscrição de utilizador. Para contas de modo de serviço do Batch, terá de criar um VHD em primeiro lugar, em seguida, importar o VHD para um recurso de imagem gerida. Dependendo do modo de alocação agrupamento, (subscrição de utilizador ou serviço do Batch), os passos para criar um recurso de imagem gerida irão variar.

Certifique-se de que o recurso utilizado para criar a imagem gerida existe para os tempos de vida de qualquer conjunto a referenciar a imagem personalizada. Falhas ao fazer isso podem resultar em falhas de alocação do conjunto e/ou falhas de redimensionar. 

Se a imagem ou o recurso subjacente for removido, poderá receber um erro semelhante a: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Se isto acontecer, certifique-se de que o recurso subjacente não foi removido.

Para obter mais informações sobre como utilizar o Packer para criar uma VM, consulte [crie uma imagem do Linux com o Packer](../virtual-machines/linux/build-image-with-packer.md) ou [crie uma imagem do Windows com o Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral aprofundada do Batch, consulte [soluções com o Batch de computação paralelas em grande escala de desenvolver](batch-api-basics.md).
