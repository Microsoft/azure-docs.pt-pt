---
title: Meu primeiro runbook gráfico na automação do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook gráfico simples.
keywords: runbook, modelo do runbook, automatização do runbook, runbook do azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cdd015d9f29c3fb672d626f32a485271e2757c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850317"
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Este tutorial explica como criar um [ runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automatização do Azure. Comece com um runbook simples que testa e publica, enquanto aprende como controlar o estado da tarefa de runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Em seguida, conclui o tutorial, tornando o runbook mais robusto ao adicionar parâmetros do runbook e ligações condicionais.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. O utilizador para e inicia esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-runbook"></a>Criar runbook

Comece por criar um runbook simples que produz o texto *Hello World*.

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns Recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

2. Selecione **Runbooks** em **GESTÃO DE PROCESSOS** para abrir a lista de runbooks.
3. Crie um novo runbook selecionando **+ Adicionar um runbook**e, em seguida, clique em **criar um novo runbook**.
4. Atribua ao runbook o nome *MyFirstRunbook-Graphical*.
5. Neste caso, irá criar um [runbook gráfico](automation-graphical-authoring-intro.md), pelo que deve selecionar **Gráfico** para o **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## <a name="add-activities"></a>Adicionar atividades

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook. Irá adicionar um cmdlet **Write-Output** para apresentar texto a partir do runbook.

1. No controlo da Biblioteca, clique na caixa de texto de pesquisa e escreva **Write-Output**. Os resultados da pesquisa são apresentados na imagem seguinte: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Desloque-se para baixo até à parte inferior da lista. Pode clicar com o botão direito do rato em **Write-Output** e selecionar **Adicionar à tela** ou clicar na elipse junto ao cmdlet e, em seguida, selecionar **Adicionar à tela**.
1. Clique na atividade **Write-Output** na tela. Essa ação abre a página controle de configuração, que permite que você configure a atividade.
1. O campo **Etiqueta** utiliza o nome do cmdlet por predefinição, mas pode alterá-lo para algo mais amigável. Altere-o para *Escrever Olá, Mundo para a saída*.
1. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros e tem de selecionar o que irá utilizar. Neste caso, **Write-Output** tem apenas um conjunto de parâmetros, pelo que não precisa de selecionar um.

1. Selecione o parâmetro **InputObject**. Este é o parâmetro onde especifica o texto a enviar para o fluxo de saída.
1. Na lista pendente **Origem de dados**, selecione **Expressão do PowerShell**. A lista pendente **Origem de dados** fornece diferentes origens que utiliza para preencher um valor do parâmetro.

   Pode utilizar a saída de origens como outra atividade, um recurso de Automatização ou uma expressão do PowerShell. Neste caso, o resultado é apenas *Hello World*. Pode utilizar uma expressão do PowerShell e especificar uma cadeia.<br>

1. Na caixa **Expressão**, escreva *“Olá, Mundo”* e, em seguida, clique duas vezes em **OK** para voltar à tela.
1. Guarde o runbook ao clicar em **Guardar**.

## <a name="test-the-runbook"></a>Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Selecione o **painel de teste** para abrir a página de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado no painel.

   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Irá então mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.

1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, verá *Hello World*.<br> ![Olá Mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche a página de teste para retornar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Selecione **publicar** para publicar o runbook e, em seguida, **Sim** quando solicitado.
1. Se você rolar para a esquerda para exibir o runbook na página **Runbooks** , ele mostrará um **status de criação** **publicado**.
1. Role de volta à direita para exibir a página para **MyFirstRunbook-Graphical**.

   As opções na parte superior permitem-nos iniciar o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.

1. Selecione **Iniciar** e **Sim** quando solicitado a iniciar o runbook.
1. Uma página de trabalho é aberta para o trabalho de runbook que foi criado. Certifique-se de que o **Estado da tarefa** mostra **Concluído**.
1. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. A página **saída** é aberta e você pode ver a *Olá, mundo* no painel.
1. Feche a página saída.
1. Clique em **todos os logs** para abrir a página fluxos para o trabalho de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
1. Feche a página todos os logs e a página do trabalho para retornar à página MyFirstRunbook-Graphical.
1. Para exibir todos os trabalhos do runbook, feche a página do **trabalho** e selecione **trabalhos** em **recursos**. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.
1. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="create-variable-assets"></a>Criar ativos de variável

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Antes de configurar o runbook para realizar a autenticação, crie uma variável para conter o ID de subscrição e referenciá-lo depois de configurar a atividade para realizar a autenticação no passo 6 abaixo. A inclusão de uma referência ao contexto de subscrição permite-lhe trabalhar facilmente entre várias subscrições. Antes de continuar, copie o seu ID de subscrição da opção Subscrições fora do Painel de navegação.

1. Na página contas de automação, selecione **variáveis** em **recursos compartilhados**.
1. Selecione **Adicionar uma variável**.
1. Na página nova variável, na caixa **nome** , digite **AzureSubscriptionId** e, na caixa **valor** , insira sua ID de assinatura. Mantenha *cadeia* para o **Tipo** e o valor predefinido para **Encriptação**.
1. Clique em **Criar** para criar a variável.

## <a name="add-authentication"></a>Adicionar autenticação

Agora que tem uma variável para conter o ID de subscrição, pode configurar o runbook para se autenticar com as credenciais Run As que são mencionadas nos [pré-requisitos](#prerequisites). Você faz isso adicionando o **ativo** de conexão executar como do Azure e o cmdlet **Connect-AzureRmAccount** à tela.

1. Navegue de volta para o runbook e selecione **Editar** na página MyFirstRunbook-gráfico.
1. Você não precisa mais do **Olá, mundo de gravação para** obter mais resultados, portanto, clique nas reticências (...) e selecione **excluir**.
1. No controlo da Biblioteca, expanda **ATIVOS**, **Ligações** e adicione **AzureRunAsConnection** à tela ao selecionar **Adicionar à tela**.
1. No controle biblioteca, digite **Connect-AzureRmAccount** na caixa de texto de pesquisa.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá atualizar seus módulos na sua conta de automação.

1. Adicione **Connect-AzureRmAccount** à tela.
1. Coloque o cursor sobre **Obter Ligação Run As** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Connect-AzureRmAccount**. A seta que criou é uma *ligação*. O runbook começa com **Get execute as Connection** e, em seguida, executa **Connect-AzureRmAccount**.<br> ![Criar ligação entre atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, selecione **Connect-AzureRmAccount** e, no painel de controle de configuração, digite **logon no Azure** na caixa de texto **rótulo** .
1. Clique em **parâmetros** e a página configuração de parâmetro de atividade será exibida.
1. O **Connect-AzureRmAccount** tem vários conjuntos de parâmetros, portanto, você precisa selecionar um antes de poder fornecer valores de parâmetro. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros são exibidos na página configuração do parâmetro de atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros de conta do Azure RM](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página valor do parâmetro, selecione **saída da atividade** para **a fonte de dados** e selecione **obter conexão executar como** na lista, na caixa de texto **caminho do campo** , digite **ApplicationId**e clique em **OK**. Está a especificar o nome da propriedade para o Caminho do campo porque a atividade produz um objeto com várias propriedades.
1. Clique em **CERTIFICATETHUMBPRINT**e, na página valor do parâmetro, selecione **saída da atividade** para a fonte de **dados**. Selecione **Obter Executar como Ligação** a partir da lista, no **Caminho do campo** tipo caixa de texto **CertificateThumbrprint** e, em seguida, clique em **OK**.
1. Clique em **servicePrincipalName**e, na página valor do parâmetro, selecione **constante** para a **fonte de dados**, clique na opção **verdadeiro**e, em seguida, clique em **OK**.
1. Clique em **tenantid**e, na página valor do parâmetro, selecione **saída da atividade** para a fonte de **dados**. Selecione **Obter Ligação Run As** na lista, na caixa de texto **Caminho do campo** escreva **TenantId** e, em seguida, clique duas vezes em **OK**.
1. No controlo da Biblioteca, escreva **Set-AzureRmContext** na caixa de texto de pesquisa.
1. Adicione **Set-AzureRmContext** à tela.
1. Na tela, selecione **Set-AzureRmContext** e, no painel de controlo de Configuração, escreva **Especificar Id de Subscrição** na caixa de texto **Etiqueta**.
1. Clique em **parâmetros** e a página configuração de parâmetro de atividade será exibida.
1. **Set-AzureRmContext** tem vários conjuntos de parâmetros, pelo que deve selecionar um antes de poder apresentar valores de parâmetros. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **SubscriptionId**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros são exibidos na página configuração do parâmetro de atividade. Clique em **SubscriptionID**
1. Na página valor do parâmetro, selecione **ativo variável** para a **fonte de dados** e selecione **AzureSubscriptionId** na lista e clique em **OK** duas vezes.
1. Coloque o cursor sobre **Iniciar sessão no Azure** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Subscrição**.

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Adicionar atividade para iniciar uma VM

Aqui, adiciona uma atividade **Start-AzureRmVM** para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição do Azure e, por agora, iremos codificar esse nome para o cmdlet.

1. No controlo da Biblioteca, escreva **Start-AzureRm** na caixa de texto de pesquisa.
2. Adicione **Start-AzureRmVM** à tela e, em seguida, clique e arraste-a para baixo de **Especificar Id de Subscrição**.
1. Coloque o cursor sobre **Especificar Id de Subscrição** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzureRmVM**.
1. Selecione **Start-AzureRmVM**. Clique em **Parâmetros** e, em seguida, em **Conjunto de Parâmetros** para ver os conjuntos de **Start-AzureRmVM**. Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName**. Existem pontos de exclamação junto a **ResourceGroupName** e **Name**. Isto indica que são parâmetros obrigatórios. Tenha também em atenção que ambos esperam valores de cadeia.
1. Selecione **Name**. Selecione **Expressão do PowerShell** para a **Origem de dados** e escreva o nome da máquina virtual entre aspas duplas com a qual começou este runbook. Clique em **OK**.
1. Selecione **ResourceGroupName**. Utilize **Expressão do PowerShell** para a **Origem de dados** e escreva o nome do grupo de recursos entre aspas duplas. Clique em **OK**.
1. Clique em Painel de teste para que possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="add-additional-input-parameters"></a>Adicionar parâmetros de entrada adicionais

Atualmente, o nosso runbook inicia a máquina virtual no grupo de recursos que especificou no cmdlet **Start-AzureRmVM**. O runbook poderia ser mais útil se pudéssemos especificar os dois quando o runbook é iniciado. Agora, irá adicionar parâmetros de entrada ao runbook para oferecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook-Graphical** .
1. Selecione **entrada e saída** e, em seguida, **Adicionar entrada** para abrir o painel parâmetro de entrada do runbook.
1. Especifique *VMName* para o **Nome**. Mantenha *cadeia* para o **Tipo**, mas pode alterar **Obrigatório** para *Sim*. Clique em **OK**.
1. Crie um segundo parâmetro de entrada obrigatório denominado *ResourceGroupName* e, em seguida, clique em **OK** para fechar o painel **Entrada e saída**.<br> ![Parâmetros de Entrada do Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecione a atividade **Start-AzureRmVM** e, em seguida, clique em **Parâmetros**.
1. Altere a **Origem de dados** de **Nome** para **Entrada do runbook** e, em seguida, selecione **VMName**.
1. Altere a **Origem de dados** de **ResourceGroupName** para **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**.<br> ![Parâmetros de Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.
1. Feche o painel de Teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que iniciou no passo anterior.
1. Clique em **Iniciar** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.
1. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## <a name="create-a-conditional-link"></a>Criar um link condicional

Agora modifique o runbook para que apenas tente iniciar a máquina virtual se esta ainda não tiver iniciado. Para tal, irá adicionar um cmdlet **Get-AzureRmVM** ao runbook, que obtém o estado de nível de instância da máquina virtual. Irá então adicionar um módulo de código de Fluxo de Trabalho do PowerShell chamado **Obter Estado** com um fragmento de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado. Uma ligação condicional do módulo **Obter Estado** apenas executa **Start-AzureRmVM** se o estado de execução atual estiver parado. Por último, gere uma mensagem a informar se a VM foi iniciada com êxito ou não, com o cmdlet Write-Output do PowerShell.

1. Abra **MyFirstRunbook-Graphical** no editor gráfico.
1. Remova a ligação entre **Especificar Id de Subscrição** e **Start-AzureRmVM** ao clicar na mesma e, em seguida, premir a tecla *Eliminar*.
1. No controlo da Biblioteca, escreva **Get-AzureRm** na caixa de texto de pesquisa.
1. Adicione **Get-AzureRmVM** à tela.
1. Selecione **Get-AzureRmVM** e, em seguida, **Conjunto de Parâmetros** para ver os conjuntos de **Get-AzureRmVM**. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**. Existem pontos de exclamação junto a **ResourceGroupName** e **Name**. Isto indica que são parâmetros obrigatórios. Tenha também em atenção que ambos esperam valores de cadeia.
1. Em **Origem de dados** para **Nome**, selecione **Entrada do runbook** e, em seguida, selecione **VMName**. Clique em **OK**.
1. Em **Origem de dados** para **ResourceGroupName**, selecione **Entrada do runbook** e, em seguida, selecione **ResourceGroupName**. Clique em **OK**.
1. Em **Origem de dados** para **Estado**, selecione **Valor constante** e, em seguida, clique em **Verdadeiro**. Clique em **OK**.
1. Crie uma ligação de **Especificar Id de Subscrição** para **Get-AzureRmVM**.
1. No controlo da biblioteca, expanda **Controlo do Runbook** e adicione **Código** à tela.  
1. Crie uma ligação de **Get-AzureRmVM** para **Código**.  
1. Clique em **Código** e, no painel de Configuração, altere a etiqueta para **Obter Estado**.
1. Selecione o parâmetro de **código** e a página **Editor de código** será exibida.  
1. No editor de código, cole o fragmento de código seguinte:

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Crie uma ligação de **Obter Estado** para **Start-AzureRmVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecione a ligação e, no painel de Configuração, altere **Aplicar condição** para **Sim**. Tenha em atenção que a ligação muda para uma linha tracejada que indica que a atividade de destino apenas é executada se a condição for resolvida como verdadeira.  
1. Para **Expressão de condição**, escreva *$ActivityOutput['Get Status'] -eq "Stopped"* . Agora, **Start-AzureRmVM** só é executado se a máquina virtual estiver parada.
1. No controlo da Biblioteca, expanda **Cmdlets** e, em seguida, **Microsoft.PowerShell.Utility**.
1. Adicione **Write-Output** à tela duas vezes.
1. No primeiro controlo **Write-Output**, clique em **Parâmetros** e altere o valor de **Etiqueta** para *Notify VM Started*.
1. Para **InputObject**, altere **Origem de dados** para **Expressão do PowerShell** e escreva a expressão *"$VMName successfully started."* .
1. No primeiro controlo **Write-Output**, clique em **Parâmetros** e altere o valor de **Etiqueta** para *Notify VM Started*
1. Para **InputObject**, altere **Origem de dados** para **Expressão do PowerShell** e escreva a expressão *"$VMName could not start."* .
1. Crie uma ligação de **Start-AzureRmVM** para **Notify VM Started** e **Notify VM Start Failed**.
1. Selecione a ligação para **Notify VM Started** e altere **Aplicar condição** para **True**.
1. Para a **Expressão de condição**, escreva *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*. Agora, este controlo Write-Output apenas é executado se a máquina virtual for iniciada com êxito.
1. Selecione a ligação para **Notify VM Start Failed** e altere **Aplicar condição** para **True**.
1. Para a **Expressão de condição**, escreva *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Agora, este controlo Write-Output apenas é executado se a máquina virtual não for iniciada com êxito. Seu runbook deve ser semelhante à imagem a seguir: <br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Guarde o runbook e abra o painel de Teste.
1. Inicie o runbook com a máquina virtual parada e este deve iniciar.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre a Criação de Gráficos, consulte [Criação de gráficos na Automatização do Azure](automation-graphical-authoring-intro.md)
* Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)


