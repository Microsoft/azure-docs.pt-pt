---
title: Carregar soluções de atualização e controlo de alterações para a Automatização do Azure
description: Saiba como carregar soluções de atualização e controlo de alterações para a Automatização do Azure.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d247369647106cf1671a8770a6dce21f1a34a4b9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082199"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Carregar soluções de atualização e controlo de alterações para a Automatização do Azure

Neste tutorial, irá aprender a carregar automaticamente soluções de Atualização, Controlo de Alterações e Inventário para VMs para a Automatização do Azure:

> [!div class="checklist"]
> * Carregar uma VM do Azure
> * Ativar soluções
> * Instalar e atualizar módulos
> * Importar o runbook de inclusão
> * Iniciar o runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="onboard-an-azure-vm"></a>Carregar uma VM do Azure

Existem várias formas de carregar máquinas. Pode carregar a solução [a partir de uma máquina virtual](automation-onboard-solutions-from-vm.md), [ao pesquisar em várias máquinas](automation-onboard-solutions-from-browse.md), [a partir da sua conta de Automatização](automation-onboard-solutions-from-automation-account.md) ou através do runbook. Este tutorial explica a ativação da Gestão de Atualizações através de um runbook. Para carregar Máquinas Virtuais do Azure à escala, é necessário carregar uma VM existente com a solução de Controlo de alterações ou Gestão de atualizações. Neste passo, vai carregar uma máquina virtual com Gestão de atualizações e Controlo de alterações.

### <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

A solução de Controlo de Alterações e Inventário fornece a capacidade de [controlar as alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) nas suas máquinas virtuais. Neste passo, vai ativar a solução numa máquina virtual.

1. No menu à esquerda, selecione **Contas de Automatização** e, em seguida, selecione a sua conta de automatização na lista.
1. Selecione **Inventário** em **GESTÃO DE CONFIGURAÇÃO**.
1. Selecione uma área de trabalho existente do Log Analytics ou crie uma nova. Clique no botão **Ativar**.

![Carregar a solução de Atualização](media/automation-onboard-solutions/inventory-onboard.png)

Quando a notificação de inclusão da solução de controlo de alterações e inventário estiver concluída, clique em **Gestão de Atualizações**, em **GESTÃO DE CONFIGURAÇÃO**.

### <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

A solução de Gestão de Atualizações permite gerir atualizações e correções para as VMs do Azure. Pode avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM. Neste passo, vai ativar a solução para a sua VM.

1. A partir da sua Conta de Automatização, selecione **Gestão de atualizações** em **GESTÃO DE ATUALIZAÇÕES**.
1. A área de trabalho do Log Analytics selecionada é a mesma área de trabalho utilizado no passo anterior. Clique em **Ativar** para integrar a solução de Gestão de atualizações.

![Carregar a solução de Atualização](media/automation-onboard-solutions/update-onboard.png)

Enquanto a solução de Gestão de atualizações está a ser instalada, é apresentada uma faixa azul. Quando a solução for ativada, selecione **Controlo de alterações**, em **GESTÃO DE CONFIGURAÇÃO**, para ir para o passo seguinte.

### <a name="select-azure-vm-to-be-managed"></a>Selecionar a VM do Azure a gerir

Agora que as soluções estão ativadas, pode adicionar uma VM do Azure para carregar para essas soluções.

1. A partir da sua Conta de Automatização, na página **Controlo de alterações**, selecione **+ Adicionar a VM do Azure** para adicionar a máquina virtual.

1. Selecione a VM na lista e selecione **Ativar**. Esta ação ativa a solução de Controlo de alterações e Inventário para a máquina virtual.

   ![Ativar a solução de atualização para a VM](media/automation-onboard-solutions/enable-change-tracking.png)

1. Quando a notificação de inclusão da VM estiver concluída, a partir da sua Conta de Automatização, selecione **Gestão de atualizações** em **GESTÃO DE ATUALIZAÇÕES**.

1. Selecione **+ Adicionar VM do Azure** para adicionar a máquina virtual.

1. Selecione a VM na lista e selecione **Ativar**. Esta ação ativa a solução de Gestão de atualizações para a máquina virtual.

   ![Ativar a solução de atualização para a VM](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se não esperar para outra solução concluir, quando ativar a solução seguinte, receberá uma mensagem que diz: *Instalação de outra solução está em curso nesta ou uma máquina virtual diferente. Assim que a instalação terminar, o botão Ativar será ativado e poderá pedir a instalação da solução nesta máquina virtual.*

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário fazer a atualização para os módulos mais recentes do Azure e importar `AzureRM.OperationalInsights` para automatizar a inclusão da solução com êxito.

## <a name="update-azure-modules"></a>Atualizar Módulos do Azure

A partir da sua Conta de Automatização, selecione **Módulos** em **RECURSOS PARTILHADOS**. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. Selecione **Sim** no pedido para atualizar todos os módulos do Azure existentes para a versão mais recente.

![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Instalar o módulo AzureRM.OperationalInsights

Na página **Módulos**, selecione **Procurar galeria** para abrir a galeria de módulos. Procure AzureRM.OperationalInsights e importe este módulo para a Conta de automatização.

![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importar o runbook de inclusão

1. A partir da sua Conta de Automatização, selecione **Runbooks** sob a **AUTOMATIZAÇÃO DE PROCESSOS**.
1. Selecione **Procurar galeria**.
1. Procure **atualização e controlo de alterações**, clique no runbook e selecione **Importar** na página **Ver Origem**. Selecione **OK** para importar o runbook para a Conta de automatização.

   ![Importar o runbook de inclusão](media/automation-onboard-solutions/import-from-gallery.png)

1. Na página **Runbook**, selecione **Editar** e, em seguida, selecione **Publicar**. Na caixa de diálogo **Publicar Runbook**, selecione **Sim** para publicar o runbook.

## <a name="start-the-runbook"></a>Iniciar o runbook

Tem de ter carregado as soluções de controlo de alterações ou atualização para uma VM do Azure para iniciar este runbook. Exige uma máquina virtual e um grupo de recursos existentes com a solução carregada para parâmetros.

1. Abra o runbook Enable-MultipleSolution.

   ![Vários runbooks de soluções](media/automation-onboard-solutions/runbook-overview.png)

1. Clique no botão de início e introduza os seguintes valores de parâmetros.

   * **VMNAME** - Deixe em branco. O nome de uma VM existente a carregar para a solução de atualização ou controlo de alterações. Se deixar este valor em branco, todas as VMs no grupo de recursos são carregadas.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para as VMs a carregar.
   * **SUBSCRIPTIONID** - Deixe em branco. O ID de subscrição da nova VM a carregar. Se deixar em branco, é utilizada a subscrição da área de trabalho. Quando é indicado um ID de subscrição diferente, a conta RunAs para esta conta de automatização também deve ser adicionada como contribuidor para esta subscrição.
   * **ALREADYONBOARDEDVM** - O nome da VM que foi carregada manualmente para a solução Atualizações ou Controlo de Alterações.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - O nome do grupo de recursos do qual a VM é membro.
   * **SOLUTIONTYPE** - Introduza **Updates** ou **ChangeTracking**

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecione **OK** para iniciar a tarefa de runbook.
1. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Carregar uma máquina virtual do Azure manualmente.
> * Instalar e atualizar os módulos do Azure necessários.
> * Importar um runbook que carrega VMs do Azure.
> * Iniciar o runbook que carrega VMs do Azure automaticamente.

Siga esta ligação para saber mais sobre o agendamento de runbooks.

> [!div class="nextstepaction"]
> [Agendar runbooks](automation-schedules.md).
