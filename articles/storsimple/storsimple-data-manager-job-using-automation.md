---
title: Use a Automação Azure para lançar um trabalho no StorSimple Data Manager
description: Saiba como usar a Automação Azure para desencadear empregos de Gestor de Dados StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76273998"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Use a Automação Azure para desencadear um trabalho

Este artigo explica como pode utilizar a funcionalidade de transformação de dados dentro do serviço StorSimple Data Manager para transformar dados do dispositivo StorSimple. Pode lançar um trabalho de transformação de dados de duas formas: 

 - Utilizar o .NET SDK
 - Utilizar o livro de execução da Automação Azure
 
Este artigo detalha como criar um livro de execução da Automação Azure e depois usá-lo para iniciar um trabalho de transformação de dados. Para saber mais sobre como iniciar a transformação de dados através do .NET SDK, vá a [Use .NET SDK para desencadear empregos](storsimple-data-manager-dotnet-jobs.md)de transformação de dados .

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   Azure PowerShell instalado no computador cliente. [Baixar O Azure PowerShell.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
*   Uma definição de trabalho corretamente configurada num serviço StorSimple Data Manager dentro de um grupo de recursos.
*   Descarregue [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) o ficheiro do repositório GitHub. 
*   Descarregue [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) o script do repositório GitHub.

## <a name="step-by-step-procedure"></a>Procedimento passo a passo

### <a name="set-up-the-automation-account"></a>Configurar a conta Automation

1. Crie uma conta de automação Azure Run No portal Azure. Para isso, vá ao **mercado Azure > Everything** e, em seguida, procure a **Automação.** Selecione **contas de Automação**.

    ![Criar Run como conta de automação](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Para adicionar uma nova conta de automação, clique **+ Adicionar**.

    ![Criar Run como conta de automação](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Na **Automação add:**

   1. Forneça o **nome** da sua conta de automação.
   2. Selecione a **Subscrição** ligada ao seu serviço StorSimple Data Manager.
   3. Crie um novo grupo de recursos ou selecione a partir de um grupo de recursos existente.
   4. Selecione um **Local**.
   5. Deixe o padrão **Criar Executar Como** opção de conta selecionada.
   6. Para obter um link para acesso rápido no painel de instrumentos, verifique **pin para o painel de instrumentos**. Clique em **Criar**.

      ![Criar Run como conta de automação](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Após a criação da conta de automação com sucesso, é notificado.
    
      ![Notificação para implementação da conta de automação](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Para mais informações, vá à [Create a Run As account](../automation/automation-create-runas-account.md).

3. Na conta recém-criada, vá aos **Recursos Partilhados > Módulos** e clique **+ Adicionar módulo**.

    ![Módulo de importação 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navegue na localização `DataTransformationApp.zip` do ficheiro a partir do seu computador local e selecione e abra o módulo. Clique **em OK** para importar o módulo.

    ![Módulo de importação 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando a Azure Automation importa um módulo para a sua conta, extrai metadados sobre o módulo. Esta operação pode demorar alguns minutos.

   ![Módulo de importação 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Recebe uma notificação de que o módulo está a ser implantado e outra notificação quando o processo estiver concluído.  O estado dos **Módulos** altera-se ao **Disponível**.

    ![Módulo de importação 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importar, publicar e executar livro de execução automation

Execute os seguintes passos para importar, publicar e executar o livro de execução para desencadear a definição de trabalho.

1. No portal do Azure, abra a sua conta da Automatização. Vá ao **Process Automation > Runbooks** e clique **+ Adicione um livro de execução**.

    ![Adicionar livro de corridas 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Em **Adicionar livro de execução,** clique em importar um livro de **execução existente**.

3. Aponte para o ficheiro `Trigger-DataTransformation-Job.ps1` de script Azure PowerShell para o ficheiro **Runbook**. O tipo de livro de execução é automaticamente selecionado. Forneça um nome e uma descrição opcional para o livro de execução. Clique em **Criar**.

    ![Adicionar livro de corridas 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. O novo livro de execução aparece na lista de livros de execução para a conta Automation. Selecione e clique neste livro de execução.

    ![Adicionar livro de corridas 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Editar o livro de corridas e clicar no painel **de teste.**

    ![Adicionar livro de corridas 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Forneça os parâmetros como o nome do seu serviço StorSimple Data Manager, o grupo de recursos associados e o nome da definição de emprego. **Começa** o teste. O relatório é gerado quando a execução está completa. Para mais informações, vá testar um livro de [corridas](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Adicionar livro de corridas 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Inspecione a saída do livro de ensaios no painel de ensaio. Se estiver satisfeito, feche o painel. Clique em **Publicar** e quando solicitado para confirmação, confirme e publique o livro de execução.

    ![Adicionar livro de corridas 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Volte para **runbooks** e selecione o livro de execução recém-criado.

    ![Adicionar livro de corridas 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Inicie** o livro de corridas. No **livro de arranque,** introduza todos os parâmetros. Clique em **OK** para submeter e iniciar o trabalho de transformação de dados.

10. Para monitorizar o progresso do trabalho no portal Azure, vá ao **Jobs** no seu serviço StorSimple Data Manager. Selecione e clique no trabalho para ver os detalhes do trabalho.

    ![Adicionar livro de corridas 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Passos seguintes

[Utilize o StorSimple Data Manager UI para transformar os seus dados](storsimple-data-manager-ui.md).
