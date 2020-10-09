---
title: Mova um cofre de recuperação do local de Azure para outra região
description: Descreve como mover um cofre dos Serviços de Recuperação (Recuperação do Local de Azure) para outra região do Azure
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: a8cb5ec782b5932c13e321b2ba2d6513597fef52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87422645"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Mova um cofre de serviços de recuperação e configuração de recuperação do local de Azure para outra região do Azure

Existem vários cenários em que talvez queira mover os seus recursos Azure existentes de uma região para outra. Exemplos são para a gestão, razões de governação, ou por causa de fusões e aquisições de empresas. Um dos recursos relacionados que pode querer mover quando mover os seus VMs Azure é a configuração de recuperação de desastres. 

Não há forma de mover uma configuração de recuperação de desastres existente de uma região para outra. Isto porque configuraste a tua região alvo com base na tua região de VM de origem. Quando decide alterar a região de origem, as configurações anteriormente existentes da região alvo não podem ser reutilizadas e devem ser reiniciadas. Este artigo define o processo passo a passo para reconfigurar a configuração da recuperação de desastres e movê-lo para uma região diferente.

Neste documento, irá:

> [!div class="checklist"]
> * Verifique os pré-requisitos para a mudança.
> * Identifique os recursos utilizados pela Recuperação do Sítio Azure.
> * Desativar a replicação.
> * Apagar os recursos.
> * Configurar a Recuperação do Local com base na nova região de origem para os VMs.

> [!IMPORTANT]
> Atualmente, não há uma maneira de mover um cofre dos Serviços de Recuperação e a configuração de recuperação de desastres como é para uma região diferente. Este artigo guia-o através do processo de desativação da replicação e da sua instalação na nova região.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que remove e apaga a configuração de recuperação de desastres antes de tentar mover os VMs Azure para uma região diferente. 

  > [!NOTE]
  > Se a sua nova região-alvo para o Azure VM for a mesma que a região alvo de recuperação de desastres, pode usar a configuração de replicação existente e movê-la. Siga os passos em [Move Azure IaaS VMs para outra região de Azure.](azure-to-azure-tutorial-migrate.md)

- Certifique-se de que está a tomar uma decisão informada e que as partes interessadas são informadas. O seu VM não será protegido contra desastres até que a mudança do VM esteja completa.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Identificar os recursos que foram usados pela Recuperação do Sítio Azure
Recomendamos que faça este passo antes de avançar para o próximo. É mais fácil identificar os recursos relevantes enquanto os VMs estão a ser replicados.

Para cada Azure VM que está a ser replicado, vá a **Itens Protegidos**  >  **Propriedades de Itens Replicados**  >  **Properties** e identifique os seguintes recursos:

- Grupo de recursos-alvo
- Conta de armazenamento de cache
- Conta de armazenamento alvo (no caso de um Azure VM não gerido baseado em disco) 
- Rede alvo


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Desative a configuração de recuperação de desastres existente

1. Vai para o cofre dos Serviços de Recuperação.
2. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina e selecione **Desativar a replicação**.
3. Repita este passo para todos os VMs que pretende mover.

> [!NOTE]
> O serviço de mobilidade não será desinstalado dos servidores protegidos. Deve desinstalá-lo manualmente. Se planeia proteger novamente o servidor, pode não conseguir desinstalar o serviço de mobilidade.

## <a name="delete-the-resources"></a>Eliminar os recursos

1. Vai para o cofre dos Serviços de Recuperação.
2. Selecione **Eliminar**.
3. Elimine todos os outros recursos que [identificou anteriormente.](#identify-the-resources-that-were-used-by-azure-site-recovery)
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Mover VMs Azure para a nova região alvo

Siga os passos nestes artigos com base na sua exigência de mover os VMs Azure para a região alvo:

- [Mover VMs do Azure para outra região](azure-to-azure-tutorial-migrate.md)
- [Mover VMs Azure em Zonas de Disponibilidade](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>Criação da Recuperação do Local com base na nova região de origem para os VMs

Configure a recuperação de desastres para os VMs Azure que foram transferidos para a nova região seguindo os passos na criação de [recuperação de desastres para os VMs do Azure.](azure-to-azure-tutorial-enable-replication.md)
