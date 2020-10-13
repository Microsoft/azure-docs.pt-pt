---
title: Use a Azure Automation para lançar um emprego no StorSimple Data Manager
description: Saiba como utilizar a Azure Automation para desencadear empregos de Gestor de Dados StorSimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f13e402344111dea28514ed7b0d381b46ff73064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743612"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Utilizar a Automatização do Azure para acionar uma tarefa

Este artigo explica como pode utilizar a funcionalidade de transformação de dados dentro do serviço StorSimple Data Manager para transformar dados do dispositivo StorSimple. Pode lançar um trabalho de transformação de dados de duas formas: 

 - Utilizar o .NET SDK
 - Use o runbook da Azure Automation
 
Este artigo detalha como criar um runbook da Azure Automation e depois usá-lo para iniciar um trabalho de transformação de dados. Para saber mais sobre como iniciar a transformação de dados através de .NET SDK, vá ao [Use .NET SDK para desencadear empregos de transformação de dados](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   Azure PowerShell instalado no computador cliente. [Baixar Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   Uma definição de emprego corretamente configurada num serviço StorSimple Data Manager dentro de um grupo de recursos.
*   Descarregue  [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) o ficheiro do repositório do GitHub. 
*   Baixe [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) o script do repositório do GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

### <a name="set-up-the-automation-account"></a>Configurar a conta De Automação

1. Crie uma conta Azure Run Como conta de automação no portal Azure. Para tal, vá ao **mercado Azure > Tudo** e depois procure por **Automação.** **Selecione contas de automação**.

    ![Criar Run como conta de automação](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para adicionar uma nova conta de automação, clique **em + Adicionar**.

    ![Criar Executar como conta de automação 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Na **Automatização**de Adicionar:

   1. Forneça o **Nome** da sua conta de automação.
   2. Selecione a **Subscrição** ligada ao seu serviço StorSimple Data Manager.
   3. Crie um novo grupo de recursos ou selecione a partir de um grupo de recursos existente.
   4. Selecione uma **localização**.
   5. Deixe a opção **de execução por** defeito Como a opção de conta selecionada.
   6. Para obter um link para acesso rápido no painel de instrumentos, verifique **Pin para painel de instrumentos**. Clique em **Criar**.

      ![Criar Executar como conta de automação 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Depois de a conta de automação ter sido criada com sucesso, é notificado.
    
      ![Notificação para implantação da conta de automação](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para mais informações, aceda à [conta Criar uma Conta Como](../automation/automation-create-runas-account.md)Executada.

3. Na conta recém-criada, vá a **Recursos Partilhados > Módulos** e clique **+ Adicionar módulo.**

    ![Módulo de importação 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navegue pela localização do `DataTransformationApp.zip` ficheiro a partir do computador local e selecione e abra o módulo. Clique **em OK** para importar o módulo.

    ![Módulo de importação 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando a Azure Automation importa um módulo para a sua conta, extrai metadados sobre o módulo. Esta operação pode demorar alguns minutos.

   ![Módulo de importação 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Recebe uma notificação de que o módulo está a ser implementado e outra notificação quando o processo estiver concluído.  O estado em **Módulos** muda para **Disponível**.

    ![Módulo de importação 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar o runbook automation

Execute os seguintes passos para importar, publicar e executar o livro de bordo para desencadear a definição de emprego.

1. No portal do Azure, abra a sua conta da Automatização. Vá a **Processos de Automatização > Runbooks** e clique **+ Adicione um runbook**.

    ![Adicionar runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. No **Add runbook,** clique **em Importar um livro de recortes existente.**

3. Aponte para o ficheiro de script Azure PowerShell `Trigger-DataTransformation-Job.ps1` para o **ficheiro Runbook**. O tipo de livro de execução é selecionado automaticamente. Forneça um nome e uma descrição opcional para o livro de recortes. Clique em **Criar**.

    ![Adicionar runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. O novo runbook aparece na lista de runbooks para a conta Automation. Selecione e clique neste livro de execução.

    ![Adicionar runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Edite o runbook e clique no **painel de teste.**

    ![Adicionar runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Forneça os parâmetros como o nome do seu serviço StorSimple Data Manager, o grupo de recursos associado e o nome de definição de trabalho. **Começa** o teste. O relatório é gerado quando a execução está completa. Para mais informações, aceda a como [testar um livro de recortes.](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)

    ![Adicionar runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Inspecione a saída do livro de ensaios no painel de ensaio. Se estiver satisfeito, feche o painel. Clique **em Publicar** e quando solicitado para confirmação, confirme e publique o livro de recortes.

    ![Adicionar runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Volte para **runbooks** e selecione o livro de recortes recém-criado.

    ![Adicionar runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Começa** o livro de corridas. No **runbook Start,** introduza todos os parâmetros. Clique **em OK** para submeter e iniciar o trabalho de transformação de dados.

10. Para monitorizar o progresso do trabalho no portal Azure, vá ao **Jobs** no seu serviço StorSimple Data Manager. Selecione e clique no trabalho para ver os detalhes do trabalho.

    ![Adicionar runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md).
