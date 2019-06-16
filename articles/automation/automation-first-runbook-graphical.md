---
title: Meu primeiro runbook gráfico na automatização do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook gráfico simples.
keywords: runbook, modelo do runbook, automatização do runbook, runbook do azure
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/13/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be811d0dc2ce2eca0b20ca12165eaf0799bd6b5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61078105"
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
3. Criar um novo runbook, selecionando **+ adicionar um runbook**, em seguida, clique em **criar um runbook novo**.
4. Atribua ao runbook o nome *MyFirstRunbook-Graphical*.
5. Neste caso, irá criar um [runbook gráfico](automation-graphical-authoring-intro.md), pelo que deve selecionar **Gráfico** para o **Tipo de runbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## <a name="add-activities"></a>Adicionar atividades

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook. Irá adicionar um cmdlet **Write-Output** para apresentar texto a partir do runbook.

1. No controlo da Biblioteca, clique na caixa de texto de pesquisa e escreva **Write-Output**. Os resultados da pesquisa são apresentados na imagem seguinte: <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Desloque-se para baixo até à parte inferior da lista. Pode clicar com o botão direito do rato em **Write-Output** e selecionar **Adicionar à tela** ou clicar na elipse junto ao cmdlet e, em seguida, selecionar **Adicionar à tela**.
1. Clique na atividade **Write-Output** na tela. Esta ação abre a página de controlo de configuração, o que permite-lhe configurar a atividade.
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

1. Selecione **painel de teste** para abrir a página de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado no painel.

   O estado da tarefa começa como *Em fila* com a indicação de que está à espera que uma função de trabalho de runbook na cloud fique disponível. Irá então mudar para *A iniciar* quando uma função de trabalho reivindicar a tarefa e, em seguida, *A executar* quando o runbook começar a ser executado.

1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. Neste caso, verá *Hello World*.<br> ![Olá Mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche a página de teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Selecione **Publish** para publicar o runbook e, em seguida **Sim** quando lhe for pedido.
1. Se se deslocar para a esquerda para ver o runbook no **Runbooks** página, mostra um **estado de criação** de **publicada**.
1. Desloque-se para a direita para ver a página para **MyFirstRunbook-Graphical**.

   As opções na parte superior permitem-nos iniciar o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.

1. Selecione **começar** e, em seguida **Sim** quando lhe for pedido para iniciar o runbook.
1. Uma página de tarefa é aberta para a tarefa de runbook que foi criada. Certifique-se de que o **Estado da tarefa** mostra **Concluído**.
1. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. O **saída** é aberta a página, e pode ver a *Hello World* no painel.
1. Feche a página de saída.
1. Clique em **todos os registos** para abrir a página de fluxos para a tarefa de runbook. Apenas deve conseguir ver *Hello World* no fluxo de saída, mas isto pode mostrar outros fluxos de uma tarefa de runbook, tais como Verboso e Erro se o runbook escrever nos mesmos.
1. Feche a página de todos os registos e a página da tarefa para voltar à página de MyFirstRunbook-Graphical.
1. Para ver todas as tarefas do runbook fechar os **tarefa** página e selecione **trabalhos** sob **recursos**. Isto apresenta uma lista de todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.
1. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Isto permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="create-variable-assets"></a>Criar recursos de variável

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Antes de configurar o runbook para realizar a autenticação, crie uma variável para conter o ID de subscrição e referenciá-lo depois de configurar a atividade para realizar a autenticação no passo 6 abaixo. A inclusão de uma referência ao contexto de subscrição permite-lhe trabalhar facilmente entre várias subscrições. Antes de continuar, copie o seu ID de subscrição da opção Subscrições fora do Painel de navegação.

1. Na página contas de automatização, selecione **variáveis** sob **recursos PARTILHADOS**.
1. Selecione **adicionar uma variável**.
1. Na página de variável, na **nome** , introduza **AzureSubscriptionId** e, no **valor** caixa introduzir o seu ID de subscrição. Mantenha *cadeia* para o **Tipo** e o valor predefinido para **Encriptação**.
1. Clique em **Criar** para criar a variável.

## <a name="add-authentication"></a>Adicionar autenticação

Agora que tem uma variável para conter o ID de subscrição, pode configurar o runbook para se autenticar com as credenciais Run As que são mencionadas nos [pré-requisitos](#prerequisites). Fazê-lo ao adicionar a ligação Run As do Azure **Asset** e **Connect-AzureRmAccount** cmdlet para a tela.

1. Navegue de volta para o runbook e selecione **editar** na página de MyFirstRunbook-Graphical.
1. Não é necessário o **escrever Hello World no resultado** , por isso clique nas reticências (...) e selecione **eliminar**.
1. No controlo da Biblioteca, expanda **ATIVOS**, **Ligações** e adicione **AzureRunAsConnection** à tela ao selecionar **Adicionar à tela**.
1. No controlo da biblioteca, escreva **Connect-AzureRmAccount** na caixa de texto de pesquisa.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Quando pesquisar a sua biblioteca de itens, se não vir **Connect-AzureRMAccount**, pode utilizar **Add-AzureRmAccount**, ou pode atualizar os módulos na conta de automatização.

1. Adicione **Connect-AzureRmAccount** à tela.
1. Coloque o cursor sobre **Obter Ligação Run As** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Connect-AzureRmAccount**. A seta que criou é uma *ligação*. O runbook for iniciado com **obter ligação Run As** e, em seguida, execute **Connect-AzureRmAccount**.<br> ![Criar ligação entre atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, selecione **Connect-AzureRmAccount** e no tipo de painel de controlo de configuração **iniciar sessão no Azure** no **etiqueta** caixa de texto.
1. Clique em **parâmetros** e é apresentada a página de configuração de parâmetro de atividade.
1. **Connect-AzureRmAccount** tem vários conjuntos de parâmetros, pelo que deve selecionar um antes de poder apresentar valores de parâmetros. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros são apresentados na página de configuração de parâmetro de atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros de conta do Azure RM](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página do valor do parâmetro, selecione **saída da atividade** para o **origem de dados** e selecione **obter ligação Run As** na lista, no **caminho do campo** tipo de caixa de texto **ApplicationId**e, em seguida, clique em **OK**. Está a especificar o nome da propriedade para o Caminho do campo porque a atividade produz um objeto com várias propriedades.
1. Clique em **CERTIFICATETHUMBPRINT**e na página do valor do parâmetro, selecione **saída da atividade** para o **origem de dados**. Selecione **Obter Executar como Ligação** a partir da lista, no **Caminho do campo** tipo caixa de texto **CertificateThumbrprint** e, em seguida, clique em **OK**.
1. Clique em **SERVICEPRINCIPAL**e na página do valor do parâmetro, selecione **ConstantValue** para o **origem de dados**, clique na opção **verdadeiro**, e, em seguida, clique em **OK**.
1. Clique em **TENANTID**e na página do valor do parâmetro, selecione **saída da atividade** para o **origem de dados**. Selecione **Obter Ligação Run As** na lista, na caixa de texto **Caminho do campo** escreva **TenantId** e, em seguida, clique duas vezes em **OK**.
1. No controlo da Biblioteca, escreva **Set-AzureRmContext** na caixa de texto de pesquisa.
1. Adicione **Set-AzureRmContext** à tela.
1. Na tela, selecione **Set-AzureRmContext** e, no painel de controlo de Configuração, escreva **Especificar Id de Subscrição** na caixa de texto **Etiqueta**.
1. Clique em **parâmetros** e é apresentada a página de configuração de parâmetro de atividade.
1. **Set-AzureRmContext** tem vários conjuntos de parâmetros, pelo que deve selecionar um antes de poder apresentar valores de parâmetros. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **SubscriptionId**.
1. Depois de selecionar o conjunto de parâmetros, os parâmetros são apresentados na página de configuração de parâmetro de atividade. Clique em **SubscriptionID**
1. Na página do valor do parâmetro, selecione **recurso de variável** para o **origem de dados** e selecione **AzureSubscriptionId** na lista e clique em **OK** duas vezes.
1. Coloque o cursor sobre **Iniciar sessão no Azure** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Subscrição**.

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="add-activity-to-start-a-vm"></a>Adicionar Atividade para iniciar uma VM

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

1. Abra o editor gráfico clicando **edite** sobre o **MyFirstRunbook-Graphical** painel.
1. Selecione **de entrada e saída** e, em seguida **Adicionar entrada de** para abrir o painel de parâmetro de entrada do Runbook.
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

## <a name="create-a-conditional-link"></a>Criar uma ligação condicional

Agora modifique o runbook para que apenas tente iniciar a máquina virtual se esta ainda não tiver iniciado. Para tal, irá adicionar um cmdlet **Get-AzureRmVM** ao runbook, que obtém o estado de nível de instância da máquina virtual. Irá então adicionar um módulo de código de Fluxo de Trabalho do PowerShell chamado **Obter Estado** com um fragmento de código do PowerShell para determinar se o estado da máquina virtual está em execução ou parado. Uma ligação condicional do módulo **Obter Estado** apenas executa **Start-AzureRmVM** se o estado de execução atual estiver parado. Por último, gere uma mensagem a informar se a VM foi iniciada com êxito ou não, com o cmdlet Write-Output do PowerShell.

1. Open **MyFirstRunbook-Graphical** no editor gráfico.
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
1. Selecione **código** parâmetro e o **Editor de código** é apresentada a página.  
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
1. Para a **Expressão de condição**, escreva *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*. Agora, este controlo Write-Output apenas é executado se a máquina virtual não for iniciada com êxito. O runbook deve ter um aspeto semelhante à imagem seguinte: <br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Guarde o runbook e abra o painel de Teste.
1. Inicie o runbook com a máquina virtual parada e este deve iniciar.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre a Criação de Gráficos, consulte [Criação de gráficos na Automatização do Azure](automation-graphical-authoring-intro.md)
* Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)


