---
title: Usar a automação do Azure para iniciar um trabalho no Gerenciador de Dados do StorSimple
description: Saiba como usar a automação do Azure para disparar trabalhos de Gerenciador de Dados do StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273998"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Usar a automação do Azure para disparar um trabalho

Este artigo explica como você pode usar o recurso de transformação de dados dentro do serviço de Gerenciador de Dados do StorSimple para transformar dados do dispositivo StorSimple. Você pode iniciar um trabalho de transformação de dados de duas maneiras: 

 - Utilizar o .NET SDK
 - Usar o runbook de automação do Azure
 
Este artigo fornece detalhes sobre como criar um runbook de automação do Azure e usá-lo para iniciar um trabalho de transformação de dados. Para saber mais sobre como iniciar a transformação de dados por meio do SDK do .NET, acesse [usar o SDK do .net para disparar trabalhos de transformação de dados](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

*   Azure PowerShell instalado no computador cliente. [Baixar Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Uma definição de trabalho configurada corretamente em um serviço de Gerenciador de Dados do StorSimple dentro de um grupo de recursos.
*   Baixe [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) arquivo do repositório github. 
*   Baixe [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) script do repositório github.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

### <a name="set-up-the-automation-account"></a>Configurar a conta de automação

1. Crie uma conta de automação executar como do Azure no portal do Azure. Para fazer isso, vá para o **Azure marketplace > tudo** e, em seguida, pesquise **automação**. Selecione **contas de automação**.

    ![Criar conta de automação executar como](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para adicionar uma nova conta de automação, clique em **+ Adicionar**.

    ![Criar conta de automação executar como](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Em **Adicionar automação**:

   1. Forneça o **nome** da sua conta de automação.
   2. Selecione a **assinatura** vinculada ao seu serviço de Gerenciador de dados do StorSimple.
   3. Crie um novo grupo de recursos ou selecione um grupo de recursos existente.
   4. Selecione uma **Localização**.
   5. Deixe a opção padrão **criar conta Executar como** selecionada.
   6. Para obter um link para acesso rápido no painel, marque **fixar no painel**. Clique em **Criar**.

      ![Criar conta de automação executar como](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Depois que a conta de automação for criada com êxito, você será notificado.
    
      ![Notificação de implantação da conta de automação](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para obter mais informações, acesse [criar uma conta Executar como](../automation/automation-create-runas-account.md).

3. Na conta recém-criada, acesse **recursos compartilhados > módulos** e clique em **+ Adicionar módulo**.

    ![Importar módulo 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navegue até o local do arquivo de `DataTransformationApp.zip` do computador local e selecione e abra o módulo. Clique em **OK** para importar o módulo.

    ![Importar módulo 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando a automação do Azure importa um módulo para sua conta, ela extrai metadados sobre o módulo. Esta operação pode levar alguns minutos.

   ![Importar módulo 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Você recebe uma notificação informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.  O status em **módulos** é alterado para **disponível**.

    ![Importar módulo 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook de automação

Execute as etapas a seguir para importar, publicar e executar o runbook para disparar a definição de trabalho.

1. No portal do Azure, abra a sua conta da Automatização. Vá para **processo de automação > Runbooks** e clique em **+ Adicionar um runbook**.

    ![Adicionar runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Em **Adicionar runbook**, clique em **importar um runbook existente**.

3. Aponte para o arquivo de script de Azure PowerShell `Trigger-DataTransformation-Job.ps1` para o **arquivo de runbook**. O tipo de runbook é selecionado automaticamente. Forneça um nome e uma descrição opcional para o runbook. Clique em **Criar**.

    ![Adicionar runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. O novo runbook aparece na lista de runbooks para a conta de automação. Selecione e clique neste runbook.

    ![Adicionar runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edite o runbook e clique em painel de **teste** .

    ![Adicionar runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Forneça os parâmetros, como o nome do seu serviço de Gerenciador de Dados do StorSimple, o grupo de recursos associado e o nome da definição de trabalho. **Inicie** o teste. O relatório é gerado quando a execução é concluída. Para obter mais informações, acesse como [testar um runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Adicionar runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Inspecione a saída do runbook no painel de teste. Se estiver satisfeito, feche o painel. Clique em **publicar** e, quando for solicitado a confirmar, confirme e publique o runbook.

    ![Adicionar runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Volte para **Runbooks** e selecione o runbook recém-criado.

    ![Adicionar runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Inicie** o runbook. Em **Iniciar runbook**, insira todos os parâmetros. Clique em **OK** para enviar e iniciar o trabalho de transformação de dados.

10. Para monitorar o andamento do trabalho no portal do Azure, vá para **trabalhos** em seu serviço de Gerenciador de dados do StorSimple. Selecione e clique no trabalho para exibir os detalhes do trabalho.

    ![Adicionar runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Passos seguintes

[Use Gerenciador de dados do StorSimple interface do usuário para transformar seus dados](storsimple-data-manager-ui.md).
