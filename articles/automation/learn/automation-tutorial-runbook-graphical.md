---
title: Criar um livro gráfico na Automação Azure
description: Este artigo ensina-o a criar, testar e publicar um simples livro gráfico na Azure Automation.
keywords: runbook, modelo do runbook, automatização do runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 3cd5db3736d5eda88e7cad7bda1966efb2b00977
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744742"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutorial: Criar um livro de execução gráfico

Este tutorial explica como criar um [ runbook gráfico](../automation-runbook-types.md#graphical-runbooks) na Automatização do Azure. Pode criar e editar livros de execução gráficos e gráficos powerShell Workflow utilizando o editor gráfico no portal Azure. 

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar um simples livro de execução gráfica
> * Teste e publique o livro de corridas
> * Executar e rastrear o estado do trabalho do livro de corridas
> * Atualize o livro de corridas para iniciar uma máquina virtual Azure, com parâmetros de livro e ligações condicionais

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](../automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que paraste e começas esta máquina, não devia ser um VM de produção.

## <a name="step-1---create-runbook"></a>Passo 1 – Criar runbook

Comece por criar um livro simples que produz o texto `Hello World` .

1. No portal do Azure, abra a sua conta da Automatização. 

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria desses ativos são os módulos automaticamente incluídos numa nova conta Automation. Também deve ter o ativo credencial associado à sua subscrição.

2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.

3. Crie um novo livro de corridas selecionando **Criar um livro de execução**.

4. Atribua ao runbook o nome **MyFirstRunbook-Graphical**.

5. Neste caso, vai criar um [livro gráfico.](../automation-graphical-authoring-intro.md) **Selecione Gráfico** para **o tipo Derbook**.<br> ![Novo runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)<br>

6. Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Passo 2 - Adicionar atividades

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook. Vai adicionar um `Write-Output` cmdlet ao texto de saída do livro de execução.

1. No controlo da Biblioteca, clique no campo de pesquisa e escreva `write-output` . Os resultados da pesquisa são mostrados na imagem seguinte. <br> ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. Desloque-se para baixo até à parte inferior da lista. Clique no direito **Write-Output** e **selecione Adicionar a tela**. Em alternativa, pode clicar na elipse (...) ao lado do nome cmdlet e, em seguida, selecionar **Adicionar a tela**.

3. Clique na atividade **Write-Output** na tela. Esta ação abre a página de controlo de Configuração, que lhe permite configurar a atividade.

4. O campo **Label** predefinido para o nome do cmdlet, mas pode mudá-lo para algo mais amigável. Mude `Write Hello World to output` para.

5. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros, e você precisa selecionar qual usar. Neste caso, `Write-Output` tem apenas um parâmetro definido.

6. Selecione o `InputObject` parâmetro. Este é o parâmetro que utiliza para especificar o texto para enviar para o fluxo de saída.

7. O menu de dropdown **de fonte de dados** fornece fontes que pode usar para preencher um valor de parâmetro. Neste menu, selecione **powerShell expressão**. 

   Pode utilizar a saída de fontes como outra atividade, um ativo Automation ou uma expressão PowerShell. Neste caso, a saída é `Hello World` justa. Pode utilizar uma expressão do PowerShell e especificar uma cadeia.<br>

8. No campo **Expression,** digite `Hello World` e clique em **OK** duas vezes para voltar à tela.

9. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o livro de execução para o disponibilizar em produção, deve testá-lo para se certificar de que funciona corretamente. Testar um livro de execução executa a sua versão Draft e permite-lhe visualizar a sua saída interativamente.

1. Selecione painel de **teste** para abrir o painel de teste.

2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.

3. Note que é criado um trabalho de livro de [rés](../automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estado de trabalho começa como , indicando que o trabalho está à espera que um trabalhador do `Queued` livro na nuvem fique disponível. O estado muda para `Starting` quando um trabalhador reclama o trabalho. Finalmente, o estado torna-se `Running` quando o livro de corridas realmente começa a funcionar.

4. Quando o trabalho do livro de execução estiver concluído, o painel de teste mostra a sua saída. Neste caso, `Hello World` vê.

    ![Olá, Mundo](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está em modo Draft. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. **Selecione Publicar** para publicar o livro de execução e, em seguida, **sim** quando solicitado.

2. Percorra à esquerda para ver o livro de execução na página Runbooks e note que o valor **do Estado de Autor** está definido para **Publicado**.

3. Percorra para a direita para ver a página para **MyFirstRunbook-Graphical**.

   As opções em toda a parte superior permitem-lhe iniciar o livro de corridas agora, agendar um futuro início ou criar um [webhook](../automation-webhooks.md) para que o livro de execução possa ser iniciado através de uma chamada HTTP.

4. Selecione **Iniciar** e, **em seguida, Sim** quando solicitado para iniciar o livro de execução.

5. Um painel de trabalho é aberto para o trabalho de livro que foi criado. Verifique se o campo de estado do **trabalho** mostra **concluído**.

6. Clique em **Saída** para abrir a página Saída, onde pode ver `Hello World` visualizado.

7. Feche a página de saída.

8. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve ver `Hello World` no fluxo de saída. 

    Note que o painel streams pode mostrar outros fluxos para um trabalho de livro de corridas, como verbose e fluxos de erro, se o livro de execução escrever para eles.

9. Feche o painel de Streams e o painel de trabalho para voltar à página MyFirstRunbook-Graphical.

10. Para ver todos os trabalhos para o livro de corridas, selecione **Jobs** under **Resources**. A página Jobs lista todos os empregos criados pelo seu livro de corridas. Só devias ver um emprego na lista, já que só geriste o trabalho uma vez.

11. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o livro de corridas. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de corridas.

## <a name="step-5---create-variable-assets"></a>Passo 5 – criar recursos de variável

Testou e publicou o seu livro de corridas, mas até agora não faz nada de útil para gerir os recursos do Azure. Antes de configurar o livro de execução para autenticar, deve criar uma variável para manter o ID de subscrição, configurar uma atividade para autenticar e, em seguida, fazer referência à variável. Incluir uma referência ao contexto de subscrição permite-lhe trabalhar facilmente com várias subscrições.

1. Copie o seu ID de subscrição a partir da opção **Subscrições** no painel Navegante.

2. Na página Contas de Automação, selecione **Variáveis** em **Recursos Partilhados.**

3. **Selecione Adicionar uma variável**.

4. Na nova página variável, faça as seguintes definições nos campos fornecidos.

    * **Nome** - insira `AzureSubscriptionId` .
    * **Valor** - introduza o seu ID de subscrição. 
    * **Tipo** - mantenha a corda selecionada.
    * **Encriptação,** use o valor padrão.

5. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Passo 6 - Adicionar autenticação

Agora que tem uma variável para segurar o ID de subscrição, pode configurar o livro de execução para autenticar com as credenciais Run As para a sua subscrição. Faça isto adicionando o Azure Run Como ligação como um ativo. Também deve adicionar o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e o [cmdlet Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) à tela.

>[!NOTE]
>Para os livros de execução da PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Note que estes pseudónimos não estão disponíveis para os seus livros gráficos. Um livro gráfico só pode `Connect-AzAccount` usar-se sozinho.

1. Navegue para o seu livro de execução e selecione **Editar** na página MyFirstRunbook-Graphical.

2. Já não precisas da `Write Hello World to output` entrada. Basta clicar na elipse e selecionar **Apagar**.

3. No controlo da Biblioteca, expandir **ATIVOS,** depois **Ligações**. Adicione `AzureRunAsConnection` à tela selecionando **Adicionar à tela**.

4. Mude o nome `AzureRunAsConnection` para `Get Run As Connection` .

5. No controlo da Biblioteca, digite `Connect-AzAccount` no campo de pesquisa.

6. Adicione `Connect-AzAccount` à tela.

7. Pairar sobre `Get Run As Connection` até que um círculo apareça no fundo da forma. Clique no círculo e arraste a seta `Connect-AzAccount` para formar um link. O livro de corridas começa com `Get Run As Connection` e depois `Connect-AzAccount` corre.<br> ![Criar ligação entre atividades](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. Na tela, selecione `Connect-AzAccount` . No painel de controlo de configuração, digite **login a Azure** no campo **Label.**

9. Clique em **Parâmetros**, e aparece a página de Configuração do Parâmetro de Atividade.

10. O `Connect-AzAccount` cmdlet tem vários conjuntos de parâmetros, e você precisa selecionar um antes de fornecer valores de parâmetro. Clique no **conjunto de parâmetros** e, em seguida, selecione **ServicePrincipalCertificateWithSubscriptionId**.

11. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros de conta Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. Na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique em **OK**.

   * **Fonte de dados** - selecione **Saída de Atividade**.
   * Lista de fonte de dados - **selecione Get Automation Connection**.
   * **Caminho de campo** - tipo `ApplicationId` . Você está especificando o nome da propriedade para o caminho de campo porque a atividade produz um objeto com múltiplas propriedades.

13. Clique em **CERTIFICATETHUMBPRINT**, e na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique **OK**.

    * **Fonte de dados** - selecione **Saída de Atividade**.
    * Lista de fonte de dados - **selecione Get Automation Connection**.
    * **Caminho de campo** - tipo `CertificateThumbprint` .

14. Clique em **SERVICEPRINCIPAL**, e na página Valor do Parâmetro, **selecione ConstantValue** para o campo **fonte de dados;** clique na opção **True;** e, em seguida, clicar **OK**.

15. Clique em **TENANTID**e faça as seguintes definições na página Valor do Parâmetro. Quando terminar, clique **ok** duas vezes.

    * **Fonte de dados** - selecione **Saída de Atividade**. 
    * Lista de fonte de dados - **selecione Get Automation Connection**.
    * **Caminho de campo** - tipo `TenantId` . 

16. No controlo da Biblioteca, digite `Set-AzContext` no campo de pesquisa.

17. Adicione `Set-AzContext` à tela.

18. Selecione `Set-AzContext` na tela. No painel de controlo de configuração, introduza `Specify Subscription Id` no campo **Etiqueta.**

19. Clique em **Parâmetros** e aparece a página de Configuração do Parâmetro de Atividade.

20. O `Set-AzContext` cmdlet tem vários conjuntos de parâmetros, e você precisa selecionar um antes de fornecer valores de parâmetro. Clique no **Conjunto de Parâmetros** e, em seguida, selecione **SubscriptionId**.

21. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique em **SubscriptionID**.

22. Na página Valor do Parâmetro, selecione **Variable Asset** para o campo fonte **de dados** e selecione **AzureSubscriptionId** da lista de origem. Quando terminar, clique **ok** duas vezes.

23. Pairar sobre `Login to Azure` até que um círculo apareça no fundo da forma. Clique no círculo e arraste a seta para `Specify Subscription Id` . O seu livro de corridas deve parecer o seguinte neste momento.

    ![Configuração de autenticação de runbook](../media/automation-tutorial-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passo 7 – adicionar atividade para iniciar uma máquina virtual

Agora tem de adicionar uma `Start-AzVM` atividade para iniciar uma máquina virtual. Pode escolher qualquer VM na sua subscrição Azure e, por enquanto, está a codificar o seu nome para o cmdlet [Start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. No controlo da Biblioteca, digite `Start-Az` no campo de pesquisa.

2. Adicione `Start-AzVM` à tela e, em seguida, clique e arraste-a por baixo `Specify Subscription Id` .

3. Pairar sobre `Specify Subscription Id` até que um círculo apareça no fundo da forma. Clique no círculo e arraste a seta para `Start-AzVM` .

4. Selecione `Start-AzVM`. Clique em **Parâmetros** e, **em seguida, parameter set** para ver os conjuntos para a atividade.

5. Selecione **ResourceGroupNameParameterSetName** para o conjunto de parâmetros. Os campos **ResourceGroupName** e **Nome** têm marcas de exclamação ao lado deles para indicar que são parâmetros necessários. Note que ambos os campos esperam valores de cordas.

6. Selecione **Name**. Escolha a **expressão PowerShell** para o campo **fonte de dados.** Para o VM que utiliza para iniciar este livro de execução, digite o nome da máquina rodeado de cotações duplas. Clique em **OK**.

7. Selecione **ResourceGroupName**. Utilize a expressão de valor **PowerShell** para o campo fonte de **dados** e escreva em nome do grupo de recursos rodeado de cotações duplas. Clique em **OK**.

8. Clique no **painel de teste** para que possa testar o livro de corridas.

9. Clique **em Começar** para iniciar o teste. Uma vez concluído, certifique-se de que o VM começou. O seu livro de corridas deve parecer o seguinte neste momento.

    ![Configuração de autenticação de runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passo 8 - Adicionar parâmetros adicionais de entrada

O seu livro de execução inicia atualmente o VM no grupo de recursos que especificou para o `Start-AzVM` cmdlet. O livro de execução será mais útil se especificar tanto o nome como o grupo de recursos quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao livro de execução para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** na página MyFirstRunbook-Graphical.

2. Selecione **entrada e saída** **e,** em seguida, adicione a entrada para abrir o painel de parâmetro de entrada de saída do livro de execução.

3. Faça as seguintes definições nos campos fornecidos **e,** em seguida, clique OK .
   * **Nome** - especificar `VMName` .
   * **Tipo** - mantenha a fixação das cordas.
   * **Obrigatório** - alterar o valor para **Sim**.

4. Crie um segundo parâmetro de entrada obrigatório chamado `ResourceGroupName` e, em seguida, clique em **OK** para fechar o painel de entrada e saída.<br> ![Parâmetros de Entrada do Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. Selecione a `Start-AzVM` atividade e, em seguida, clique em **Parâmetros**.

6. Altere o campo de **origem de dados** para a entrada de **Nome** para Livro **de Execução**. Em seguida, selecione **VMName**.

7. Altere o campo de **origem de dados** para o **ResourceGroupName** para a entrada do Livro de **Execução** e, em seguida, selecione **ResourceGroupName**.<br> ![Parâmetros start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.

9. Feche o painel de Teste.

10. Clique em **Publicar** para publicar a nova versão do runbook.

11. Pare o VM que começou anteriormente.

12. Clique em **Iniciar** para iniciar o runbook. Digite os valores para `VMName` e `ResourceGroupName` para o VM que vai começar.

13. Quando o livro de execução estiver concluído, certifique-se de que o VM foi iniciado.

## <a name="step-9---create-a-conditional-link"></a>Passo 9 – criar uma ligação condicional

Agora pode modificar o livro de execução para que apenas tente iniciar o VM se ainda não estiver iniciado. Faça-o adicionando um cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) que recupera o estado de nível de instância do VM. Em seguida, pode adicionar um módulo de código powerShell workflow chamado `Get Status` com um excerto de código PowerShell para determinar se o estado VM está em execução ou parado. Uma ligação condicional do módulo só funciona se o estado de `Get Status` `Start-AzVM` funcionamento atual for interrompido. No final deste procedimento, o seu livro de execução utiliza o `Write-Output` cmdlet para enviar uma mensagem para informá-lo se o VM foi iniciado com sucesso.

1. Abra **myFirstRunbook-Graphical** no editor gráfico.

2. Retire o link entre `Specify Subscription Id` e `Start-AzVM` clicando nele e, em seguida, **pressionando Delete**.

3. No controlo da Biblioteca, digite `Get-Az` no campo de pesquisa.

4. Adicione `Get-AzVM` à tela.

5. Selecione `Get-AzVM` e, em seguida, clique em **Parâmetros Paravê-lo** para ver os conjuntos para o cmdlet. 

6. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**. Os campos **ResourceGroupName** e **Name** têm marcas de exclamação ao lado deles, indicando que especificam os parâmetros necessários. Note que ambos os campos esperam valores de cordas.

7. Em fonte **de dados** para **Nome**, selecione a entrada do Livro de **Execução**e, **em seguida, VMName**. Clique em **OK**.

8. Sob **a fonte de dados** do **ResourceGroupName**, selecione a entrada do Livro de **Execução,** em **seguida, ResourceGroupName**. Clique em **OK**.

9. Sob **a fonte de Dados** para o **Estado,** selecione **valor constante,** em **seguida, Verdadeiro**. Clique em **OK**.

10. Criar uma ligação `Specify Subscription Id` de `Get-AzVM` .

11. No controlo da Biblioteca, expanda o Controlo do Livro de **Execução** e adicione **Código** à tela.  

12. Criar uma ligação `Get-AzVM` de `Code` .  

13. Clique e, no painel de `Code` configuração, mude a etiqueta para **Obter O Estado**.

14. Selecione `Code` e aparece a página Do Editor de Código.  

15. Colhe o seguinte código na página do editor.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

16. Criar uma ligação `Get Status` de `Start-AzVM` .

    ![Runbook com Módulo de Código](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. Selecione o link e, no painel de configuração, mude a **condição de aplicar** a **Sim**. Note que a ligação se torna uma linha tracejada, indicando que a atividade alvo só funciona se a condição se resolver verdadeiramente.  

18. Para **expressão condição,** escreva `$ActivityOutput['Get Status'] -eq "Stopped"` . `Start-AzVM`agora só corre se o VM for parado.

19. No controlo da Biblioteca, expanda **Cmdlets** e, em seguida, **Microsoft.PowerShell.Utility**.

20. Adicione `Write-Output` a tela duas vezes.

21. Para o primeiro `Write-Output` controlo, clique em **Parâmetros** e altere o valor do **Rótulo** para notificar o **VM iniciado**.

22. Para **inputObject,** altere a **fonte de dados** para a expressão **PowerShell**, e escreva na expressão `$VMName successfully started.` .

23. No segundo `Write-Output` controlo, clique em **Parâmetros** e altere o valor do **Rótulo** para notificar o arranque **do VM falhado**.

24. Para **inputObject,** altere a **fonte de dados** para a expressão **PowerShell**, e escreva na expressão `$VMName could not start` .

25. Criar ligações de `Start-AzVM` `Notify VM Started` `Notify VM Start Failed` e.

26. Selecione o link `Notify VM Started` e mude a condição de **aplicar** a verdade.

27. Para a **expressão Condição,** escreva `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` . Este `Write-Output` controlo agora só funciona se o VM começar com sucesso.

28. Selecione o link `Notify VM Start Failed` e mude a condição de **aplicar** a verdade.

29. Para o campo de **expressão condição,** escreva `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` . Este `Write-Output` controlo agora só funciona se o VM não for iniciado com sucesso. O seu livro de corridas deve parecer a seguinte imagem.

    ![Runbook com Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Guarde o runbook e abra o painel de Teste.

31. Inicie o livro de corridas com o VM parado e a máquina deve arrancar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre autoria gráfica, consulte [Autor um livro gráfico em Automação Azure](../automation-graphical-authoring-intro.md).
* Para começar com os livros de execução da PowerShell, consulte [Create a PowerShell runbook](automation-tutorial-runbook-textual-powershell.md).
* Para começar com os livros de execução powerShell Workflow, consulte [Create a PowerShell workflow runbook](automation-tutorial-runbook-textual.md).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).