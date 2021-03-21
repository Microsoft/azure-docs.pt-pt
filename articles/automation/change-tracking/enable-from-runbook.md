---
title: Ativar o rastreio e inventário de mudança de automatização Azure a partir de um livro de bordo
description: Este artigo diz como ativar o Change Tracking e o Inventário a partir de um livro de recortes.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100585930"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Ativar o Controlo de alterações e Inventário a partir de um runbook

Este artigo descreve como pode utilizar um livro de bordo para ativar [o Change Tracking and Inventory](overview.md) para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking and Inventory.

> [!NOTE]
> Ao permitir o Rastreio e Inventário de Alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho Log Analytics e uma conta Automation. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](../how-to/region-mappings.md)

Este método utiliza dois livros de execução:

* **Activar-MultipleSolution** - O diário principal que solicita informações de configuração, consulta o VM especificado e executa outras verificações de validação e, em seguida, invoca o runbook **Enable-AutomationSolution** para configurar o Tracking e Inventário de Mudança para cada VM dentro do grupo de recursos especificado.
* **Activar-AutomatizaçãoSOlução** - Permite o Rastreio de Alterações e Inventário para um ou mais VMs especificados no grupo de recursos-alvo. Verifica se os pré-requisitos são cumpridos, verifica que a extensão VM do Log Analytics está instalada e instala-se se não for encontrada, e adiciona os VMs à configuração de âmbito no espaço de trabalho especificado do Log Analytics ligado à conta Automation.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../automation-security-overview.md) para gerir máquinas.
* [Log Analytics espaço de trabalho](../../azure-monitor/logs/design-logs-deployment.md)
* Uma [máquina virtual.](../../virtual-machines/windows/quick-create-portal.md)
* Dois ativos de automatização, que são utilizados pelo manual **enable-AutomationSolution.** Este runbook, se já não existir na sua conta Demôm automação, é automaticamente importado pelo manual **enable-multipleSolution** durante a sua primeira execução.
    * *LASolutionSubscriptionId*: ID de assinatura de onde está localizado o espaço de trabalho Log Analytics.
    * *LASolutionWorkspaceId*: ID do espaço de trabalho do Log Analytics ligado à sua conta Automation.

    Estas variáveis são usadas para configurar o espaço de trabalho do VM a bordo. Se estes não forem especificados, o script procura pela primeira vez qualquer VM a bordo para Alterar Tracking e Inventário na sua subscrição, seguido da subscrição em que a conta Automation está, seguida de todas as outras subscrições a que a sua conta de utilizador tem acesso. Se não estiver corretamente configurado, isto pode resultar em que as suas máquinas sejam a bordo de algum espaço de trabalho aleatório do Log Analytics.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

1. No portal Azure, navegue para **contas de automação.** Na página **'Contas de Automação',** selecione a sua conta na lista.

1. Na sua conta de Automação, selecione **Inventário** ou **Rastreio de Alterações** em **Gestão de Configuração.**

1. Selecione o espaço de trabalho 'Log Analytics' e, em seguida, clique em **Ativar**. Enquanto o Inventário ou o Rastreio de Alterações estão a ser ativados, é mostrado um banner.

    ![Ativar o Controlo de Alterações e Inventário](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar os módulos Azure mais recentes e importar o módulo [Az.OperationalInsights](/powershell/module/az.operationalinsights) para ativar com sucesso a Gestão de Atualização para os seus VMs com o livro de aplicação.

1. Na sua conta Demômes, selecione Módulos em **Recursos Partilhados.** 

2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente.

3. Clique **em Sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/enable-from-runbook/update-modules.png)

4. Regresso aos **Módulos** em **Recursos Partilhados.**

5. Selecione **A galeria Browse** para abrir a galeria do módulo.

6. Procure `Az.OperationalInsights` e importe este módulo na sua conta Demôm automação.

    ![Importar o módulo OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Selecione Azure VM para gerir

Com o Change Tracking e o Inventário ativados, pode adicionar um VM Azure para gestão pela funcionalidade.

1. A partir da sua conta Demômes, selecione **Alterar rastreio** ou **Inventário** em **Gestão de Configuração.**

2. Clique **em Adicionar VMs Azure** para adicionar o seu VM.

3. Escolha o seu VM na lista e clique em **Enable**. Esta ação permite alterar o rastreio e o inventário para o VM.

   ![Ativar o rastreio e inventário de alterações para VM](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Se tentar ativar outra funcionalidade antes de a configuração do Change Tracking e Do Inventário tiver sido concluída, receberá esta mensagem: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importar um livro de corridas para permitir o Rastreio e Inventário de Alterações

1. Na sua conta Demôm automação, selecione **Runbooks** em **Automação de Processos.**

2. Selecione **Procurar galeria**.

3. Procure por **atualização e altere o rastreio**.

4. Selecione o runbook e clique em **Importar** na página **'Ver Fonte'.**

5. Clique **em OK** para importar o livro de bordo para a conta Automation.

   ![Livro de importação para configuração](media/enable-from-runbook/import-from-gallery.png)

6. Na página **Runbook,** selecione o manual **enable-multiplesolution** e, em seguida, clique em **Editar**. No editor textual,  **selecione Publicar**.

7. Quando solicitado para confirmar, clique em **Sim** para publicar o livro de recortes.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter ativado o Change Tracking and Inventory para que um VM Azure inicie este livro de recortes. Requer um VM existente e um grupo de recursos com a funcionalidade ativada para configurar um ou mais VMs no grupo de recursos-alvo.

1. Abra o **manual enable-multipleSolution.**

   ![Livro de corridas de solução múltipla](media/enable-from-runbook/runbook-overview.png)

2. Clique no botão de partida e introduza os valores dos parâmetros nos seguintes campos:

   * **VMNAME** - O nome de um VM existente para adicionar a Change Tracking and Inventory. Deixe este campo em branco para adicionar todos os VMs no grupo de recursos.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para os VMs para ativar.
   * **SUBSCRIÇÃO -** O ID de subscrição do novo VM para ativar. Deixe este campo em branco para utilizar a subscrição do espaço de trabalho. Quando utilizar um ID de subscrição diferente, adicione a conta Run As para a sua conta Demôm automação como contribuinte para a subscrição.
   * **JÁ A PARTIR DEVM** - O nome do VM que já está habilitado manualmente para atualizações.
   * **JÁ A PARTIR DEVMRESOURCEGROUP** - Nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduza **o ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/enable-from-runbook/runbook-parameters.png)

3. Selecione **OK** para iniciar a tarefa de runbook.

4. Monitorize o progresso do trabalho do runbook e quaisquer erros da página **Jobs.**

## <a name="next-steps"></a>Passos seguintes

* Para agendar um livro de [corridas, consulte Gerir os horários na Azure Automation.](../shared-resources/schedules.md)

* Para obter detalhes sobre o trabalho com a funcionalidade, consulte [Gerir o Rastreio de Mudança](manage-change-tracking.md) e Gerir o [Inventário.](manage-inventory-vms.md)

* Para resolver problemas gerais com a funcionalidade, consulte [problemas de rastreio e inventário de alterações de resolução de problemas](../troubleshoot/change-tracking.md).


