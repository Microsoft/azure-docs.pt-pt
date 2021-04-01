---
title: Chamar aplicações lógicas do Power Automate e do Power Apps
description: As aplicações lógicas de chamada do Microsoft Power Automate fluem exportando aplicações lógicas como conectores.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91762510"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Chamar aplicações lógicas do Power Automate e do Power Apps

Para ligar para as suas aplicações lógicas a partir do Microsoft Power Automamate e microsoft Power Apps, pode exportar as suas aplicações lógicas como conectores. Quando expõe uma aplicação lógica como um conector personalizado num ambiente power automate ou Power Apps, pode então ligar para a sua aplicação lógica a partir de fluxos lá.

Se pretender migrar o seu fluxo de Power Automamate ou Power to Logic Apps, consulte os [fluxos de exportação da Power Automamate e implemente para Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Nem todos os conectores Power Automate estão disponíveis em Azure Logic Apps. Só pode migrar fluxos Power Automamate que tenham os conectores equivalentes em Azure Logic Apps. Por exemplo, o gatilho do botão, o conector de aprovação e o conector de notificação são específicos do Automatismo de Energia. Atualmente, os fluxos baseados no OpenAPI no Power Automamate não são suportados para exportação e implementação como modelos de aplicações lógicas.
>
> * Para descobrir quais os conectores Power Automamate que não têm equivalentes de Aplicações Lógicas, consulte [conectores Power Automamate](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Para descobrir quais os conectores de Aplicações Lógicas que não possuem equivalentes power automate, consulte [conectores de Aplicações Lógicas](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma licença power automamate ou Power Apps.

* Uma aplicação lógica com um gatilho de pedido para exportar.

* Um fluxo em Power Automamate ou Power Apps a partir do qual pretende chamar a sua aplicação lógica.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Exporte a sua app lógica como um conector personalizado

Antes de poder ligar para a sua aplicação lógica a partir de Power Automamate ou Power Apps, tem primeiro de exportar a sua aplicação lógica como um conector personalizado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa do portal Azure, insira `Logic Apps` . Nos resultados, em **Serviços,** selecione **Logic Apps**.

1. Selecione a aplicação lógica que pretende exportar.

1. No menu da sua aplicação lógica, selecione **Export.**

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Screenshot da página da aplicação lógica no portal Azure, mostrando menu com botão 'Exportar' selecionado.":::

1. No painel **De Exportação,** para **Nome,** insira um nome para o conector personalizado na sua aplicação lógica. Na lista **Ambiente,** selecione o ambiente Power Automamate ou Power Apps a partir do qual pretende chamar a sua aplicação lógica. Quando tiver terminado, selecione **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Screenshot do painel de exportação para aplicação lógica, mostrando campos necessários para o nome e ambiente do conector personalizado.":::

1. Para confirmar que a sua aplicação lógica foi exportada com sucesso, verifique o painel de notificações.

### <a name="exporting-errors"></a>Erros de exportação

Aqui estão os erros que podem ocorrer quando exporta a sua aplicação lógica como um conector personalizado e as suas soluções sugeridas:

* **Falhou em arranjar ambientes. Certifique-se de que a sua conta está configurada para automatizar a energia e, em seguida, tente novamente.**

* **A atual Aplicação Lógica não pode ser exportada. Para exportar, selecione uma Aplicação Lógica que tenha um gatilho de pedido.** [](./logic-apps-workflow-actions-triggers.md#request-trigger)

## <a name="connect-to-your-logic-app-from-power-automate"></a>Conecte-se à sua aplicação lógica a partir de Power Automamate

Para ligar à aplicação lógica que exportou com o seu fluxo Power Automamate:

1. Inicie sessão no [Power Automate](https://flow.microsoft.com).

1. A partir do menu de página inicial **power automatismo,** selecione **Os meus fluxos**.

1. Na página **Flows,** selecione o fluxo que pretende ligar à sua aplicação lógica.

1. A partir do menu da sua página de fluxo, **selecione Editar.**

1. No editor de fluxo, selecione **&#43; Novo passo**.

1. Em **Escolha uma ação,** na caixa de pesquisa, insira o nome do seu conector de aplicação lógica. Opcionalmente, para mostrar apenas os conectores personalizados no seu ambiente, filtra os resultados selecionando o separador **Personalizado.**

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Screenshot do editor de fluxo Power Automamate, mostrando um novo passo a ser adicionado para o conector personalizado e ações disponíveis.":::

1. Selecione a ação que pretende tomar com o seu conector de aplicação lógica. 

1. Forneça a informação de que a ação passa para o conector da aplicação lógica.

1. Para guardar as suas alterações, a partir do menu de editor power automamate, **selecione Save**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No serviço Logic Apps, encontre a aplicação lógica que exportou.

1. Confirme que a sua aplicação lógica funciona da forma que espera no seu fluxo Power Automamate.

## <a name="delete-logic-app-connector-from-power-automate"></a>Eliminar o conector de aplicação lógica do Power Automate

1. Inicie sessão no [Power Automate](https://flow.microsoft.com).

1. Na página inicial do **Power Automate,** selecione **os** &gt; **conectores Data Custom** no menu.

1. Na lista, encontre o seu conector personalizado e selecione o botão Elipses **(...**) &gt; **Delete**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Screenshot da página 'Conectores personalizados' power automamate, mostrando os botões de gestão de conector personalizados da aplicação lógica.":::

1. Para confirmar a eliminação, selecione **OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Conecte-se à sua aplicação lógica a partir de Power Apps

Para ligar à aplicação lógica que exportou com o fluxo de Power Apps:

1. Inicie sessão no [Power Apps](https://powerapps.microsoft.com/).

1. Na página inicial do **Power Apps,** selecione **Flows** no menu.

1. Na página **Flows,** selecione o fluxo que pretende ligar à sua aplicação lógica.

1. Na página do seu fluxo, **selecione Editar** no menu do fluxo.

1. No editor de fluxo, selecione o botão **de passo&#43; Novo.**

1. Em **Escolha uma ação** no novo passo, insira o nome do seu conector de aplicação lógica na caixa de pesquisa. Opcionalmente, filtra os resultados através do separador **Personalizado** para ver apenas conectores personalizados no seu ambiente.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Screenshot do editor de fluxo power apps, mostrando um novo passo a ser adicionado para o conector personalizado e ações disponíveis.":::

1. Selecione a ação que pretende tomar com o conector. 

1. Configure a informação que a sua ação passa para o conector de aplicações lógicas.

1. No menu de editores power apps, **selecione Guardar** para guardar as suas alterações. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No serviço Logic Apps, encontre a aplicação lógica que exportou.

1. Confirme que a sua aplicação lógica está a funcionar como pretendido com o fluxo de Power Apps.

## <a name="delete-logic-app-connector-from-power-apps"></a>Eliminar o conector de aplicativos lógicos das Power Apps

1. Inicie sessão no [Power Apps](https://powerapps.microsoft.com).

1. Na página inicial das **Power Apps,** selecione **Data** &gt; **Custom Connectors** no menu.

1. Na lista, encontre o seu conector personalizado e selecione o botão Elipses **(...**) &gt; **Delete**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Screenshot da página 'Connectors personalizado' das Power Apps, mostrando os botões de gestão de conector personalizados da aplicação lógica.":::

1. Para confirmar a eliminação, selecione **OK**.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [Conectores para Apps Azure Logic](../connectors/apis-list.md)
* Saiba mais sobre [a Azure Logic Apps](../logic-apps/logic-apps-overview.md)
