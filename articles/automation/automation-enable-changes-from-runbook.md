---
title: Ativar o rastreio e o inventário de alterações de automatização azure a partir de um livro de execução
description: Este artigo diz como ativar o Rastreio de Mudanças e Inventário a partir de um livro de execução.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826747"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Ativar o Controlo de alterações e Inventário a partir de um runbook

Este artigo descreve como pode usar um livro de execução para ativar a funcionalidade de Rastreio e Inventário de [Alterações](change-tracking.md) para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando o Change Tracking e o Inventory. 

> [!NOTE]
> Ao permitir o rastreio e inventário de alterações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário 

1. No portal Azure, selecione **Contas de Automação**e, em seguida, selecione a sua conta De automação na lista.
1. **Selecione inventário** sob gestão de **configuração.**
1. Selecione um espaço de trabalho existente no Log Analytics ou crie um novo. 
1. Clique em **Ativar**.

    ![Ativar o Controlo de Alterações e Inventário](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione Azure VM para gerir

Com o Change Tracking e o Inventory ativados, pode adicionar um VM Azure para gestão pela funcionalidade.

1. Na sua conta de Automação, selecione **'Localizar rastreio** ou **Inventário'** sob gestão de **configuração**.

2. Clique em **Adicionar VMs Azure** para adicionar o seu VM.

3. Escolha o seu VM na lista e clique em **Ativar**. Esta ação permite o rastreio de mudança e inventário para o VM.

   ![Ativar o rastreio e o inventário de alterações para VM](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Se tentar ativar outra funcionalidade antes de terminar a configuração do Change Tracking and Inventory, recebe esta mensagem:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os mais recentes módulos Azure e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para permitir com sucesso o Rastreio e Inventário de Alterações para o seu VM.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados.** 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique **sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-enable-changes-from-runbook/update-modules.png)

4. Regresso aos Módulos em **Recursos Partilhados.** **Modules** 
5. **Selecione Galeria Browse** para abrir a galeria do módulo. 
6. Procure a Az.OperationalInsights e importe este módulo para a conta Automation.

    ![Importar o módulo OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importar um livro de execução para permitir o rastreio e o inventário de alterações

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
2. Selecione **Procurar galeria**.
3. Procurar `update and change tracking`.
4. Selecione o livro de execução e clique **em Importar** na página 'Ver Origem'. 
5. Clique **em OK** para importar o livro de corridas para a conta Automation.

   ![Livro de importação para configuração](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Na página 'Livro de Execução', clique em **Editar**, em seguida, selecione **Publicar**. 
7. No painel Publish Runbook, clique **em Sim** para publicar o livro de execução.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter ativado o Change Tracking e o Inventário para um VM Azure para iniciar este livro de execução. Requer um VM e um grupo de recursos existentes com a funcionalidade ativada para parâmetros.

1. Abra o livro de execução **Enable-MultipleSolution.**

   ![Vários runbooks de soluções](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Clique no botão de arranque e introduza os valores dos parâmetros nos seguintes campos:

   * **VMNAME** - O nome de um VM existente para adicionar ao Change Tracking e Inventário. Deixe este campo em branco para adicionar todos os VMs no grupo de recursos.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para os VMs para ativar.
   * **SUBSCRIÇÃOID** - O ID de subscrição do novo VM para ativar. Deixe este campo em branco para utilizar a subscrição do espaço de trabalho. Quando utilizar um ID de subscrição diferente, adicione o Run As conta para a sua conta Automation como contribuinte para a subscrição.
   * **JÁONBOARDEDVM** - O nome do VM que já está manualmente habilitado para alterações.
   * **JÁONBOARDEDVMRESOURCEGROUP** - O nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduza **changetracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selecione **OK** para iniciar a tarefa de runbook.
1. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="next-steps"></a>Passos seguintes

* Para agendar um livro de recortes, consulte [gerir os horários em Automação Azure.](shared-resources/schedules.md)
* Para mais detalhes sobre o trabalho com a funcionalidade, consulte [Manage Change Tracking and Inventory](change-tracking-file-contents.md).
* Para obter informações sobre configurações de âmbito, consulte [Trabalhar com configurações de âmbito para Rastreio e Inventário](automation-scope-configurations-change-tracking.md)de Alterações .
* Para aprender a utilizar a funcionalidade para identificar software instalado no seu ambiente, consulte [Descubra qual o software instalado nos seus VMs](automation-tutorial-installed-software.md).
* Se não quiser integrar a sua conta de Automação com um espaço de trabalho log Analytics ao ativar a funcionalidade, consulte o espaço de [trabalho Unlink a partir da conta Automation](automation-unlink-workspace-change-tracking.md).
* Quando terminar a implementação de alterações nos VMs, pode removê-las conforme descrito em [Remover VMs de Change Tracking e Inventory](automation-remove-vms-from-change-tracking.md).
* Para resolver problemas gerais com a funcionalidade, consulte problemas de rastreio e inventário de resolução de [problemas.](troubleshoot/change-tracking.md)