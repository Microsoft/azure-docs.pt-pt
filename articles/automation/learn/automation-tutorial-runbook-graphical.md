---
title: Criar um runbook gráfico na Azure Automation
description: Este artigo ensina-o a criar, testar e publicar um simples livro gráfico na Azure Automation.
keywords: runbook, modelo do runbook, automatização do runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 1b6c02778b0ee790d81c713283e653058c29c153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92899804"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutorial: Criar um runbook gráfico

Este tutorial explica como criar um [ runbook gráfico](../automation-runbook-types.md#graphical-runbooks) na Automatização do Azure. Pode criar e editar livros gráficos e gráficos de fluxo de trabalho PowerShell utilizando o editor gráfico no portal Azure. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um simples runbook gráfico
> * Teste e publique o livro de corridas
> * Correr e acompanhar o estado do trabalho de runbook
> * Atualize o runbook para iniciar uma máquina virtual Azure, com parâmetros de runbook e links condicional

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free)
* [Conta de automatização](../index.yml) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Uma vez que paras e começas esta máquina, não deve ser um VM de produção.
* Se necessário, [importe módulos Azure](../shared-resources/modules.md) ou [módulos de atualização](../automation-update-azure-modules.md) com base nos cmdlets que utiliza.

## <a name="step-1---create-runbook"></a>Passo 1 – Criar runbook

Comece por criar um simples livro de execução que produz o texto `Hello World` .

1. No portal do Azure, abra a sua conta da Automatização.

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria desses ativos são os módulos automaticamente incluídos numa nova conta Automation. Também deverá ter o ativo credencial associado à sua subscrição.

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Criar um novo livro de execução selecionando **Criar um livro de bordo**.

4. Atribua ao runbook o nome **MyFirstRunbook-Graphical**.

5. Neste caso, vai criar um [livro gráfico.](../automation-graphical-authoring-intro.md) Selecione **Graphical** para **o tipo Runbook**.

    ![Novo runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Passo 2 - Adicionar atividades

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook. Vai adicionar um `Write-Output` cmdlet ao texto de saída do livro de bordo.

1. No controlo da Biblioteca, clique no campo de pesquisa e escreva `write-output` . Os resultados da pesquisa são apresentados na imagem seguinte.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Desloque-se para baixo até à parte inferior da lista. Clique com o botão direito **Write-Output** e **selecione Adicionar à tela**. Em alternativa, pode clicar na elipse (...) ao lado do nome cmdlet e, em seguida, selecionar **Adicionar à tela**.

3. Clique na atividade **Write-Output** na tela. Esta ação abre a página de controlo de Configuração, que lhe permite configurar a atividade.

4. O campo **Label** desrescume do nome do cmdlet, mas pode alterá-lo para algo mais amigável. Altere-a para `Write Hello World to output`.

5. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários parâmetros e é necessário selecionar qual deles usar. Neste caso, `Write-Output` tem apenas um parâmetro definido.

6. Selecione o `InputObject` parâmetro. Este é o parâmetro que utiliza para especificar o texto a enviar para o fluxo de saída.

7. O menu de entrega de fonte de **dados** fornece fontes que pode usar para preencher um valor de parâmetro. Neste menu, selecione **a expressão PowerShell**.

   Pode utilizar a saída a partir de fontes como outra atividade, um ativo Automation ou uma expressão PowerShell. Neste caso, a saída é `Hello World` justa. Pode utilizar uma expressão do PowerShell e especificar uma cadeia.

8. No campo **Expressão,** `"Hello World"` escreva e clique **em OK** duas vezes para voltar à tela.

9. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o livro de bordo para o disponibilizar em produção, deverá testá-lo para se certificar de que funciona corretamente. Testar um runbook executa a sua versão Draft e permite-lhe visualizar a sua saída de forma interativa.

1. Selecione **Test pane** para abrir o painel de teste.

2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.

3. Note que é criada uma [função de runbook](../automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estatuto de trabalho começa por `Queued` ser , indicando que o trabalho está à espera que um trabalhador da lista na nuvem fique disponível. O estado muda para `Starting` quando um trabalhador reclama o emprego. Finalmente, o estado torna-se `Running` quando o livro de bordo realmente começa a funcionar.

4. Quando o trabalho do runbook estiver concluído, o painel de teste apresenta a sua saída. Neste caso, `Hello World` vês.

    ![Saída de runbook Hello World](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está no modo Draft. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Selecione **Publicar** para publicar o livro de recortes e, em seguida, **Sim** quando solicitado.

2. Percorra a esquerda para ver o livro de recortes na página Runbooks e note que o valor **do Estado de Autoria** está definido para **Publicado**.

3. Volte para o direito de ver a página para **MyFirstRunbook-Graphical**.

   As opções em toda a parte superior permitem-lhe iniciar o runbook agora, agendar uma hora de início futura, ou criar um [webhook](../automation-webhooks.md) para que o livro de aplicação possa ser iniciado através de uma chamada HTTP.

4. Selecione **Iniciar** e, em seguida, **Sim** quando solicitado para iniciar o livro de execução.

5. Abre-se um painel job para o trabalho de runbook que foi criado. Verifique se o campo **de estado de** trabalho mostra **concluído**.

6. Clique **em Saída** para abrir a página de Saída, onde pode ver `Hello World` visualizado.

7. Feche a página de Saída.

8. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve ver `Hello World` no fluxo de saída.

    Note que o painel streams pode mostrar outros fluxos para uma tarefa de runbook, como verbose e fluxos de erro, se o livro de bordo lhes escrever.

9. Feche o painel de streams e o painel de trabalho para voltar à página MyFirstRunbook-Graphical.

10. Para ver todos os trabalhos para o livro, selecione **Jobs** under **Resources**. A página Jobs lista todos os empregos criados pelo seu runbook. Devia ver apenas um emprego na lista, já que só dirigiu o trabalho uma vez.

11. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o runbook. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de recortes.

## <a name="step-5---create-variable-assets"></a>Passo 5 – criar recursos de variável

Testou e publicou o seu livro de recortes, mas até agora não faz nada de útil para gerir os recursos do Azure. Antes de configurar o livro de aplicação para autenticar, deve criar uma variável para manter o ID de subscrição, configurar uma atividade para autenticar e, em seguida, referenciar a variável. Incluir uma referência ao contexto de subscrição permite-lhe trabalhar facilmente com várias subscrições.

1. Copie o seu ID de subscrição a partir da opção **Subscrições** no painel de navegação.

2. Na página Contas de Automação, selecione Variáveis em **Recursos** **Partilhados.**

3. **Selecione Adicione uma variável.**

4. Na nova página variável, faça as seguintes definições nos campos fornecidos.

    * **Nome** - insira `AzureSubscriptionId` .
    * **Valor** - insira o seu ID de assinatura.
    * **Tipo** - mantenha a corda selecionada.
    * **Encriptação-** use o valor padrão.

5. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Passo 6 - Adicionar autenticação

Agora que tem uma variável para manter o ID de subscrição, pode configurar o runbook para autenticar com as credenciais Run As para a sua subscrição. Faça-o adicionando o Azure Run Como uma ligação como um ativo. Também deve adicionar o [cmdlet Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) e o [cmdlet Set-AzContext](/powershell/module/az.accounts/Set-AzContext) à tela.

>[!NOTE]
>Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Note que estes pseudónimos não estão disponíveis para os seus livros gráficos. Um livro gráfico só pode `Connect-AzAccount` usar-se sozinho.

1. Navegue no seu runbook e **selecione Editar** na página MyFirstRunbook-Graphical.

2. Já não precisas da `Write Hello World to output` entrada. Basta clicar na elipse e selecionar **Eliminar**.

3. No controlo da Biblioteca, expanda **o ATIVO,** depois **as Ligações**. Adicione `AzureRunAsConnection` à tela selecionando Adicionar à **tela**.

4. Renomeado `AzureRunAsConnection` para `Get Run As Connection` .

5. No controlo da Biblioteca, digite `Connect-AzAccount` no campo de pesquisa.

6. Adicione `Connect-AzAccount` à tela.

7. Paire sobre `Get Run As Connection` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta `Connect-AzAccount` para formar um link. O livro começa com `Get Run As Connection` e depois `Connect-AzAccount` corre.

    ![Criar ligação entre atividades](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na tela, selecione `Connect-AzAccount` . No painel de controlo de configuração, **digite Login para Azure** no campo **'Etiquetar'.**

9. Clique em **Parâmetros** e aparece a página de configuração do parâmetro de atividade.

10. O `Connect-AzAccount` cmdlet tem vários parâmetros e precisa de selecionar um antes de fornecer valores de parâmetros. Clique **em Conjunto de Parâmetros** e, em seguida, selecione **ServicePrincipalCertificateWithSubscriptionId**.

11. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique em **APPLICATIONID**.

    ![Adicionar parâmetros de conta Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique em **OK**.

   * **Fonte de dados** - selecione **a saída de Atividade**.
   * Lista de fontes de dados -- **selecione Get Automation Connection**.
   * **Caminho de** campo - tipo `ApplicationId` . Você está especificando o nome da propriedade para o caminho de campo porque a atividade produz um objeto com múltiplas propriedades.

13. Clique em **CERTIFICATETHUMBPRINT**, e na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique em **OK**.

    * **Fonte de dados** - selecione **a saída de Atividade**.
    * Lista de fontes de dados -- **selecione Get Automation Connection**.
    * **Caminho de** campo - tipo `CertificateThumbprint` .

14. Clique em **SERVICEPRINCIPAL**, e na página Valor do Parâmetro, selecione **ConstantValue** para o campo **de origem de dados.** Clique na opção **True** e, em seguida, clique **em OK**.

15. Clique **em TENANTID** e faça as seguintes definições na página Valor do Parâmetro. Quando terminar, clique **em OK** duas vezes.

    * **Fonte de dados** - selecione **a saída de Atividade**.
    * Lista de fontes de dados -- **selecione Get Automation Connection**.
    * **Caminho de** campo - tipo `TenantId` .

16. No controlo da Biblioteca, digite `Set-AzContext` no campo de pesquisa.

17. Adicione `Set-AzContext` à tela.

18. Selecione `Set-AzContext` sobre a tela. No painel de controlo de configuração, introduza `Specify Subscription Id` no campo **'Etiquetar'.**

19. Clique em **Parâmetros** e aparece a página de configuração do parâmetro de atividade.

20. O `Set-AzContext` cmdlet tem vários parâmetros e precisa de selecionar um antes de fornecer valores de parâmetros. Clique **em Conjunto de Parâmetros** e, em seguida, selecione **SubscriptionId**.

21. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique **em SubscriçãoID**.

22. Na página Valor do Parâmetro, selecione **Ativo Variável** para o campo **de origem** de dados e selecione **AzureSubscriptionId** da lista de origem. Quando terminar, clique **em OK** duas vezes.

23. Paire sobre `Login to Azure` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para `Specify Subscription Id` . O seu livro deve parecer o seguinte neste momento.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Screenshot do runbook depois de arrastar a seta para 'Especificar iD de assinatura'.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passo 7 – adicionar atividade para iniciar uma máquina virtual

Agora deve adicionar uma `Start-AzVM` atividade para iniciar uma máquina virtual. Pode escolher qualquer VM na sua subscrição Azure e, por enquanto, está a codificar o seu nome no cmdlet [Start-AzVM.](/powershell/module/az.compute/start-azvm)

1. No controlo da Biblioteca, digite `Start-Az` no campo de pesquisa.

2. Adicione `Start-AzVM` à tela e, em seguida, clique e arraste-a para baixo `Specify Subscription Id` .

3. Paire sobre `Specify Subscription Id` até que um círculo apareça na parte inferior da forma. Clique no círculo e arraste a seta para `Start-AzVM` .

4. Selecione `Start-AzVM`. Clique em **Parâmetros** e, em seguida, **Parâmetro Definido** para ver os conjuntos para a atividade.

5. Selecione **ResourceGroupNameParameterSetName** para o conjunto de parâmetros. Os campos **ResourceGroupName** e **Name** têm marcas de exclamação ao seu lado para indicar que são parâmetros necessários. Note que ambos os campos esperam valores de cordas.

6. Selecione **Name**. Escolha **a expressão PowerShell** para o campo **de origem de dados.** Para o VM que utiliza para iniciar este livro de recortes, escreva o nome da máquina rodeado de citações duplas. Clique em **OK**.

7. Selecione **ResourceGroupName**. Utilize a **expressão PowerShell** de valor para o campo **de origem de dados** e escreva em nome do grupo de recursos rodeado de cotações duplas. Clique em **OK**.

8. Clique **no painel de teste para** que possa testar o livro de execução.

9. Clique **em Iniciar** o teste. Uma vez concluído, certifique-se de que o VM começou. O seu livro deve parecer o seguinte neste momento.

    ![Saída de Start-AzVM do runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passo 8 - Adicione parâmetros de entrada adicionais

O seu runbook inicia atualmente o VM no grupo de recursos especificado para o `Start-AzVM` cmdlet. O livro de execução será mais útil se especificar o nome e o grupo de recursos quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** na página MyFirstRunbook-Graphical.

2. Selecione **a entrada e a saída** e, em seguida, adicione a **entrada** para abrir o painel de parâmetro de entrada do runbook.

3. Faça as seguintes definições nos campos fornecidos e, em seguida, clique **em OK**.
   * **Nome** - especificar `VMName` .
   * **Tipo** - mantenha a definição de corda.
   * **Obrigatório** - alterar o valor para **Sim**.

4. Crie um segundo parâmetro de entrada obrigatório chamado `ResourceGroupName` e, em seguida, clique **em OK** para fechar o painel de entrada e saída.

    ![Parâmetros de entrada do runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selecione a `Start-AzVM` atividade e, em seguida, clique em **Parâmetros**.

6. Alterar o campo **de origem de dados** para a entrada **"Nome** para **Runbook".** Em seguida, selecione **VMName**.

7. Altere o campo **de origem de dados** para a entrada **Do Conjunto de Recursos** para a entrada do **Runbook** e, em seguida, selecione **o Nome do Grupo de Recursos**.

    ![Parâmetros Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.

9. Feche o painel de Teste.

10. Clique em **Publicar** para publicar a nova versão do runbook.

11. Pare o VM que começou anteriormente.

12. Clique em **Iniciar** para iniciar o runbook. Digite os valores `VMName` para e para o `ResourceGroupName` VM que vai começar.

13. Quando o livro de execução estiver concluído, certifique-se de que o VM foi iniciado.

## <a name="step-9---create-a-conditional-link"></a>Passo 9 – criar uma ligação condicional

Agora pode modificar o livro de execução de modo a que só tente iniciar o VM se ainda não estiver iniciado. Faça-o adicionando um cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) que recupere o estado de nível de instância do VM. Em seguida, pode adicionar um módulo de código de fluxo de trabalho PowerShell chamado `Get Status` com um corte de código PowerShell para determinar se o estado VM está em funcionamento ou parado. Uma ligação condicional do `Get Status` módulo só funciona se o estado de `Start-AzVM` funcionamento atual for interrompido. No final deste procedimento, o seu runbook utiliza o `Write-Output` cmdlet para enviar uma mensagem para informá-lo se o VM foi iniciado com sucesso.

1. Abra **myFirstRunbook-Graphical** no editor gráfico.

2. Remova o link entre `Specify Subscription Id` e `Start-AzVM` clicando nele e pressionando **delete**.

3. No controlo da Biblioteca, digite `Get-Az` no campo de pesquisa.

4. Adicione `Get-AzVM` à tela.

5. Selecione `Get-AzVM` e, em seguida, clique em **Parâmetro Definir** para ver os conjuntos para o cmdlet.

6. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**. Os campos De Nome e **Nome** **do Grupo de Recursos** têm marcas de exclamação ao seu lado, indicando que especificam os parâmetros necessários. Note que ambos os campos esperam valores de cordas.

7. Sob **fonte de dados** para **nome,** selecione **entrada Runbook** e, em seguida, **VMName**. Clique em **OK**.

8. Sob **fonte de dados** para **ResourceGroupName**, selecione **entrada Runbook** e, em seguida, **Grupo de RecursosName**. Clique em **OK**.

9. Sob **fonte de dados** para **estado**, selecione **valor constante,** em seguida, **Verdadeiro**. Clique em **OK**.

10. Criar uma ligação `Specify Subscription Id` de `Get-AzVM` .

11. No controlo da Biblioteca, expanda o **Runbook Control** e adicione **Código** à tela.  

12. Criar uma ligação `Get-AzVM` de `Code` .  

13. Clique `Code` e, no painel de configuração, altere a etiqueta para **obter** o estado .

14. Selecione `Code` e a página do Editor de Código aparece.  

15. Cole o seguinte código na página do editor.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

16. Criar uma ligação `Get Status` de `Start-AzVM` .

    ![Runbook com Módulo de Código](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selecione o link e, no painel de configuração, **altere a condição** de **Sim**. Note que a ligação se torna uma linha tracejada, indicando que a atividade do alvo só funciona se a condição se resolver a verdade.  

18. Para **a expressão da condição,** escreva `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM` agora só funciona se o VM for parado.

19. No controlo da Biblioteca, expanda **Cmdlets** e, em seguida, **Microsoft.PowerShell.Utility**.

20. Adicione `Write-Output` à tela duas vezes.

21. Para o primeiro `Write-Output` controlo, clique em **Parâmetros** e altere o valor **do Rótulo** para notificar **o VM Iniciado**.

22. Para **o InputObject,** altere a **fonte de dados** para **a expressão PowerShell** e escreva a expressão `$VMName successfully started.` .

23. No segundo `Write-Output` controlo, clique em **Parâmetros** e altere o valor **do Rótulo** para notificar o início **vm falhado**.

24. Para **o InputObject,** altere a **fonte de dados** para **a expressão PowerShell** e escreva a expressão `$VMName could not start` .

25. Criar links de `Start-AzVM` e para `Notify VM Started` `Notify VM Start Failed` .

26. Selecione o link para `Notify VM Started` e **altere a condição de aplicação** à verdadeira.

27. Para a **expressão condição,** escreva `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Este `Write-Output` controlo só funciona agora se o VM começar com sucesso.

28. Selecione o link para `Notify VM Start Failed` e **altere a condição de aplicação** à verdadeira.

29. Para o campo **de expressão condição,** escreva `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Este `Write-Output` controlo só funciona agora se o VM não for iniciado com sucesso. O seu livro deve parecer a seguinte imagem.

    ![Runbook com Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Guarde o runbook e abra o painel de Teste.

31. Inicie o livro com o VM parado e a máquina deve arrancar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre a autoria gráfica, consulte [o Autor um livro gráfico em Azure Automation.](../automation-graphical-authoring-intro.md)
* Para começar com os livros powerShell, consulte [Criar um livro de execução PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Para começar com os livros de fluxo de trabalho PowerShell, consulte [Criar um livro de fluxo de trabalho PowerShell](automation-tutorial-runbook-textual.md).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
