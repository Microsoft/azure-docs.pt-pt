---
title: O meu primeiro livro gráfico na Automação Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook gráfico simples.
keywords: runbook, modelo do runbook, automatização do runbook, runbook do azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6a967f328a4fbe17f2c451d35f413bd7fdcbc24a
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331473"
---
# <a name="my-first-graphical-runbook"></a>O meu primeiro runbook gráfico

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [python](automation-first-runbook-textual-python2.md)
> 

Este tutorial explica como criar um [ runbook gráfico](automation-runbook-types.md#graphical-runbooks) na Automatização do Azure. Comece com um simples livro de corridas que pode testar e publicar, enquanto aprende a rastrear o estado do trabalho do livro de corridas. Em seguida, modifique o livro de execução para realmente gerir os recursos do Azure, neste caso iniciando uma máquina virtual Azure. Complete o tutorial para tornar o livro de corridas mais robusto adicionando parâmetros de livro e ligações condicionais.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que paraste e começas esta máquina, não devia ser um VM de produção.

## <a name="step-1---create-runbook"></a>Passo 1 – Criar runbook

Comece por criar um runbook simples que produz o texto **Hello World**.

1. No portal do Azure, abra a sua conta da Automatização. 

    A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria desses ativos são os módulos automaticamente incluídos numa nova conta Automation. Também deve ter o ativo credencial associado à sua subscrição.
2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.
3. Crie um novo livro de corridas selecionando **Criar um livro de execução**.
4. Atribua ao runbook o nome **MyFirstRunbook-Graphical**.
5. Neste caso, vai criar um [livro gráfico.](automation-graphical-authoring-intro.md) **Selecione Gráfico** para **o tipo Derbook**.<br> ![Novo runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Clique em **Criar** para criar o runbook e abrir o editor gráfico.

## <a name="step-2---add-activities"></a>Passo 2 - Adicionar atividades

O controlo da Biblioteca no lado esquerdo do editor permite-lhe selecionar as atividades a adicionar ao runbook. Irá adicionar um cmdlet **Write-Output** para apresentar texto a partir do runbook.

1. No controlo da Biblioteca, clique no campo de pesquisa e digite a **saída de escrita**. Os resultados da pesquisa são mostrados na imagem seguinte. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Desloque-se para baixo até à parte inferior da lista. Clique no direito **Write-Output** e **selecione Adicionar a tela**. Em alternativa, pode clicar na elipse (...) ao lado do nome cmdlet e, em seguida, selecionar **Adicionar a tela**.
1. Clique na atividade **Write-Output** na tela. Esta ação abre a página de controlo de Configuração, que lhe permite configurar a atividade.
1. O campo **Label** predefinido para o nome do cmdlet, mas pode mudá-lo para algo mais amigável. Altere-o para **Escrever Olá, Mundo para a saída**.
1. Clique em **Parâmetros** para fornecer valores para os parâmetros do cmdlet.

   Alguns cmdlets têm vários conjuntos de parâmetros, e você precisa selecionar qual usar. Neste caso, a **Write-Output** tem apenas um parâmetro definido.

1. Selecione o parâmetro *InputObject*. Este é o parâmetro que utiliza para especificar o texto para enviar para o fluxo de saída.
1. O menu de dropdown **de fonte de dados** fornece fontes que pode usar para preencher um valor de parâmetro. Neste menu, selecione **powerShell expressão**. 

   Pode utilizar a saída de fontes como outra atividade, um ativo Automation ou uma expressão PowerShell. Neste caso, o resultado é apenas **Hello World**. Pode utilizar uma expressão do PowerShell e especificar uma cadeia.<br>

1. No campo **Expression,** **escreva Hello World** e clique em **OK** duas vezes para voltar à tela.
1. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o livro de execução para o disponibilizar em produção, deve testá-lo para se certificar de que funciona corretamente. Testar um livro de execução executa a sua versão Draft e permite-lhe visualizar a sua saída interativamente.

1. Selecione painel de **teste** para abrir o painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
1. Note que é criado um trabalho de livro de [rés](automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estado de trabalho começa como **Fila,** indicando que o trabalho está à espera que um trabalhador do livro na nuvem fique disponível. O estado muda para **Começar** quando um trabalhador reclama o trabalho. Finalmente, o estado torna-se **Running** quando o livro de corridas realmente começa a funcionar.

1. Quando o trabalho do livro de execução estiver concluído, o painel de teste mostra a sua saída. Neste caso, verá **Hello World**.<br> ![Olá Mundo](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está em modo Draft. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. **Selecione Publicar** para publicar o livro de execução e, em seguida, **sim** quando solicitado.
1. Percorra à esquerda para ver o livro de execução na página Runbooks e note que o valor **do Estado de Autor** está definido para **Publicado**.
1. Percorra para a direita para ver a página para **MyFirstRunbook-Graphical**.

   As opções em toda a parte superior permitem-lhe iniciar o livro de corridas agora, agendar um futuro início ou criar um [webhook](automation-webhooks.md) para que o livro de execução possa ser iniciado através de uma chamada HTTP.

1. Selecione **Iniciar** e, **em seguida, Sim** quando solicitado para iniciar o livro de execução.
1. Um painel de trabalho é aberto para o trabalho de livro que foi criado. Verifique se o campo de estado do **trabalho** mostra **concluído**.
1. Clique em **Saída** para abrir a página Output, onde pode ver **o Hello World** exibido.
1. Feche a página de saída.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deves ver o **Hello World** na corrente de saída. 

    Note que o painel streams pode mostrar outros fluxos para um trabalho de livro de corridas, como verbose e fluxos de erro, se o livro de execução escrever para eles.
1. Feche o painel de Streams e o painel de trabalho para voltar à página **MyFirstRunbook-Graphical.**
1. Para ver todos os trabalhos para o livro de corridas, selecione **Jobs** under **Resources**. A página Jobs lista todos os empregos criados pelo seu livro de corridas. Só devias ver um emprego na lista, já que só geriste o trabalho uma vez.
1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o livro de corridas. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de corridas.

## <a name="step-5---create-variable-assets"></a>Passo 5 – criar recursos de variável

Testou e publicou o seu livro de corridas, mas até agora não faz nada de útil para gerir os recursos do Azure. Antes de configurar o livro de execução para autenticar, deve criar uma variável para manter o ID de subscrição, configurar uma atividade para autenticar e, em seguida, fazer referência à variável. Incluir uma referência ao contexto de subscrição permite-lhe trabalhar facilmente com várias subscrições.

1. Copie o seu ID de subscrição a partir da opção **Subscrições** no painel Navegante.
1. Na página Contas de Automação, selecione **Variáveis** em **Recursos Partilhados.**
1. **Selecione Adicionar uma variável**.
1. Na nova página variável, faça as seguintes definições nos campos fornecidos.

    * **Nome** -- insira **AzureSubscriptionId**.
    * **Valor** - introduza o seu ID de subscrição. 
    * **Tipo** - mantenha a corda selecionada.
    * **Encriptação-** Use o valor padrão.
1. Clique em **Criar** para criar a variável.

## <a name="step-6---add-authentication"></a>Passo 6 - Adicionar autenticação

Agora que tem uma variável para segurar o ID de subscrição, pode configurar o livro de execução para autenticar com as credenciais Run As para a sua subscrição. Faça isto adicionando o Azure Run Como ligação como um ativo. Também deve adicionar o cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) e o [cmdlet Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) à tela.

>[!NOTE]
>Para os livros de execução PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são pseudónimos de **Connect-AzAccount**. Note que estes pseudónimos não estão disponíveis para os seus livros gráficos. Um livro de execução gráfico só pode usar o **próprio Connect-AzAccount.**

1. Navegue para o seu livro de execução e selecione **Editar** na página **MyFirstRunbook-Graphical.**
1. Já não precisas do Write Hello World para entrar na **produção.** Basta clicar na elipse e selecionar **Apagar**.
1. No controlo da Biblioteca, expandir **ATIVOS,** depois **Ligações**. Adicione **AzureRunAsConnection** à tela selecionando **Adicionar à tela**.
1. No controlo da Biblioteca, digite **Connect-AzAccount** no campo de pesquisa.
1. Adicione **Connect-AzAccount** à tela.
1. Coloque o cursor sobre **Obter Ligação Run As** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Connect-AzAccount** para formar um link. O livro de execução começa com **Get Run As Connection** e, em seguida, executa **Connect-AzAccount**.<br> ![Criar ligação entre atividades](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Na tela, selecione **Connect-AzAccount**. No painel de controlo de configuração, digite **login a Azure** no campo **Label.**
1. Clique em **Parâmetros**, e aparece a página de Configuração do Parâmetro de Atividade.
1. O cmdlet **Connect-AzAccount** tem vários parâmetros e precisa selecionar um antes de fornecer valores de parâmetros. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **ServicePrincipalCertificate**.
1. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique em **APPLICATIONID**.<br> ![Adicionar parâmetros da conta Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique em **OK**.

   * **Fonte de dados** - selecione **Saída de Atividade**.
   * Lista de fonte de dados - **selecione Get Run As Connection**.
   * **Percurso de campo** - tipo **ApplicationId**. Está a especificar o nome da propriedade para o caminho de campo porque a atividade produz um objeto com múltiplas propriedades.
1. Clique em **CERTIFICATETHUMBPRINT**, e na página Valor do Parâmetro, faça as seguintes definições e, em seguida, clique **OK**.

    * **Fonte de dados** - selecione **Saída de Atividade**.
    * Lista de fonte de dados - **selecione Get Run As Connection**.
    * **Caminho de campo** - tipo **CertificadoImpressão DePolegar**.
1. Clique em **SERVICEPRINCIPAL**, e na página Valor do Parâmetro, **selecione ConstantValue** para o campo **fonte de dados;** clique na opção **True;** e, em seguida, clicar **OK**.
1. Clique em **TENANTID**e faça as seguintes definições na página Valor do Parâmetro. Quando terminar, clique **ok** duas vezes.

    * **Fonte de dados** - selecione **Saída de Atividade**. 
    * Lista de fonte de dados - **selecione Get Run As Connection**.
    * **Caminho de campo** - tipo **TenantId**. 
1. No controlo da Biblioteca, digite **Set-AzContext** no campo de pesquisa.
1. Adicione **set-AzContext** à tela.
1. Selecione **Set-AzContext** na tela. No painel de controlo de configuração, introduza o **Id de Subscrição Especificado** no campo **label.**
1. Clique em **Parâmetros** e aparece a página de Configuração do Parâmetro de Atividade.
1. O **cmdlet Set-AzContext** tem vários conjuntos de parâmetros, e você precisa selecionar um antes de fornecer valores de parâmetro. Clique em **Conjunto de Parâmetros** e, em seguida, selecione o conjunto de parâmetros **SubscriptionId**.
1. Os parâmetros para este conjunto de parâmetros são apresentados na página de Configuração do Parâmetro de Atividade. Clique em **SubscriptionID**.
1. Na página Valor do Parâmetro, selecione **Variable Asset** para o campo fonte **de dados** e selecione **AzureSubscriptionId** da lista de origem. Quando terminar, clique **ok** duas vezes.
1. Coloque o cursor sobre **Iniciar sessão no Azure** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Especificar Id de Subscrição**. 

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Passo 7 – adicionar atividade para iniciar uma máquina virtual

Agora tem de adicionar uma atividade **start-AzVM** para iniciar uma máquina virtual. Pode escolher qualquer VM na sua subscrição Azure e, por enquanto, está a codificar o seu nome para o cmdlet [Start-AzVM.](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0)

1. No controlo da Biblioteca, **digite Start-Az** no campo de pesquisa.
2. Adicione o **Start-AzVM** à tela e, em seguida, clique e arraste-o por baixo do Id de **subscrição Especificado**.
1. Coloque o cursor sobre **Especificar Id de Subscrição** até aparecer um círculo na parte inferior da forma. Clique no círculo e arraste a seta para **Start-AzVM**.
1. Selecione **Start-AzVM**. Clique em **Parâmetros** e, **em seguida, parameter set** para ver os conjuntos para a atividade.
1. Selecione o conjunto de parâmetros **ResourceGroupNameParameterSetName**. Os campos **ResourceGroupName** e **Nome** têm marcas de exclamação ao lado deles para indicar que são parâmetros necessários. Note que ambos os campos esperam valores de cordas.
1. Selecione **Name**. Escolha a **expressão PowerShell** para o campo **fonte de dados.** Para o VM que utiliza para iniciar este livro de execução, digite o nome da máquina rodeado de cotações duplas. Clique em **OK**.
1. Selecione **ResourceGroupName**. Utilize a expressão de valor **PowerShell** para o campo fonte de **dados** e escreva em nome do grupo de recursos rodeado de cotações duplas. Clique em **OK**.
1. Clique no **painel de teste** para que possa testar o livro de corridas.
1. Clique **em Começar** para iniciar o teste. Uma vez concluído, certifique-se de que o VM começou. 

O runbook deve ter o seguinte aspeto neste momento: <br>![Configuração de autenticação de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Passo 8 - Adicionar parâmetros adicionais de entrada

O seu livro de execução inicia atualmente o VM no grupo de recursos que especificou para o cmdlet **Start-AzVM.** O livro de execução será mais útil se especificar tanto o nome como o grupo de recursos quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao livro de execução para fornecer essa funcionalidade.

1. Abra o editor gráfico clicando em **Editar** no painel **MyFirstRunbook-Graphical.**
1. Selecione **entrada e saída** **e,** em seguida, adicione a entrada para abrir o painel de parâmetro de entrada de saída do livro de execução.
1. Faça as seguintes definições nos campos fornecidos **e,** em seguida, clique OK .
   * **Nome** - especificar **VMName**.
   * **Tipo** - mantenha a fixação das cordas.
   * **Obrigatório** - alterar o valor para **Sim**.
1. Crie um segundo parâmetro de entrada obrigatório chamado *ResourceGroupName* e, em seguida, clique em **OK** para fechar o painel de entrada e saída.<br> ![Parâmetros de Entrada do Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Selecione a atividade **Start-AzVM** e, em seguida, clique em **Parâmetros**.
1. Altere o campo de **origem de dados** para a entrada de **Nome** para Livro **de Execução**. Em seguida, selecione **VMName**.
1. Altere o campo de **origem de dados** para o **ResourceGroupName** para a entrada do Livro de **Execução** e, em seguida, selecione **ResourceGroupName**.<br> ![parâmetros Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.
1. Feche o painel de Teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare o VM que começou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. Digite os valores para **VMName** e **ResourceGroupName** para o VM que vai iniciar.
1. Quando o livro de execução estiver concluído, certifique-se de que o VM foi iniciado.

## <a name="step-9---create-a-conditional-link"></a>Passo 9 – criar uma ligação condicional

Agora pode modificar o livro de execução para que apenas tente iniciar o VM se ainda não estiver iniciado. Faça-o adicionando um cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) que recupera o estado de nível de instância do VM. Em seguida, pode adicionar um módulo de código powerShell workflow chamado **Get Status** com um excerto de código PowerShell para determinar se o estado VM está em execução ou parado. Uma ligação condicional do módulo **Get Status** só executa **start-AzVM** se o estado de funcionamento atual for interrompido. No final deste procedimento, o seu livro de execução utiliza o cmdlet **write-output** para enviar uma mensagem para informá-lo se o VM foi iniciado com sucesso.

1. Abra **myFirstRunbook-Graphical** no editor gráfico.
1. Remova o link entre **especificar o Id de subscrição** e o **Start-AzVM** clicando nele e, em seguida, premindo **Delete**.
1. No controlo da Biblioteca, escreva **Get-Az** no campo de pesquisa.
1. Adicione **Get-AzVM** à tela.
1. Selecione **Get-AzVM** e, em seguida, **parâmetro Definido** para visualizar os conjuntos para o cmdlet. 
1. Selecione o conjunto de parâmetros **GetVirtualMachineInResourceGroupNameParamSet**. Os campos **ResourceGroupName** e **Name** têm marcas de exclamação ao lado deles, indicando que especificam os parâmetros necessários. Note que ambos os campos esperam valores de cordas.
1. Em fonte **de dados** para **Nome**, selecione a entrada do Livro de **Execução**e, **em seguida, VMName**. Clique em **OK**.
1. Sob **a fonte de dados** do **ResourceGroupName**, selecione a entrada do Livro de **Execução,** em **seguida, ResourceGroupName**. Clique em **OK**.
1. Sob **a fonte de Dados** para o **Estado,** selecione **valor constante,** em **seguida, Verdadeiro**. Clique em **OK**.
1. Crie um link do Id de **Subscrição Especificado** para **Get-AzVM**.
1. No controlo da Biblioteca, expanda o Controlo do Livro de **Execução** e adicione **Código** à tela.  
1. Criar um link de **Get-AzVM** a **Code**.  
1. Clique em **Código** e, no painel de configuração, mude a etiqueta para **obter o estado**.
1. **Aparecerá** código e a página Do Editor de Código.  
1. Colhe o seguinte código na página do editor.

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

1. Crie um link de **Get Status** para **Start-AzVM**.<br> ![Runbook com Módulo de Código](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Selecione o link e, no painel de configuração, mude a **condição de aplicar** a **Sim**. Note que a ligação se torna uma linha tracejada, indicando que a atividade alvo só funciona se a condição se resolver verdadeiramente.  
1. Para **expressão condição,** escreva `$ActivityOutput['Get Status'] -eq "Stopped"`. **O Start-AzVM** só funciona agora se o VM for parado.
1. No controlo da Biblioteca, expanda **Cmdlets** e, em seguida, **Microsoft.PowerShell.Utility**.
1. Adicione **Write-Output** à tela duas vezes.
1. Para o primeiro controlo **de saída de escrita,** clique em **Parâmetros** e altere o valor do **rótulo** para notificar o **VM iniciado**.
1. Para **inputObject,** altere a **fonte de dados** para a expressão **PowerShell**, e digite a expressão **$VMName iniciado com sucesso.**
1. No segundo controlo **de saída de escrita,** clique em **Parâmetros** e altere o valor do **rótulo** para notificar o arranque **do VM falhado**.
1. Para **inputObject,** altere a **fonte de dados** para a expressão **PowerShell**, e digite a expressão **$VMName não poderia iniciar.**
1. Criar links desde **start-AzVM** para notificar o **VM iniciado** e notificar o **arranque do VM falhado**.
1. Selecione o link para **Notificar vM iniciado** e alterar **condição de aplicação** para verdade.
1. Para a **expressão Condição,** escreva `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Este controlo **de saída de escrita** funciona agora apenas se o VM começar com sucesso.
1. Selecione o link para **notificar o VM Iniciar falhado** e alterar **condição de aplicação** para a verdade.
1. Para o campo de **expressão condição,** digite `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Este controlo **de saída de escrita** funciona agora apenas se o VM não for iniciado com sucesso. O seu livro de corridas deve parecer a seguinte imagem: <br> ![Runbook com Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Guarde o runbook e abra o painel de Teste.
1. Inicie o livro de corridas com o VM parado e a máquina deve arrancar.

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre autoria gráfica, consulte [a autoria gráfica em Automação Azure.](automation-graphical-authoring-intro.md)
* Para começar com runbooks do PowerShell, consulte [My first PowerShell runbook (O meu primeiro runbook do PowerShell)](automation-first-runbook-textual-powershell.md).
* Para começar com os livros de execução powerShell Workflow, consulte o meu primeiro livro de execução de fluxo de [trabalho PowerShell](automation-first-runbook-textual.md).