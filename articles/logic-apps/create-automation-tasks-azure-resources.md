---
title: Criar tarefas de automatização para gerir e monitorizar recursos do Azure
description: Crie tarefas automatizadas que o ajudem a gerir os recursos do Azure e monitorize os custos criando fluxos de trabalho que funcionam em Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719054"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Gerir os recursos do Azure e monitorizar os custos criando tarefas de automação (pré-visualização)

> [!IMPORTANT]
> Esta capacidade está disponível em pré-visualização pública, é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para ajudá-lo a gerir mais facilmente [os recursos do Azure,](../azure-resource-manager/management/overview.md#terminology) pode criar tarefas de gestão automatizadas para um determinado grupo de recursos ou recursos utilizando modelos de tarefas de automação, que variam de disponibilidade com base no tipo de recurso. Por exemplo, para uma [conta de armazenamento Azure,](../storage/common/storage-account-overview.md)pode configurar uma tarefa de automação que lhe envia o custo mensal dessa conta de armazenamento. Para uma [máquina virtual Azure,](https://azure.microsoft.com/services/virtual-machines/)pode criar uma tarefa de automatização que liga ou desliga essa máquina virtual num horário predefinido.

Nos bastidores, uma tarefa de automação é na verdade um fluxo de trabalho que funciona no serviço [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e é faturado usando as [mesmas taxas de preços](https://azure.microsoft.com/pricing/details/logic-apps/) e modelo de [preços.](../logic-apps/logic-apps-pricing.md) Depois de criar a tarefa, pode visualizar e editar o fluxo de trabalho subjacente abrindo a tarefa no Logic App Designer. Depois de uma tarefa terminar pelo menos uma corrida, pode rever o estado, histórico, entradas e saídas para cada execução.

Aqui estão os modelos de tarefas atualmente disponíveis nesta pré-visualização:

| Tipo de recurso | Modelos de tarefa de automação |
|---------------|---------------------------|
| Grupos de recursos do Azure | **Quando o recurso é eliminado** |
| Todos os recursos do Azure | **Enviar custo mensal para recurso** |
| Máquinas virtuais do Azure | Além disso, <p>- **Desligar a Máquina Virtual** <br>- **Iniciar máquina virtual** |
| Contas de Armazenamento do Azure | Além disso, <p>- **Apagar bolhas antigas** |
| Azure Cosmos DB | Além disso, <p>- **Enviar resultado de consulta via e-mail** |
|||

Este artigo mostra-lhe como completar as seguintes tarefas:

* [Crie uma tarefa de automatização](#create-automation-task) para um recurso Azure específico.

* [Reveja o histórico de uma tarefa](#review-task-history), que inclui o estado de execução, entradas, saídas e outras informações históricas.

* [Edite a tarefa](#edit-task) para que possa atualizar a tarefa ou personalizar o fluxo de trabalho subjacente da tarefa no Logic App Designer.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Como é que as tarefas de automatização diferem da Azure Automation?

Atualmente, pode criar uma tarefa de automatização apenas ao nível dos recursos, ver o histórico da tarefa e editar o fluxo de trabalho de aplicações lógicas subjacentes da tarefa, que é alimentado pelo serviço [Azure Logic Apps.](../logic-apps/logic-apps-overview.md) As tarefas de automatização são mais básicas e leves do que [a Azure Automation.](../automation/automation-intro.md)

Em comparação, a Azure Automation é um serviço de automação e configuração baseado na nuvem que suporta uma gestão consistente em todos os seus ambientes Azure e não-Azure. O serviço compreende [a automatização de processos para orquestrar processos](../automation/automation-intro.md#process-automation) utilizando [runbooks,](../automation/automation-runbook-execution.md)gestão de configuração com rastreio e inventário de [alterações,](../automation/change-tracking/overview.md)gestão de atualização, capacidades partilhadas e características heterogéneas. A automatização dá-lhe controlo total durante a implantação, operações e desmantelamento de cargas de trabalho e recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O recurso Azure que pretende gerir. Este artigo utiliza uma conta de armazenamento Azure como exemplo.

* Uma conta office 365 se quiser seguir em frente com o exemplo, que lhe envia e-mail usando o Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Criar uma tarefa de automação

1. No [portal Azure,](https://portal.azure.com)encontre o recurso que pretende gerir.

1. No menu de recursos, percorra a secção **Automação** e selecione **Tarefas**

   ![Screenshot que mostra o portal Azure e um menu de recursos de conta de armazenamento onde a secção "Automação" tem o item do menu "Tarefas" selecionado.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. No painel **tarefas,** **selecione Adicione** para que possa selecionar um modelo de tarefa.

   ![Screenshot que mostra o painel de conta de armazenamento "Tarefas" onde a barra de ferramentas tem "Adicionar" selecionada](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. No painel **'Adicionar uma tarefa',** em **'Selecionar um modelo',** selecione o modelo para a tarefa que pretende criar. Se a página seguinte não aparecer, selecione **Seguinte: Autenticação**.

   Este exemplo continua selecionando o custo mensal enviar para o modelo de tarefa **de recurso.**

   ![Screenshot que mostra as seleções, "Enviar custo mensal para o recurso" e "Seguinte: Autenticação"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Em **Autenticação**, na secção **Ligações,** selecione **Criar** para cada ligação que aparecer na tarefa para que possa fornecer credenciais de autenticação para todas as ligações. Os tipos de ligações em cada tarefa variam em função da tarefa.

   Este exemplo mostra apenas uma das ligações que é exigida por esta tarefa.

   ![Screenshot que mostra a opção "Criar" selecionada para a ligação Azure Resource Manager](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Quando for solicitado, inscreva-se com as suas credenciais de conta Azure.

   ![Screenshot que mostra a seleção, "Iniciar sin"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Cada ligação autenticada com sucesso é semelhante a este exemplo:

   ![Screenshot que mostra ligação criada com sucesso](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Depois de autenticar todas as ligações, selecione **Seguinte: Configuração** se a página seguinte não aparecer.

1. Em **Configuração**, forneça um nome para a tarefa e qualquer outra informação necessária para a tarefa. Quando concluir, selecione **Criar**.

   > [!NOTE]
   > Não é possível alterar o nome da tarefa após a criação, por isso considere um nome que ainda se aplica se [editar o fluxo de trabalho subjacente](#edit-task-workflow). As alterações que faz ao fluxo de trabalho subjacente aplicam-se apenas à tarefa que criou e não ao modelo de tarefa.
   >
   > Por exemplo, se nomear a sua `Send monthly cost` tarefa, mas depois editar o fluxo de trabalho subjacente para ser executado semanalmente, não pode alterar o nome da sua tarefa para `Send weekly cost` .

   As tarefas que enviam notificações por e-mail requerem um endereço de e-mail.

   ![Screenshot que mostra as informações necessárias para a tarefa selecionada](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   A tarefa que criou, que é automaticamente ao vivo e em execução, aparece agora na lista de tarefas da **Automação.**

   ![Screenshot que mostra a lista de tarefas de automatização](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Se a tarefa não aparecer imediatamente, tente refrescar a lista de tarefas ou aguarde um pouco antes de se refrescar. Na barra de ferramentas, **selecione Refresh**.

   Após a execução da tarefa selecionada, obtém-se um e-mail que se parece com este exemplo:

   ![Screenshot que mostra notificação de e-mail enviada por tarefa](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Rever o histórico de tarefas

Para ver o histórico de execuções de uma tarefa juntamente com os seus estatutos, entradas, saídas e outras informações, siga estes passos:

1. No [portal Azure,](https://portal.azure.com)encontre o recurso que tem o histórico de tarefas que pretende rever.

1. No menu do recurso, em **Definições,** **selecione Tarefas de Automação**.

1. Na lista de tarefas, encontre a tarefa que pretende rever. Na coluna **Runs** desta tarefa, selecione **Ver**.

   ![Screenshot que mostra uma tarefa e a opção "Ver" selecionada](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   O painel **de história de Runs** mostra todas as corridas para a tarefa juntamente com os seus estatutos, horários de início, identificadores e durações de execução.

   ![Screenshot que mostra as execuções de uma tarefa, os seus estatutos, e outras informações](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Aqui os possíveis estatutos para uma corrida:

   | Estado | Descrição |
   |--------|-------------|
   | **Cancelado** | A tarefa foi cancelada durante a execução. |
   | **Com falhas** | A tarefa tem pelo menos uma ação falhada, mas não houve ações subsequentes para lidar com a falha. |
   | **Em Execução** | A tarefa está em curso. |
   | **Com êxito** | Todas as ações foram bem sucedidas. Uma tarefa ainda pode terminar com sucesso se uma ação falhar, mas existiu uma ação subsequente para lidar com a falha. |
   | **Em espera** | A corrida ainda não começou e está interrompida porque um caso anterior da tarefa ainda está em andamento. |
   |||

   Para mais informações, consulte [Review executa a história](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Para ver os status e outras informações para cada passo numa corrida, selecione que correr.

   O painel **de execução da aplicação Logic** abre e mostra o fluxo de trabalho subjacente que funciona.

   * Um fluxo de trabalho começa sempre com um [*gatilho*](../connectors/apis-list.md#triggers-actions). Para esta tarefa, o fluxo de trabalho começa com o gatilho [ **de Recorrência**](../connectors/connectors-native-recurrence.md).

   * Cada passo mostra o seu estado e duração de funcionação. Passos que têm durações de 0 segundos demoraram menos de 1 segundo a correr.

   ![Screenshot que mostra cada passo na corrida, estado e duração de execução](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Para rever as entradas e saídas para cada passo, selecione o passo, que se expande.

   Este exemplo mostra as entradas para o gatilho de Recorrência, que não tem saídas porque o gatilho apenas especifica quando o fluxo de trabalho funciona e não fornece saídas para as ações subsequentes ao processo.

   ![Screenshot que mostra o gatilho expandido e entradas](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Em contraste, o **Send a** action tem entradas de ações anteriores no fluxo de trabalho e saídas.

   ![Screenshot que mostra uma ação expandida, entradas e saídas](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Para aprender como pode construir os seus próprios fluxos de trabalho automatizados para que possa integrar aplicações, dados, serviços e sistemas para além do contexto das tarefas de automação para recursos Azure, consulte [Quickstart: Crie o seu primeiro fluxo de trabalho de integração utilizando apps Azure Logic Apps - portal Azure.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Editar a tarefa

Para alterar uma tarefa, tem estas opções:

* [Edite a tarefa "inline"](#edit-task-inline) para que possa alterar as propriedades da tarefa, como informações de ligação ou informações de configuração, por exemplo, o seu endereço de e-mail.

* [Editar o fluxo de trabalho subjacente da tarefa](#edit-task-workflow) no Logic App Designer.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Editar a tarefa inline

1. No [portal Azure,](https://portal.azure.com)encontre o recurso que tem a tarefa que pretende atualizar.

1. No menu do recurso, em **Automação,** selecione **Tarefas**.

1. Na lista de tarefas, encontre a tarefa que pretende atualizar. Abra as elipses da tarefa **(...**) e **selecione Editar na linha**.

   ![Screenshot que mostra o menu de elipses abertas e a opção selecionada, "Editar em linha"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Por predefinição, o **separador Autenticação** aparece e mostra as ligações existentes.

1. Para adicionar novas credenciais de autenticação ou selecionar diferentes credenciais de autenticação existentes para uma ligação, abra o menu de elipses da ligação **(...**) e selecione ou **Adicione novas ligações** ou, se disponível, diferentes credenciais de autenticação.

   ![Screenshot que mostra o separador autenticação, as ligações existentes e o menu de elipses selecionadas](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Para atualizar outras propriedades de tarefa, selecione **Seguinte: Configuração**.

   Para a tarefa neste exemplo, o único imóvel disponível para edição é o endereço de e-mail.

   ![Screenshot que mostra o separador Configuração](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Quando tiver terminado, selecione **Guardar**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Editar o fluxo de trabalho subjacente da tarefa

Quando altera o fluxo de trabalho subjacente para uma tarefa de automatização, as suas alterações afetam apenas a instância de tarefa que criou, e não o modelo que cria a tarefa. Depois de escruissar e guardar as suas alterações, o nome que forneceu para a sua tarefa original pode já não descrever a tarefa com precisão, pelo que poderá ter de recriar a tarefa com um nome diferente.

> [!TIP]
> Como uma boa prática, clone o fluxo de trabalho subjacente para que possa editar a versão copiada. Desta forma, pode escoar e testar as suas alterações na cópia enquanto a tarefa original de automatização continua a funcionar e a funcionar sem correr o risco de perturbar ou quebrar a funcionalidade existente. Depois de terminar as alterações e estiver convencido de que a nova versão funciona com sucesso, pode desativar ou eliminar a tarefa original de automatização e utilizar a versão clonada para a sua tarefa de automação. Os seguintes passos incluem informações sobre como clonar o seu fluxo de trabalho.

1. No [portal Azure,](https://portal.azure.com)encontre o recurso que tem a tarefa que pretende atualizar.

1. No menu do recurso, em **Automação,** selecione **Tarefas**.

1. Na lista de tarefas, encontre a tarefa que pretende atualizar. Abra as elipses da tarefa **(...**) e selecione **Abrir em Aplicações Lógicas**.

   ![Screenshot que mostra o menu de elipses abertas e a opção selecionada, "Open in Logic Apps"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   O fluxo de trabalho subjacente da tarefa abre-se no serviço Azure Logic Apps e mostra o painel **de visão geral** onde pode ver o mesmo histórico de execuções que está disponível para a tarefa.

   ![Screenshot que mostra a tarefa em Azure Logic Apps ver com painel de visão geral selecionado](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Para abrir o fluxo de trabalho subjacente no Logic App Designer, no menu da aplicação lógica, selecione **o designer de aplicações Logic**.

   ![Screenshot que mostra a opção de menu "Logic app designer" selecionada e superfície de designer com o fluxo de trabalho subjacente](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Agora pode editar as propriedades para o gatilho e ações do fluxo de trabalho, bem como editar o gatilho e as ações que definem o próprio fluxo de trabalho. No entanto, como boas práticas, siga os passos para clonar o seu fluxo de trabalho para que possa fazer as suas alterações numa cópia enquanto o fluxo de trabalho original continua a funcionar e a funcionar.

1. Para clonar o seu fluxo de trabalho e editar a versão copiada, siga estes passos:

   1. No menu de fluxo de trabalho da aplicação lógica, selecione **Overview**.

   1. Na barra de ferramentas do painel de ferramentas da panorâmica geral, selecione **Clone**.

   1. No painel de criação de aplicativos lógico, em **Nome,** insira um novo nome para o seu fluxo de trabalho de aplicações lógica copiada.

      Com exceção **do Status da Aplicação Lógica,** as outras propriedades não estão disponíveis para edição. 
      
   1. Em **Lógica App Status**, selecione **Disabled** para que o fluxo de trabalho clonado não funcione enquanto escoda as suas alterações. Pode ativar o fluxo de trabalho quando estiver pronto para testar as suas alterações.

   1. Depois de o Azure terminar de abastecer o seu fluxo de trabalho clonado, encontre e abra o fluxo de trabalho no Logic App Designer.

1. Para visualizar as propriedades para o gatilho ou uma ação, expanda o gatilho ou a ação.

   Por exemplo, pode alterar o gatilho de Recorrência para funcionar semanalmente, em vez de mensalmente.

   ![Screenshot que mostra o gatilho de recorrência expandido com a lista de frequência aberta para mostrar opções de frequência disponíveis](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Para obter mais informações sobre o gatilho de Recorrência, consulte [Criar, programar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de Recorrência](../connectors/connectors-native-recurrence.md). Para obter mais informações sobre outros gatilhos e ações que pode utilizar, consulte [Conectores para Aplicações Lógicas Azure.](../connectors/apis-list.md)

1. Para guardar as suas alterações, na barra de ferramentas do designer, **selecione Save**.

   ![Screenshot que mostra a barra de ferramentas do designer e o comando "Save" selecionado](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Para testar e executar o fluxo de trabalho atualizado, na barra de ferramentas do designer, selecione **Run**.

   Após o final da corrida, o designer mostra os detalhes de execução do fluxo de trabalho.

   ![Screenshot que mostra os detalhes de execução do fluxo de trabalho no designer](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Para desativar o fluxo de trabalho para que a tarefa não continue a funcionar, consulte [gerir aplicações lógicas no portal Azure](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Enviar comentários

Gostaríamos de ouvir-te! Para reportar bugs, fornecer feedback ou fazer perguntas sobre esta capacidade de pré-visualização, [contacte a equipa de Apps Azure Logic](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Gerir aplicativos lógicos no portal Azure](../logic-apps/manage-logic-apps-with-azure-portal.md)
