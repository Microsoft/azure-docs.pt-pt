---
title: Guia de instruções sobre o ajuste automático de notificações por email – banco de dados SQL do Azure | Microsoft Docs
description: Habilitar notificações por email para o ajuste automático de consulta do banco de dados SQL do Azure.
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
ms.openlocfilehash: df9390c00c34fce82de8cc17efb5cc3bce2e4e3d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569437"
---
# <a name="email-notifications-for-automatic-tuning"></a>Notificações por email para ajuste automático

As recomendações de ajuste do banco de dados SQL são geradas pelo [ajuste automático](sql-database-automatic-tuning.md)do banco de dados SQL. Essa solução monitora e analisa continuamente as cargas de trabalho de bancos de dados SQL, fornecendo recomendações de ajuste personalizadas para cada banco de dados individual relacionado à criação de índice, exclusão de índice e otimização de planos de execução de consulta.

As recomendações de ajuste automático do banco de dados SQL podem ser exibidas na [portal do Azure](sql-database-advisor-portal.md), recuperadas com chamadas à [API REST](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor) ou usando comandos [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) e [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) . Este artigo se baseia no uso de um script do PowerShell para recuperar as recomendações de ajuste automático.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>Automatizar notificações por email para recomendações de ajuste automático

A solução a seguir automatiza o envio de notificações por email que contêm recomendações de ajuste automático. A solução descrita consiste em automatizar a execução de um script do PowerShell para recuperar recomendações de ajuste usando a [automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro)e a automação de agendamento de trabalho de entrega de email usando [Microsoft Flow](https://flow.microsoft.com).

## <a name="create-azure-automation-account"></a>Criar conta de automação do Azure

Para usar a automação do Azure, a primeira etapa é criar uma conta de automação e configurá-la com os recursos do Azure a serem usados para a execução do script do PowerShell. Para saber mais sobre a automação do Azure e seus recursos, consulte [introdução à automação do Azure](https://docs.microsoft.com/azure/automation/automation-offering-get-started).

Siga estas etapas para criar uma conta de automação do Azure por meio do método de seleção e configuração do aplicativo de automação no Marketplace:

- Faça logon no portal do Azure
- Clique em " **+ criar um recurso**" no canto superior esquerdo
- Pesquise "**automação**" (pressione Enter)
- Clique no aplicativo de automação nos resultados da pesquisa

![Adicionando a automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- Uma vez dentro do painel "criar uma conta de automação", clique em "**criar**"
- Preencher as informações necessárias: Insira um nome para esta conta de automação, selecione sua ID de assinatura do Azure e os recursos do Azure a serem usados para a execução do script do PowerShell
- Para a opção "**criar conta Executar como do Azure**", selecione **Sim** para configurar o tipo de conta sob o qual o script do PowerShell é executado com a ajuda da automação do Azure. Para saber mais sobre os tipos de conta, confira [conta Executar como](https://docs.microsoft.com/azure/automation/automation-create-runas-account)
- Conclua a criação da conta de automação clicando em **criar**

> [!TIP]
> Registre o nome da sua conta de automação do Azure, a ID da assinatura e os recursos (como copiar e colar em um bloco de notas) exatamente como inserido ao criar o aplicativo de automação. Você precisará dessas informações mais tarde.
>

Se você tiver várias assinaturas do Azure para as quais deseja criar a mesma automação, você precisará repetir esse processo para suas outras assinaturas.

## <a name="update-azure-automation-modules"></a>Atualizar módulos de automação do Azure

O script do PowerShell para recuperar a recomendação de ajuste automático usa os comandos [Get-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Get-azResource) e [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlDatabaseRecommendedAction) para os quais o módulo do Azure versão 4 e superior é necessário.

- Caso os módulos do Azure precisem de atualização, consulte [AZ module support in Azure Automation](../automation/az-modules.md).

## <a name="create-azure-automation-runbook"></a>Criar runbook de automação do Azure

A próxima etapa é criar um runbook na automação do Azure dentro do qual o script do PowerShell para a recuperação de recomendações de ajuste reside.

Siga estas etapas para criar um novo runbook de automação do Azure:

- Acesse a conta de automação do Azure criada na etapa anterior
- Uma vez no painel da conta de automação, clique no item de menu "**Runbooks**" no lado esquerdo para criar um novo runbook de automação do Azure com o script do PowerShell. Para saber mais sobre como criar runbooks de automação, confira [criando um novo runbook](../automation/manage-runbooks.md#create-a-runbook).
- Para adicionar um novo runbook, clique na opção de menu " **+ Adicionar um runbook**" e, em seguida, clique em "**criação rápida – criar um novo runbook**".
- No painel runbook, digite o nome do seu runbook (para fins deste exemplo, "**AutomaticTuningEmailAutomation**" é usado), selecione o tipo de runbook como **PowerShell** e grave uma descrição desse runbook para descrever sua finalidade.
- Clique no botão **criar** para concluir a criação de um novo runbook

![Adicionar runbook de automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

Siga estas etapas para carregar um script do PowerShell dentro do runbook criado:

- Dentro do painel "**Editar runbook do PowerShell**", selecione "**RUNBOOKS**" na árvore de menu e expanda a exibição até ver o nome do runbook (neste exemplo, "**AutomaticTuningEmailAutomation**"). Selecione este runbook.
- Na primeira linha do "Editar runbook do PowerShell" (começando com o número 1), copie-cole o seguinte código de script do PowerShell. Esse script do PowerShell é fornecido como está para você começar. Modifique o script para que o conjunto atenda às suas necessidades.

No cabeçalho do script do PowerShell fornecido, você precisa substituir `<SUBSCRIPTION_ID_WITH_DATABASES>` pela sua ID de assinatura do Azure. Para saber como recuperar sua ID de assinatura do Azure, Confira como [obter seu GUID de assinatura do Azure](https://blogs.msdn.microsoft.com/mschray/20../../getting-your-azure-subscription-guid-new-portal/).

No caso de várias assinaturas, você pode adicioná-las como delimitado por vírgula à propriedade "$subscriptions" no cabeçalho do script.

```powershell
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

Clique no botão "**salvar**" no canto superior direito para salvar o script. Quando estiver satisfeito com o script, clique no botão "**publicar**" para publicar esse runbook.

No painel principal do runbook, você pode optar por clicar no botão "**Iniciar**" para **testar** o script. Clique na "**saída**" para exibir os resultados do script executado. Essa saída será o conteúdo do seu email. A saída de exemplo do script pode ser vista na captura de tela a seguir.

![Executar exibir recomendações de ajuste automático com a automação do Azure](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

Certifique-se de ajustar o conteúdo Personalizando o script do PowerShell para suas necessidades.

Com as etapas acima, o script do PowerShell para recuperar as recomendações de ajuste automático é carregado na automação do Azure. A próxima etapa é automatizar e agendar o trabalho de entrega de email.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Automatizar os trabalhos de email com o Microsoft Flow

Para concluir a solução, como a etapa final, crie um fluxo de automação em Microsoft Flow que consiste em três ações (trabalhos):

1. "**Automação do Azure-criar trabalho**" – usado para executar o script do PowerShell para recuperar as recomendações de ajuste automático dentro do runbook de automação do Azure
2. "**Automação do Azure-obter saída do trabalho**" – usada para recuperar a saída do script do PowerShell executado
3. "**Office 365 Outlook – enviar um email**" – usado para enviar emails. Os emails são enviados usando a conta do Office 365 da pessoa que cria o fluxo.

Para saber mais sobre os recursos de Microsoft Flow, consulte [introdução ao Microsoft Flow](https://docs.microsoft.com/flow/getting-started).

O pré-requisito para esta etapa é inscrever-se para [Microsoft Flow](https://flow.microsoft.com) conta e fazer logon. Uma vez dentro da solução, siga estas etapas para configurar um **novo fluxo**:

- Acessar o item de menu "**meus fluxos**"
- Dentro de meus fluxos, selecione o link " **+ criar de branco**" na parte superior da página
- Clique no link "**Pesquisar por centenas de conectores e gatilhos**" na parte inferior da página
- No campo de pesquisa, digite"recorrência" e selecione "**agenda-recorrência**" nos resultados da pesquisa para agendar a execução do trabalho de entrega de email.
- No painel de recorrência no campo frequência, selecione a frequência de agendamento para execução deste fluxo, como enviar email automatizado a cada minuto, hora, dia, semana, etc.

A próxima etapa é adicionar três trabalhos (criar, obter saída e enviar email) ao fluxo recorrente recém-criado. Para realizar a adição dos trabalhos necessários ao fluxo, siga estas etapas:

1. Criar ação para executar o script do PowerShell para recuperar as recomendações de ajuste

   - Selecione " **+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência
   - No campo de pesquisa, digite "**automação**" e selecione "**automação do Azure – criar trabalho**" nos resultados da pesquisa
   - No painel criar trabalho, configure as propriedades do trabalho. Para essa configuração, você precisará de detalhes de sua ID de assinatura do Azure, grupo de recursos e conta de automação **gravados anteriormente** no **painel da conta de automação**. Para saber mais sobre as opções disponíveis nesta seção, consulte [automação do Azure – criar trabalho](https://docs.microsoft.com/connectors/azureautomation/#create-job).
   - Conclua a criação dessa ação clicando em "**salvar fluxo**"

2. Criar ação para recuperar a saída do script do PowerShell executado

   - Selecione " **+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência
   - Na pesquisa, digite "**automação**" e selecione "**automação do Azure – obter saída do trabalho**" nos resultados da pesquisa. Para saber mais sobre as opções disponíveis nesta seção, consulte [automação do Azure – obter saída de trabalho](https://docs.microsoft.com/connectors/azureautomation/#get-job-output).
   - Preencher campos necessários (semelhante à criação do trabalho anterior) – preencha sua ID de assinatura do Azure, grupo de recursos e conta de automação (conforme inserido no painel de conta de automação)
   - Clique dentro do campo "**ID do trabalho**" para que o menu "**conteúdo dinâmico**" apareça. No menu, selecione a opção "ID do**trabalho**".
   - Conclua a criação dessa ação clicando em "**salvar fluxo**"

3. Criar ação para enviar email usando a integração do Office 365

   - Selecione " **+ nova etapa**", seguida por "**Adicionar uma ação**" dentro do painel fluxo de recorrência
   - Na pesquisa, digite "**enviar um email**" e selecione "**Office 365 Outlook – enviar um email**" nos resultados da pesquisa
   - No campo "**para**", digite o endereço de email para o qual você precisa enviar o email de notificação
   - No campo "**assunto**", digite o assunto do seu email, por exemplo "notificação por email de recomendações de ajuste automático"
   - Clique dentro do campo "**corpo**" para que o menu "**conteúdo dinâmico**" apareça. A partir desse menu, em "**obter saída do trabalho**", selecione "**conteúdo**"
   - Conclua a criação dessa ação clicando em "**salvar fluxo**"

> [!TIP]
> Para enviar emails automatizados para destinatários diferentes, crie fluxos separados. Nesses fluxos adicionais, altere o endereço de email do destinatário no campo "para" e a linha de assunto do email no campo "assunto". A criação de novos runbooks na automação do Azure com scripts personalizados do PowerShell (como, por exemplo, com a ID da assinatura do Azure) permite mais personalização de cenários automatizados, como enviar por email os destinatários separados no ajuste automático recomendações para assinaturas separadas.
>

O procedimento acima conclui as etapas necessárias para configurar o fluxo de trabalho de entrega de email. Todo o fluxo que consiste em três ações criadas é mostrado na imagem a seguir.

![Exibir fluxo de notificações de email de ajuste automático](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

Para testar o fluxo, clique em "**executar agora**" no canto superior direito dentro do painel fluxo.

As estatísticas de execução dos trabalhos automatizados, mostrando o sucesso das notificações por email enviadas, podem ser vistas no painel análise de fluxo.

![Fluxo em execução para o ajuste automático de notificações por email](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

A análise de fluxo é útil para monitorar o sucesso das execuções de trabalho e, se necessário, para solução de problemas.  No caso da solução de problemas, você também pode querer examinar o log de execução de script do PowerShell acessível por meio do aplicativo de automação do Azure.

A saída final do email automatizado é semelhante ao seguinte email recebido após a criação e a execução desta solução:

![Saída de email de exemplo do ajuste automático de notificações por email](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

Ao ajustar o script do PowerShell, você pode ajustar a saída e a formatação do email automatizado às suas necessidades.

Você pode personalizar ainda mais a solução para criar notificações por email com base em um evento de ajuste específico e em vários destinatários, para várias assinaturas ou bancos de dados, dependendo dos cenários personalizados.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como o ajuste automático pode ajudá-lo a melhorar o desempenho do banco de dados, consulte [ajuste automático no banco de dados SQL do Azure](sql-database-automatic-tuning.md).
- Para habilitar o ajuste automático no banco de dados SQL do Azure para gerenciar sua carga de trabalho, consulte [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para examinar e aplicar manualmente as recomendações de ajuste automático, consulte [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
