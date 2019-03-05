---
title: A otimização automática de e-mail guia de procedimentos de notificações - base de dados SQL do Azure | Documentos da Microsoft
description: Ative as notificações de email para o ajuste de consulta automática da base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: f68097f7b97814bc24926b6fc1b0bb2a750855a2
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311275"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações de e-mail para a otimização automática

Base de dados SQL recomendações de otimização são geradas pela base de dados do Azure SQL [otimização automática](sql-database-automatic-tuning.md). Esta solução continuamente monitora e analisa as cargas de trabalho de bases de dados SQL fornecer personalizado recomendações para cada base de dados individual relacionados com a criação de índices, a eliminação de índice e a otimização de planos de execução de consulta de otimização.

SQL da base de dados recomendações de otimização automática pode ser visualizada no [portal do Azure](sql-database-advisor-portal.md), obtido com [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) chama ou através da utilização [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [ PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) comandos. Este artigo baseia-se sobre como usar um script do PowerShell para obter recomendações de otimização automática.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar as notificações por e-mail para recomendações de otimização automática

A seguinte solução automatiza o envio de notificações de e-mail que contém recomendações de otimização automática. A solução descrita consiste em automatizar a execução de um script do PowerShell para obter recomendações de otimização usando [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro), e através da tarefa de entrega de e-mail de automatização de agendamento [Microsoft Flow ](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta de automatização do Azure

Para utilizar a automatização do Azure, a primeira etapa é criar uma conta de automatização e configurá-lo com recursos do Azure a utilizar para a execução do script do PowerShell. Para saber mais sobre a automatização do Azure e as respetivas capacidades, consulte [introdução à automatização do Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Siga estes passos para criar a conta de automatização do Azure através do método de seleção e configurar a aplicação de automação do Marketplace:

- Inicie sessão no portal do Azure
- Clique em "**+ criar um recurso**" no canto superior esquerdo
- Procure por "**automatização**" (prima enter)
- Clique na aplicação de automatização nos resultados da pesquisa

![Adicionar automatização do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Uma vez dentro do painel de "Criar uma conta de automatização", clique em "**criar**"
- Preencher as informações necessárias: introduza um nome para esta conta de automatização, selecione os recursos de ID e o Azure de subscrição do Azure a ser utilizado para a execução de script do PowerShell
- Para a "**criar conta Run do Azure**" opção, selecione **Sim** para configurar o tipo de conta na qual PowerShell script é executado com a ajuda da automatização do Azure. Para saber mais sobre os tipos de conta, veja [conta Run As](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Concluir a criação da conta de automatização ao clicar no **Create**

> [!TIP]
> Registe o seu nome de conta de automatização do Azure, ID de subscrição e recursos (como copiar-colar, para um bloco de notas) exatamente como introduziu ao criar a aplicação de automatização. Mais tarde estas informações são necessárias.
>

Se tiver várias subscrições do Azure para o qual pretende criar a mesma automação, terá de repetir este processo para as suas outras subscrições.

## <a name="update-azure-automation-modules"></a>Atualizar módulos da automatização do Azure

O script do PowerShell para obter a recomendação de otimização automática utiliza [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) comandos para a atualização dos módulos do Azure para a versão 4 e acima, é necessário.

Siga estes passos para atualizar módulos do Azure PowerShell:

- Aceder ao painel de aplicação de automatização e selecione "**módulos**" no menu do lado esquerdo (role para baixo à medida que este item de menu está sob recursos partilhados).
- No painel de módulos, clique em "**atualização módulos do Azure**" na parte superior e aguarde até que é apresentada a mensagem "módulos do Azure foram atualizados". Este processo pode demorar alguns minutos a concluir.

![Atualizar módulos da automatização do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

Versão necessária de necessidades de módulos Azurerm e azurerm. SQL para ser a versão 4 e superior.

## <a name="create-azure-automation-runbook"></a>Criar Runbook de automatização do Azure

A próxima etapa é criar um Runbook na automatização do Azure dentro da qual reside o script do PowerShell para obtenção de recomendações de otimização.

Siga estes passos para criar um novo runbook de automatização do Azure:

- Aceder à conta de automatização do Azure que criou no passo anterior
- Uma vez, no painel de conta de automatização, clique em de "**Runbooks**" item de menu no lado esquerdo para criar um novo runbook de automatização do Azure com o script do PowerShell. Para saber mais sobre como criar runbooks de automatização, veja [criar um novo runbook](../automation/manage-runbooks.md#create-a-runbook).
- Para adicionar um novo runbook, clique em de "**+ adicionar um runbook**" opção de menu e, em seguida, clique no "**rápido criar – criar um runbook novo**".
- No painel do Runbook, digite o nome do runbook (para efeitos deste exemplo, "**AutomaticTuningEmailAutomation**" é utilizado), selecione o tipo de runbook como **PowerShell** e crie uma descrição do Este runbook para descrever a sua finalidade.
- Clique nas **criar** botão para concluir a criação de um novo runbook

![Adicionar runbook da automatização do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Siga estes passos para carregar um script do PowerShell dentro do runbook criado:

- Dentro de "**editar Runbook do PowerShell**"painel, selecione"**RUNBOOKS**" no menu de árvore e expanda a exibição até ver o nome do runbook (neste exemplo " **AutomaticTuningEmailAutomation**"). Selecione este runbook.
- Na primeira linha do "Editar Runbook do PowerShell" (começando com o número 1), copiar-colar o seguinte código de script do PowerShell. Este script do PowerShell é fornecido como-é para começar. Modificar o script para suite suas necessidades.

No cabeçalho do script do PowerShell fornecido, é necessário substituir `<SUBSCRIPTION_ID_WITH_DATABASES>` com o ID de subscrição do Azure. Para saber como obter o seu ID de subscrição do Azure, veja [obter o GUID de subscrição do Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

Em caso de várias subscrições, pode adicioná-los como delimitada por vírgulas para a propriedade de "$subscriptions" no cabeçalho do script.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
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

Clique em de "**guardar**" botão no canto superior direito para guardar o script. Quando estiver satisfeito com o script, clique em de "**publicar**" botão para publicar este runbook.

No painel do runbook principal, pode optar por clique na "**começar**" botão **testar** o script. Clique em de "**saída**" para ver resultados do script foi executado. Esta saída vai ser o conteúdo da mensagem de e-mail. A saída de exemplo do script pode ser vista na seguinte captura de ecrã.

![Recomendações com a automatização do Azure de otimização automática da vista de execução](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Certifique-se para ajustar o conteúdo ao personalizar o script do PowerShell para as suas necessidades.

Com os passos acima, o script do PowerShell para obter recomendações de otimização automática é carregado na automatização do Azure. A próxima etapa é automatizar e agendar a tarefa de entrega de e-mail.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizar as tarefas de e-mail com o Microsoft Flow

Para concluir a solução, como a etapa final, crie um fluxo de automatização no Microsoft Flow consiste em três ações (tarefas):

1. "**Automatização do azure - criar tarefa**" – utilizada para executar o script do PowerShell para obter recomendações dentro do runbook de automatização do Azure de otimização automática
2. "**Automatização do azure - obter Resultado da tarefa**" – utilizado para obter o resultado do script do PowerShell executado
3. "**Office 365 Outlook – enviar um e-mail**" – utilizado para enviar e-mail. Emails são enviados com a conta do Office 365 do indivíduo a criar o fluxo.

Para saber mais sobre as funcionalidades do Microsoft Flow, veja [introdução ao Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

Pré-requisitos para este passo é inscrever-se para obter [Microsoft Flow](https://flow.microsoft.com) conta e iniciar sessão. Uma vez dentro da solução, siga estes passos para configurar uma **novo fluxo**:

- Acesso "**os meus fluxos**" item de menu
- Dentro os meus fluxos, selecione a "**+ criar do zero**" link na parte superior da página
- Clique na ligação "**procure centenas de conectores e acionadores**" na parte inferior da página
- O tipo de campo de pesquisa "**periodicidade**" e selecione "**Schedule - Recurrence**" resultados da pesquisa para agendar a tarefa de entrega de e-mail para ser executado.
- No painel de periodicidade no campo de frequência, selecione a frequência de agendamento para este fluxo executar, como automatizada de envio de e-mail cada minuto, hora, dia, semana, etc.

A próxima etapa é adicionar três tarefas (criar, saída de get e envio de e-mail) para o fluxo periódico recém-criado. Para realizar a adicionar as tarefas necessárias para o fluxo, siga estes passos:

1. Criar ação para executar o script do PowerShell para obter recomendações de otimização

   - Selecione "**+ novo passo**", seguido de"**adicionar uma ação**" dentro do painel de fluxo de periodicidade
   - O tipo de campo de pesquisa "**automatização**"e selecione"**automatização do Azure – criar tarefa**" resultados da pesquisa
   - No painel de trabalho de criar, configure as propriedades da tarefa. Para esta configuração, terá de detalhes da sua subscrição do Azure ID do grupo de recursos e a conta de automatização **gravado anteriormente** com o **painel de conta de automatização**. Para saber mais sobre as opções disponíveis nesta secção, veja [automatização do Azure - criar tarefa](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Concluir a criação desta ação clicando em "**Guardar fluxo**"

2. Criar uma ação para obter o resultado do script do PowerShell executado

   - Selecione "**+ novo passo**", seguido de"**adicionar uma ação**" dentro do painel de fluxo de periodicidade
   - Na pesquisa arquivou o tipo "**automatização**"e selecione"**automatização do Azure – resultado da tarefa Get**" resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta secção, veja [automatização do Azure – resultado da tarefa Get](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Preencher campos necessário (semelhante a criar a tarefa anterior) - preencher a sua subscrição do Azure ID, grupo de recursos e a conta de automatização (tal como foi introduzido no painel de conta de automatização)
   - Clique dentro do campo "**ID da tarefa**" para o "**conteúdo dinâmico**" menu sejam apresentados. Neste menu, selecione a opção "**ID da tarefa**".
   - Concluir a criação desta ação clicando em "**Guardar fluxo**"

3. Criar uma ação para enviar e-mail através de integração do Office 365

   - Selecione "**+ novo passo**", seguido de"**adicionar uma ação**" dentro do painel de fluxo de periodicidade
   - Na pesquisa arquivou o tipo "**enviar um e-mail**"e selecione"**Outlook do Office 365 – enviar um e-mail**" resultados da pesquisa
   - Na "**para**" campo escreva o endereço de e-mail para que precisa enviar o e-mail de notificação
   - Na "**assunto**" no assunto da mensagem de e-mail, por exemplo "recomendações de otimização automática notificação por e-mail" de tipo de campo
   - Clique dentro do campo "**corpo**" para o "**conteúdo dinâmico**" menu sejam apresentados. De dentro neste menu, sob "**obter Resultado da tarefa**", selecione"**conteúdo**"
   - Concluir a criação desta ação clicando em "**Guardar fluxo**"

> [!TIP]
> Para enviar correio eletrónico automatizado para destinatários diferentes, crie fluxos separados. Estes fluxos adicionais, altere o endereço de e-mail do destinatário no campo "Para" e a linha de assunto de e-mail no campo "Assunto". Scripts do PowerShell personalizados, criar novos runbooks na automatização do Azure com (por exemplo, com a alteração do ID de subscrição do Azure) permite a personalização adicional de cenários automatizados, como, por exemplo é como envio por email separados destinatários sintonização automática recomendações para assinaturas separadas.
>

Acima-conclui os passos necessários para configurar o fluxo de trabalho de tarefa de entrega de e-mail. Todo o fluxo consiste em três ações criadas é mostrado na imagem seguinte.

![Vista de fluxo de notificações de e-mail de otimização automática](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Para testar o fluxo, clique em "**executar agora**" no canto superior direito dentro do painel de fluxo.

Estatísticas da execução de tarefas automáticas, que mostra o êxito de notificações por e-mail enviadas, podem ser vistas a partir do painel de análise de fluxo.

![Executar fluxo para notificações de e-mail de otimização automática](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A análise de fluxo é útil para monitorizar o êxito de execuções de tarefas e, se necessário para resolução de problemas.  No caso de resolução de problemas, pode também querer examinar o registo de execução de script de PowerShell acessível através da aplicação de automatização do Azure.

O resultado final da mensagem de e-mail automatizado é semelhante para o seguinte e-mail recebido após criar e executar esta solução:

![Saída de e-mail de exemplo de notificações de e-mail de otimização automática](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Ao ajustar o script do PowerShell, pode ajustar a saída e formatação da mensagem de e-mail automatizado para as suas necessidades.

Pode personalizar ainda mais a solução para criar notificações de e-mail com base num evento específico de Otimização e para vários destinatários, para várias subscrições ou bases de dados, dependendo dos cenários personalizados.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como a otimização automática pode ajudá-lo a melhorar o desempenho da base de dados, consulte [otimização automática na base de dados do Azure SQL](sql-database-automatic-tuning.md).
- Para ativar a otimização automática na base de dados do Azure SQL para gerir a sua carga de trabalho, consulte [ativar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Analisar manualmente e aplicar recomendações de otimização automática, consulte [localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
