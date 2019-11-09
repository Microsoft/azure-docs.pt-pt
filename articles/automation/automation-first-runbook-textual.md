---
title: Meu primeiro runbook do fluxo de trabalho do PowerShell na automação do Azure
description: Este tutorial orienta-o ao longo da criação, do teste e da publicação de um runbook de texto simples através do Fluxo de Trabalho do PowerShell.
keywords: fluxo de trabalho powerShell, exemplos de fluxo de trabalho do powershell, fluxo de trabalho do powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4973a5215d93a1ce9d0a80f83264dd8c1fbe0888
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887225"
---
# <a name="my-first-powershell-workflow-runbook"></a>O meu primeiro runbook do Fluxo de Trabalho do PowerShell

> [!div class="op_single_selector"]
> * [Gráficos](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Este tutorial explica como criar um [Runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) na Automatização do Azure. Você começa com um runbook simples que você testa e publica enquanto explica como acompanhar o status do trabalho de runbook. Em seguida, modifique o runbook para gerir recursos do Azure, neste caso, iniciar uma máquina virtual do Azure. Por fim, você torna o runbook mais robusto adicionando parâmetros de runbook.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Subscrição do Azure. Se ainda não tiver uma, pode [ativar as vantagens de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se numa conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Conta de automatização](automation-offering-get-started.md) para manter o runbook e autenticar-se nos recursos do Azure.  Esta conta tem de ter permissão para iniciar e parar a máquina virtual.
* Uma máquina virtual do Azure. Você pára e inicia esse computador para que ele não seja uma VM de produção.

## <a name="step-1---create-new-runbook"></a>Passo 1 – criar um novo runbook

Você começa criando um runbook simples que gera o texto *Olá, mundo*.

1. No portal do Azure, abra a sua conta da Automatização.

   A página da conta da Automatização dá-lhe uma vista rápida dos recursos nesta conta. Já deverá ter alguns recursos. A maioria destes ativos são os módulos que estão incluídos automaticamente na nova conta da Automatização. Também deverá ter o recurso da Credencial que está mencionado nos [Pré-requisitos](#prerequisites).

1. Clique em **Runbooks** em **automação de processo** para abrir a lista de runbooks.
1. Crie um novo runbook clicando no botão **+ Adicionar um runbook** e, em seguida, **criar um novo runbook**.
1. Atribua ao runbook o nome *MyFirstRunbook-Workflow*.
1. Nesse caso, você criará um [runbook de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) , portanto, selecione fluxo de trabalho do **PowerShell** para **tipo de runbook**.
1. Clique em **Criar** para criar o runbook e abra o editor de texto.

## <a name="step-2---add-code-to-the-runbook"></a>Passo 2 - adicionar código ao runbook

Pode escrever o código do tipo diretamente no runbook ou pode selecionar os cmdlets, runbooks e recursos no controlo da Biblioteca e adicioná-los ao runbook com quaisquer parâmetros relacionados. Para esta explicação, você digita diretamente no runbook.

1. No momento, o runbook está vazio com apenas a palavra-chave de *fluxo de trabalho* necessária, o nome do seu runbook e as chaves que demonstram todo o fluxo de trabalho.

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

Antes de publicar o runbook para o disponibilizar na produção, deve testá-lo para garantir que funciona corretamente. Quando testa um runbook, executa a versão de **Rascunho** e veja o resultado de forma interativa.

1. Clique em **Painel de teste** para abrir o Painel de teste.
1. Clique em **Iniciar** para iniciar o teste. Essa opção deve ser a única opção habilitada.
1. Uma [tarefa do runbook](automation-runbook-execution.md) é criada e o respetivo estado é apresentado.

   O status do trabalho será iniciado como *na fila* , indicando que está aguardando que um runbook Worker na nuvem fique disponível. Ele é movido para *iniciando* quando um trabalho alega o trabalho e, em seguida, é *executado* quando o runbook realmente começa a ser executado.

1. Quando a tarefa de runbook tiver concluído, o resultado é apresentado. No seu caso, você deve ver *Olá, mundo*.

   ![Olá, Mundo](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Feche o painel de Teste para voltar à tela.

## <a name="step-4---publish-and-start-the-runbook"></a>Passo 4 – publicar e iniciar o runbook

O runbook que criou ainda está no modo de Rascunho. Você deve publicá-lo antes de poder executá-lo em produção. Quando publica um runbook, substitui a versão Publicada existente pela versão de Rascunho. No seu caso, você ainda não tem uma versão publicada porque acabou de criar o runbook.

1. Clique em **Publicar** para publicar o runbook e, em seguida, em **Sim** quando lhe for pedido.
1. Se você rolar para a esquerda para exibir o runbook no painel **Runbooks** agora, ele mostrará um **status de criação** **publicado**.
1. Desloque-se novamente para a direita para ver o painel para **MyFirstRunbook-Workflow**.
   As opções na parte superior permitem-nos iniciar o runbook, agendar o seu início num momento futuro ou criar um [webhook](automation-webhooks.md) para que possa ser iniciado através de uma chamada HTTP.
1. Você só deseja iniciar o runbook, portanto, clique em **Iniciar** e em **Sim** quando solicitado.

   ![Iniciar o runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Um painel de trabalho é aberto para o trabalho de runbook que você criou. Você pode fechar esse painel, mas, nesse caso, deixá-lo aberto para que você possa observar o progresso do trabalho.
1. O status do trabalho é mostrado no **Resumo do trabalho** e corresponde aos status que você viu quando testou o runbook.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Quando o estado de runbook mostrar *Concluído*, clique em **Resultado**. O painel saída é aberto e você pode ver sua *Olá, mundo*.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Feche o painel Resultado.
1. Clique em **Todos os Registos** para abrir o painel Fluxos da tarefa de runbook. Você só deve ver *Olá, mundo* no fluxo de saída, mas essa exibição pode mostrar outros fluxos para um trabalho de runbook, como detalhado e erro se o runbook gravar neles.

   ![Resumo da Tarefa](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Feche a página fluxos e a página trabalho para retornar à página MyFirstRunbook.
1. Clique em **trabalhos** para abrir a página trabalhos para este runbook. Esta página lista todos os trabalhos criados por este runbook. Você só deve ver um trabalho listado, pois você executou o trabalho apenas uma vez.

   ![Tarefas](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Você pode clicar nesse trabalho para abrir a mesma página de trabalho que você exibiu quando iniciou o runbook. Essa ação permite que você volte no tempo e exiba os detalhes de qualquer trabalho que foi criado para um runbook específico.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Passo 5 – adicionar autenticação para gerir os recursos do Azure

Testou e publicou o seu runbook, mas, até ao momento, não faz nada de útil. Deve fazer com que gira os recursos do Azure. Isso não pode fazer isso, a menos que você tenha autenticado usando as credenciais que são mencionadas nos [pré-requisitos](#prerequisites). Você faz isso com o cmdlet **Connect-AzAccount** .

1. Abra o editor de texto clicando em **Editar** no painel MyFirstRunbook-Workflow.
2. Você não precisa mais da linha **Write-Output** , então vá em frente e exclua-a.
3. Posicione o cursor sobre uma linha em branco entre as chavetas.
4. Escreva ou copie e cole o seguinte código que irá processar a autenticação com a conta da Execução da Automatização como:

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzAccount** e **login-AzAccount** agora são aliases para **Connect-AzAccount**. Se o cmdlet **Connect-AzAccount** não existir, você poderá usar **Add-AzAccount** ou **login-AzAccount**ou poderá [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de automação para as versões mais recentes.

> [!NOTE]
> Talvez seja necessário [atualizar seus módulos](automation-update-azure-modules.md) mesmo que você tenha acabado de criar uma nova conta de automação.

1. Clique em **painel de teste** para que você possa testar o runbook.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, deverá receber um resultado parecido ao seguinte que mostra informações básicas da sua conta. Essa ação confirma que a credencial é válida.

   ![Autenticar](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Passo 6 – adicionar código para iniciar uma máquina virtual

Agora que seu runbook está Autenticando para sua assinatura do Azure, você pode gerenciar recursos. Você adiciona um comando para iniciar uma máquina virtual. Você pode escolher qualquer máquina virtual em sua assinatura do Azure e, por enquanto, você está codificando esse nome no runbook. Se você estiver gerenciando recursos em várias assinaturas, precisará usar o parâmetro **-AzContext** junto com [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Após *Connect-AzAccount*, digite *Start-AzVM-Name ' VMName '-ResourceGroupName ' NameofResourceGroup* ', fornecendo o nome e o nome do grupo de recursos da máquina virtual para iniciar.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Salve o runbook e, em seguida, clique em **painel de teste** para que você possa testá-lo.
1. Clique em **Iniciar** para iniciar o teste. Depois de terminar, verifique se a máquina virtual foi iniciada.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Passo 7 – adicionar um parâmetro de entrada ao runbook

o runbook inicia atualmente a máquina virtual que você codificou no runbook, mas seria mais útil se você pudesse especificar a máquina virtual quando o runbook for iniciado. Você adiciona parâmetros de entrada ao runbook para fornecer essa funcionalidade.

1. Adicione parâmetros para *VMName* e *ResourceGroupName* ao runbook e use essas variáveis com o cmdlet **Start-AzVM** como no exemplo abaixo.

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

2. Guarde o runbook e abra o painel de Teste. Agora você pode fornecer valores para as duas variáveis de entrada que estão no teste.
3. Feche o painel de Teste.
4. Clique em **Publicar** para publicar a nova versão do runbook.
5. Pare a máquina virtual que iniciou no passo anterior.
6. Clique em **Iniciar** para iniciar o runbook. Escreva **VMName** e **ResourceGroupName** na máquina virtual que vai iniciar.

   ![Iniciar o Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Depois de o runbook terminar, verifique se a máquina virtual foi iniciada.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Para começar com runbooks Gráficos, consulte o artigo [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar com runbooks do PowerShell, consulte [O meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md)
* Para saber mais sobre os tipos de runbook, as vantagens e limitações, consulte o artigo [Tipos de runbook da Automatização do Azure](automation-runbook-types.md)
* Para mais informações sobre a funcionalidade de suporte de scripts do PowerShell, consulte o artigo [Suporte de scripts do PowerShell Nativo na Automatização do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
