---
title: Tutorial - Criar e gerir orçamentos do Azure
description: Este tutorial ajuda-o a planear e a explicar os custos dos serviços Azure que consome.
author: bandersmsft
ms.author: banders
ms.date: 03/09/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 2c0853b68e432fa94e661e68486b9e58b21aa845
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012452"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: Criar e gerir orçamentos do Azure

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Ajudam-no a informar os outros sobre os custos para que possam proativamente gerir os custos e para monitorizar o progresso dos custos ao longo do tempo. Pode configurar alertas com base no seu custo real ou custo previsto para garantir que o seu gasto está dentro do seu limite de gastos organizacionais. Quando os limiares orçamentais que criou são excedidos, apenas são acionadas as notificações. Nenhum dos seus recursos é afetado e o consumo não será suspenso. Pode utilizar os orçamentos para comparar e acompanhar as despesas enquanto analisa os custos.

Os dados de custo e utilização estão normalmente disponíveis dentro de 8 a 24 horas e os orçamentos são avaliados em relação a estes custos a cada 24 horas. Confirme que se familiariza com as especificidades das [Atualizações de dados dos custos e da utilização](./understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Quando um limiar do orçamento é atingido, são normalmente enviadas notificações por e-mail num prazo de uma hora após a avaliação.

Os orçamentos são repostos automaticamente no final de um período (mensal, trimestral ou anual) com a mesma quantidade de orçamento quando seleciona uma data de expiração no futuro. Uma vez que os orçamentos são repostos com a mesma quantidade de orçamento, tem de criar orçamentos separados quando os montantes orçamentados diferirem para períodos futuros. Quando um orçamento expira, é automaticamente apagado.

Os exemplos mostrados neste tutorial irão guiá-lo através da criação e edição de um orçamento para uma subscrição EA (Contrato Enterprise).

Veja o vídeo [Apply budgets to subscriptions using the Azure portal](https://www.youtube.com/watch?v=UrkHiUx19Po) (Aplicar orçamentos a subscrições com o portal do Azure) para ver como pode criar orçamentos no Azure para monitorizar os custos. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Criar e editar orçamentos com o PowerShell
> * Criar um orçamento com um modelo do Azure Resource Manager

## <a name="prerequisites"></a>Pré-requisitos

São suportados orçamentos para os seguintes tipos e âmbitos de conta do Azure:

- Âmbitos do controlo de acesso baseado em funções do Azure (RBAC do Azure)
    - Grupos de gestão
    - Subscrição
- Âmbitos do Contrato Enterprise
    - Conta de faturação
    - Departamento
    - Conta de inscrição
- Contratos individuais
    - Conta de faturação
- Âmbitos do Contrato de Cliente Microsoft
    - Conta de faturação
    - Perfil de faturação
    - Secção de fatura
    - Cliente
- Âmbitos do AWS
    - Conta externa
    - Subscrição externa


Para ver os orçamentos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure.

Se tiver uma subscrição nova, não pode criar imediatamente um orçamento nem utilizar outras funcionalidades do Cost Management. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

No caso de subscrições EA, tem de ter acesso de leitura para ver os orçamentos. Para criar e gerir orçamentos, tem de ter permissão de contribuidor.

As seguintes permissões, ou âmbitos, do Azure são suportadas por subscrição para os orçamentos por utilizador e grupo. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Contribuidor e contribuidor do Cost Management: podem criar, modificar ou eliminar os orçamentos deles. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor e leitor do Cost Management: podem ver os orçamentos para os quais têm permissões.

Para obter mais informações sobre a atribuição da permissão para os dados do Cost Management, veja [Atribuir acesso aos dados do Cost Management](./assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Pode criar um orçamento para a subscrição do Azure por período mensal, semestral ou anual.

Para criar ou ver um orçamento, abra um âmbito no portal Azure e selecione **Orçamentos** no menu. Por exemplo, navegue para **Subscrições**, selecione uma subscrição na lista e, em seguida, selecione **Orçamentos** no menu. Em Orçamentos, utilize o atalho **Âmbito** para mudar para um âmbito diferente, como um grupo de gestão. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

Depois de criar os orçamentos, mostrarão uma vista simples dos gastos atuais.

Selecione **Adicionar**.

:::image type="content" source="./media/tutorial-acm-create-budgets/budgets-cost-management.png" alt-text="Screenthost mostrando uma lista de orçamentos já criados." lightbox="./media/tutorial-acm-create-budgets/budgets-cost-management.png" :::

Na janela **Criar orçamento**, confirme que o âmbito mostrado é o correto. Escolha os filtros que quer adicionar. Os filtros permitem-lhe criar orçamentos para custos específicos, como grupos de recursos numa subscrição ou um serviço como máquinas virtuais. Qualquer filtro que utilize na análise de custos pode também ser aplicado a um orçamento.

Depois de identificar o seu âmbito e filtros, escreva um nome de orçamento. Em seguida, escolha um período de reposição de orçamento (mensal, trimestral ou anual). O período de reset determina a janela de tempo analisada pelo orçamento. O custo avaliado pelo orçamento começa do zero a cada início de um novo período. Quando cria um orçamento trimestral, este funciona da mesma maneira que um orçamento mensal. A diferença está no facto de o montante do orçamento do trimestre ser equitativamente repartido entre os três meses desse trimestre. O montante de um orçamento anual é equitativamente repartido entre os 12 meses do ano do calendário.

Se tiver uma subscrição Pay As You Go, MSDN ou do Visual Studio, o período de faturação poderá não estar alinhado com o mês do calendário. Para esses tipos de subscrições e grupos de recursos, pode criar um orçamento que esteja alinhado com o seu período de faturação ou com os meses do calendário. Para criar um orçamento alinhado com o período de faturação, selecione um período de reposição: **Mês de faturação**, **Trimestre de faturação** ou **Ano de faturação**. Para criar um orçamento alinhado com o mês do calendário, selecione um período de reposição: **Mensal**, **Trimestral** ou **Anual**.

Em seguida, identifique a data de expiração quando o orçamento deixará de ser válido e deixará de avaliar os custos.

Com base nos campos escolhidos no orçamento até agora, é mostrado um gráfico para o ajudar a selecionar um limiar a utilizar no orçamento. O orçamento sugerido baseia-se no custo previsto mais elevado em que poderá incorrer em períodos futuros. Pode alterar o montante do orçamento.

:::image type="content" source="./media/tutorial-acm-create-budgets/create-monthly-budget.png" alt-text="Screenshot mostrando criação de orçamento com dados mensais de custos." lightbox="./media/tutorial-acm-create-budgets/create-monthly-budget.png" :::

Depois de configurar o valor do orçamento, selecione **Próximo** para configurar alertas orçamentais para custos reais e alertas orçamentais previstos.

## <a name="configure-actual-costs-budget-alerts"></a>Configurar alertas orçamentais de custos reais

Os orçamentos requerem, no mínimo, um limiar de custo (% do orçamento) e um endereço de e-mail correspondente. Pode incluir, opcionalmente, até cinco limiares e cinco endereços de e-mail num único orçamento. Quando um limiar do orçamento é atingido, são normalmente enviadas notificações por e-mail num prazo de uma hora após a avaliação. Os alertas orçamentais de custos reais são gerados pelo custo real que adquiriu em relação aos limiares orçamentais configurados.

## <a name="configure-forecasted-budget-alerts"></a>Configurar alertas orçamentais previstos

Os alertas previstos fornecem uma notificação avançada de que as suas tendências de gastos são suscetíveis de exceder o seu orçamento. Os alertas utilizam [previsões de custos previstas.](quick-acm-cost-analysis.md#understand-forecast) Os alertas são gerados quando a projeção de custos prevista excede o limiar definido. Pode configurar um limiar previsto (% do orçamento). Quando um limiar orçamental previsto é atingido, as notificações são normalmente enviadas no prazo de uma hora após a avaliação.

Para alternar entre configurar um alerta de custos Atual vs Previsto, utilize o `Type` campo ao configurar o alerta como mostrado na imagem seguinte.

Se quiser receber e-mails, adicione azure-noreply@microsoft.com à sua lista de remetentes aprovados para que os e-mails não vão para a sua pasta de e-mail de lixo. Para obter mais informações sobre as notificações, veja [Utilizar alertas de custos](./cost-mgt-alerts-monitor-usage-spending.md).

No exemplo seguinte, um alerta de e-mail é gerado quando 90% do orçamento é atingido. Se criar um orçamento com a API de Orçamentos, também poderá atribuir funções a pessoas para receberem alertas. A atribuição de funções a pessoas não é suportada no portal do Azure. Para obter mais informações sobre a API de orçamentos do Azure, veja [Budgets API](/rest/api/consumption/budgets) (API de Orçamentos). Se quiser que um alerta de e-mail seja enviado num idioma diferente, veja [Regiões suportadas para e-mails de alerta de orçamento](manage-automation.md#supported-locales-for-budget-alert-emails).

Os limites de alerta suportam um intervalo de 0,01% a 1000% do limiar orçamental que forneceu.

:::image type="content" source="./media/tutorial-acm-create-budgets/budget-set-alert.png" alt-text="Screenshot mostrando condições de alerta." lightbox="./media/tutorial-acm-create-budgets/budget-set-alert.png" :::

Após criar o orçamento, este é apresentado na análise de custos. A visualização do orçamento em relação à sua tendência de despesas é um dos primeiros passos quando começa a [analisar os custos e as despesas](./quick-acm-cost-analysis.md).

:::image type="content" source="./media/tutorial-acm-create-budgets/cost-analysis.png" alt-text="Screenshot mostrando um orçamento de exemplo com gastos mostrados na análise de custos." lightbox="./media/tutorial-acm-create-budgets/cost-analysis.png" :::

No exemplo anterior, criou um orçamento para uma subscrição. Também pode criar um orçamento para um grupo de recursos. Se quiser criar um orçamento para um grupo de recursos, navegue para **Cost Management + Faturação** &gt; **Subscrições** &gt; selecione uma subscrição > **Grupos de recursos** > selecione um grupo de recursos > **Orçamentos** > e, em seguida, deverá **Adicionar** um orçamento.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Criar um orçamento para os custos combinados do Azure e do AWS

Pode agrupar os custos do Azure e do AWS ao atribuir um grupo de gestão ao conector juntamente com as contas consolidadas e associadas. Atribua as suas subscrições do Azure ao mesmo grupo de gestão. Em seguida, crie um orçamento para os custos combinados.

1. Em Cost Management, selecione **Orçamentos**.
1. Selecione **Adicionar**.
1. Selecione **Alterar âmbito** e, em seguida, selecione o grupo de gestão.
1. Continue a criar o orçamento até estar concluído.

## <a name="costs-in-budget-evaluations"></a>Avaliações dos custos no orçamento

As avaliações dos custos do orçamento incluem agora os dados das instâncias reservadas e das compras. Se os custos se aplicarem a si, poderá receber alertas, uma vez que os custos são incorporados nas avaliações. Inscreva-se no [portal Azure](https://portal.azure.com) para verificar se os limiares orçamentais estão devidamente configurados para dar conta dos novos custos. Os custos faturados do Azure não são alterados. Os orçamentos fazem agora uma avaliação em relação a um conjunto de custos mais completo. Se os custos não se aplicarem a si, o comportamento do orçamento permanecerá inalterado.

Se quiser filtrar os novos custos de forma a que os orçamentos sejam avaliados apenas em comparação com os custos de consumo do Azure do proprietário, adicione os seguintes filtros ao orçamento:

- Tipo de editor: Azure
- Tipo de custo: Utilização

As avaliações dos custos do orçamento baseiam-se no custo real, não incluem amortizações. Para obter mais informações sobre as opções de filtragem disponíveis para os orçamentos, veja [Compreender as opções de agrupamento e filtragem](group-filter.md).

## <a name="trigger-an-action-group"></a>Acionar um grupo de ações

Quando cria ou edita um orçamento de um âmbito de subscrição ou grupo de recursos, pode configurá-lo para chamar um grupo de ações. O grupo de ações pode realizar várias ações quando o limiar do orçamento for atingido. 

De momento, os Grupos de Ações são suportados apenas nos âmbitos de subscrição e grupo de recursos. Para obter mais informações sobre a criação de grupos de ação, consulte [configurar as definições do grupo de ação básica](../../azure-monitor/alerts/action-groups.md#configure-basic-action-group-settings). 

Para obter mais informações sobre a utilização da automatização baseada em orçamentos com os grupos de ações, veja [Gerir os custos com os orçamentos do Azure](../manage/cost-management-budget-scenario.md).

Para criar ou atualizar grupos de ação, **selecione Gerir o grupo de ação** enquanto estiver a criar ou editar um orçamento.

:::image type="content" source="./media/tutorial-acm-create-budgets/trigger-action-group.png" alt-text="Screenshot mostrando um exemplo de criação de um orçamento para mostrar Grupos de ação Gerir." lightbox="./media/tutorial-acm-create-budgets/trigger-action-group.png" :::

Em seguida, selecione **Adicionar grupo de ações** e crie o grupo de ações.

A integração de orçamentos com os grupos de ações apenas funciona para grupos de ações que têm o esquema de alerta comum desativado. Para obter mais informações sobre como desativar o esquema, veja [Como posso ativar o esquema de alerta comum?](../../azure-monitor/alerts/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="create-and-edit-budgets-with-powershell"></a>Criar e editar orçamentos com o PowerShell

Se for cliente da EA, pode criar e editar orçamentos programáticamente utilizando o módulo Azure PowerShell. Para transferir a versão mais recente do Azure PowerShell, execute o seguinte comando:

```azurepowershell-interactive
install-module -name Az
```

Os comandos de exemplo seguintes criam um orçamento.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Criar um orçamento com um modelo do Azure Resource Manager

Pode criar um orçamento com um modelo do Azure Resource Manager. Para utilizar o modelo, veja [Criar um orçamento com um modelo do Azure Resource Manager](quick-create-budget-template.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um orçamento e já não precisa mais dele, veja os respetivos detalhes e elimine-o.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Criar e editar orçamentos com o PowerShell
> * Criar um orçamento com um modelo do Azure Resource Manager

Avance para o próximo tutorial para criar uma exportação periódica dos dados de gestão de custos.

> [!div class="nextstepaction"]
> [Criar e gerir dados exportados](tutorial-export-acm-data.md)