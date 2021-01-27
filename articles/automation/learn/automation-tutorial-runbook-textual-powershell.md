---
title: Criar um livro de corridas PowerShell na Azure Automation
description: Este artigo ensina-o a criar, testar e publicar um simples livro de artigos da PowerShell.
keywords: Azure powershell, tutorial de script do powershell, automatização de powershell
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: 896b4db433164471f41aa09791ede5d677028bfb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896617"
---
# <a name="tutorial-create-a-powershell-runbook"></a>Tutorial: Criar um livro de execução PowerShell

Este tutorial explica como criar um [ runbook do PowerShell](../automation-runbook-types.md#powershell-runbooks) na Automatização do Azure. Os livros powerShell são baseados no Windows PowerShell. Pode editar diretamente o código do livro de texto utilizando o editor de texto no portal Azure.

> [!div class="checklist"]
> * Criar um livro de execução PowerShell simples
> * Teste e publique o livro de corridas
> * Correr e acompanhar o estado do trabalho de runbook
> * Atualize o runbook para iniciar uma máquina virtual Azure com parâmetros de runbook

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver um, pode [ativar os benefícios do seu assinante MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Conta de automatização](../automation-quickstart-create-account.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Uma vez que paras e começas esta máquina, não deve ser um VM de produção.
* Se necessário, [importe módulos Azure](../shared-resources/modules.md) ou [módulos de atualização](../automation-update-azure-modules.md) com base nos cmdlets que utiliza.

## <a name="differences-from-powershell-workflow-runbooks"></a>Diferenças dos livros de fluxo de trabalho powerShell

Os livros powerShell têm o mesmo ciclo de vida, capacidades e gestão que os livros de fluxo de trabalho powerShell. No entanto, existem algumas diferenças e limitações.

| Característica  | PowerShell Runbooks | Livros de fluxo de trabalho PowerShell |
| ------ | ----- | ----- |
| Velocidade | Corra rápido como não usam um passo de compilação. | Corra mais devagar. |
| Pontos de verificação | Não apoie postos de controlo. Um livro de execução PowerShell só pode retomar o funcionamento desde o início. | Utilize postos de controlo, que permitem que um livro retome o funcionamento a partir de qualquer ponto. |
| Execução de Comando | Apoiar apenas a execução em série. | Apoie a execução em série e paralela.|
| Espaço runspace | Um único espaço de corrida executa tudo num guião. | Um espaço de execução separado pode ser usado para uma atividade, um comando ou um bloco de script. |

Além destas diferenças, os livros de execução PowerShell têm [algumas diferenças sintáticas](/previous-versions/technet-magazine/dn151046(v=msdn.10)) dos livros de fluxo de trabalho powerShell.

## <a name="step-1---create-runbook"></a>Passo 1 – Criar runbook

Comece por criar um simples livro de execução que produz o texto `Hello World` .

1. No portal do Azure, abra a sua conta da Automatização.

2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.

3. Criar um novo livro de execução selecionando **Criar um livro de bordo**.

4. Atribua ao runbook o nome **MyFirstRunbook PowerShell**.

5. Neste caso, vais criar um livro de [regras powerShell.](../automation-runbook-types.md#powershell-runbooks) Selecione **PowerShell** para **o tipo Runbook**.

6. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para este tutorial, vais escrever código diretamente no livro de bordo.

1. O seu livro está vazio. Digite `Write-Output "Hello World"` o corpo do guião.

   ![Olá, Mundo](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"></a> Passo 3 - Teste o livro de corridas

Antes de publicar o livro de bordo para o disponibilizar em produção, deverá testá-lo para se certificar de que funciona corretamente. Testar um runbook executa a sua versão Draft e permite-lhe visualizar a sua saída de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.

2. Clique em **Iniciar** para iniciar o teste. Esta deve ser a única opção ativada.

3. Note que é criada uma [função de runbook](../automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estatuto de trabalho começa como "Fila", indicando que o trabalho está à espera que um trabalhador da lista na nuvem fique disponível. O estado muda para Começar quando um trabalhador reclama o emprego. Finalmente, o estado torna-se Running quando o livro de bordo realmente começa a funcionar.

4. Quando o trabalho do runbook estiver concluído, o painel de teste apresenta a sua saída. Neste caso, `Hello World` vês.

   ![Resultado do Painel de Teste](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está no modo Draft. Tem de ser publicado antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.

2. Percorra a esquerda para ver o livro de recortes na página Runbooks e note que o valor **do Estado de Autoria** está definido para **Publicado**.

3. Volte para a direita para ver a página para **MyFirstRunbook-PowerShell**.
   
   As opções em toda a parte superior permitem-lhe iniciar o runbook agora, agendar uma hora de início futura, ou criar um [webhook](../automation-webhooks.md) para que o livro de aplicação possa ser iniciado através de uma chamada HTTP.

4. Selecione **Iniciar** e, em seguida, **Sim** quando solicitado para iniciar o livro de execução. 

5. Abre-se um painel job para o trabalho de runbook que foi criado. Embora possa fechar este painel, deixe-o aberto agora para que possa ver o progresso do trabalho. O estado do trabalho é mostrado no **Resumo do Trabalho,** e os possíveis estatutos são descritos para testar o livro de recortes.

   ![Resumo da Tarefa](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Assim que o estado do livro de execução aparecer concluído, clique em **Saída** para abrir a página de Saída, onde pode ver `Hello World` visualizado.

   ![Resultado da Tarefa](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. Feche a página de Saída.

8. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve ver `Hello World` no fluxo de saída.

    Note que o painel streams pode mostrar outros fluxos para uma tarefa de runbook, como verbose e fluxos de erro, se o livro de bordo lhes escrever.

   ![Todos os Registos](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. Feche o painel de streams e o painel de trabalho para voltar à página MyFirstRunbook-PowerShell.

10. Em **Detalhes,** clique em **Jobs** para abrir a página Jobs para este livro. Esta página lista todos os trabalhos criados pelo seu runbook. Só deve ver um emprego na lista, já que só dirigiu o trabalho uma vez.

   ![Lista de Tarefas](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o runbook. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de recortes.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Para isso, o livro deve ser capaz de autenticar utilizando a conta Run As que foi criada automaticamente quando criou a sua conta Demôm automação.

Como mostrado no exemplo abaixo, a ligação Run As é feita com o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Se estiver a gerir recursos em várias subscrições, tem de utilizar o `AzContext` parâmetro com [o Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext).

> [!NOTE]
> Para os livros powerShell, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para `Connect-AzAccount` . Pode utilizar estes cmdlets ou [atualizar os seus módulos](../automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Poderá ter de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```

1. Abra o editor textual clicando em **Editar** na página MyFirstRunbook-PowerShell.

2. Já não precisas da `Write-Output` linha. Basta apagar.

3. Digite ou copie e cole o seguinte código, que trata da autenticação com a sua conta Automation Run As.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

4. Clique **no painel de teste para** que possa testar o livro de execução.

5. Clique em **Iniciar** para iniciar o teste. Uma vez concluída, deverá ver uma saída semelhante à seguinte, apresentando informações básicas da sua conta. Esta saída confirma que a Conta Run As é válida.

   ![Autenticar](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que o seu runbook está a autenticar a sua subscrição Azure, pode gerir recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer máquina virtual na sua subscrição Azure e apenas código rígido que nome no runbook por enquanto.

1. Ao seu script de runbook, adicione o cmdlet [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) para iniciar a máquina virtual. Como mostrado abaixo, o cmdlet inicia uma máquina virtual com o nome `VMName` e com um grupo de recursos chamado `ResourceGroupName` .

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

2. Guarde o livro de correr e, em seguida, clique em **Test panê-lo** para que possa testá-lo.

3. Clique **em Iniciar** o teste. Uma vez concluída, certifique-se de que a máquina virtual já começou.

## <a name="step-7---add-an-input-parameter"></a>Passo 7 - Adicione um parâmetro de entrada

O seu runbook funciona atualmente a máquina virtual que codificaste no livro de recortes. O livro de recortes é mais útil se especificar a máquina virtual quando o livro de recortes for iniciado. Vamos adicionar parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. No editor textual, modifique o `Start-AzVM` cmdlet para utilizar variáveis para os parâmetros `VMName` e `ResourceGroupName` . 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -and ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                               -ServicePrincipal `
                               -Tenant $connection.TenantID `
                               -ApplicationId $connection.ApplicationID `
                               -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

2. Guarde o runbook e abra o painel de Teste. Agora pode apresentar valores para as duas variáveis de entrada que utiliza no teste.

3. Feche o painel de Teste.

4. Clique em **Publicar** para publicar a nova versão do runbook.

5. Pare a máquina virtual que começou anteriormente.

6. Clique em **Iniciar** para iniciar o runbook. 

7. Digite os valores **de VMNAME** e **RESOURCEGROUPNAME** para a máquina virtual que vai iniciar e, em seguida, clique **em OK**.

    ![Transmitir Parâmetro](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Quando o livro de execução estiver concluído, certifique-se de que a máquina virtual foi iniciada.

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [PowerShell Docs](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
* Para começar com os runbooks gráficos, consulte [Criar um runbook gráfico](automation-tutorial-runbook-graphical.md).
* Para começar com os livros de fluxo de trabalho PowerShell, consulte [Criar um manual de fluxo de trabalho PowerShell](automation-tutorial-runbook-textual.md).
* Para saber mais sobre os tipos de runbook e as suas vantagens e limitações, consulte [os tipos de runbook da Azure Automation](../automation-runbook-types.md).
* Para obter mais informações sobre a funcionalidade de suporte ao script PowerShell, consulte o [suporte para scripts Native PowerShell na Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
