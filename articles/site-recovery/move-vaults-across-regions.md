---
title: Mova um cofre de recuperação de sítio sinuoso para outra região
description: Descreve como mover um cofre de Serviços de Recuperação (Recuperação do Local Azure) para outra região de Azure
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74090301"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Mova um cofre de serviços de recuperação e configuração de recuperação do site Azure para outra região de Azure

Existem vários cenários em que talvez queira mover os seus recursos Azure existentes de uma região para outra. Exemplos são para a gestão, razões de governação, ou por causa de fusões e aquisições de empresas. Um dos recursos relacionados que você pode querer mover quando move os seus VMs Azure é a configuração de recuperação de desastres. 

Não há forma de mover uma configuração de recuperação de desastres existente de uma região para outra. Isto porque configuraste a tua região alvo com base na tua região VM fonte. Quando decide mudar a região de origem, as configurações anteriormente existentes da região alvo não podem ser reutilizadas e devem ser redefinidas. Este artigo define o processo passo a passo para reconfigurar a configuração da recuperação de desastres e movê-la para outra região.

Neste documento, irá:

> [!div class="checklist"]
> * Verifique os pré-requisitos para a mudança.
> * Identifique os recursos utilizados pela Azure Site Recovery.
> * Desativar a replicação.
> * Apagar os recursos.
> * Configurar a Recuperação do Local com base na nova região de origem para os VMs.

> [!IMPORTANT]
> Atualmente, não há forma de mover um cofre dos Serviços de Recuperação e a configuração de recuperação de desastres como é para uma região diferente. Este artigo guia-o através do processo de desativação da replicação e da sua criação na nova região.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que remove e elimina a configuração de recuperação de desastres antes de tentar mover os VMs Azure para outra região. 

  > [!NOTE]
  > Se a sua nova região alvo para o VM Azure for a mesma que a região-alvo de recuperação de desastres, pode usar a configuração de replicação existente e movê-la. Siga os passos em [Move Azure IaaS VMs para outra região de Azure.](azure-to-azure-tutorial-migrate.md)

- Certifique-se de que está a tomar uma decisão informada e que as partes interessadas são informadas. O seu VM não será protegido contra desastres até que a mudança do VM esteja completa.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identifique os recursos utilizados pela Azure Site Recovery
Recomendamos que faça este passo antes de passar para o próximo. É mais fácil identificar os recursos relevantes enquanto os VMs estão a ser replicados.

Para cada VM Azure que está a ser replicado, vá a Itens **Protegidos** > **Propriedades de Itens Replicados** > **e** identifique os seguintes recursos:

- Grupo de recursos-alvo
- Conta de armazenamento cache
- Conta de armazenamento-alvo (em caso de um VM Azure baseado em disco não gerido) 
- Rede de destino


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Desativar a configuração de recuperação de desastres existente

1. Vá ao cofre dos Serviços de Recuperação.
2. Em **itens protegidos,** > **Replicated Items**clique à direita na máquina e selecione **a replicação de desativação**.
3. Repita este passo para todos os VMs que quer mover.

> [!NOTE]
> O serviço de mobilidade não será desinstalado dos servidores protegidos. Deve desinstalá-lo manualmente. Se planeia proteger novamente o servidor, pode não instalar o serviço de mobilidade.

## <a name="delete-the-resources"></a>Eliminar os recursos

1. Vá ao cofre dos Serviços de Recuperação.
2. Selecione **Eliminar**.
3. Elimine todos os outros recursos que [identificou anteriormente.](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Mover VMs Azure para a nova região alvo

Siga os passos nestes artigos com base na sua exigência de mover VMs Azure para a região alvo:

- [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
- [Mover VMs do Azure para Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Configurar a Recuperação do Site com base na nova região de origem para os VMs

Configure a recuperação de desastres para os VMs Azure que foram transferidos para a nova região seguindo os passos na criação de recuperação de [desastres para os VMs Azure](azure-to-azure-tutorial-enable-replication.md).
