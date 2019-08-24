---
title: Mover uma configuração de Azure Site Recovery para outra região do Azure | Microsoft Docs
description: Diretrizes para mover uma configuração de Site Recovery para outra região do Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 2cf06a0c4e35d22cbad260201183516db2f07436
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013467"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Mover um cofre dos serviços de recuperação e Azure Site Recovery configuração para outra região do Azure

Há vários cenários nos quais você pode desejar mover seus recursos existentes do Azure de uma região para outra. Os exemplos são para capacidade de gerenciamento, motivos de governança ou devido a aquisições e fusões da empresa. Um dos recursos relacionados que você pode querer mover ao mover suas VMs do Azure é a configuração de recuperação de desastre. 

Não há nenhuma maneira de primeira classe de mover uma configuração de recuperação de desastre existente de uma região para outra. Isso ocorre porque você configurou sua região de destino com base na sua região de VM de origem. Quando você decide alterar a região de origem, as configurações existentes anteriores da região de destino não podem ser reutilizadas e devem ser redefinidas. Este artigo define o processo passo a passo para reconfigurar a configuração de recuperação de desastres e movê-la para uma região diferente.

Neste documento, você vai:

> [!div class="checklist"]
> * Verifique os pré-requisitos para a movimentação.
> * Identifique os recursos que foram usados pelo Azure Site Recovery.
> * Desabilite a replicação.
> * Exclua os recursos.
> * Configure Site Recovery com base na nova região de origem para as VMs.

> [!IMPORTANT]
> Atualmente, não existe uma maneira de primeira classe de mover um cofre de serviços de recuperação e a configuração de recuperação de desastres para uma região diferente. Este artigo orienta você pelo processo de desabilitar a replicação e configurá-la na nova região.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de remover e excluir a configuração de recuperação de desastre antes de tentar mover as VMs do Azure para uma região diferente. 

  > [!NOTE]
  > Se sua nova região de destino para a VM do Azure for igual à região de destino de recuperação de desastre, você poderá usar sua configuração de replicação existente e movê-la. Siga as etapas em [mover VMs de IaaS do Azure para outra região do Azure](azure-to-azure-tutorial-migrate.md).

- Certifique-se de que você está tomando uma decisão informada e que as partes interessadas são informadas. Sua VM não será protegida contra desastres até que a movimentação da VM seja concluída.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificar os recursos que foram usados pelo Azure Site Recovery
Recomendamos que você faça essa etapa antes de prosseguir para a próxima. É mais fácil identificar os recursos relevantes enquanto as VMs estão sendo replicadas.

Para cada VM do Azure que está sendo replicada, acesse **itens** > protegidos**Propriedades** de**itens** > replicados e identifique os seguintes recursos:

- Grupo de recursos de destino
- Conta de armazenamento em cache
- Conta de armazenamento de destino (no caso de uma VM do Azure baseada em disco não gerenciada) 
- Rede de destino


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Desabilitar a configuração de recuperação de desastre existente

1. Vá para o cofre dos serviços de recuperação.
2. Em **itens** > protegidos itens**replicados**, clique com o botão direito do mouse no computador e selecione **desabilitar replicação**.
3. Repita essa etapa para todas as VMs que você deseja mover.

> [!NOTE]
> O serviço de mobilidade não será desinstalado dos servidores protegidos. Você deve desinstalá-lo manualmente. Se você planeja proteger o servidor novamente, poderá ignorar a desinstalação do serviço de mobilidade.

## <a name="delete-the-resources"></a>Eliminar os recursos

1. Vá para o cofre dos serviços de recuperação.
2. Selecione **Eliminar**.
3. Exclua todos os outros recursos que você [identificou anteriormente](#identify-the-resources-that-were-used-by-azure-site-recovery).
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Mover VMs do Azure para a nova região de destino

Siga as etapas nestes artigos com base em seu requisito para mover VMs do Azure para a região de destino:

- [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
- [Mover VMs do Azure para Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Configurar Site Recovery com base na nova região de origem para as VMs

Configure a recuperação de desastre para as VMs do Azure que foram movidas para a nova região seguindo as etapas em [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-tutorial-enable-replication.md).
