---
title: Início Rápido do Azure - Criar uma conta de Automatização do Azure | Microsoft Docs
description: Saiba como criar uma conta de Automatização do Azure e executar um runbook
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7704f080b7c1878f2fa2b079a1f242c8c2cc87a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81536986"
---
# <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Pode criar uma conta Azure Automation através do Azure, utilizando o portal Azure, uma interface de utilizador baseada no navegador que permite o acesso a uma série de recursos. Uma conta de Automação pode gerir recursos em todas as regiões e subscrições para um determinado inquilino. 

Este quickstart guia-o na criação de uma conta De automação e na execução de um livro de execução na conta. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

[Inscreva-se no Azure.](https://portal.azure.com)

## <a name="create-automation-account"></a>Criar conta de Automatização

1. Escolha um nome para a sua conta Azure. Os nomes das contas de automação são únicos por região e grupo de recursos. Os nomes das contas de Automação que foram eliminadas podem não estar imediatamente disponíveis.

    > [!NOTE]
    > Não é possível alterar o nome da conta depois de ter sido introduzido na interface de utilizador. 

2. Clique no botão **Criar um botão de recurso** encontrado no canto superior esquerdo do portal Azure.

3. Selecione **ferramentas de gestão de TI &** e, em seguida, selecione **Automation**.

4. Insira as informações da conta, incluindo o nome da conta selecionada. Para **Criar uma conta Run As do Azure**, escolha **Sim** para que os artefactos para simplificar a autenticação no Azure sejam ativados automaticamente. Quando a informação estiver completa, clique em **Criar** para iniciar a implementação da conta Automation.

    ![Introduza as informações sobre a conta de Automatização na página](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Para obter uma lista atualizada de locais para os quais pode implementar uma conta de Automação, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Quando a implementação estiver concluída, clique em **Todos os Serviços**.

6. Selecione **Contas de Automação** e, em seguida, escolha a conta De automatização que criou.

    ![Descrição geral da conta de Automatização](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Executar um runbook

Execute um dos runbooks do tutorial.

1. Clique em **Runbooks** em **Automação de Processos**. A lista de runbooks é apresentada. Por padrão, vários livros de execução tutoriais estão ativados na conta.

    ![Lista de runbooks da conta de Automatização](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selecione o runbook **AzureAutomationTutorialScript**. Esta ação abre a página de descrição geral do runbook.

    ![Descrição geral do runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Clique em **Iniciar**e na página Iniciar Runbook, clique em **OK** para iniciar o runbook.

    ![Página de tarefa do runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Depois de se `Running`tornar o estado de trabalho , clique em **Output** ou **All Logs** para visualizar a saída do livro de trabalho. Para este runbook do tutorial, o resultado é uma lista dos seus recursos Azure.

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, implementou uma conta de Automatização, iniciou uma tarefa de runbook e visualizou os resultados das tarefas. Para saber mais sobre a Automatização do Azure, avance para o início rápido para criar o seu primeiro runbook.

> [!div class="nextstepaction"]
> [Guia de Introdução à Automatização - Criar Runbook](./automation-quickstart-create-runbook.md)

