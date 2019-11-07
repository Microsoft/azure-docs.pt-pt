---
title: Tutorial – criar e gerenciar orçamentos do Azure | Microsoft Docs
description: Este tutorial ajuda a planejar e considerar os custos dos serviços do Azure que você consome.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8c3c0574389fc9808af3cd70c928ede82d375076
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720722"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: criar e gerenciar orçamentos do Azure

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Com os orçamentos, pode contribuir para os serviços do Azure que consome ou subscrever durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar de forma proativa os custos e para monitorar como os gastos avançam ao longo do tempo. Quando os limites de orçamento que você criou forem excedidos, somente as notificações serão disparadas. Nenhum dos seus recursos é afetado e seu consumo não é interrompido. Você pode usar orçamentos para comparar e acompanhar os gastos à medida que analisa os custos.

Os dados de custo e de uso geralmente estão disponíveis em 12-16 horas e os orçamentos são avaliados em relação a esses custos a cada quatro horas. As notificações por email normalmente são recebidas dentro de 12-16 horas.

Os orçamentos são redefinidos automaticamente no final de um período (mensal, trimestral ou anual) para o mesmo valor de orçamento quando você seleciona uma data de expiração no futuro. Como elas são redefinidas com o mesmo valor de orçamento, você precisa criar orçamentos separados quando valores de moeda orçados diferem para períodos futuros.

Os exemplos neste tutorial orientam você na criação e edição de um orçamento para uma assinatura do EA (Azure Enterprise Agreement).

Assista ao vídeo [como criar um orçamento para monitorar seus gastos com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=ExIVG_Gr45A) para ver como você pode criar orçamentos no Azure para monitorar os gastos.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Os orçamentos têm suporte para uma variedade de tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). Para exibir orçamentos, você precisa de, pelo menos, acesso de leitura para sua conta do Azure.

 Para assinaturas do EA do Azure, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador. Você pode criar orçamentos individuais para assinaturas EA e grupos de recursos. No entanto, você não pode criar orçamentos para contas de cobrança de EA.

As seguintes permissões do Azure, ou escopos, têm suporte por assinatura para orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Contribuidor e colaborador de gerenciamento de custos – pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor de leitor e gerenciamento de custos – pode exibir os orçamentos aos quais eles têm permissão.

Para obter mais informações sobre como atribuir permissão aos dados de gerenciamento de custos, consulte [atribuição de acesso a dados de gerenciamento de custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual. O conteúdo de navegação na portal do Azure determina se você cria um orçamento para uma assinatura ou para um grupo de gerenciamento.

Para criar ou exibir um orçamento, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Por exemplo, navegue até **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione **orçamentos** no menu. Use o **escopo** do Pill para alternar para um escopo diferente, como um grupo de gerenciamento, em orçamentos. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

Depois de criar orçamentos, eles mostram uma exibição simples de seus gastos atuais com eles.

Clique em **Adicionar**.

![Exemplo mostrando uma lista de orçamentos já criados](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **criar orçamento** , verifique se o escopo mostrado está correto. Escolha os filtros que você deseja adicionar. Os filtros permitem que você crie orçamentos para custos específicos, como grupos de recursos em uma assinatura ou um serviço como máquinas virtuais. Qualquer filtro que você possa usar na análise de custo também pode ser aplicado a um orçamento.

Depois de identificar o escopo e os filtros, digite um nome de orçamento. Em seguida, escolha um período mensal, trimestral ou anual de redefinição de orçamento. Esse período de redefinição determina a janela de tempo que é analisada pelo orçamento. O custo avaliado pelo orçamento começa em zero no início de cada novo período. Quando você cria um orçamento trimestral, ele funciona da mesma maneira que um orçamento mensal. A diferença é que o valor do orçamento para o trimestre é dividido uniformemente entre os três meses do trimestre. Um valor de orçamento anual é dividido uniformemente entre todos os 12 meses do ano civil.

Se você tiver uma assinatura paga conforme o uso, MSDN ou Visual Studio, o período de cobrança da fatura poderá não ser alinhado ao mês do calendário. Para esses tipos de assinatura e grupos de recursos, você pode criar um orçamento alinhado ao seu período de fatura ou a meses de calendário. Para criar um orçamento alinhado ao período da fatura, selecione um período de redefinição de **mês de cobrança**, **trimestre de cobrança**ou ano de **cobrança**. Para criar um orçamento alinhado ao mês do calendário, selecione um período de redefinição **mensal**, **trimestral**ou **anual**.

Em seguida, identifique a data de validade quando o orçamento se tornar inválido e pare de avaliar seus custos.

Com base nos campos escolhidos no orçamento até o momento, um grafo é mostrado para ajudá-lo a selecionar um limite a ser usado para seu orçamento. O orçamento sugerido é baseado no custo previsto mais alto que você pode incorrer em períodos futuros. Você pode alterar o valor do orçamento.

![Exemplo mostrando a criação de orçamento com dados de custo mensal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Depois de configurar o valor do orçamento, clique em **Avançar** para configurar alertas de orçamento. Os orçamentos exigem pelo menos um limite de custo (% do orçamento) e um endereço de email correspondente. Opcionalmente, você pode incluir até cinco limites e cinco endereços de email em um único orçamento. Quando um limite de orçamento é atingido, as notificações de email normalmente são recebidas em menos de 20 horas. Para obter mais informações sobre notificações, consulte [usar alertas de custo](cost-mgt-alerts-monitor-usage-spending.md). No exemplo a seguir, um alerta de email é gerado quando 90% do orçamento é atingido. Se você criar um orçamento com a API de orçamentos, também poderá atribuir funções a pessoas para receber alertas. Não há suporte para a atribuição de funções a pessoas no portal do Azure. Para saber mais sobre a API de orçamentos do Azure, confira [API de orçamentos](/rest/api/consumption/budgets).

![Exemplo mostrando condições de alerta](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Depois de criar um orçamento, ele é mostrado na análise de custo. A exibição do seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](quick-acm-cost-analysis.md).

![Orçamento de exemplo e gastos mostrados na análise de custo](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, você criou um orçamento para uma assinatura. No entanto, você também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de custos + cobrança** &gt; **assinaturas** &gt; selecione uma assinatura > **grupos de recursos** > selecione um grupo de recursos > **orçamentos** > e, em seguida, **Adicione** um orçamento.

## <a name="trigger-an-action-group"></a>Disparar um grupo de ações

Quando você cria ou edita um orçamento para um escopo de grupo de recursos ou assinatura, você pode configurá-lo para chamar um grupo de ação. O grupo de ações pode executar uma variedade de ações diferentes quando o limite do orçamento é atingido. Para obter mais informações sobre grupos de ações, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como usar a automação baseada em orçamento com grupos de ação, consulte [gerenciar custos com orçamentos do Azure](../billing/billing-cost-management-budget-scenario.md).

Para criar ou atualizar grupos de ações, clique em **gerenciar grupos de ações** enquanto estiver criando ou editando um orçamento.

![Exemplo de criação de um orçamento para mostrar grupos de ação de gerenciamento](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Em seguida, clique em **Adicionar grupo de ações** e crie o grupo de ações.


![Imagem da caixa Adicionar grupo de ações](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Depois que o grupo de ações for criado, feche a caixa para retornar ao seu orçamento.

Configure seu orçamento para usar o grupo de ações quando um limite individual for atingido. Há suporte para até cinco limites diferentes.

![Exemplo mostrando a seleção do grupo de ações para uma condição de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

O exemplo a seguir mostra os limites de orçamento definidos como 50%, 75% e 100%. Cada um é configurado para disparar as ações especificadas dentro do grupo de ações designado.

![Exemplo mostrando condições de alerta configuradas com vários grupos de ação e tipos de ações](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

A integração de orçamento com grupos de ação só funciona para grupos de ação que têm o esquema de alerta comum desabilitado. Para obter mais informações sobre como desabilitar o esquema, consulte [como fazer habilitar o esquema de alerta comum?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

Avance para o próximo tutorial para criar uma exportação recorrente para seus dados de gerenciamento de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)
