---
title: Meu primeiro runbook do fluxo de trabalho do PowerShell na automatização do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook de texto simples através do Fluxo de Trabalho do PowerShell.
keywords: fluxo de trabalho powerShell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho do powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2bc4d4034d63ed190f6964caa2bccf1ad8590a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57833820"
---
# <a name="my-first-powershell-workflow-runbook"></a>O meu primeiro runbook do Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [Runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automatização do Azure. Comece com um runbook simples que e publicamos enquanto Explicamos como controlar o estado do trabalho do runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, tornar o runbook mais robusto ao adicionar parâmetros do runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Parar e iniciar esta máquina, pelo que não deve ser uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook

Comece por criar um runbook simples que produz o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

1. Clique em **Runbooks** sob **automatização de processos** para abrir a lista de runbooks.
1. Criar um runbook novo ao clicar no **+ adicionar um runbook** botão e, em seguida **criar um runbook novo**.
1. Atribua ao runbook o nome *MyFirstRunbook-Workflow*.
1. Neste caso, vamos criar um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) por isso, selecione **fluxo de trabalho do Powershell** para **tipo de Runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Nestas instruções, escrever diretamente no runbook.

1. O runbook está atualmente vazio com apenas o necessário *fluxo de trabalho* palavra-chave, o nome de runbook e as chavetas que encases todo o fluxo de trabalho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Escreva *Write-Output "Olá, Mundo."* entre as chavetas.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Esta opção deve ser a única opção ativada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.

   O estado da tarefa começará como *em fila* que indica que está à espera de um runbook worker na cloud fique disponível. Move para *inicial* quando uma função de trabalho reivindicar a tarefa e, em seguida *em execução* quando o runbook começa a ser executado.  

1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No seu caso, verá *Olá, mundo*.

   ![Olá, Mundo](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Terá de o publicar antes de pode executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se se deslocar para a esquerda para ver o runbook no **Runbooks** painel, ele mostra agora uma **estado de criação** de **publicada**.
1. Desloque-se novamente para a direita para ver o painel para **MyFirstRunbook-Workflow**.  
   As opções na parte superior permitem-nos iniciar o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
1. pretende iniciar o runbook por isso, clique em **começar** e, em seguida **Sim** quando lhe for pedido.

   ![Iniciar o runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. É aberto um painel de tarefas para a tarefa de runbook que criou. Pode fechar este painel, mas neste caso, deixá-lo aberto para que possa ver o progresso da tarefa.
1. O estado da tarefa é mostrado na **resumo da tarefa** e corresponde aos Estados que vimos quando testado o runbook.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. É aberto o painel de resultados, e pode ver seus *Olá, mundo*.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  

1. Feche o painel Resultado.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. deverá ver apenas *Olá, mundo* na saída do fluxo, mas esta vista pode mostrar outros fluxos de uma tarefa de runbook, tais como verboso e erro se o runbook escreve nos mesmos.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Feche a página de fluxos e a página da tarefa para voltar à página de MyFirstRunbook.
1. Clique em **tarefas** para abrir a página de tarefas para este runbook. Esta página lista todas as tarefas criadas por este runbook. apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.

   ![Tarefas](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Pode clicar nesta tarefa para abrir a mesma página de tarefa que visualizou quando iniciou o runbook. Esta ação permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Ele não é possível fazer isso no entanto, a menos que autenticado com as credenciais que são mencionadas na [pré-requisitos](#prerequisites). faz isso com o **Connect-AzureRmAccount** cmdlet.

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-Workflow.
2. Não é necessário o **Write-Output** linha, então, vamos continuar e eliminá-lo.
3. Posicione o cursor sobre uma linha em branco entre as chavetas.
4. Escreva ou copie e cole o seguinte código que irá processar a autenticação com a conta da Execução da Automatização como:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** agora são aliases **Connect-AzureRMAccount**. Se o **Connect-AzureRMAccount** cmdlet não existir, pode usar **Add-AzureRmAccount** ou **Login-AzureRmAccount**, ou pode [atualizar seus módulos ](automation-update-azure-modules.md) na conta de automatização para as versões mais recentes.

> [!NOTE]
> Talvez seja preciso [atualizar seus módulos](automation-update-azure-modules.md) , mesmo que acabou de criar uma nova conta de automatização.

1. Clique em **painel de teste** para que pode testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Esta ação confirma que a credencial é válida.

   ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que o runbook está a autenticar a sua subscrição do Azure, pode gerir os recursos. Adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição do Azure e, por enquanto estiver codificar esse nome no runbook. Se estiver a gerir recursos entre várias subscrições, tem de utilizar o **- AzureRmContext** parâmetro juntamente com [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

1. Após *Connect-AzureRmAccount*, tipo *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* fornecendo o nome e o nome do grupo de recursos da máquina virtual para começar.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzureRmContext $AzureContext
   }
   ```

1. Guarde o runbook e, em seguida, clique em **painel de teste** para que pode testá-lo.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook

o runbook atualmente inicia a máquina virtual que codificado no runbook, mas seria mais útil se especificar a máquina virtual quando o runbook é iniciado. Adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros a *VMName* e *ResourceGroupName* ao runbook e utilize estas variáveis com o cmdlet **Start-AzureRmVM** como no exemplo abaixo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Guarde o runbook e abra o painel de Teste. Agora pode fornecer valores para as duas variáveis de entrada que estão no teste.
3. Feche o painel de Teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que iniciou no passo anterior.
6. Clique em **Iniciar** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.

   ![Iniciar o Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.  

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
