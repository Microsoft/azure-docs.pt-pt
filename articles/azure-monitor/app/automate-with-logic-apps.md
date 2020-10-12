---
title: Automatizar processos de Insights de Aplicações Azure utilizando apps lógicas
description: Saiba como pode automatizar rapidamente processos repetíveis adicionando o conector Application Insights à sua aplicação lógica.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: f6406c2e6fb933c561a8ae54009499768c81a204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970862"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizar processos de Insights de Aplicações utilizando apps lógicas

Encontra-se repetidamente a executar as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Pretende automatizar estas consultas para encontrar tendências e anomalias e depois construir os seus próprios fluxos de trabalho à sua volta? O conector Azure Application Insights para Aplicações Lógicas é a ferramenta certa para este fim.

> [!NOTE]
> O conector Azure Application Insights foi substituído pelo [conector Azure Monitor](../platform/logicapp-flow-connector.md) que está integrado com o Azure Ative Directory em vez de necessitar de uma chave API e também permite obter dados de um espaço de trabalho log Analytics.

Com esta integração, você pode automatizar inúmeros processos sem escrever uma única linha de código. Pode criar uma aplicação lógica com o conector Application Insights para automatizar rapidamente qualquer processo de Insights de Aplicação. 

Também pode adicionar ações adicionais. A funcionalidade De Aplicações Lógicas do Azure App Service disponibiliza centenas de ações. Por exemplo, ao utilizar uma aplicação lógica, pode enviar automaticamente uma notificação de e-mail ou criar um bug em Azure DevOps. Também pode usar um dos muitos [modelos](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) disponíveis para ajudar a acelerar o processo de criação da sua aplicação lógica. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar uma aplicação lógica para Insights de Aplicações

Neste tutorial, aprende-se a criar uma aplicação lógica que utiliza o algoritmo de agrupamento de analíticos para agrupar atributos nos dados para uma aplicação web. O fluxo envia automaticamente os resultados por e-mail, apenas um exemplo de como pode utilizar as Aplicações Insights Analytics e Logic Apps em conjunto. 

### <a name="step-1-create-a-logic-app"></a>Passo 1: Criar uma aplicação lógica
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique **em Criar um recurso,** selecione Web + **Mobile**e, em seguida, selecione **a Aplicação Lógica**.

    ![Nova janela de aplicativos de lógica](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passo 2: Criar um gatilho para a sua aplicação lógica
1. Na janela **Logic App Designer,** em **Iniciar com um gatilho comum,** selecione **Recorrência**.

    ![Janela do Designer de Aplicativos Lógicas](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Na caixa  **Interval,** tipo **1** e depois, caixa**de frequência,** selecione **Day**.

    ![Janela "Recorrência" do Designer de Aplicativos Lógicas](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação de Insights de Aplicação
1. Clique **em Novo passo**.

1. Na caixa de pesquisa de ação Escolha uma caixa de pesquisa de **ação,** **digite Azure Application Insights**.

1. Em **Ações**, clique em **Azure Application Insights - Visualizar consulta de Analytics**.

    ![Janela de Design de Aplicativos Lógicas "Escolha uma ação"](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar-se a um recurso Application Insights

Para completar este passo, precisa de um ID de aplicação e uma chave API para o seu recurso. Pode recuperá-los do portal Azure, como mostra o seguinte diagrama:

![O Screenshot mostra a página de Acesso API no portal Azure com o botão de chave Create API selecionado.](./media/automate-with-logic-apps/5apiaccess.png)

![ID de aplicação no portal Azure](./media/automate-with-logic-apps/6apikey.png)

Forneça um nome para a sua ligação, o ID da aplicação e a chave API.

![Janela de ligação de fluxo de aplicativo de lógica](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especificar a consulta e o tipo de gráfico analítico
No exemplo seguinte, a consulta seleciona os pedidos falhados no último dia e correlaciona-os com exceções que ocorreram no âmbito da operação. A análise correlaciona os pedidos falhados, com base no identificador operation_Id. A consulta segmenta então os resultados utilizando o algoritmo de autocluster. 

Quando criar as suas próprias consultas, verifique se estão a funcionar corretamente em Analytics antes de adicioná-la ao seu fluxo.

1. Na **caixa de consulta,** adicione a seguinte consulta analítica:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. Na caixa **'Tipo gráfico',** selecione **Tabela html**.

    ![Janela de configuração de consulta de analítica](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Passo 6: Configurar a app lógica para enviar e-mail

1. Clique **em Novo passo**.

1. Na caixa de pesquisa, **escreva Office 365 Outlook**.

1. Clique **no Office 365 Outlook - Envie um e-mail**.

    ![Seleção do Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. Na janela Enviar uma janela **de e-mail,** faça o seguinte:

   a. Digite o endereço de e-mail do destinatário.

   b. Digite um assunto para o e-mail.

   c. Clique em qualquer lugar na caixa **Body** e, em seguida, no menu de conteúdo dinâmico que abre à direita, selecione **Corpo**.
    
   d. Clique na descida do **novo parâmetro Para** baixo e selecione Anexos e É HTML.

      ![Screenshot mostra a janela Enviar uma janela de e-mail com a caixa de corpo realçada e o menu de conteúdo Dinâmico com Corpo realçado no lado direito.](./media/automate-with-logic-apps/10emailbody.png)

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. No menu de conteúdo dinâmico, faça o seguinte:

    a. Selecione **Nome de anexo**.

    b. Selecione **o conteúdo do anexo**.
    
    c. Na caixa **HTML Is,** selecione **Sim**.

      ![Ecrã de configuração de e-mail do Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Passo 7: Guardar e testar a sua aplicação lógica
* Clique em **Guardar** para guardar as alterações.

Pode esperar que o gatilho execute a aplicação lógica, ou pode executar a aplicação lógica imediatamente selecionando **Run**.

![Tela de criação de aplicativos lógico](./media/automate-with-logic-apps/13save.png)

Quando a sua aplicação lógica é executado, os destinatários especificados na lista de e-mails receberão um e-mail que se parece com o seguinte:

![Mensagem de e-mail de aplicativo lógica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação [de consultas analíticas.](../log-query/get-started-queries.md)
- Saiba mais sobre o [Logic Apps](../../logic-apps/logic-apps-overview.md).



<!--Link references-->

