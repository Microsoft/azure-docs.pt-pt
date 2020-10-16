---
title: Ativar o rastreio e inventário de mudança de automatização Azure a partir de um livro de bordo
description: Este artigo diz como ativar o Change Tracking e o Inventário a partir de um livro de recortes.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186287"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Ativar o Controlo de alterações e Inventário a partir de um runbook

Este artigo descreve como pode utilizar um livro de bordo para ativar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking and Inventory. 

> [!NOTE]
> Ao permitir o Rastreio e Inventário de Alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](./index.yml) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário 

1. No portal Azure, selecione **Contas de Automação**e, em seguida, selecione a sua conta Demôm automação na lista.
1. Selecione **Inventário** em **Gestão de Configuração**.
1. Selecione um espaço de trabalho log analytics existente ou crie um novo. 
1. Clique em **Ativar**.

    ![Ativar o Controlo de Alterações e Inventário](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione Azure VM para gerir

Com o Change Tracking e o Inventário ativados, pode adicionar um VM Azure para gestão pela funcionalidade.

1. A partir da sua conta Demômes, selecione **Alterar rastreio** ou **Inventário** em **Gestão de Configuração.**

2. Clique **em Adicionar VMs Azure** para adicionar o seu VM.

3. Escolha o seu VM na lista e clique em **Enable**. Esta ação permite alterar o rastreio e o inventário para o VM.

   ![Ativar o rastreio e inventário de alterações para VM](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Se tentar ativar outra funcionalidade antes de a configuração do Change Tracking e Do Inventário tiver sido concluída, receberá esta mensagem: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar os mais recentes módulos Azure e importar o módulo [Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) para ativar com sucesso o Change Tracking and Inventory para o seu VM.

1. Na sua conta Demômes, selecione Módulos em **Recursos Partilhados.** **Modules** 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique **em Sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-enable-changes-from-runbook/update-modules.png)

4. Regresso aos **Módulos** em **Recursos Partilhados.** 
5. Selecione **A galeria Browse** para abrir a galeria do módulo. 
6. Procure a Az.OperationalInsights e importe este módulo para a conta Automation.

    ![Importar o módulo OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importar um livro de corridas para permitir o Rastreio e Inventário de Alterações

1. Na sua conta Demôm automação, selecione **Runbooks** em **Automação de Processos.**
2. Selecione **Procurar galeria**.
3. Procurar `update and change tracking`.
4. Selecione o runbook e clique em **Importar** na página 'Ver Fonte'. 
5. Clique **em OK** para importar o livro de bordo para a conta Automation.

   ![Livro de importação para configuração](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Na página Runbook, clique em **Editar**e, em seguida, **selecione Publicar**. 
7. No painel de execução de publicação, clique em **Sim** para publicar o livro de recortes.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter ativado o Change Tracking and Inventory para que um VM Azure inicie este livro de recortes. Requer um VM existente e um grupo de recursos com a funcionalidade ativada para parâmetros.

1. Abra o **manual enable-multipleSolution.**

   ![Vários runbooks de soluções](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Clique no botão de partida e introduza os valores dos parâmetros nos seguintes campos:

   * **VMNAME** - O nome de um VM existente para adicionar a Change Tracking and Inventory. Deixe este campo em branco para adicionar todos os VMs no grupo de recursos.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para os VMs para ativar.
   * **SUBSCRIÇÃO -** O ID de subscrição do novo VM para ativar. Deixe este campo em branco para utilizar a subscrição do espaço de trabalho. Quando utilizar um ID de subscrição diferente, adicione a conta Run As para a sua conta Demôm automação como contribuinte para a subscrição.
   * **JÁ A PARTIR DEVM** - O nome do VM que já está habilitado manualmente para alterações.
   * **JÁ A PARTIR DEVMRESOURCEGROUP** - Nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduza **o ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selecione **OK** para iniciar a tarefa de runbook.
1. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="next-steps"></a>Passos seguintes

* Para agendar um livro de [corridas, consulte Gerir os horários na Azure Automation.](shared-resources/schedules.md)
* Para obter detalhes sobre o trabalho com a funcionalidade, consulte [Manage Change Tracking and Inventory](change-tracking-file-contents.md).
* Para resolver problemas gerais com a funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](troubleshoot/change-tracking.md).
