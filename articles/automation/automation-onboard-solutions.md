---
title: Atualização onboard, alteração de rastreio e soluções de inventário para a Automação Azure
description: Saiba como carregar soluções de atualização e controlo de alterações para a Automatização do Azure.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457625"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Atualização onboard, alteração de rastreio e soluções de inventário para a Automação Azure

Neste tutorial, irá aprender a carregar automaticamente soluções de Atualização, Controlo de Alterações e Inventário para VMs para a Automatização do Azure:

> [!div class="checklist"]
> * Carregar uma VM do Azure
> * Ativar soluções
> * Instalar e atualizar módulos
> * Importar o runbook de inclusão
> * Iniciar o runbook

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário o seguinte:

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual](../virtual-machines/windows/quick-create-portal.md) para carregar.

## <a name="onboard-an-azure-vm"></a>Carregar uma VM do Azure

Existem várias formas de carregar máquinas. Pode carregar a solução [a partir de uma máquina virtual](automation-onboard-solutions-from-vm.md), [ao pesquisar em várias máquinas, ](automation-onboard-solutions-from-browse.md) [a partir da sua conta de Automatização](automation-onboard-solutions-from-automation-account.md) ou através do runbook. Este tutorial explica a ativação da Gestão de Atualizações através de um runbook. Para carregar Máquinas Virtuais do Azure à escala, é necessário carregar uma VM existente com a solução de Controlo de alterações ou Gestão de atualizações. Neste passo, vai carregar uma máquina virtual com Gestão de atualizações e Controlo de alterações.

### <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

As soluções de Rastreio e Inventário de Alterações permitem-lhe [rastrear alterações](automation-vm-change-tracking.md) e [inventários](automation-vm-inventory.md) nas suas máquinas virtuais. Neste passo, permite-se ativar as soluções numa máquina virtual.

1. No portal Azure, selecione **Contas de Automação**e, em seguida, selecione a sua conta de automação na lista.
1. **Selecione inventário** sob gestão de **configuração.**
1. Selecione um espaço de trabalho existente no Log Analytics ou crie um novo. 
1. Clique em **Ativar**.

    ![Carregar a solução de Atualização](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

A solução de Gestão de Atualizações permite gerir atualizações e correções para as VMs do Azure. Pode avaliar o estado das atualizações disponíveis, agendar a instalação de atualizações necessárias e rever os resultados de implementação para verificar se as atualizações foram aplicadas com êxito à VM. Neste passo, vai ativar a solução para a sua VM.

1. Na sua conta De automação, selecione Gestão de **Atualizações** na secção Gestão de **Atualizações.**
1. O espaço de trabalho de log analytics selecionado é o espaço de trabalho utilizado na etapa anterior. Clique em **Ativar** para integrar a solução de Gestão de atualizações. Enquanto a solução de Gestão de atualizações está a ser instalada, é apresentada uma faixa azul. 

    ![Carregar a solução de Atualização](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Selecionar a VM do Azure a gerir

Agora que as soluções estão ativadas, pode adicionar uma VM do Azure para carregar para essas soluções.

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** Gestão de **Configuração**.' 
2. Na página de rastreio de alteração, clique em **Adicionar VMs Azure** para adicionar o seu VM.

3. Selecione o seu VM na lista e clique em **Ativar**. Esta ação permite as soluções de Rastreio e Inventário de Alterações para o VM.

   ![Ativar a solução de atualização para a VM](media/automation-onboard-solutions/enable-change-tracking.png)

4. Quando a notificação de embarque vM estiver concluída, selecione **a gestão de atualização** no âmbito **da Atualização**.

5. Selecione **Adicionar VMs Azure** para adicionar o seu VM.

6. Selecione a VM na lista e selecione **Ativar**. Esta ação permite a solução de Gestão de Atualização para o VM.

   ![Ativar a solução de atualização para a VM](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se não esperar que a outra solução esteja concluída, ao ativar a próxima solução, recebe a mensagem:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os mais recentes módulos Azure e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para automatizar com sucesso a solução no embarque.

## <a name="update-azure-modules"></a>Atualizar Módulos do Azure

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados.** 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique **sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

![Atualizar módulos](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Instale módulo Az.OperationalInsights

1. Na conta Automation, selecione **Módulos** em **Recursos Partilhados.** 
2. **Selecione Galeria Browse** para abrir a galeria do módulo. 
3. Procure a Az.OperationalInsights e importe este módulo para a conta Automation.

![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importar o runbook de inclusão

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
1. Selecione **Procurar galeria**.
1. Procurar `update and change tracking`.
3. Selecione o livro de execução e clique **em Importar** na página 'Ver Origem'. 
4. Clique **em OK** para importar o livro de corridas para a conta Automation.

   ![Importar o runbook de inclusão](media/automation-onboard-solutions/import-from-gallery.png)

6. Na página 'Livro de Execução', clique em **Editar**, em seguida, selecione **Publicar**. 
7. No painel Publish Runbook, clique **em Sim** para publicar o livro de execução.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter embarcado em soluções de rastreio ou atualização para um VM Azure para iniciar este livro de execução. Exige uma máquina virtual e um grupo de recursos existentes com a solução carregada para parâmetros.

1. Abra o livro de execução **Enable-MultipleSolution.**

   ![Vários runbooks de soluções](media/automation-onboard-solutions/runbook-overview.png)

1. Clique no botão de início e introduza os seguintes valores de parâmetros.

   * **VMNAME** - Deixe em branco. O nome de uma VM existente a carregar para a solução de atualização ou controlo de alterações. Se deixar este valor em branco, todas as VMs no grupo de recursos são carregadas.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para as VMs a carregar.
   * **SUBSCRIPTIONID** - Deixe em branco. O ID de subscrição da nova VM a carregar. Se deixar em branco, é utilizada a subscrição da área de trabalho. Quando é indicado um ID de subscrição diferente, a conta RunAs para esta conta de automatização também deve ser adicionada como contribuidor para esta subscrição.
   * **ALREADYONBOARDEDVM** - O nome da VM que foi carregada manualmente para a solução Atualizações ou Controlo de Alterações.
   * **JÁONBOARDEDVMRESOURCEGROUP** - O nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduza **atualizações** ou **ChangeTracking**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selecione **OK** para iniciar a tarefa de runbook.
1. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover um VM da Atualização:

1. No seu espaço de trabalho Log Analytics, retire o `MicrosoftDefaultScopeConfig-Updates`VM da pesquisa guardada para a configuração de âmbito . Pesquisas guardadas podem ser encontradas sob **o General** no seu espaço de trabalho.
2. Remova o [agente Log Analytics para Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o agente Log Analytics para o [Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Carregar uma máquina virtual do Azure manualmente.
> * Instalar e atualizar os módulos do Azure necessários.
> * Importar um runbook que carrega VMs do Azure.
> * Iniciar o runbook que carrega VMs do Azure automaticamente.

Siga esta ligação para saber mais sobre o agendamento de runbooks.

> [!div class="nextstepaction"]
> [Agendar runbooks](automation-schedules.md).
