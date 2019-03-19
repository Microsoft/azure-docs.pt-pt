---
title: Meu primeiro runbook do PowerShell na automatização do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook do PowerShell simples.
keywords: Azure powershell, tutorial de script do powershell, automatização de powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b08e1489cf337360e838a3b5d5531fa2d4c0073b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57846961"
---
# <a name="my-first-powershell-runbook"></a>O meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [ runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Comece com um runbook simples que testar e publicar enquanto ficará a saber como controlar o estado do trabalho do runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por último, tornar o runbook mais robusto ao adicionar parâmetros do runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-quickstart-create-account.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Parar e iniciar esta máquina, pelo que não deve ser uma VM de produção.

## <a name="create-new-runbook"></a>Criar novo runbook

Comece por criar um runbook simples que produz o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.
2. Clique em **Runbooks** sob **automatização de processos** para abrir a lista de runbooks.
3. Criar um runbook novo ao clicar o **+ adicionar um runbook** botão e, em seguida **criar um runbook novo**.
4. Atribua ao runbook o nome *MyFirstRunbook PowerShell*.
5. Neste caso, vamos criar um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) por isso, selecione **Powershell** para **tipo de Runbook**.
6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="add-code-to-the-runbook"></a>Adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Nestas instruções, escrever diretamente no runbook.

1. O runbook está atualmente vazio, tipo *Write-Output "Hello World".* no corpo do script.

   ![Olá, Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  

2. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"> </a> Testar o runbook

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e vê o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
3. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.

   O estado da tarefa começa como *em fila* que indica que está à espera de um runbook worker na cloud fique disponível. Move para *inicial* quando uma função de trabalho reivindicar a tarefa e, em seguida *em execução* quando o runbook começa a ser executado.  

4. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No seu caso, verá *Olá, mundo*.

   ![Resultado do Painel de Teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  

5. Feche o painel de Teste para voltar à tela.

## <a name="publish-and-start-the-runbook"></a>Publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Tem de ser publicado antes de pode executá-lo em produção.  Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se se deslocar para a esquerda para ver o runbook no **Runbooks** painel, ele mostra agora uma **estado de criação** de **publicada**.
1. Desloque-se para trás para a direita para ver o painel de **MyFirstRunbook-PowerShell**.  
   As opções na parte superior permitem-nos iniciar o runbook, ver o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
1. Pretende iniciar o runbook, por isso, clique em **começar** e, em seguida, clique em **Ok** quando a página Iniciar Runbook for aberto.
1. Uma página de tarefa é aberta para a tarefa de runbook que criou. Pode fechar este painel, mas neste caso, deixá-lo aberto para que possa ver o progresso da tarefa.
1. O estado da tarefa é mostrado na **resumo da tarefa** e corresponde aos Estados que vimos quando testado o runbook.

   ![Resumo da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Uma vez o estado de runbook mostrar *concluído*, em **descrição geral** clique **saída**. É aberto o painel de resultados, e pode ver seus *Olá, mundo*.

   ![Resultado da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página de saída.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Deverá ver apenas *Olá, mundo* na saída do fluxo, mas esta saída pode mostrar outros fluxos de uma tarefa de runbook, tais como verboso e erro se o runbook escreve nos mesmos.

   ![Todos os Registos](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche a página de fluxos e a página da tarefa para voltar à página de MyFirstRunbook-PowerShell.
1. Sob **detalhes**, clique em **tarefas** para abrir o painel de tarefas para este runbook. Esta página lista todas as tarefas criadas por este runbook. Apenas deve conseguir ver uma tarefa listada, uma vez que apenas executou a tarefa uma vez.

   ![Lista de Tarefas](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  

1. Pode clicar nesta tarefa para abrir o mesmo painel Tarefas que visualizou quando iniciou o runbook. Esta ação permite-lhe voltar atrás no tempo e ver os detalhes de qualquer tarefa que foi criada para um determinado runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Adicionar autenticação para gerir recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Não é possível fazê-lo embora a menos que tenha autenticar através de uma ligação Run As que foi criada automaticamente quando criar a sua conta de automatização. Utilize a ligação Run As com o **Connect-AzureRmAccount** cmdlet. Se estiver a gerir recursos em várias subscrições, tem de utilizar o **- AzureRmContext** parâmetro juntamente com [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process
   
   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Abra o editor de texto ao clicar em **editar** na página de MyFirstRunbook-PowerShell.
1. Não é necessário o **Write-Output** linha, então, vamos continuar e eliminá-lo.
1. Escreva ou copie e cole o seguinte código que processa a autenticação com a conta da Execução da Automatização como:

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** e **Login-AzureRmAccount** agora são aliases **Connect-AzureRMAccount**. Se o **Connect-AzureRMAccount** cmdlet não existir, pode usar **Add-AzureRmAccount** ou **Login-AzureRmAccount**, ou pode atualizar os módulos na sua Automação Conta para as versões mais recentes.

1. Clique em **painel de teste** para que pode testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Esta saída confirma que a conta Run As é válida.

   ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Adicionar código para iniciar uma máquina virtual

Agora que o runbook está a autenticar a sua subscrição do Azure, pode gerir os recursos. Adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição do Azure e, para, agora, iremos codificar esse nome no runbook.

1. Após *Connect-AzureRmAccount*, tipo *Start-AzureRmVM-Name 'VMName' - ResourceGroupName 'NameofResourceGroup'* fornecendo o nome e o nome do grupo de recursos da máquina virtual para começar.  

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Guarde o runbook e, em seguida, clique em **painel de teste** para que pode testá-lo.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="add-an-input-parameter"></a>Adicionar um parâmetro de entrada

O runbook atualmente inicia a máquina virtual que codificado no runbook, mas seria mais útil se especificar a máquina virtual quando o runbook é iniciado. Adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros *VMName* e *ResourceGroupName* para o runbook e utilize estas variáveis com o **Start-AzureRmVM** cmdlet como no exemplo seguinte.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
   -ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
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
2. Runbooks de fluxo de trabalho do PowerShell suportam pontos de verificação Utilizar pontos de verificação, os runbooks do fluxo de trabalho do PowerShell podem retomar a partir de qualquer ponto no runbook. Os runbooks do PowerShell só pode ser retomar desde o início.
3. Runbooks de fluxo de trabalho do PowerShell suportam a execução paralela e serial. Os runbooks do PowerShell só pode executar comandos serialmente.
4. Um runbook de fluxo de trabalho do PowerShell, uma atividade, um comando ou um script de bloco pode ter seu próprio espaço de execução. Runbook do PowerShell, tudo num script é executado num único espaço de execução. Também existem algumas [diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) entre um runbook de PowerShell nativo e um runbook de Fluxo de Trabalho do PowerShell.

## <a name="next-steps"></a>Passos Seguintes

* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do fluxo de trabalho do PowerShell, consulte o artigo [O meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
