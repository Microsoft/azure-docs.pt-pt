---
title: Meu primeiro runbook do PowerShell na automação do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook do PowerShell simples.
keywords: Azure powershell, tutorial de script do powershell, automatização de powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f950a2fed2fbd355fc99453f09b655463e67102d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850878"
---
# <a name="my-first-powershell-runbook"></a>O meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [ runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Você começa com um runbook simples que você testa e publica enquanto aprende a acompanhar o status do trabalho de runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por fim, você torna o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-quickstart-create-account.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você pára e inicia esse computador para que ele não seja uma VM de produção.
* Talvez seja necessário [atualizar seus módulos do Azure](automation-update-azure-modules.md) com base nos cmdlets que você usa.

## <a name="create-new-runbook"></a>Criar novo runbook

Você começa criando um runbook simples que gera o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.
2. Clique em **Runbooks** em **automação de processo** para abrir a lista de runbooks.
3. Crie um novo runbook clicando no botão **+ Adicionar um runbook** e, em seguida, **criar um novo runbook**.
4. Atribua ao runbook o nome *MyFirstRunbook PowerShell*.
5. Nesse caso, você vai criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) , portanto, selecione **PowerShell** para **tipo de runbook**.
6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para esta explicação, você digita diretamente no runbook.

1. Seu runbook está vazio no momento, digite *Write-Output "Olá, mundo".* no corpo do script.

   ![Olá, Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a> Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
3. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.

   O status do trabalho começa como *na fila* , indicando que está aguardando que um runbook Worker na nuvem fique disponível. Ele é movido para *iniciando* quando um trabalho alega o trabalho e, em seguida, é *executado* quando o runbook realmente começa a ser executado.

4. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No seu caso, você deve ver *Olá, mundo*.

   ![Resultado do Painel de Teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Ele deve ser publicado antes que você possa executá-lo na produção.  Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, você ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **status de criação** **publicado**.
1. Desloque-se para trás para a direita para ver o painel de **MyFirstRunbook-PowerShell**.
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
1. Você deseja iniciar o runbook, portanto, clique em **Iniciar** e, em seguida, clique em **OK** quando a página Iniciar runbook for aberta.
1. Uma página de trabalho é aberta para o trabalho de runbook que você criou. Você pode fechar esse painel, mas, nesse caso, deixá-lo aberto para que você possa observar o progresso do trabalho.
1. O status do trabalho é mostrado no **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.

   ![Resumo da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Quando o status do runbook mostrar *concluído*, em **visão geral** , clique em **saída**. O painel saída é aberto e você pode ver sua *Olá, mundo*.

   ![Resultado da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página saída.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Você só deve ver *Olá, mundo* no fluxo de saída, mas essa saída pode mostrar outros fluxos para um trabalho de runbook, como detalhado e erro se o runbook gravar neles.

   ![Todos os Registos](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche a página fluxos e a página trabalho para retornar à página MyFirstRunbook-PowerShell.
1. Em **detalhes**, clique em **trabalhos** para abrir o painel trabalhos para este runbook. Esta página lista todos os trabalhos criados por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.

   ![Lista de Tarefas](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Essa ação permite que você volte no tempo e exiba os detalhes de qualquer trabalho que foi criado para um runbook específico.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerenciar recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Não é possível fazer isso, a menos que você o autentique usando uma conexão executar como que é criada automaticamente quando você cria sua conta de automação. Use a conexão executar como com o cmdlet **Connect-AzureRmAccount** . Se você estiver gerenciando recursos em várias assinaturas, precisará usar o parâmetro **-AzureRmContext** junto com [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Abra o editor de texto clicando em **Editar** na página MyFirstRunbook-PowerShell.
1. Você não precisa mais da linha **Write-Output** , então vá em frente e exclua-a.
1. Escreva ou copie e cole o seguinte código que processa a autenticação com a conta da Execução da Automatização como:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **login-AzureRmAccount** agora são aliases para **Connect-AzureRmAccount**. Se o cmdlet **Connect-AzureRMAccount** não existir, você poderá usar **Add-AzureRMAccount** ou **login-AzureRMAccount**ou poderá atualizar seus módulos em sua conta de automação para as versões mais recentes.

1. Clique em **painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Essa saída confirma que a conta Executar como é válida.

   ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Adicionar código para iniciar uma máquina virtual

Agora que seu runbook está Autenticando para sua assinatura do Azure, você pode gerenciar recursos. Você adiciona um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e, por enquanto, você codifica esse nome no runbook.

1. Após *Connect-AzureRmAccount*, digite *Start-AzureRmVM-Name ' VMName '-ResourceGroupName ' NameofResourceGroup* ', fornecendo o nome e o nome do grupo de recursos da máquina virtual para iniciar.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Salve o runbook e, em seguida, clique em **painel de teste** para que você possa testá-lo.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="add-an-input-parameter"></a>Adicionar um parâmetro de entrada

O runbook inicia atualmente a máquina virtual que você codificou no runbook, mas seria mais útil se você especificar a máquina virtual quando o runbook for iniciado. Você adiciona parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e *ResourceGroupName* ao runbook e use essas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo a seguir.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Guarde o runbook e abra o painel de Teste. Agora pode fornecer valores para as duas variáveis de entrada que são utilizadas no teste.
1. Feche o painel de Teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que iniciou no passo anterior.
1. Clique em **OK** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.<br><br> ![Transmitir Parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## <a name="differences-from-powershell-workflow"></a>Diferenças do Fluxo de Trabalho do PowerShell

Os runbooks do PowerShell têm o mesmo ciclo de vida, capacidades e gestão que os runbooks do Fluxo de trabalho do PowerShell, mas existem algumas diferenças e limitações:

1. Os runbooks do PowerShell são executados rapidamente em comparação com os runbooks do Fluxo de Trabalho do PowerShell, uma vez que não têm o passo de compilação.
2. Os runbooks de fluxo de trabalho do PowerShell dão suporte a pontos de verificação, usando pontos de verificação, runbooks de fluxo de trabalho do PowerShell podem continuar de qualquer ponto no runbook Os runbooks do PowerShell só podem ser retomados do início.
3. Os runbooks de fluxo de trabalho do PowerShell dão suporte à execução paralela e serial. Os runbooks do PowerShell só podem executar comandos em série.
4. Em um runbook de fluxo de trabalho do PowerShell, uma atividade, um comando ou um bloco de script pode ter seu próprio runspace. Em um runbook do PowerShell, tudo em um script é executado em um único runspace. Também existem algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook de PowerShell nativo e um runbook de Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](/powershell/scripting/overview).
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
