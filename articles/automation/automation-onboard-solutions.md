---
title: Ativar a gestão da atualização da automação Azure a partir de runbook
description: Este artigo diz como ativar a Gestão de Atualização a partir de um livro de bordo.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: d55a47e4eb921c71ff3539d34b6db514684da073
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186134"
---
# <a name="enable-update-management-from-a-runbook"></a>Ativar a Gestão de Atualizações a partir de um runbook

Este artigo descreve como pode utilizar um livro de bordo para ativar a funcionalidade [de Gestão](automation-update-management.md) de Atualização para VMs no seu ambiente. Para ativar os VMs Azure em escala, tem de ativar um VM existente utilizando a Gestão de Atualização. 

> [!NOTE]
> Ao ativar a Gestão de Atualização, apenas certas regiões são suportadas para ligar um espaço de trabalho log Analytics e uma conta Demôm automação. Para obter uma lista dos pares de mapeamento suportados, consulte [o mapeamento da Região para a conta de Automação e o espaço de trabalho log Analytics.](how-to/region-mappings.md)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](./index.yml) para gerir máquinas.
* Uma [máquina virtual.](../virtual-machines/windows/quick-create-portal.md)

## <a name="enable-update-management"></a>Ativar a Gestão de Atualizações

1. Na sua conta Demômes, selecione **Gestão de Atualização** sob **Gestão de Atualização**.

2. Selecione o espaço de trabalho 'Log Analytics' e, em seguida, clique em **Ativar**. Enquanto a Gestão de Atualização está a ser ativada, é mostrado um banner azul. 

    ![Ativar a Gestão de Atualizações](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selecione Azure VM para gerir

Com a Gestão de Atualização ativada, pode adicionar um VM Azure para receber atualizações.

1. A partir da sua conta Demômes, selecione **A gestão de Atualização** sob **gestão de Atualização.**

2. **Selecione Adicionar VMs Azure** para adicionar o seu VM.

3. Escolha o VM da lista e clique **em Ativar** a configuração do VM para atualizações. 

   ![Ativar a gestão de atualização para VM](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Se tentar ativar outra funcionalidade antes de a configuração da Atualização Gestão estiver concluída, receberá esta mensagem:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Instalar e atualizar módulos

É necessário atualizar os mais recentes módulos Azure e importar o módulo [Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) para ativar com sucesso a Gestão de Atualização para os seus VMs.

1. Na sua conta Demômes, selecione Módulos em **Recursos Partilhados.** **Modules** 
2. Selecione **Atualizar Módulos do Azure** para atualizar os módulos do Azure para a versão mais recente. 
3. Clique **em Sim** para atualizar todos os módulos Azure existentes para a versão mais recente.

    ![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)

4. Regresso aos **Módulos** em **Recursos Partilhados.** 
5. Selecione **A galeria Browse** para abrir a galeria do módulo. 
6. Procure `Az.OperationalInsights` e importe este módulo na sua conta Demôm automação.

    ![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importar um runbook para permitir a Gestão de Atualização

1. Na sua conta Demôm automação, selecione **Runbooks** em **Automação de Processos.**
2. Selecione **Procurar galeria**.
3. Procurar `update and change tracking`.
4. Selecione o runbook e clique em **Importar** na página 'Ver Fonte'. 
5. Clique **em OK** para importar o livro de bordo para a conta Automation.

   ![Livro de importação para configuração](media/automation-onboard-solutions/import-from-gallery.png)

6. Na página Runbook, clique em **Editar**e, em seguida, **selecione Publicar**. 
7. No painel de execução de publicação, clique em **Sim** para publicar o livro de recortes.

## <a name="start-the-runbook"></a>Iniciar o runbook

Deve ter ativado a Gestão de Atualização para um VM Azure iniciar este runbook. Requer um VM existente e um grupo de recursos com a funcionalidade ativada para parâmetros.

1. Abra o **manual enable-multipleSolution.**

   ![Livro de corridas de solução múltipla](media/automation-onboard-solutions/runbook-overview.png)

2. Clique no botão de partida e introduza os valores dos parâmetros nos seguintes campos:

   * **VMNAME** - O nome de um VM existente para adicionar à Gestão de Atualização. Deixe este campo em branco para adicionar todos os VMs no grupo de recursos.
   * **VMRESOURCEGROUP** - O nome do grupo de recursos para os VMs para ativar.
   * **SUBSCRIÇÃO -** O ID de subscrição do novo VM para ativar. Deixe este campo em branco para utilizar a subscrição do espaço de trabalho. Quando utilizar um ID de subscrição diferente, adicione a conta Run As para a sua conta Demôm automação como contribuinte para a subscrição.
   * **JÁ A PARTIR DEVM** - O nome do VM que já está habilitado manualmente para atualizações.
   * **JÁ A PARTIR DEVMRESOURCEGROUP** - Nome do grupo de recursos a que pertence o VM.
   * **SOLUTIONTYPE** - Introduzir **atualizações**.

   ![Parâmetros do runbook Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

3. Selecione **OK** para iniciar a tarefa de runbook.
4. Monitorize o progresso e os erros na página de tarefa de runbook.

## <a name="next-steps"></a>Passos seguintes

* Para agendar um livro de [corridas, consulte Gerir os horários na Azure Automation.](shared-resources/schedules.md)
* Para utilizar a Gestão de Atualização para VMs, consulte [Gerir atualizações e patches para os seus VMs Azure](automation-tutorial-update-management.md).
* Para resolver os erros gerais de Gestão de Atualização, consulte [problemas de Gestão de Atualização de Resolução de Problemas](troubleshoot/update-management.md).
* Para resolver problemas com o agente de atualização do Windows, consulte [problemas de agente de atualização do Windows de resolução de resolução](troubleshoot/update-agent-issues.md)de problemas .
* Para resolver problemas com o agente de atualização Linux, consulte [problemas de agente de atualização do Linux de resolução](troubleshoot/update-agent-issues-linux.md)de resolução de problemas .
