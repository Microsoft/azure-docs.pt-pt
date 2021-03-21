---
title: Controlo de manutenção para máquinas virtuais Azure utilizando o portal Azure
description: Saiba como controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o controlo de manutenção e o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0dea30723a3ebe1598d6304ac3c98bfe1b55b466
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563018"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Atualizações de controlo com o Controlo de Manutenção e o portal Azure

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados ao Azure. Este tópico abrange as opções do portal Azure para o controlo da Manutenção. Para obter mais sobre os benefícios da utilização do controlo de manutenção, das suas limitações e outras opções de gestão, consulte [atualizações da plataforma de gestão com o Controlo de Manutenção.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

1. Inicie sessão no portal do Azure.

1. Procurar **configurações de manutenção**.

   ![Screenshot mostrando como abrir configurações de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Clique em **Adicionar**.

   ![Screenshot mostrando como adicionar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Escolha um grupo de subscrição e recursos, forneça um nome para a configuração e escolha uma região. Clique em **Seguinte**.

   ![Screenshot mostrando básicos de configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Adicione etiquetas e valores. Clique em **Seguinte**.

   ![Screenshot mostrando como adicionar tags a uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Reveja o resumo. Clique em **Criar**.

   ![Screenshot mostrando como criar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Depois de concluída a implementação, clique **em Ir para o recurso**.

   ![Screenshot mostrando implementação de configuração de manutenção completa](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Atribuir a configuração

Na página de detalhes da configuração de manutenção, clique em Atribuições e, em seguida, clique em **Atribuir recurso**. 

![Screenshot mostrando como atribuir um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Selecione os recursos a que deseja a configuração de manutenção atribuída e clique em **Ok**. A coluna **Tipo** mostra se o recurso é um VM isolado ou um hospedeiro dedicado a Azure. O VM tem de estar em execução para atribuir a configuração. Ocorre um erro se tentar atribuir uma configuração a um VM que está parado. 

<!---Shantanu to add details about the error case--->

![Screenshot mostrando como selecionar um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Configuração de verificação

Pode verificar se a configuração foi aplicada corretamente ou verificar se existe qualquer configuração de manutenção que esteja atualmente atribuída através de **Configurações de Manutenção**. A coluna **Tipo** mostra se a configuração é atribuída a um VM isolado ou a um hospedeiro dedicado a Azure. 

![Screenshot mostrando como verificar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Também pode verificar a configuração de uma máquina virtual específica na página de propriedades. Clique **em Manutenção** para ver a configuração atribuída à máquina virtual.

![Screenshot mostrando como verificar manutenção para um anfitrião](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Verifique se há atualizações pendentes

Há também duas formas de verificar se as atualizações estão pendentes para uma configuração de manutenção. Nas **Configurações de Manutenção**, nos detalhes da configuração, clique em **Atribuições** e verifique o **estado da manutenção**.

![Screenshot mostrando como verificar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Também pode verificar um anfitrião específico usando **Máquinas Virtuais** ou propriedades do anfitrião dedicado. 

![Screenshot que mostra o estado de manutenção realçado.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Aplicar atualizações

Pode aplicar atualizações pendentes a pedido utilizando **Máquinas Virtuais.** Nos detalhes do VM, clique em **Manutenção** e clique **em Aplicar a manutenção agora**.

![Screenshot mostrando como aplicar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Verifique o estado da aplicação de atualizações 

Pode verificar o progresso das atualizações para uma configuração em **Configurações de Manutenção** ou utilizando **Máquinas Virtuais.** Nos detalhes do VM, clique em **Manutenção.** No exemplo seguinte, o **estado de Manutenção** mostra que está **pendente** uma atualização.

![Screenshot mostrando como verificar o estado das atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Excluir uma configuração de manutenção

Para eliminar uma configuração, abra os detalhes da configuração e clique em **Eliminar**.

![Screenshot que mostra como apagar uma configuração.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
