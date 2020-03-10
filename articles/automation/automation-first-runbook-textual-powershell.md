---
title: O meu primeiro livro powerShell em Automação Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook do PowerShell simples.
keywords: Azure powershell, tutorial de script do powershell, automatização de powershell
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384854"
---
# <a name="my-first-powershell-runbook"></a>O meu primeiro runbook do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [ runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Comece com um simples livro de corridas que pode testar e publicar, enquanto aprende a rastrear o estado do trabalho do livro de corridas. Em seguida, modifique o livro de execução para realmente gerir os recursos do Azure, neste caso iniciando uma máquina virtual Azure. Complete o tutorial para tornar o livro de corridas mais robusto adicionando parâmetros de livro.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-quickstart-create-account.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que paraste e começas esta máquina, não devia ser um VM de produção.
* Se necessário, [importe módulos Azure](shared-resources/modules.md) ou atualização de [módulos](automation-update-azure-modules.md) com base nos cmdlets que utilizar.

## <a name="differences-from-powershell-workflow-runbooks"></a>Diferenças dos livros de workflow powerShell

Os livros de execução powerShell têm o mesmo ciclo de vida, capacidades e gestão que os livros de workflow powerShell. No entanto, existem algumas diferenças e limitações.

* Os livros powerShell são rápidos em comparação com os livros de execução powerShell Workflow, uma vez que não usam um passo de compilação.
* Os livros powerShell não suportam postos de controlo, que são usados em livros de fluxo de trabalho powerShell. Utilizando postos de controlo, os livros de fluxo de trabalho powerShell podem retomar o funcionamento a partir de qualquer ponto. O seu livro de execução PowerShell só pode retomar a operação desde o início.
* Os livros de execução powerShell só podem executar comandos em série. Os livros de execução powerShell Workflow suportam a execução em série e paralela.
* Para um livro de corridas powerShell, tudo num guião corre num único espaço de corrida. Num livro de fluxo de fluxo de trabalho powerShell, uma atividade, um comando ou um bloco de script saem do seu próprio espaço de execução. 
* Os livros de execução da PowerShell têm [algumas diferenças sintáticas](https://technet.microsoft.com/magazine/dn151046.aspx) dos livros de execução powerShell Workflow.

## <a name="step-1---create-runbook"></a>Passo 1 – Criar runbook

Comece por criar um runbook simples que produz o texto **Hello World**.

1. No portal do Azure, abra a sua conta da Automatização.
2. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.
3. Crie um novo livro de corridas selecionando **Criar um livro de execução**.
4. Atribua ao runbook o nome **MyFirstRunbook PowerShell**.
5. Neste caso, vais criar um livro de [execução da PowerShell.](automation-runbook-types.md#powershell-runbooks) Selecione **Powershell** para **o tipo 'Livro de execução.'**
6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, vais escrever código diretamente no livro de corridas.

1. O teu livro está vazio. Escreva `Write-Output "Hello World"` no corpo do guião.

   ![Olá, Mundo](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a> Passo 3 - Teste o livro de corridas

Antes de publicar o livro de execução para o disponibilizar em produção, deve testá-lo para se certificar de que funciona corretamente. Testar um livro de execução executa a sua versão Draft e permite-lhe visualizar a sua saída interativamente.

1. Clique em **Painel de teste** para abrir o Painel de teste.
2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.
3. Note que é criado um trabalho de livro de [rés](automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estado de trabalho começa como **Fila,** indicando que o trabalho está à espera que um trabalhador do livro na nuvem fique disponível. O estado muda para **Começar** quando um trabalhador reclama o trabalho. Finalmente, o estado torna-se **Running** quando o livro de corridas realmente começa a funcionar.

4. Quando o trabalho do livro de execução estiver concluído, o painel de teste mostra a sua saída. Neste caso, verá **Hello World**.

   ![Resultado do Painel de Teste](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está em modo Draft. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Percorra à esquerda para ver o livro de execução na página Runbooks e note que o valor **do Estado de Autor** está definido para **Publicado**.
1. Desloque-se para trás para a direita para ver o painel de **MyFirstRunbook-PowerShell**.
   
   As opções em toda a parte superior permitem-lhe iniciar o livro de corridas agora, agendar um futuro início ou criar um [webhook](automation-webhooks.md) para que o livro de execução possa ser iniciado através de uma chamada HTTP.
1. Selecione **Iniciar** e, **em seguida, Sim** quando solicitado para iniciar o livro de execução. 
1. Um painel de trabalho é aberto para o trabalho de livro que foi criado. Embora possa fechar este painel, deixe-o aberto agora para que possa ver o progresso do trabalho. O estado de trabalho é mostrado no Resumo do **Trabalho**, e os possíveis estatutos são descritos para testar o livro de execução.

   ![Resumo da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Assim que o estado do livro de execução mostrar **concluído,** clique em **Saída** para abrir a página De saída, onde pode ver **o Hello World** apresentado.

   ![Resultado da Tarefa](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Feche a página de saída.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deves ver o **Hello World** na corrente de saída.

    Note que o painel streams pode mostrar outros fluxos para um trabalho de livro de corridas, como verbose e fluxos de erro, se o livro de execução escrever para eles.

   ![Todos os Registos](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Feche o painel de Streams e o painel de trabalho para voltar à página **MyFirstRunbook-PowerShell.**
1. Em **Detalhes,** clique **em Jobs** para abrir a página Jobs para este livro de execução. Esta página lista todos os empregos criados pelo seu livro de corridas. Só devias ver um emprego na lista, já que só geriste o trabalho uma vez.

   ![Lista de Tarefas](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o livro de corridas. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de corridas.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Para isso, o livro de recortes deve ser capaz de autenticar usando a conta Run As que foi criada automaticamente quando criou a sua conta Desmase.

Como mostra o exemplo abaixo, a ligação Run As é feita com o cmdlet [Connect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) Se estiver a gerir recursos em várias subscrições, precisa de utilizar o parâmetro *AzContext* juntamente com o [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> Para os livros de execução PowerShell, **Add-AzAccount** e **Add-AzureRMAccount** são pseudónimos de **Connect-AzAccount**. Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. Abra o editor textual clicando em **Editar** na página **MyFirstRunbook-PowerShell.**
1. Já não precisas da linha **write-output.** Basta ir em frente e apagá-lo.
1. Digite ou copie e cole o seguinte código, que trata a autenticação com a sua Execução de Automação Como conta.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Clique no **painel de teste** para que possa testar o livro de corridas.
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluída, deverá ver a saída semelhante à seguinte, exibindo informações básicas da sua conta. Esta saída confirma que a Conta Run As é válida.

   ![Autenticar](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que o seu livro de execução está a autenticar a sua subscrição Azure, pode gerir recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição Azure, e apenas código rígido esse nome no livro de execução por enquanto.

1. Ao seu script de livro de execução, adicione o cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) para iniciar a máquina virtual. Como mostrado abaixo, o cmdlet inicia uma máquina virtual com o nome **VMName** e com um grupo de recursos chamado **ResourceGroupName**.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Guarde o livro de recortes e, em seguida, clique em **testa** para que possa testá-lo.
1. Clique **em Começar** para iniciar o teste. Uma vez concluída, certifique-se de que a máquina virtual começou.

## <a name="step-7---add-an-input-parameter"></a>Passo 7 - Adicione um parâmetro de entrada

O seu livro de execução começa atualmente a máquina virtual que codificano no livro de execução. O livro de execução será mais útil se especificar a máquina virtual quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao livro de execução para fornecer essa funcionalidade.

1. No editor textual, modifique o cmdlet **Start-AzVM** para utilizar variáveis para os parâmetros *VMName* e *ResourceGroupName*. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.
1. Feche o painel de Teste.
1. Clique em **Publicar** para publicar a nova versão do runbook.
1. Pare a máquina virtual que começou anteriormente.
1. Clique em **Iniciar** para iniciar o runbook. 
1. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** para a máquina virtual que vai iniciar e, em seguida, clique **OK**.<br><br> ![Transmitir Parâmetro](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Quando o livro de execução estiver concluído, certifique-se de que a máquina virtual foi iniciada.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](/powershell/scripting/overview).
* Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](automation-first-runbook-graphical.md)
* Para começar a utilizar runbooks do Fluxo de Trabalho do PowerShell, veja [O meu primeiro runbook do Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md).
* Para saber mais sobre os tipos de livro de corridas e as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](automation-runbook-types.md).
* Para obter mais informações sobre a funcionalidade de suporte ao script PowerShell, consulte o suporte do [script Native PowerShell na Automação Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
