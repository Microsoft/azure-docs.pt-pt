---
title: Automatizar os processos de insights de aplicação azure utilizando aplicações lógicas
description: Saiba como pode automatizar rapidamente processos repetíveis adicionando o conector De Insights de Aplicação à sua aplicação lógica.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: 4a0944e661932d86fac75f78c4faf5be751806c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79473171"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizar processos de insights de aplicação utilizando aplicações lógicas

Encontra-se repetidamente a executar as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Está a tentar automatizar estas consultas para encontrar tendências e anomalias e depois construir os seus próprios fluxos de trabalho à sua volta? O conector Azure Application Insights para Aplicações Lógicas é a ferramenta certa para este fim.

> [!NOTE]
> O conector Azure Application Insights foi substituído pelo [conector Azure Monitor](../platform/logicapp-flow-connector.md) que está integrado com o Diretório Ativo Azure em vez de necessitar de uma chave API e também permite recuperar dados de um espaço de trabalho do Log Analytics.

Com esta integração, pode automatizar inúmeros processos sem escrever uma única linha de código. Pode criar uma aplicação lógica com o conector Application Insights para automatizar rapidamente qualquer processo de Insights de Aplicação. 

Também pode adicionar ações adicionais. A funcionalidade de Aplicações Lógicas do Azure App Service disponibiliza centenas de ações. Por exemplo, ao utilizar uma aplicação lógica, pode enviar automaticamente uma notificação por e-mail ou criar um bug em Azure DevOps. Também pode usar um dos muitos [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponíveis para ajudar a acelerar o processo de criação da sua aplicação lógica. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar uma aplicação lógica para Insights de Aplicação

Neste tutorial, aprende-se a criar uma aplicação lógica que utiliza o algoritmo de autocluster Analytics para atributos de grupo nos dados de uma aplicação web. O fluxo envia automaticamente os resultados por e-mail, apenas um exemplo de como pode utilizar as Aplicações Insights Analytics e Logic Apps em conjunto. 

### <a name="step-1-create-a-logic-app"></a>Passo 1: Criar uma aplicação lógica
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique **em Criar um recurso,** selecione Web + **Mobile,** e depois selecione **Logic App**.

    ![Nova janela de aplicativo lógica](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passo 2: Criar um gatilho para a sua aplicação lógica
1. Na janela **Logic App Designer,** sob **iniciar com um gatilho comum,** selecione **Recurrence**.

    ![Janela de designer de aplicativos lógicos](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Na caixa **intervalo,** tipo **1** e depois, caixa**de frequência,** selecione **Dia**.

    ![Janela de Designer de Aplicações Lógica "Recorrência"](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação de Insights de Aplicação
1. Clique em **novo passo**.

1. Na **caixa de** pesquisa de ação Escolha uma caixa de pesquisa de ação, **digite Insights de aplicação Azure**.

1. Em **Ações**, clique em **Insights de Aplicação Azure - Visualizar consulta de Analytics**.

    ![Lógica App Designer "Escolha uma ação" janela](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso Deinsights de Aplicação

Para completar este passo, precisa de um ID de aplicação e de uma chave API para o seu recurso. Pode recuperá-los do portal Azure, como mostra o seguinte diagrama:

![ID de aplicação no portal Azure](./media/automate-with-logic-apps/5apiaccess.png)

![ID de aplicação no portal Azure](./media/automate-with-logic-apps/6apikey.png)

Forneça um nome para a sua ligação, o ID da aplicação e a chave API.

![Janela de conexão de fluxo de designer de aplicativológica](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especificar a consulta de Analytics e o tipo de gráfico
No exemplo seguinte, a consulta seleciona os pedidos falhados no último dia e correlaciona-os com exceções que ocorreram no âmbito da operação. A análise correlaciona os pedidos falhados, com base no identificador operation_Id. A consulta segmenta então os resultados utilizando o algoritmo de autocluster. 

Quando criar as suas próprias consultas, verifique se estão a funcionar corretamente no Analytics antes de adicioná-lo ao seu fluxo.

1. Na caixa **de consulta,** adicione a seguinte consulta de Analytics:

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

1. Na caixa **'Tipo gráfico',** selecione **Tabela Html**.

    ![Janela de configuração de consulta de análise](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Passo 6: Configure a aplicação lógica para enviar e-mail

1. Clique em **novo passo**.

1. Na caixa de pesquisa, digite **office 365 Outlook**.

1. Clique no **Office 365 Outlook - Envie um e-mail**.

    ![Seleção de Perspetivas do Office 365](./media/automate-with-logic-apps/9sendemail.png)

1. No Enviar uma janela de **e-mail,** faça o seguinte:

   a. Digite o endereço de e-mail do destinatário.

   b. Digite um assunto para o e-mail.

   c. Clique em qualquer lugar da caixa **Body** e, em seguida, no menu de conteúdo dinâmico que se abre à direita, selecione **Body**.
    
   d. Clique no **novo parâmetro Adicionar** novo parâmetro e selecione Anexos e Is HTML.

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. No menu de conteúdo dinâmico, faça o seguinte:

    a. Selecione **Nome de anexo**.

    b. Selecione **o conteúdo do anexo**.
    
    c. Na caixa **Is HTML,** selecione **Sim**.

      ![Ecrã de configuração de e-mail office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Passo 7: Guarde e teste a sua aplicação lógica
* Clique em **Guardar** para guardar as suas alterações.

Pode esperar que o gatilho execute a aplicação lógica, ou pode executar a aplicação lógica imediatamente selecionando **Executar**.

![Tela de criação de aplicativológico](./media/automate-with-logic-apps/13save.png)

Quando a sua aplicação lógica for executado, os destinatários especificados na lista de e-mails receberão um e-mail que se parece com o seguinte:

![Mensagem de e-mail de aplicativo lógico](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação de [consultas de Análise.](../../azure-monitor/log-query/get-started-queries.md)
- Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





