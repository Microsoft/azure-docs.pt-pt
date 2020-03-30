---
title: O meu primeiro livro de workflow PowerShell na Automação Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook de texto simples através do Fluxo de Trabalho do PowerShell.
keywords: fluxo de trabalho powerShell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho do powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367266"
---
# <a name="my-first-powershell-workflow-runbook"></a>O meu primeiro runbook do Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Pitão](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [Runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automatização do Azure. Comece com um simples livro de corridas que testa e publique enquanto aprende a rastrear o estado do trabalho do livro de corridas. Em seguida, modifique o livro de execução para realmente gerir os recursos do Azure, ilustrado através do arranque de uma máquina virtual Azure. Por último, torne o livro de corridas mais robusto adicionando parâmetros de livro.

>[!NOTE]
>Este artigo foi atualizado para utilizar o novo módulo AZ do Azure PowerShell. Pode continuar a utilizar o módulo AzureRM, que continuará a receber correções de erros até, pelo menos, dezembro de 2020. Para obter mais informações sobre o novo módulo Az e a compatibilidade do AzureRM, veja [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para instruções de instalação do módulo Az no seu Executor Híbrido, consulte [Instalar o Módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para a sua conta Automation, pode atualizar os seus módulos para a versão mais recente, utilizando [como atualizar os módulos Azure PowerShell em Automação Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Subscrição do Azure. Se ainda não tiver um, pode ativar os seus benefícios de [subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure. Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Já que paraste e começas esta máquina, não devia ser um VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook

Comece por criar um livro simples `Hello World`que produz o texto .

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria desses ativos são os módulos automaticamente incluídos numa nova conta Automation. Também deve ter o ativo credencial associado à sua subscrição.
 
1. Selecione **Runbooks** em **Process Automation** para abrir a lista de livros de execução.
1. Crie um novo livro de corridas selecionando **Criar um livro de execução**.
1. Atribua ao runbook o nome **MyFirstRunbook-Workflow**.
1. Neste caso, vai criar um livro de [execução powerShell Workflow.](automation-runbook-types.md#powershell-workflow-runbooks) **Selecione PowerShell Workflow** para **o tipo de Livro de Execução**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever código diretamente no livro de execução, ou pode selecionar cmdlets, livros de execução e ativos do controlo da Biblioteca e adicioná-los ao livro de execução com quaisquer parâmetros relacionados. Para este tutorial, digite código diretamente no livro de corridas.

1. O seu livro de execução está `Workflow` atualmente vazio apenas com a palavra-chave necessária, o nome do livro de execução e os aparelhos que enquadrem todo o fluxo de trabalho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Digite `Write-Output "Hello World"` entre o aparelho.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Guarde o runbook ao clicar em **Guardar**.

## <a name="step-3---test-the-runbook"></a>Passo 3 – testar o runbook

Antes de publicar o livro de execução para o disponibilizar em produção, deve testá-lo para se certificar de que funciona corretamente. Testar um livro de execução executa a sua versão Draft e permite-lhe visualizar a sua saída interativamente.

1. Selecione painel de **teste** para abrir o painel de teste.
1. Clique **em Iniciar** o teste, com o teste a única opção ativada.
1. Note que é criado um trabalho de livro de [rés](automation-runbook-execution.md) e o seu estado é apresentado no painel.

   O estado de `Queued`trabalho começa como , indicando que o trabalho está à espera que um trabalhador do livro na nuvem fique disponível. O estado `Starting` muda para quando um trabalhador reclama o trabalho. Finalmente, o `Running` estado torna-se quando o livro de corridas realmente começa a funcionar.

1. Quando o trabalho do livro de execução estiver concluído, o painel de teste mostra a sua saída. Neste caso, `Hello World`vê.

   ![Olá, Mundo](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O livro de execução que criou ainda está em modo Draft. Tem de publicá-lo antes de o poder executar em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. Neste caso, ainda não tem uma versão Publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Percorra à esquerda para ver o livro de **execução** na página Runbooks e note que o campo **De autor** está definido para **Publicado**.
1. Percorra para a direita para ver a página para **MyFirstRunbook-Workflow**.

   As opções em toda a parte superior permitem-lhe iniciar o livro de corridas agora, agendar um futuro início ou criar um [webhook](automation-webhooks.md) para que o livro de execução possa ser iniciado através de uma chamada HTTP.

1. Selecione **Iniciar** e, **em seguida, Sim** quando solicitado para iniciar o livro de execução.

   ![Iniciar o runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Um painel de trabalho é aberto para o trabalho de livro que foi criado. Neste caso, deixe o painel aberto para que possa ver o progresso do trabalho.

1. Note que o estado do trabalho é mostrado no Resumo do **Trabalho**. Este estado corresponde aos estados que viu ao testar o livro de corridas.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Assim que o `Completed`estado do livro de execução aparecer, clique em **Saída**. A página Saída está aberta, `Hello World` onde pode ver a sua mensagem.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Feche a página de saída.

1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Só deve `Hello World` ver no fluxo de saída. Note que o painel streams pode mostrar outros fluxos para um trabalho de livro de corridas, tais como fluxos de verbosa e erro, se o livro de execução lhes escrever.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Feche o painel de Streams e o painel de trabalho para voltar à página **MyFirstRunbook.**
1. Clique em **Jobs** under **Resources** para abrir a página Jobs para este livro de execução. Esta página lista todos os empregos criados pelo seu livro de corridas. Só deve ver um emprego na lista, já que só dirigiu o trabalho uma vez.

   ![Tarefas](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Clique no nome do trabalho para abrir o mesmo painel de trabalho que viu quando iniciou o livro de corridas. Utilize este painel para ver os detalhes de qualquer trabalho criado para o livro de corridas.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Não pode fazê-lo a menos que autentica usar as credenciais para a subscrição. A autenticação `Connect-AzAccount` utiliza o cmdlet.

>[!NOTE]
>Para os livros `Add-AzAccount` de `Add-AzureRMAccount` execução da `Connect-AzAccount`PowerShell, e são pseudónimos para . Pode utilizar estes cmdlets ou pode [atualizar os seus módulos](automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Pode precisar de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

1. Navegue para a página **MyFirstRunbook-Workflow** e abra o editor textual clicando em **Editar**.
2. Apague `Write-Output` a linha.
3. Posicione o cursor sobre uma linha em branco entre as chavetas.
4. Digite ou copie e cole o seguinte código, que trata a autenticação com a sua Execução de Automação Como conta.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Clique no **painel de teste** para que possa testar o livro de corridas.
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluída, deverá ver a saída semelhante à seguinte, exibindo informações básicas da sua conta. Para nós, este número confirma a importância do dia.

   ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que o seu livro de execução está a autenticar a subscrição do Azure, pode gerir recursos. Vamos adicionar um comando para iniciar uma máquina virtual. Pode escolher qualquer VM na sua subscrição Azure, e por enquanto está a codificar esse nome no livro de execução. Se estiver a gerir recursos em várias subscrições, precisa de utilizar o `AzContext` parâmetro com o cmdlet [Get-AzContext.](/powershell/module/az.accounts/get-azcontext)

1. Forneça o nome e o nome do grupo de recursos do VM para começar por introduzir uma chamada para o cmdlet [Start-AzVM,](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) como mostrado abaixo. 

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

1. Guarde o livro de recortes e, em seguida, clique em **testa** para que possa testá-lo.
1. Clique em **Iniciar** para iniciar o teste. Uma vez concluído, verifique se o VM foi iniciado.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook

O seu livro de execução começa atualmente o VM que tem codificado no livro de execução. Será mais útil se puder especificar o VM quando o livro de execução for iniciado. Vamos adicionar parâmetros de entrada ao livro de execução para fornecer essa funcionalidade.

1. Adicione variáveis `VMName` para `ResourceGroupName` os parâmetros e parâmetros ao livro `Start-AzVM` de execução, e use as variáveis com o cmdlet como mostrado abaixo.

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
7. Digite os valores para **VMNAME** e **RESOURCEGROUPNAME** para o VM que vai iniciar.

   ![Iniciar o Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Quando o livro de execução estiver concluído, verifique se o VM já começou.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o PowerShell, incluindo módulos de referência linguística e aprendizagem, consulte os [Docs PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para começar com livros gráficos, veja [o meu primeiro livro de corridas gráficos.](automation-first-runbook-graphical.md)
* Para começar com os livros de execução da PowerShell, veja o meu primeiro livro de [corridas PowerShell.](automation-first-runbook-textual-powershell.md)
* Para saber mais sobre os tipos de livro de corridas e as suas vantagens e limitações, consulte os tipos de livro de [execução da Automação Azure](automation-runbook-types.md).
* Para obter mais informações sobre as funcionalidades de suporte ao script PowerShell, consulte o suporte do [script Native PowerShell em Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
