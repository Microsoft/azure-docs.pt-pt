---
title: Ativar a Gestão de Atualização de Automação Azure a partir do livro de execução
description: Este artigo diz como ativar a Gestão de Atualizações a partir de um livro de execução.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 39ebdb6937b03d72365e9d3785af9571ebb66186
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836061"
---
# <a name="enable-update-management-from-a-runbook"></a>Ativar a Gestão de Atualizações a partir de um runbook

Este artigo descreve como pode utilizar um livro de execução para ativar a funcionalidade [de Gestão](automation-update-management.md) de Atualizações para VMs no seu ambiente. Para ativar os VMs Azure em escala, deve ativar um VM existente utilizando a Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualizações, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta de Automação. Para obter uma lista dos pares de mapeamento suportados, consulte [O mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Automation, selecione **Gestão de Atualização** em Gestão de **Atualizações**.

2. Selecione o espaço de trabalho do Log Analytics e, em seguida, clique em **Ativar**. Enquanto a Atualização de Gestão está a ser ativada, é mostrado um banner azul. 

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione Azure VM para gerir

Com a Atualização ativada, pode adicionar um VM Azure para receber atualizações.

1. Na sua conta Automation, selecione **Gestão de Atualização** sob gestão de **Atualização**.

2. Selecione **Adicionar VMs Azure** para adicionar o seu VM.

3. Escolha o VM da lista e clique em **Ativar** para configurar o VM para atualizações. 

   ![Ativar a Gestão de Atualizações para VM](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Se tentar ativar outra funcionalidade antes de terminar a configuração da Atualização, recebe esta mensagem:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar para os mais recentes módulos Azure e importar o módulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) para ativar com sucesso a Gestão de Atualizações para os seus VMs.

1. Na sua conta de Automação, selecione **Módulos** em **Recursos Partilhados.** 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique **sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)

4. Regresso aos Módulos em **Recursos Partilhados.** **Modules** 
5. **Selecione Galeria Browse** para abrir a galeria do módulo. 
6. Procure `Az.OperationalInsights` e importe este módulo na sua conta De automação.

    ![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importar um livro de execução para permitir a Gestão de Atualizações

1. Na sua conta de Automação, selecione **Runbooks** em **Automação de Processos**.
2. Selecione **Procurar galeria**.
3. Procurar `update and change tracking`.
4. Selecione o livro de execução e clique **em Importar** na página 'Ver Origem'. 
5. Clique **em OK** para importar o livro de corridas para a conta Automation.

   ![Livro de importação para configuração](media/automation-onboard-solutions/import-from-gallery.png)

6. Na página 'Livro de Execução', clique em **Editar**, em seguida, selecione **Publicar**. 
7. No painel Publish Runbook, clique **em Sim** para publicar o livro de execução.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter ativado a Gestão de Atualizações para um VM Azure para iniciar este livro de execução. Requer um VM e um grupo de recursos existentes com a funcionalidade ativada para parâmetros.

1. Abra o livro de execução **Enable-MultipleSolution.**

   ![Livro de execução de solução múltipla](media/automation-onboard-solutions/runbook-overview.png)

2. Clique no botão de arranque e introduza os valores dos parâmetros nos seguintes campos:

   * **VMNAME** - O nome de um VM existente para adicionar à Gestão de Atualizações. Deixe este campo em branco para adicionar todos os VMs no grupo de recursos.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para os VMs para ativar.
   * **SUBSCRIÇÃOID** - O ID de subscrição do novo VM para ativar. Deixe este campo em branco para utilizar a subscrição do espaço de trabalho. Quando utilizar um ID de subscrição diferente, adicione o Run As conta para a sua conta Automation como contribuinte para a subscrição.
   * **JÁONBOARDEDVM** - O nome do VM que já está manualmente habilitado para atualizações.
   * **JÁONBOARDEDVMRESOURCEGROUP** - O nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduzir **Atualizações**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Selecione **OK** para iniciar a tarefa de runbook.
4. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="next-steps"></a>Passos seguintes

* Para agendar um livro de recortes, consulte [gerir os horários em Automação Azure.](shared-resources/schedules.md)
* Para utilizar a Gestão de Atualizações para VMs, consulte ['Gerir atualizações e patches' para os seus VMs Azure](automation-tutorial-update-management.md).
* Para configurações de âmbito, consulte [Trabalhar com configurações de âmbito](automation-scope-configurations-update-management.md).
* Se já não necessitar do espaço de trabalho do Log Analytics, consulte instruções no [espaço de trabalho Unlink da Automação para gestão](automation-unlink-workspace-update-management.md)de atualizações .
* Para eliminar VMs da Atualização, consulte [Remover VMs da Gestão de Atualizações](automation-remove-vms-from-update-management.md).
* Para resolver os erros gerais de Gestão de Atualizações, consulte [os problemas](troubleshoot/update-management.md)de Gestão de Atualização de Resolução de Problemas .
* Para resolver problemas com o agente de atualização do Windows, consulte problemas de agente de [atualização do Windows](troubleshoot/update-agent-issues.md).
* Para resolver problemas com o agente de atualização linux, consulte problemas de agente de[atualização Da Troubleshoot Linux](troubleshoot/update-agent-issues-linux.md).