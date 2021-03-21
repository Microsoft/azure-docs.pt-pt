---
title: Criar um livro de fluxo de trabalho PowerShell na Azure Automation
description: Este artigo ensina-o a criar, testar e publicar um simples manual de fluxo de trabalho powerShell.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: e822e17f154b72854c5150e071301d7bbd53072e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890819"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>Tutorial: Criar um manual de fluxo de trabalho PowerShell

Este tutorial explica como criar um [Runbook do Fluxo de Trabalho do PowerShell](../automation-runbook-types.md#powershell-workflow-runbooks) na Automatização do Azure. Os livros de fluxo de trabalho PowerShell são livros de texto baseados no fluxo de trabalho do Windows PowerShell. Pode criar e editar o código do livro de texto utilizando o editor de texto no portal Azure. 

> [!div class="checklist"]
> * Criar um simples manual de fluxo de trabalho PowerShell
> * Teste e publique o livro de corridas
> * Correr e acompanhar o estado do trabalho de runbook
> * Atualize o runbook para iniciar uma máquina virtual Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../index.yml) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Uma vez que paras e começas esta máquina, não deve ser um VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook

Comece por criar um simples livro de execução que produz o texto `Hello World` .

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria desses ativos são os módulos automaticamente incluídos numa nova conta Automation. Também deverá ter o ativo credencial associado à sua subscrição.
 
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Criar um novo livro de execução selecionando **Criar um livro de bordo**.

4. Atribua ao runbook o nome **MyFirstRunbook-Workflow**.

5. Neste caso, vai criar um livro de [trabalho powerShell Workflow.](../automation-runbook-types.md#powershell-workflow-runbooks) Selecione **powerShell Workflow** para **o tipo Runbook**.

6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever código diretamente no livro de bordo, ou pode selecionar cmdlets, runbooks e ativos do controlo da Biblioteca e adicioná-los ao livro de recortes com quaisquer parâmetros relacionados. Para este tutorial, escreva código diretamente no livro de bordo.

1. O seu runbook está atualmente vazio apenas com a `Workflow` palavra-chave necessária, o nome do livro de corridas e os aparelhos que envolvem todo o fluxo de trabalho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. Digite `Write-Output "Hello World"` entre os aparelhos.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o livro de bordo para o disponibilizar em produção, deverá testá-lo para se certificar de que funciona corretamente. Testar um runbook executa a sua versão Draft e permite-lhe visualizar a sua saída de forma interativa.

1. Selecione **Test pane** para abrir o painel de teste.

2. Clique **em Iniciar** o teste, com o teste a única opção ativada.

3. Note que é criada uma [função de runbook](../automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estatuto de trabalho começa como "Fila", indicando que o trabalho está à espera que um trabalhador da lista na nuvem fique disponível. O estado muda para Começar quando um trabalhador reclama o emprego. Finalmente, o estado torna-se Running quando o livro de bordo realmente começa a funcionar.

4. Quando o trabalho do runbook estiver concluído, o painel de teste apresenta a sua saída. Neste caso, `Hello World` vês.

   ![Olá, Mundo](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está no modo Draft. Deve publicá-lo antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.

2. Percorra a esquerda para ver o livro de recortes na página **Runbooks** e note que o campo **de Estado de Autoria** está definido para **Publicado**.

3. Volte para a direita para ver a página para **MyFirstRunbook-Workflow**.

   As opções em toda a parte superior permitem-lhe iniciar o runbook agora, agendar uma hora de início futura, ou criar um [webhook](../automation-webhooks.md) para que o livro de aplicação possa ser iniciado através de uma chamada HTTP.

4. Selecione **Iniciar** e, em seguida, **Sim** quando solicitado para iniciar o livro de execução.

   ![Iniciar o runbook](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. Abre-se um painel job para o trabalho de runbook que foi criado. Neste caso, deixe o painel aberto para que possa ver o progresso do trabalho.

6. Note que o estado do trabalho é indicado no **Resumo do Trabalho**. Este estado corresponde aos estados que viu ao testar o livro de recortes.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="Screenshot do painel de trabalho do runbook mostrando a secção de Resumo de Trabalho realçada.":::

7. Assim que o estado do livro de execução aparecer concluído, clique em **Saída**. A página Saída é aberta, onde pode ver a sua `Hello World` mensagem.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="Screenshot do painel de trabalho do runbook mostrando o botão de saída realçado.":::

8. Feche a página de Saída.

9. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve ver `Hello World` no fluxo de saída. Note que o painel streams pode mostrar outros fluxos para uma tarefa de runbook, como verbose e fluxos de erro, se o livro de bordo lhes escrever.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="Screenshot do painel de trabalho do runbook mostrando o botão Todos os Registos realçados.":::

10. Feche o painel streams e o painel de Trabalho para voltar à página MyFirstRunbook.

11. Clique em **Jobs** under **Resources** para abrir a página Jobs para este livro de recortes. Esta página lista todos os trabalhos criados pelo seu runbook. Só deve ver um emprego na lista, já que só dirigiu o trabalho uma vez.

   ![Tarefas](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o runbook. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de recortes.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Não pode fazer isso a menos que autentica usando as credenciais para a subscrição. A autenticação utiliza o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

>[!NOTE]
>Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Pode utilizar estes cmdlets ou [atualizar os seus módulos](../automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Poderá ter de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

1. Navegue para a página MyFirstRunbook-Workflow e abra o editor textual clicando **em Editar**.

2. Apague a `Write-Output` linha.

3. Posicione o cursor sobre uma linha em branco entre as chavetas.

4. Digite ou copie e cole o seguinte código, que trata da autenticação com a sua conta Automation Run As.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Clique **no painel de teste para** que possa testar o livro de execução.

6. Clique em **Iniciar** para iniciar o teste. Uma vez concluída, deverá ver uma saída semelhante à seguinte, apresentando informações básicas da sua conta. Esta ação confirma que a credencial é válida.

   ![Autenticar](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está a autenticar a subscrição do Azure, pode gerir recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer VM na sua assinatura Azure e, por enquanto, está a codificar esse nome no livro de recortes. Se estiver a gerir recursos em várias subscrições, tem de utilizar o `AzContext` parâmetro com o [cmdlet Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Forneça o nome e o nome do grupo de recursos do VM para começar por introduzir uma chamada para o cmdlet [Start-AzVM,](/powershell/module/Az.Compute/Start-AzVM) conforme mostrado abaixo. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Guarde o livro de correr e, em seguida, clique em **Test panê-lo** para que possa testá-lo.

3. Clique em **Iniciar** para iniciar o teste. Uma vez concluído, verifique se o VM foi iniciado.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook

O seu runbook começa atualmente o VM que tem codificado no livro de recortes. Será mais útil se puder especificar o VM quando o livro de bordo for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione variáveis para o `VMName` e `ResourceGroupName` parâmetros ao runbook, e use as variáveis com o `Start-AzVM` cmdlet como mostrado abaixo.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Guarde o runbook e abra o painel de Teste. Agora pode fornecer valores para as duas variáveis de entrada que estão no teste.

3. Feche o painel de Teste.

4. Clique em **Publicar** para publicar a nova versão do runbook.

5. Pare o VM que começou.

6. Clique em **Iniciar** para iniciar o runbook. 

7. Digite os valores **de VMNAME** e **RESOURCEGROUPNAME** para o VM que vai iniciar.

   ![Iniciar o Runbook](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Quando o livro de execução estiver concluído, verifique se o VM começou.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
* Para começar com os runbooks gráficos, consulte [Criar um runbook gráfico](automation-tutorial-runbook-graphical.md).
* Para começar com os livros powerShell, consulte [Criar um livro de execução PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Para saber mais sobre os tipos de runbook e as suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](../automation-runbook-types.md).
* Para obter mais informações sobre as funcionalidades de suporte ao script PowerShell, consulte [o suporte para scripts Native PowerShell na Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
