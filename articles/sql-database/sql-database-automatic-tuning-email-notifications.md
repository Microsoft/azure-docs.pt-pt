---
title: Notificações de email de afinação automática como orientar
description: Ativar notificações de e-mail para afinação automática de consultas automáticas azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: b3b235833e794e48ae655d184bf938effc0d7ac0
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768373"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações por e-mail para afinação automática

As recomendações de afinação da Base de Dados SQL são geradas pela [afinação automática](sql-database-automatic-tuning.md)da Base de Dados Azure SQL . Esta solução monitoriza e analisa continuamente as cargas de trabalho das Bases de Dados SQL, fornecendo recomendações de afinação personalizadas para cada base de dados relacionada com a criação de índices, a eliminação de índices e a otimização dos planos de execução de consultas.

Base de Dados SQL Recomendações de afinação automática podem ser vistas no [portal Azure,](sql-database-advisor-portal.md)recuperados com chamadas [REST API,](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) ou utilizando comandos [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [PowerShell.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) Este artigo baseia-se na utilização de um script PowerShell para recuperar recomendações de afinação automática.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar notificações de e-mail para recomendações de afinação automática

A seguinte solução automatiza o envio de notificações por e-mail contendo recomendações de afinação automática. A solução descrita consiste em automatizar a execução de um script PowerShell para recuperar recomendações de afinação usando [a Automação Azure](https://docs.microsoft.com/azure/automation/automation-intro), e automatização de agendamento de trabalho de entrega de e-mail usando [o Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta de Automação Azure

Para utilizar a Automação Azure, o primeiro passo é criar uma conta de automação e configurá-la com recursos Azure para usar para a execução do script PowerShell. Para saber mais sobre a Automação Azure e as suas capacidades, consulte [Getting started with Azure automation](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Siga estes passos para criar a Conta de Automação Azure através do método de seleção e configuração da aplicação Automation a partir do Marketplace:

1. Inicie sessão no portal do Azure.
1. Clique em "**+ Criar um recurso**" no canto superior esquerdo.
1. Procure por "**Automação**" (pressione a entrada).
1. Clique na aplicação Automation nos resultados da pesquisa.

    ![Adicionar automação Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

1. Uma vez dentro do painel "Criar uma Conta de Automação", clique em "**Criar**".
1. Preencha as informações necessárias: insira um nome para esta conta de automação, selecione o seu ID de subscrição Azure e os recursos Azure para ser usado para a execução do script PowerShell.
1. Para a opção "**Create Azure Run As account**", selecione **Sim** para configurar o tipo de conta sob a qual o script PowerShell funciona com a ajuda da Automação Azure. Para saber mais sobre os tipos de conta, consulte [Executar Como conta](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
1. Concluir a criação da conta de automação clicando na **Create**.

> [!TIP]
> Grave o nome da sua conta Azure Automation, ID de subscrição e recursos (como copiar-pasta para um bloco de notas) exatamente como introduzido enquanto cria a aplicação Automation. Precisa desta informação mais tarde.
>

Se tem várias subscrições Do Azure para as quais gostaria de construir a mesma automatização, precisa repetir este processo para as suas outras subscrições.

## <a name="update-azure-automation-modules"></a>Atualizar módulos de Automação Azure

O script PowerShell para recuperar a recomendação de afinação automática utiliza comandos [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) para os quais o Módulo Azure versão 4 ou superior é necessário.

- Caso os seus Módulos Azure precisem de ser atualizados, consulte o [suporte do módulo Az na Automação Azure](../automation/shared-resources/modules.md).

## <a name="create-azure-automation-runbook"></a>Criar o Livro de Execução de Automação Azure

O próximo passo é criar um Livro de Corridas em Automação Azure no interior do qual reside o script PowerShell para a recuperação de recomendações de afinação.

Siga estes passos para criar um novo livro de execução da Automação Azure:

1. Aceda à conta Azure Automation que criou no passo anterior.
1. Uma vez no painel de conta de automação, clique no item do menu "**Runbooks**" no lado esquerdo para criar um novo livro de execução da Automação Azure com o script PowerShell. Para saber mais sobre a criação de livros de automação, consulte Criar um novo livro de [corridas.](../automation/manage-runbooks.md#creating-a-runbook)
1. Para adicionar um novo livro de corridas, clique na opção de menu "**+Adicionar um livro**de execução " e, em seguida, clique na "**Criação rápida – Criar um novo livro**de execução "..
1. No painel Runbook, escreva em nome do seu livro de execução (para efeitos deste exemplo, "**AutomaticTuningEmailAutomation**" é usado), selecione o tipo de livro como **PowerShell** e escreva uma descrição deste livro de execução para descrever o seu propósito.
1. Clique no botão **Criar** para terminar a criação de um novo livro de corridas.

    ![Adicione o livro de execução de automação Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Siga estes passos para carregar um script PowerShell dentro do livro de execução criado:

1. Dentro do painel "**Editar PowerShell Runbook**", selecione "**RUNBOOKS**" na árvore do menu e expanda a vista até ver o nome do seu livro de execução (neste exemplo "**Automatização AutomáticaDeEmailEmail**"). Selecione este livro de execução.
1. Na primeira linha do "Edit PowerShell Runbook" (começando pelo número 1), copie-colar o seguinte código de script PowerShell. Este script PowerShell é fornecido como é para começar. Modifique o guião para acasalar as suas necessidades.

No cabeçalho do script PowerShell fornecido, `<SUBSCRIPTION_ID_WITH_DATABASES>` tem de substituir pelo seu ID de subscrição Azure. Para aprender a recuperar o seu ID de subscrição Azure, consulte Obter o seu GUIA de [Subscrição Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Em caso de várias subscrições, pode adicioná-las como comma-delimitada à propriedade "$subscriptions" no cabeçalho do script.

```powershell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
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

                # Loop through all Automatic tuning recommendation types
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

Clique no botão **"Guardar"** no canto superior direito para guardar o script. Quando estiver satisfeito com o script, clique no botão "**Publicar**" para publicar este livro de execução.

No painel principal do livro de corridas, pode optar por clicar no botão "**Iniciar**" para **testar** o script. Clique na "**Output**" para ver os resultados do script executado. Esta saída será o conteúdo do seu e-mail. A saída da amostra do script pode ser vista na seguinte imagem.

![Executar vista recomendações de afinação automática com Automação Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Certifique-se de ajustar o conteúdo personalizando o script PowerShell às suas necessidades.

Com os passos acima, o script PowerShell para recuperar recomendações de afinação automática é carregado na Automação Azure. O próximo passo é automatizar e agendar o trabalho de entrega de e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizar os trabalhos de e-mail com o Microsoft Flow

Para completar a solução, como passo final, criar um fluxo de automação no Microsoft Flow composto por três ações (empregos):

 - "**Azure Automation - Create job**" – usado para executar o script PowerShell para recuperar recomendações de afinação automática dentro do livro de execução da Automação Azure.
 - "**Azure Automation - Get job output**" – usado para recuperar a saída do script PowerShell executado.
 - "**Office 365 Outlook – Envie um e-mail**" – usado para enviar e-mail. Os e-mails são enviados usando a conta do Office 365 do indivíduo que cria o fluxo.

Para saber mais sobre as capacidades do Microsoft Flow, consulte [Getting started with Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

O pré-requisito para este passo é inscrever-se na conta [Microsoft Flow](https://flow.microsoft.com) e fazer login. Uma vez dentro da solução, siga estes passos para configurar um **novo fluxo:**

1. Access "**My flows**" menu item.
1. Dentro dos meus fluxos, selecione o link "**+Create from blank**" no topo da página.
1. Clique no link "**Procure centenas de conectores e gatilhos**" na parte inferior da página.
1. No tipo de campo de pesquisa "**recurrence**", e selecionar "**Agenda - Recorrência**" dos resultados da pesquisa para agendar o trabalho de entrega de e-mail para executar.
1. No painel de recorrência no campo de Frequência, selecione a frequência de agendamento para este fluxo executar, como enviar e-mail automatizado a cada Minuto, Hora, Dia, Semana, etc.

O próximo passo é adicionar três postos de trabalho (criar, obter produção e enviar e-mails) para o fluxo recorrente recém-criado. Para conseguir adicionar os empregos necessários ao fluxo, siga estes passos:

1. Crie ação para executar script PowerShell para recuperar recomendações de afinação

   - Selecione "**+Novo passo**", seguido de "**Adicionar uma ação**" dentro do painel de fluxo de Recorrência.
   - No tipo de campo de pesquisa "**automação**" e selecione "**Automação Azure – Criar trabalho**" a partir dos resultados da pesquisa.
   - No painel de trabalho Criar, configure as propriedades de trabalho. Para esta configuração, necessitará de detalhes do id de subscrição do Azure, do Grupo de Recursos e da Conta de Automação **anteriormente registado** seleção no **painel da Conta de Automação.** Para saber mais sobre as opções disponíveis nesta secção, consulte [A Automação Azure - Create Job](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Complete a criação desta ação clicando em "**Salvar o fluxo**".

2. Criar ação para recuperar a saída do script PowerShell executado

   - Selecione "**+Novo passo**", seguido por "**Adicionar uma ação**" dentro do painel de fluxo de recorrência
   - Na pesquisa arquivada "**automação**" e selecione "**Automação Azure – Obtenha saída**de trabalho " dos resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta secção, consulte [A Automatização Do Azure – Obtenha saída](https://docs.microsoft.com/connectors/azureautomation/#get-job-output)de emprego .
   - Povoar os campos necessários (semelhantes à criação do trabalho anterior) - preencha o id de subscrição do Azure, o Grupo de Recursos e a Conta de Automação (conforme inserido no painel da Conta de Automação).
   - Clique dentro do campo "**Job ID**" para o menu "**Conteúdo Dinâmico**" para aparecer. A partir deste menu, selecione a opção "**JOB ID**".
   - Complete a criação desta ação clicando em "**Salvar o fluxo**".

3. Criar medidas para enviar e-mail usando a integração do Office 365

   - Selecione "**+Novo passo**", seguido de "**Adicionar uma ação**" dentro do painel de fluxo de Recorrência.
   - No tipo de pesquisa arquivado "**enviar um e-mail**" e selecionar "**Office 365 Outlook – Enviar um e-mail**" dos resultados da pesquisa.
   - No tipo de campo "**Para**" no endereço de e-mail para o qual necessita de enviar o e-mail de notificação.
   - No tipo de campo "**Assunto**" no tema do seu e-mail, por exemplo , "Notificação automática de recomendações de afinação de e-mail".
   - Clique dentro do campo "**Body**" para o menu "**Conteúdo Dinâmico**" para aparecer. A partir deste menu, em "**Obter saída**de trabalho ", selecione "**Conteúdo**".
   - Complete a criação desta ação clicando em "**Salvar o fluxo**".

> [!TIP]
> Para enviar e-mails automatizados para diferentes destinatários, crie fluxos separados. Nestes fluxos adicionais, altere o endereço de e-mail do destinatário no campo "To" e a linha de assunto de e-mail no campo "Assunto". A criação de novos livros de execução na Azure Automation com scripts PowerShell personalizados (como por exemplo com a mudança de ID de subscrição do Azure) permite uma maior personalização de cenários automatizados, como por exemplo, enviar destinatários separados em recomendações de afinação automatizada para subscrições separadas.
>

Isto conclui as etapas necessárias para configurar o fluxo de trabalho de entrega de e-mail. Todo o fluxo constituído por três ações construídas é mostrado na imagem seguinte.

![Ver fluxo automático de notificações de e-mail de afinação](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Para testar o fluxo, clique em "**Run Now**" no canto superior direito dentro do painel de fluxo.

As estatísticas de funcionamento dos postos de trabalho automatizados, mostrando o sucesso das notificações de e-mail enviadas, podem ser vistas a partir do painel de análise flow.

![Fluxo de execução para notificações automáticas de e-mail de afinação](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A análise do Flow é útil para monitorizar o sucesso das execuções de emprego, e se necessário para resolução de problemas.  Em caso de resolução de problemas, também poderá querer examinar o registo de execução de scripts PowerShell acessível através da aplicação Azure Automation.

A saída final do e-mail automatizado é semelhante ao seguinte e-mail recebido após a construção e execução desta solução:

![Saída de e-mail de amostra de notificações automáticas de e-mail de afinação](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Ao ajustar o script PowerShell, pode ajustar a saída e formatação do e-mail automatizado às suas necessidades.

Poderá ainda personalizar a solução para construir notificações de e-mail com base num evento de afinação específico, e a vários destinatários, para várias subscrições ou bases de dados, dependendo dos seus cenários personalizados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como a sintonização automática pode ajudá-lo a melhorar o desempenho da base de dados, consulte [a sintonização automática na Base de Dados Azure SQL](sql-database-automatic-tuning.md).
- Para permitir a sintonização automática na Base de Dados Azure SQL para gerir a sua carga de trabalho, consulte [Afinação automática .](sql-database-automatic-tuning-enable.md)
- Para rever manualmente e aplicar recomendações de afinação automática, consulte [encontrar e aplicar recomendações](sql-database-advisor-portal.md)de desempenho .
