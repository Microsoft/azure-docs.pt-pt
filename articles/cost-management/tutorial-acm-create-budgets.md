---
title: Tutorial - criar e gerir orçamentos do Azure | Documentos da Microsoft
description: Este tutorial ajuda plano e uma conta para os custos de serviços do Azure que consumir.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: e6e20db39be8a6e60833bf5c4f9b6a34a9ead461
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013032"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: Crie e gira orçamentos do Azure

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Com os orçamentos, pode contribuir para os serviços do Azure que consome ou subscrever durante um período específico. Ajudam a informar outras pessoas sobre seus gastos para gerenciar de forma proativa os custos e para monitorizar a forma como gastos avança ao longo do tempo. Quando os limiares de orçamento que criou são excedidos, apenas notificações são acionadas. Nenhum dos seus recursos são afetados e seu consumo não está parado. Pode usar os orçamentos de comparar e controlar a analisar os custos de gastos.

Orçamentos mensais são avaliados em relação a cada quatro horas de gastos. No entanto, dados e notificações para recursos consumidos estão disponíveis no espaço de oito horas.  

Orçamentos repor automaticamente no final de um período (mensalmente, trimestralmente ou anualmente) para a mesma quantidade de orçamento ao selecionar uma data de expiração no futuro. Porque repostas com a mesma quantidade de orçamento, tem de criar orçamentos separados quando orçada diferem de valores de moeda para períodos futuros.

Os exemplos neste tutorial orientam-no através da criação e edição de um orçamento para uma subscrição do Azure Enterprise Agreement (EA).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Orçamentos são suportados para uma variedade de tipos de conta do Azure. Para ver a lista completa dos tipos de conta suportados, consulte [dados de compreender a gestão de custos](understand-cost-mgt-data.md). Para ver os orçamentos, precisa de, pelo menos, acesso de leitura para a sua conta do Azure.

 Para subscrições do EA do Azure, tem de ter acesso de leitura para ver os orçamentos. Para criar e gerir orçamentos, tem de ter permissão de contribuinte. Pode criar orçamentos individuais para as subscrições do EA e grupos de recursos. No entanto, não é possível criar orçamentos para contas de faturação de EA.

As seguintes permissões do Azure, ou âmbitos, são suportados por subscrição para os orçamentos de por utilizador e grupo. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Colaborador e contribuinte de gestão de custos – pode criar, modificar ou eliminar os seus próprios orçamentos. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor de gestão de custos – e de um leitor pode ver os orçamentos de que eles têm permissão para.

Para obter mais informações sobre a atribuição de permissão para dados de gestão de custos, veja [atribuir acesso a dados de gestão de custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Pode criar um orçamento de subscrição do Azure durante um período mensal, trimestral ou anual. O conteúdo de navegação no portal do Azure determina se criar um orçamento para uma subscrição ou para um grupo de gestão.

Para criar ou ver um orçamento, abra o âmbito pretendido no portal do Azure e selecione **orçamentos** no menu. Por exemplo, navegue até **subscrições**, selecione uma subscrição na lista e, em seguida, selecione **orçamentos** no menu. Utilize o **âmbito** envenenadas para mudar para um âmbito diferente, como um grupo de gestão, no orçamentos. Para obter mais informações sobre âmbitos, consulte [entender e trabalhar com âmbitos](understand-work-scopes.md).

Depois de criar orçamentos, mostram uma simples exibição de seus gastos atual contra eles.

Clique em **Adicionar**.

![Apresentado no portal do Azure de orçamentos de gestão de custos](./media/tutorial-acm-create-budgets/budgets01.png)

Na **orçamento criar** janela, introduza um nome de orçamento e a quantidade de orçamento. Em seguida, escolha um mensais, trimestrais, ou o período de duração anual. Em seguida, selecione uma data de fim. Orçamentos requerem o limiar, pelo menos, um custo (% do orçamento) e um endereço de e-mail correspondentes. Opcionalmente, pode incluir até cinco limiares e cinco endereços de e-mail num orçamento único. Quando é cumprido um limiar de orçamento, notificações por e-mail são normalmente recebidas em menos de oito horas. Para obter mais informações sobre notificações, consulte [alertas de custos de utilização](cost-mgt-alerts-monitor-usage-spending.md).

Eis um exemplo de criação de um orçamento mensal de US $4.500. É gerado um alerta de e-mail quando for atingido 90% do orçamento.

![Informações de exemplo mostradas na caixa de orçamento de criar](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quando cria um orçamento trimestral, ele funciona da mesma forma como um orçamento mensal. A diferença é que a quantidade de orçamento para o trimestre é uniformemente dividida entre os três meses do trimestre. Como pode esperar, uma quantidade de orçamento anual é uniformemente dividida entre todos os 12 meses do ano de calendário.

Gastos atuais face aos orçamentos é atualizada sempre que a gestão de custos recebe dados de faturas atualizados. Normalmente, diariamente.

![Informações de exemplo que mostra gastos face aos orçamentos atual](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Depois de criar um orçamento, ela é mostrada na análise de custos. Visualizar o seu orçamento em relação à tendência de gastos é uma das primeiras etapas quando começar a [analisar os custos e despesas](quick-acm-cost-analysis.md).

![Orçamento de exemplo e os gastos mostrado na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, criou um orçamento para uma subscrição. No entanto, também pode criar um orçamento para um grupo de recursos. Se quiser criar um orçamento para um grupo de recursos, navegue para **gestão de custos + faturação** &gt; **subscrições** &gt; selecionar uma subscrição > **recursos grupos** > selecione um grupo de recursos > **orçamentos** > e, em seguida, **Add** um orçamento.

## <a name="edit-a-budget"></a>Editar um orçamento

Dependendo do nível de acesso que tem, pode editar um orçamento para alterar as respetivas propriedades. No exemplo a seguir, algumas das propriedades são só de leitura porque o utilizador tem permissão de contribuinte à subscrição. Atualmente, o **data de expiração** está desativada e não pode ser modificado uma vez definida.

![Exemplo de um orçamento para alterar várias propriedades de edição](./media/tutorial-acm-create-budgets/edit-budget.png)

## <a name="trigger-an-action-group"></a>Um grupo de ação do acionador

Quando cria ou edita um orçamento para uma subscrição ou o âmbito do grupo de recursos, pode configurar para chamar um grupo de ação. O grupo de ação pode realizar uma variedade de diferentes ações quando é cumprido o limiar de orçamento. Para obter mais informações sobre grupos de ação, veja [criar e gerir grupos de ação no portal do Azure](../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como utilizar a automatização com base no orçamento com grupos de ação, veja [gerir os custos com orçamentos Azure](../billing/billing-cost-management-budget-scenario.md).

Para criar ou atualizar os grupos de ação, clique em **gerir grupos de ação** enquanto estiver a criar ou editar um orçamento.

![Exemplo de criação de um orçamento para mostrar os grupos de ação de gerir](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

Em seguida, clique em **grupo de ação de adicionar** e criar o grupo de ação.


![Imagem da caixa de grupo de ação de adicionar](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Depois da ação de criação do grupo, feche a caixa para retornar ao seu orçamento.

Configure o seu orçamento para utilizar o seu grupo de ação quando um limiar individual é cumprido. São suportados até cinco limiares diferentes.

![Exemplo que mostra a seleção de grupo de ação para uma condição de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

O exemplo seguinte mostra os limiares de orçamento definidos como 50%, 75% e 100%. Cada um é configurado para acionar as ações específicas dentro do grupo de ação designado.

![Exemplo que mostra a condições de alerta configuradas com vários grupos de ação e o tipo de ações](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

Avance para o próximo tutorial para criar uma exportação recorrente para os seus dados de gestão de custos.

> [!div class="nextstepaction"]
> [Criar e gerir os dados exportados](tutorial-export-acm-data.md)
