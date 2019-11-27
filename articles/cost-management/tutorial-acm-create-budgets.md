---
title: Tutorial - criar e gerir orçamentos do Azure | Documentos da Microsoft
description: Este tutorial ajuda plano e uma conta para os custos de serviços do Azure que consumir.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/12/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: edb46bc361c515439a93d9c3d0b9987bebe4b1b1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229884"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: Criar e gerir orçamentos do Azure

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Com os orçamentos, pode contribuir para os serviços do Azure que consome ou subscrever durante um período específico. Ajudam a informar outras pessoas sobre seus gastos para gerenciar de forma proativa os custos e para monitorizar a forma como gastos avança ao longo do tempo. Quando os limiares de orçamento que criou são excedidos, apenas notificações são acionadas. Nenhum dos seus recursos são afetados e seu consumo não está parado. Pode usar os orçamentos de comparar e controlar a analisar os custos de gastos.

Os dados de custo e de uso geralmente estão disponíveis em 12-16 horas e os orçamentos são avaliados em relação a esses custos a cada quatro horas. As notificações por email normalmente são recebidas dentro de 12-16 horas.

Orçamentos repor automaticamente no final de um período (mensalmente, trimestralmente ou anualmente) para a mesma quantidade de orçamento ao selecionar uma data de expiração no futuro. Porque repostas com a mesma quantidade de orçamento, tem de criar orçamentos separados quando orçada diferem de valores de moeda para períodos futuros.

Os exemplos neste tutorial orientam-no através da criação e edição de um orçamento para uma subscrição do Azure Enterprise Agreement (EA).

Assista ao vídeo [como criar um orçamento para monitorar seus gastos com o gerenciamento de custos do Azure](https://www.youtube.com/watch?v=ExIVG_Gr45A) para ver como você pode criar orçamentos no Azure para monitorar os gastos.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Os orçamentos têm suporte para uma variedade de tipos de conta do Azure. Para exibir a lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md). Para exibir orçamentos, você precisa de, pelo menos, acesso de leitura para sua conta do Azure.

 Para assinaturas do EA do Azure, você deve ter acesso de leitura para exibir orçamentos. Para criar e gerir orçamentos, tem de ter permissão de contribuinte. Pode criar orçamentos individuais para as subscrições do EA e grupos de recursos. No entanto, não é possível criar orçamentos para contas de faturação de EA.

As seguintes permissões do Azure, ou escopos, têm suporte por assinatura para orçamentos por usuário e grupo. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Colaborador e contribuinte de gestão de custos – pode criar, modificar ou eliminar os seus próprios orçamentos. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor de gestão de custos – e de um leitor pode ver os orçamentos de que eles têm permissão para.

Para obter mais informações sobre como atribuir permissão aos dados de gerenciamento de custos, consulte [atribuição de acesso a dados de gerenciamento de custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

- Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Pode criar um orçamento de subscrição do Azure durante um período mensal, trimestral ou anual. O conteúdo de navegação na portal do Azure determina se você cria um orçamento para uma assinatura ou para um grupo de gerenciamento.

Para criar ou exibir um orçamento, abra o escopo desejado no portal do Azure e selecione **orçamentos** no menu. Por exemplo, navegue até **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione **orçamentos** no menu. Use o **escopo** do Pill para alternar para um escopo diferente, como um grupo de gerenciamento, em orçamentos. Para obter mais informações sobre escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

Depois de criar orçamentos, mostram uma simples exibição de seus gastos atual contra eles.

Clique em **Adicionar**.

![Exemplo mostrando uma lista de orçamentos já criados](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **criar orçamento** , verifique se o escopo mostrado está correto. Escolha os filtros que você deseja adicionar. Os filtros permitem que você crie orçamentos para custos específicos, como grupos de recursos em uma assinatura ou um serviço como máquinas virtuais. Qualquer filtro que você possa usar na análise de custo também pode ser aplicado a um orçamento.

Depois de identificar o escopo e os filtros, digite um nome de orçamento. Em seguida, escolha um período mensal, trimestral ou anual de redefinição de orçamento. Esse período de redefinição determina a janela de tempo que é analisada pelo orçamento. O custo avaliado pelo orçamento começa em zero no início de cada novo período. Quando cria um orçamento trimestral, ele funciona da mesma forma como um orçamento mensal. A diferença é que a quantidade de orçamento para o trimestre é uniformemente dividida entre os três meses do trimestre. Um valor de orçamento anual é dividido uniformemente entre todos os 12 meses do ano civil.

Se você tiver uma assinatura paga conforme o uso, MSDN ou Visual Studio, o período de cobrança da fatura poderá não ser alinhado ao mês do calendário. Para esses tipos de assinatura e grupos de recursos, você pode criar um orçamento alinhado ao seu período de fatura ou a meses de calendário. Para criar um orçamento alinhado ao período da fatura, selecione um período de redefinição de **mês de cobrança**, **trimestre de cobrança**ou ano de **cobrança**. Para criar um orçamento alinhado ao mês do calendário, selecione um período de redefinição **mensal**, **trimestral**ou **anual**.

Em seguida, identifique a data de validade quando o orçamento se tornar inválido e pare de avaliar seus custos.

Com base nos campos escolhidos no orçamento até o momento, um grafo é mostrado para ajudá-lo a selecionar um limite a ser usado para seu orçamento. O orçamento sugerido é baseado no custo previsto mais alto que você pode incorrer em períodos futuros. Você pode alterar o valor do orçamento.

![Exemplo mostrando a criação de orçamento com dados de custo mensal ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Depois de configurar o valor do orçamento, clique em **Avançar** para configurar alertas de orçamento. Orçamentos requerem o limiar, pelo menos, um custo (% do orçamento) e um endereço de e-mail correspondentes. Opcionalmente, pode incluir até cinco limiares e cinco endereços de e-mail num orçamento único. Quando um limite de orçamento é atingido, as notificações de email normalmente são recebidas em menos de 20 horas. Para obter mais informações sobre notificações, consulte [usar alertas de custo](cost-mgt-alerts-monitor-usage-spending.md). No exemplo a seguir, um alerta de email é gerado quando 90% do orçamento é atingido. Se você criar um orçamento com a API de orçamentos, também poderá atribuir funções a pessoas para receber alertas. Não há suporte para a atribuição de funções a pessoas no portal do Azure. Para saber mais sobre a API de orçamentos do Azure, confira [API de orçamentos](/rest/api/consumption/budgets).

![Exemplo mostrando condições de alerta](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Depois de criar um orçamento, ela é mostrada na análise de custos. A exibição do seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](quick-acm-cost-analysis.md).

![Orçamento de exemplo e os gastos mostrado na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, criou um orçamento para uma subscrição. No entanto, também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de custos + cobrança** &gt; **assinaturas** &gt; selecione uma assinatura > **grupos de recursos** > selecione um grupo de recursos > **orçamentos** > e, em seguida, **Adicione** um orçamento.

## <a name="trigger-an-action-group"></a>Disparar um grupo de ações

Quando você cria ou edita um orçamento para um escopo de grupo de recursos ou assinatura, você pode configurá-lo para chamar um grupo de ação. O grupo de ações pode executar uma variedade de ações diferentes quando o limite do orçamento é atingido. No momento, os grupos de ação só têm suporte para escopos de grupo de recursos e assinatura. Para obter mais informações sobre grupos de ações, consulte [criar e gerenciar grupos de ações no portal do Azure](../azure-monitor/platform/action-groups.md). Para obter mais informações sobre como usar a automação baseada em orçamento com grupos de ação, consulte [gerenciar custos com orçamentos do Azure](../billing/billing-cost-management-budget-scenario.md).



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

Avance para o próximo tutorial para criar uma exportação recorrente para os seus dados de gestão de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)
