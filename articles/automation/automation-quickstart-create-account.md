---
title: Início Rápido do Azure - Criar uma conta de Automatização do Azure | Microsoft Docs
description: Este artigo ajuda-o a começar a criar uma conta Azure Automation e a executar um runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83836707"
---
# <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Pode criar uma conta Azure Automation através do Azure, utilizando o portal Azure, uma interface de utilizador baseada no navegador que permite o acesso a uma série de recursos. Uma conta Automation pode gerir recursos em todas as regiões e subscrições para um determinado inquilino. 

Este quickstart guia-o na criação de uma conta Automation e na execução de um runbook na conta. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[Inicie sessão no Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Criar conta de Automatização

1. Escolha um nome para a sua conta Azure. Os nomes das contas de automação são únicos por região e grupo de recursos. Os nomes das contas automations que foram eliminadas podem não estar imediatamente disponíveis.

    > [!NOTE]
    > Não é possível alterar o nome da conta uma vez introduzido na interface do utilizador. 

2. Clique no botão Criar um botão **de recurso** encontrado no canto superior esquerdo do portal Azure.

3. Selecione **IT & Management Tools** e, em seguida, selecione **Automação**.

4. Introduza a informação da conta, incluindo o nome da conta selecionada. Para **Criar uma conta Run As do Azure**, escolha **Sim** para que os artefactos para simplificar a autenticação no Azure sejam ativados automaticamente. Quando a informação estiver concluída, clique em **Criar** para iniciar a implementação da conta Automation.

    ![Introduza as informações sobre a conta de Automatização na página](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Para obter uma lista atualizada de locais a que pode implementar uma conta Demôm automação, consulte [produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)

5. Quando a implementação estiver concluída, clique em **Todos os Serviços**.

6. Selecione **Contas de Automação** e, em seguida, escolha a conta Automation que criou.

    ![Descrição geral da conta de Automatização](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Executar um runbook

Execute um dos runbooks do tutorial.

1. Clique **em Runbooks** em **Automação de Processos.** A lista de runbooks é apresentada. Por predefinição, vários livros de execução tutoriais estão ativados na conta.

    ![Lista de runbooks da conta de Automatização](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecione o runbook **AzureAutomationTutorialScript**. Esta ação abre a página de descrição geral do runbook.

    ![Descrição geral do runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Clique em **Iniciar** e na página Iniciar Runbook, clique em **OK** para iniciar o runbook.

    ![Página de tarefa do runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Após a situação do `Running` trabalho, clique em **Saída** ou **Em Todos os Registos** para visualizar a saída de trabalho do runbook. Para este runbook do tutorial, o resultado é uma lista dos seus recursos Azure.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, implementou uma conta de Automatização, iniciou uma tarefa de runbook e visualizou os resultados das tarefas. Para saber mais sobre a Automatização do Azure, avance para o início rápido para criar o seu primeiro runbook.

> [!div class="nextstepaction"]
> [Automation Quickstart - Criar um runbook Azure Automation](./automation-quickstart-create-runbook.md)

