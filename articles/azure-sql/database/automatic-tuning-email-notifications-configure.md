---
title: Notificações automáticas de e-mail como guiar
description: Ativar as notificações por e-mail para a afinação automática da Azure SQL Database.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 06/03/2019
ms.openlocfilehash: cdc43add2e57e45a32b0b42ead8c3107d93c193f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675243"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações por e-mail para afinação automática
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


As recomendações de afinação da base de dados Azure SQL são geradas pela [afinação automática](automatic-tuning-overview.md)da Base de Dados Azure SQL . Esta solução monitoriza e analisa continuamente as cargas de trabalho das bases de dados que fornecem recomendações de sintonização personalizadas para cada base de dados relacionadas com a criação de índices, eliminação de índices e otimização de planos de execução de consultas.

As recomendações de afinação automática da Base de Dados Azure SQL podem ser vistas no [portal Azure,](database-advisor-find-recommendations-portal.md)recuperadas com chamadas [DE API](/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) rest, ou utilizando comandos [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [PowerShell.](/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Este artigo baseia-se na utilização de um script PowerShell para recuperar recomendações de sintonização automática.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar notificações de email para recomendações de afinação automática

A seguinte solução automatiza o envio de notificações por e-mail contendo recomendações de afinação automática. A solução descrita consiste em automatizar a execução de um script PowerShell para recuperar recomendações de afinação utilizando [a Azure Automation,](../../automation/automation-intro.md)e automatização de agendamento de tarefas de entrega de e-mails utilizando [o Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta Azure Automation

Para utilizar a Azure Automation, o primeiro passo é criar uma conta de automação e configugá-la com recursos Azure para usar para a execução do script PowerShell. Para saber mais sobre a Azure Automation e as suas capacidades, consulte [Começar com a automação Azure.](../../automation/index.yml)

Siga estes passos para criar uma Conta de Automação Azure através do método de seleção e configuração de uma aplicação de Automação do Azure Marketplace:

1. Inicie sessão no portal do Azure.
1. Clique em " **+ Crie um recurso** " no canto superior esquerdo.
1. Procure por " **Automação** " (prima para entrar).
1. Clique na aplicação Automation nos resultados da pesquisa.

    ![Adicionar automatização Azure](./media/automatic-tuning-email-notifications-configure/howto-email-01.png)

1. Uma vez dentro do painel "Criar uma Conta de Automação", clique em " **Criar** ".
1. Preencha as informações necessárias: introduza um nome para esta conta de automação, selecione o ID de subscrição Azure e os recursos Azure para serem utilizados para a execução do script PowerShell.
1. Para a opção " **Create Azure Run As account** ", selecione **Sim** para configurar o tipo de conta sob a qual o script PowerShell funciona com a ajuda da Azure Automation. Para saber mais sobre tipos de conta, consulte [Run As account](../../automation/manage-runas-account.md).
1. Concluir a criação da conta de automação clicando no **Create.**

> [!TIP]
> Grave o nome da sua conta Azure Automation, ID de subscrição e recursos (como copiar para um bloco de notas) exatamente como introduzido enquanto cria a app Automation. Precisa desta informação mais tarde.

Se tiver várias subscrições Azure para as quais gostaria de construir a mesma automatização, precisa de repetir este processo para as suas outras subscrições.

## <a name="update-azure-automation-modules"></a>Atualizar módulos de Automação Azure

O script PowerShell para recuperar a recomendação de sintonização automática utiliza comandos [Get-AzResource](/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) para os quais é necessária a versão 4 e superior do Módulo Azure.

- Caso os seus Módulos Azure precisem de ser atualizados, consulte [o suporte do módulo Az na Azure Automation](../../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Criar o runbook da Azure Automation

O próximo passo é criar um Runbook na Azure Automation no interior do qual reside o script PowerShell para a recuperação das recomendações de afinação.

Siga estes passos para criar um novo runbook da Azure Automation:

1. Aceda à conta Azure Automation que criou no escalão anterior.
1. Uma vez no painel de conta de automatização, clique no item do menu " **Runbooks** " no lado esquerdo para criar um novo livro de bordo da Azure Automation com o script PowerShell. Para saber mais sobre a criação de livros de automação, consulte [Criar um novo runbook.](../../automation/manage-runbooks.md#create-a-runbook)
1. Para adicionar um novo runbook, clique na opção de menu " **+Add a runbook** " e, em seguida, clique na " **Quick create – Criar um novo runbook** "..
1. No painel Runbook, escreva o nome do seu runbook (para efeitos deste exemplo, " **AutomaticTuningEmailAutomation** " é usado), selecione o tipo de livro de execução como **PowerShell** e escreva uma descrição deste livro para descrever o seu propósito.
1. Clique no botão **Criar** para terminar a criação de um novo runbook.

    ![Adicionar o runbook de automação Azure](./media/automatic-tuning-email-notifications-configure/howto-email-03.png)

Siga estes passos para carregar um script PowerShell dentro do livro criado:

1. Dentro do painel " **Editar PowerShell Runbook** ", selecione " **RUNBOOKS** " na árvore do menu e expanda a vista até ver o nome do seu runbook (neste exemplo " **AutomaticTuningEmailAutomation** "). Selecione este livro de execução.
1. Na primeira linha do "Editar PowerShell Runbook" (a começar pelo número 1), copie o seguinte código de script PowerShell. Este script PowerShell é fornecido como é para começar. Modifique o script para a sua adaptação às suas necessidades.

No cabeçalho do script PowerShell fornecido, precisa de substituir `<SUBSCRIPTION_ID_WITH_DATABASES>` pelo seu ID de subscrição Azure. Para aprender a recuperar o seu ID de subscrição Azure, consulte [obter o seu Azure Subscription GUID](/archive/blogs/mschray/getting-your-azure-subscription-guid-new-portal).

No caso de várias subscrições, pode adicioná-las como vírgula-delimitada à propriedade "$subscriptions" no cabeçalho do script.

```powershell
# PowerShell script to retrieve Azure SQL Database automatic tuning recommendations.
#
# Provided "as-is" with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {
    Select-AzSubscription -SubscriptionId $subscriptionId
    $rgs = Get-AzResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzResource -ResourceGroupName $rgname -ResourceType $resourceType

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

Clique no botão " **Guardar** " no canto superior direito para guardar o script. Quando estiver satisfeito com o script, clique no botão " **Publicar** " para publicar este livro de execução.

No painel de runbook principal, pode optar por clicar no botão **"Iniciar"** para **testar** o script. Clique na " **Saída** " para ver os resultados do script executado. Esta saída será o conteúdo do seu e-mail. A saída da amostra do script pode ser vista na imagem seguinte.

![Executar ver recomendações de afinação automática com Azure Automation](./media/automatic-tuning-email-notifications-configure/howto-email-04.png)

Certifique-se de ajustar o conteúdo personalizando o script PowerShell às suas necessidades.

Com os passos acima, o script PowerShell para recuperar recomendações de sintonização automática é carregado na Azure Automation. O próximo passo é automatizar e agendar o trabalho de entrega de e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatize os trabalhos de e-mail com o Microsoft Flow

Para completar a solução, como passo final, criar um fluxo de automatização no Microsoft Flow composto por três ações (empregos):

- " **Azure Automation - Create job** " – usado para executar o script PowerShell para recuperar recomendações de sintonização automática dentro do runbook Azure Automation.
- " **Azure Automation - Get job output** " – usado para recuperar a saída do script powerShell executado.
- " **Office 365 Outlook – Enviar um e-mail** " - usado para enviar e-mail. Os e-mails são enviados utilizando o trabalho ou a conta escolar do indivíduo que cria o fluxo.

Para saber mais sobre as capacidades do Microsoft Flow, consulte [Começar com o Microsoft Flow](/flow/getting-started).

O pré-requisito para este passo é inscrever-se numa conta [do Microsoft Flow](https://flow.microsoft.com) e fazer login. Uma vez dentro da solução, siga estes passos para configurar um **novo fluxo:**

1. Aceder " **Os meus fluxos** " item do menu.
1. Dentro dos meus fluxos, selecione o link " **+Create a partir de branco** " no topo da página.
1. Clique no link " **Search for hundreds of connectors and triggers** " na parte inferior da página.
1. No tipo de campo de pesquisa " **recorrência** ", e selecione " **Agendar - Recorrência** " dos resultados da pesquisa para agendar o trabalho de entrega de e-mail a executar.
1. No painel de Recorrência no campo Frequência, selecione a frequência de agendamento para este fluxo a executar, como enviar e-mail automatizado a cada Minuto, Hora, Dia, Semana, etc.

O próximo passo é adicionar três postos de trabalho (criar, obter produção e enviar e-mails) ao fluxo recorrente recém-criado. Para conseguir adicionar os trabalhos necessários ao fluxo, siga estes passos:

1. Criar ação para executar o script PowerShell para recuperar recomendações de sintonização

   - Selecione " **+Novo passo** ", seguido de " **Adicionar uma ação** " dentro do painel de fluxo de recorrência.
   - No tipo de campo de pesquisa " **automação** " e selecione " **Azure Automation – Create job** " a partir dos resultados da pesquisa.
   - No painel de trabalho Create, configurar as propriedades do trabalho. Para esta configuração, necessitará de detalhes do ID de subscrição Azure, Grupo de Recursos e Conta de Automação **previamente registados** no **painel de Conta de Automação.** Para saber mais sobre as opções disponíveis nesta secção, consulte [Azure Automation - Create Job](/connectors/azureautomation/#create-job).
   - Complete a criação desta ação clicando em " **Save flow** ".

2. Criar uma ação para recuperar a saída do script powerShell executado

   - Selecione " **+Novo passo** ", seguido de " **Adicionar uma ação** " dentro do painel de fluxo de recorrência
   - No tipo de campo de pesquisa " **automação** " e selecione " **Azure Automation – Get job output** " a partir dos resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta secção, consulte [Azure Automation – Obtenha a saída de emprego.](/connectors/azureautomation/#get-job-output)
   - Os campos de povoação necessários (semelhantes à criação do trabalho anterior) - povoam o ID de subscrição Azure, o Grupo de Recursos e a Conta de Automação (conforme introduzido no painel de Conta de Automação).
   - Clique no interior do campo " **Job ID** " para que o menu de **conteúdo** dinâmico apareça. A partir deste menu, selecione a opção " **Job ID** ".
   - Complete a criação desta ação clicando em " **Save flow** ".

3. Criar uma ação para enviar e-mail usando a integração do Office 365

   - Selecione " **+Novo passo** ", seguido de " **Adicionar uma ação** " dentro do painel de fluxo de recorrência.
   - No tipo de campo de pesquisa " **enviar um e-mail** " e selecione " **Office 365 Outlook – Enviar um e-mail** " a partir dos resultados da pesquisa.
   - No tipo de campo " **Para** " para o endereço de e-mail para o qual precisa enviar o e-mail de notificação.
   - No tipo de campo " **Assunto** " no tema do seu e-mail, por exemplo , "Afinação automática de recomendações por e-mail".
   - Clique no interior do campo " **Body** " para que o menu " **Conteúdo dinâmico** " apareça. A partir deste menu, em " **Obter saída de emprego** ", selecione " **Conteúdo** ".
   - Complete a criação desta ação clicando em " **Save flow** ".

> [!TIP]
> Para enviar e-mails automatizados para diferentes destinatários, crie fluxos separados. Nestes fluxos adicionais, altere o endereço de e-mail do destinatário no campo "To" e a linha de assunto de e-mail no campo "Assunto". A criação de novos runbooks na Azure Automation com scripts PowerShell personalizados (como com a mudança do ID de subscrição Azure) permite uma maior personalização de cenários automatizados, como por exemplo, enviar e-mails a destinatários separados em recomendações de sintonização automatizada para subscrições separadas.

O acima referido conclui os passos necessários para configurar o fluxo de trabalho de trabalho de entrega de e-mail. Todo o fluxo constituído por três ações construídas é mostrado na imagem seguinte.

![Ver fluxo de notificações de email de afinação automática](./media/automatic-tuning-email-notifications-configure/howto-email-05.png)

Para testar o fluxo, clique em " **Run Now** " no canto superior direito dentro do painel de fluxo.

As estatísticas de execução dos trabalhos automatizados, mostrando o sucesso das notificações de email enviadas, podem ser vistas a partir do painel de análise do Flow.

![Fluxo de execução para notificações automáticas de e-mail de afinação](./media/automatic-tuning-email-notifications-configure/howto-email-06.png)

O painel de análise flow é útil para monitorizar o sucesso das execuções de empregos, e se necessário para a resolução de problemas.  No caso de resolução de problemas, também é possível que queira examinar o registo de execução do script PowerShell acessível através da aplicação Azure Automation.

A saída final do e-mail automatizado é semelhante ao seguinte e-mail recebido após a construção e execução desta solução:

![Saída de e-mail de amostra de notificações automáticas de email](./media/automatic-tuning-email-notifications-configure/howto-email-07.png)

Ao ajustar o script PowerShell, pode ajustar a saída e a formatação do e-mail automatizado às suas necessidades.

Poderá ainda personalizar a solução para construir notificações de email com base num evento específico de afinação e em vários destinatários, para várias subscrições ou bases de dados, dependendo dos seus cenários personalizados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como a sintonização automática pode ajudá-lo a melhorar o desempenho da base de dados, ver [sintonização automática na Base de Dados Azure SQL](automatic-tuning-overview.md).
- Para ativar a sintonização automática na Base de Dados Azure SQL para gerir a sua carga de trabalho, consulte [Ativar a sintonização automática](automatic-tuning-enable.md).
- Para rever manualmente e aplicar recomendações de afinação automática, consulte [Localizar e aplicar recomendações de desempenho](database-advisor-find-recommendations-portal.md).