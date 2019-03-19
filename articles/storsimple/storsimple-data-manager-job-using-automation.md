---
title: Utilizar a automatização do Azure para iniciar uma tarefa no Gestor de dados do StorSimple | Documentos da Microsoft
description: Saiba como utilizar a automatização do Azure para acionar tarefas de Gestor de dados do StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58086025"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Utilizar a automatização do Azure para acionar uma tarefa

Este artigo explica como pode utilizar a funcionalidade de transformação de dados no serviço StorSimple Data Manager para transformar dados de dispositivos do StorSimple. Pode iniciar uma tarefa de transformação de dados de duas formas: 

 - Utilizar o .NET SDK
 - Utilizar o runbook de automatização do Azure
 
Este artigo fornece detalhes sobre como criar um runbook da automatização do Azure e, em seguida, utilizá-lo para iniciar uma tarefa de transformação de dados. Para saber mais sobre como iniciar a transformação de dados através do SDK do .NET, aceda a [SDK de .NET de utilização para tarefas de transformação de dados de Acionador](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   O Azure PowerShell instalado no computador cliente. [Transferir o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Uma definição de tarefa corretamente configurado num serviço StorSimple Data Manager dentro de um grupo de recursos.
*   Baixe [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) ficheiro a partir do repositório do GitHub. 
*   Baixe [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) script a partir do repositório do GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

### <a name="set-up-the-automation-account"></a>Configurar a conta de automatização

1. Crie uma conta de automatização Run As do Azure no portal do Azure. Para tal, aceda a **do Azure marketplace > tudo** e, em seguida, procure **automatização**. Selecione **contas de automatização**.

    ![Criar conta de automatização Run as](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para adicionar uma nova conta de automatização, clique em **+ adicionar**.

    ![Criar conta de automatização Run as](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Na **adicionar automatização**:

   1. Forneça o **nome** da sua conta de automatização.
   2. Selecione o **subscrição** ligado ao seu serviço do StorSimple Data Manager.
   3. Criar um novo grupo de recursos ou selecione um grupo de recursos existente.
   4. Selecione uma **Localização**.
   5. Deixe a predefinição **criar conta Run as** opção selecionada.
   6. Para obter uma ligação para acesso rápido no dashboard, visite **afixar ao dashboard**. Clique em **Criar**.

      ![Criar conta de automatização Run as](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Depois da conta de automatização é criada com êxito, será notificado.
    
      ![Notificação para a implementação da conta de automatização](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para obter mais informações, aceda a [criar uma conta Run As](../automation/automation-create-runas-account.md).

3. Na conta recentemente criada, aceda a **recursos partilhados > módulos** e clique em **+ Adicionar módulo**.

    ![Importe o módulo 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navegue até à localização de `DataTransformationApp.zip` de ficheiros do computador local e selecione e abra o módulo. Clique em **OK** para importar o módulo.

    ![Importe o módulo 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando a automatização do Azure importa um módulo para a sua conta, extrai metadados sobre o módulo. Esta operação poderá demorar alguns minutos.

   ![Importe o módulo 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Receberá uma notificação que o módulo está a ser implementado e outra notificação quando o processo estiver concluído.  O estado da **módulos** é alterado para **disponível**.

    ![Importar o módulo 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook de automatização

Execute os seguintes passos para importar, publicar e executar o runbook para acionar a definição de tarefa.

1. No portal do Azure, abra a sua conta da Automatização. Aceda a **automatização de processos > Runbooks** e clique em **+ adicionar um runbook**.

    ![Adicionar runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Na **adicionar runbook**, clique em **importar um runbook existente**.

3. Aponte para o ficheiro de script do Azure PowerShell `Trigger-DataTransformation-Job.ps1` para o **ficheiro de Runbook**. O tipo de runbook é selecionado automaticamente. Forneça um nome e uma descrição opcional para o runbook. Clique em **Criar**.

    ![Adicionar runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. O runbook novo é apresentado na lista de runbooks da conta de automatização. Selecione e clique neste runbook.

    ![Adicionar runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Editar o runbook e clique em **teste** painel.

    ![Adicionar runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Forneça os parâmetros, como o nome do seu serviço do StorSimple Data Manager, o grupo de recursos associados e o nome da definição de tarefa. **Iniciar** o teste. O relatório é gerado quando a execução estiver concluída. Para obter mais informações, veja como [testar um runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Adicionar runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Verificar a saída do runbook no painel de teste. Se estiver satisfeito, feche o painel. Clique em **publicar** e quando lhe for pedida confirmação, confirme e publicar o runbook.

    ![Adicionar runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Volte ao **Runbooks** e selecione o runbook recentemente criado.

    ![Adicionar runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Iniciar** o runbook. Na **iniciar runbook**, introduza todos os parâmetros. Clique em **OK** submeter e iniciar a tarefa de transformação de dados.

10. Para monitorizar o progresso da tarefa no portal do Azure, aceda a **tarefas** no seu serviço do StorSimple Data Manager. Selecione e clique na tarefa para ver os detalhes da tarefa.

    ![Adicionar runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Passos Seguintes

[Use o StorSimple Data Manager interface de Usuário para transformar os seus dados](storsimple-data-manager-ui.md).