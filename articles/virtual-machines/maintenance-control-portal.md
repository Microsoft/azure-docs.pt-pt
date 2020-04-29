---
title: Controlo de manutenção para máquinas virtuais Azure utilizando o portal Azure
description: Aprenda a controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o controlo de manutenção e o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: c0cb4800bdabe5eb500422fca55b3060b6422e8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82139241"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Atualizações de controlo com controlo de manutenção e portal Azure

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados azure. Este tópico abrange as opções do portal Azure para o controlo de manutenção. Para obter mais informações sobre os benefícios da utilização do controlo de Manutenção, as suas limitações e outras opções de gestão, consulte [a Managing platform updates with Maintenance Control](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

1. Inicie sessão no Portal do Azure.

1. Pesquisar **configurações**de manutenção .

   ![Screenshot mostrando como abrir configurações de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Clique em **Adicionar**.

   ![Screenshot mostrando como adicionar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Escolha um grupo de subscrição e recursos, forneça um nome para a configuração e escolha uma região. Clique em **Seguinte**.

   ![Screenshot mostrando o básico de configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Adicione etiquetas e valores. Clique em **Seguinte**.

   ![Screenshot mostrando como adicionar tags a uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Reveja o resumo. Clique em **Criar**.

   ![Screenshot mostrando como criar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Depois de concluída a implementação, clique em **recorrer**.

   ![Screenshot mostrando implementação de configuração de manutenção completa](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Atribuir a configuração

Na página de detalhes da configuração de manutenção, clique em Atribuições e, em seguida, clique em **recursos de atribuição**. 

![Screenshot mostrando como atribuir um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Selecione os recursos a que pretende a configuração de manutenção atribuída e clique em **Ok**. A coluna **Tipo** mostra se o recurso é um VM isolado ou hospedeiro dedicado azure. O VM precisa de ser em execução para atribuir a configuração. Ocorre um erro se tentar atribuir uma configuração a um VM que é parado. 

<!---Shantanu to add details about the error case--->

![Screenshot mostrando como selecionar um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Verificar a configuração

Pode verificar se a configuração foi aplicada corretamente ou verificar se vê qualquer configuração de manutenção que esteja atualmente atribuída utilizando **configurações**de manutenção . A coluna **Tipo** mostra se a configuração é atribuída a um vm isolado ou hospedeiro dedicado Azure. 

![Screenshot mostrando como verificar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Também pode verificar a configuração de uma máquina virtual específica na sua página de propriedades. Clique na **Manutenção** para ver a configuração atribuída àquelo.

![Screenshot mostrando como verificar manutenção para um hospedeiro](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Verifique as atualizações pendentes

Existem também duas formas de verificar se as atualizações estão pendentes para uma configuração de manutenção. Nas **Configurações de Manutenção,** nos detalhes para a configuração, clique em **Atribuições** e verifique **o estado de manutenção**.

![Screenshot mostrando como verificar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Também pode verificar um anfitrião específico usando **Máquinas Virtuais** ou propriedades do anfitrião dedicado. 

![Screenshot mostrando como verificar manutenção para um hospedeiro](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Aplicar atualizações

Pode aplicar atualizações pendentes a pedido utilizando **máquinas virtuais**. Nos detalhes do VM, clique em **Manutenção** e clique em **Aplicar a manutenção agora**.

![Screenshot mostrando como aplicar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Verifique o estado da aplicação de atualizações 

Pode verificar o progresso das atualizações para obter uma configuração nas **Configurações de Manutenção** ou utilizar **Máquinas Virtuais**. Nos detalhes do VM, clique em **Manutenção**. No exemplo seguinte, o **estado de Manutenção** mostra que uma atualização está **pendente**.

![Screenshot mostrando como verificar o estado das atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Eliminar uma configuração de manutenção

Para eliminar uma configuração, abra os detalhes da configuração e clique em **Apagar**.

![Screenshot mostrando como verificar manutenção para um hospedeiro](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
