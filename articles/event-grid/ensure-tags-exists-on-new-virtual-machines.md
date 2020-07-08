---
title: Integrar a Automatização do Azure no Event Grid | Microsoft Docs
description: Saiba como adicionar automaticamente uma etiqueta quando é criada uma nova VM e enviar uma notificação para o Microsoft Teams.
keywords: automatização, runbook, teams, event grid, máquina virtual, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 79f3d83417a99d40ea0d4bd101a89300bc85a393
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114521"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Tutorial: Integrar a Azure Automation com a Grade de Eventos e equipas da Microsoft

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Importar um runbook de exemplo do Event Grid.
> * Criar um webhook opcional do Microsoft Teams.
> * Criar um webhook para o runbook.
> * Crie uma subscrição do Event Grid.
> * Criar uma VM que acione o runbook.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Para concluir este tutorial, é necessária uma [conta de Automatização do Azure](../automation/automation-offering-get-started.md) para manter o runbook acionado a partir da subscrição do Azure Event Grid.

* O módulo `AzureRM.Tags` tem de ser carregado na sua Conta de Automatização. Veja [Como importar módulos na Automatização do Azure](../automation/automation-update-azure-modules.md) para saber como importar módulos para a Automatização do Azure.

## <a name="import-an-event-grid-sample-runbook"></a>Importar um runbook de exemplo do Event Grid

1. Selecione a sua Conta de Automatização e selecione a página **Runbooks**.

   ![Selecionar runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Selecione o botão **Procurar galeria**.

3. Procure **Event Grid** e selecione **Integrar a Automatização do Azure no Event Grid**.

    ![Importar o runbook da galeria](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Selecione **Importar** e atribua o nome **Watch-VMWrite**.

5. Após a importação, selecione **Editar** para ver a origem do runbook. 
6. Atualize a linha 74 no script a utilizar `Tag` em vez de `Tags` .

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Selecione o botão **Publicar**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Criar um webhook opcional do Microsoft Teams

1. No Microsoft Teams, selecione **Mais Opções** junto ao nome do canal e, em seguida, selecione **Conectores**.

    ![Ligações do Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Desloque-se na lista de conectores até **Webhook de Entrada** e selecione **Adicionar**.

3. Introduza **AzureAutomationIntegration** para o nome e selecione **Criar**.

4. Copie o URL webhook para a área de transferência e guarde-o. O URL do webhook é utilizado para enviar informações para o Microsoft Teams.

5. Selecione **Concluído** para guardar o webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Criar um webhook para o runbook

1. Abra o runbook Watch-VMWrite.

2. Selecione **Webhooks** e selecione o botão **Adicionar Webhook**.

3. Introduza **WatchVMEventGrid** para o nome. Copie o URL para a área de transferência e guarde-o.

    ![Configurar o nome do webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Selecione **Configurar parâmetros e definições de execução** e introduza o URL do webhook do Microsoft Teams para **CHANNELURL**. Deixe **WEBHOOKDATA** em branco.

    ![Configurar os parâmetros do webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Selecione **Criar** para criar o webhook do runbook de Automatização.

## <a name="create-an-event-grid-subscription"></a>Criar uma subscrição do Event Grid

1. Na página de descrição geral **Conta de Automatização**, selecione **Event Grid**.

    ![Selecionar Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Clique em **+ Subscrição de Evento**.

3. Configure a subscrição com as seguintes informações:
    1. No **Tipo de Tópico**, selecione **Subscrições do Azure**.
    2. Desmarque a caixa de verificação **Subscrever todos os tipos de eventos**.
    3. Introduza **AzureAutomation** para o nome.
    4. Na lista pendente **Tipos de Eventos Definidos**, desmarque todas as opções, exceto **Êxito da Escrita de Recurso**.

        > [!NOTE] 
        > A Azure Resource Manager não diferencia atualmente entre criar e atualizar, pelo que implementar este tutorial para todos os eventos Microsoft.Resources.ResourceWriteSuccess na sua Subscrição Azure pode resultar num grande volume de chamadas.
    1. No **Tipo de Ponto Final**, selecione **Webhook**.
    2. Clique em **Selecionar um ponto final**. Na página **Selecionar Webhook** que se abre, cole o url do webhook criado para o runbook Watch-VMWrite.
    3. Em **FILTROS**, introduza a subscrição e o grupo de recursos em que quer procurar as novas VMs criadas. Deve ter o seguinte aspeto: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Selecione **Criar** para guardar a subscrição do Event Grid.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Criar uma VM que acione o runbook

1. Crie uma nova VM no grupo de recursos que especificou no filtro de prefixo da subscrição do Event Grid.

2. O runbook Watch-VMWrite deve ser chamado e uma nova etiqueta adicionada à VM.

    ![Etiqueta da VM](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. É enviada uma nova mensagem para o canal do Microsoft Teams.

    ![Notificação do Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurou a integração entre o Event Grid e a Automatização. Aprendeu a:

> [!div class="checklist"]
> * Importar um runbook de exemplo do Event Grid.
> * Criar um webhook opcional do Microsoft Teams.
> * Criar um webhook para o runbook.
> * Crie uma subscrição do Event Grid.
> * Criar uma VM que acione o runbook.

> [!div class="nextstepaction"]
> [Criar e encaminhar eventos personalizados com o Event Grid](../event-grid/custom-event-quickstart.md)
