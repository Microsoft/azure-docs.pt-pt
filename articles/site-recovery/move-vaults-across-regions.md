---
title: Movendo a configuração de Azure Site Recovery para outra região do Azure | Microsoft Docs
description: Diretrizes para mover a configuração de Site Recovery para outra região do Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ba0e2577d6fb8bac66322917936fe06825af0d96
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708299"
---
# <a name="moving-recovery-services-vault-and--azure-site-recovery-configuration-to-another-azure-region"></a>Movendo o cofre dos serviços de recuperação e Azure Site Recovery a configuração para outra região do Azure

Há vários cenários em que você deseja mover seus recursos existentes do Azure de uma região para outra capacidade de gerenciamento, motivos de governança ou devido a fusões/aquisições da empresa, etc. Um dos recursos relacionados que você pode querer mover ao mover as VMs do Azure é a configuração de recuperação de desastres para o mesmo. Não há nenhuma maneira de primeira classe de mover uma configuração de recuperação de desastre existente de uma região para outra. Isso é essencialmente porque você teria configurado sua região de destino com base em sua região de VM de origem e, quando você decidir alterar isso, as configurações existentes anteriores da região de destino não poderão ser reutilizadas e precisarão ser redefinidas. Este artigo define o processo passo a passo para reconfigurar a configuração de recuperação de desastres e mover para uma região diferente.

Neste documento, você vai:

> [!div class="checklist"]
> * Verificar pré-requisitos para a movimentação
> * Identificar os recursos que foram usados pelo Azure Site Recovery 
> * Desativar a replicação
> * Eliminar os recursos 
> * Configure novamente o site Recovery com base na nova região de origem para as VMs.

> [!IMPORTANT]
> Atualmente, não há nenhuma forma de primeira classe de mover um cofre dos serviços de recuperação e a configuração de DR como está para uma região diferente, este documento orienta o cliente pelo processo de desabilitação da replicação e sua redefinição na nova região.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de remover e excluir a configuração de recuperação de desastre antes de tentar mover as VMs do Azure para uma região diferente. 

> [!NOTE]
> Se sua nova região de destino para a VM do Azure for igual à região de destino de recuperação de desastre, você poderá usar sua configuração de replicação existente e movê-la com base nas etapas mencionadas [aqui](azure-to-azure-tutorial-migrate.md) 

- Certifique-se de que você está tomando uma decisão informada e que as partes interessadas são informadas, pois sua VM não será protegida contra desastres até que a mudança da VM seja concluída. 

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificar os recursos que foram usados pelo Azure Site Recovery
É recomendável fazer essa etapa antes de prosseguir para a próxima, pois será mais fácil identificar os recursos relevantes enquanto as VMs ainda estão sendo replicadas

Para cada VM do Azure que está sendo replicada, navegue até **itens** > protegidos**Propriedades** de**itens**>replicados e identifique os seguintes recursos

- Grupo de recursos de destino
- Conta de armazenamento em cache
- Conta de armazenamento de destino (no caso de VM do Azure baseada em disco não gerenciado) 
- Rede de destino


## <a name="disable-existing-disaster-recovery-configuration"></a>Desabilitar a configuração de recuperação de desastre existente

1. Navegue até o **cofre dos serviços de recuperação** 
2.  Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
3. Repita isso para todas as VMs que você deseja mover.
> [!NOTE]
> o serviço de mobilidade não será desinstalado dos servidores protegidos, você precisará desinstalá-lo manualmente. Se você planeja proteger o servidor novamente, poderá ignorar a desinstalação do serviço de mobilidade.

## <a name="delete-the-resources"></a>Eliminar os recursos

1. Vá até o **cofre dos serviços de recuperação**
2. Clique em **excluir**
3. Prossiga para excluir todos os outros recursos identificados na[ etapa anterior](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Mover VMs do Azure para a nova região de destino

Siga as etapas mencionadas abaixo com base em seu requisito para mover as VMs do Azure para a região de destino.

- [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
- [Mover VMs do Azure para Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

## <a name="re-set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Reconfigurar Site Recovery com base na nova região de origem para as VMs

Configure a recuperação de desastre para as VMs do Azure que foram movidas para a nova região usando as etapas mencionadas [aqui](azure-to-azure-tutorial-enable-replication.md)
