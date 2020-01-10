---
title: Automatizar processos do insights Aplicativo Azure usando aplicativos lógicos
description: Saiba como você pode automatizar rapidamente os processos reproduzíveis adicionando o conector de Application Insights ao seu aplicativo lógico.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/11/2019
ms.openlocfilehash: 95a3577d352b6bc4b56d36b5b79658571a6be1ff
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407524"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatizar processos de Application Insights usando aplicativos lógicos

Você se encontra executando repetidamente as mesmas consultas nos dados de telemetria para verificar se o serviço está funcionando corretamente? Você pretende automatizar essas consultas para encontrar tendências e anomalias e, em seguida, criar seus próprios fluxos de trabalho em relação a eles? O conector do insights Aplicativo Azure para aplicativos lógicos é a ferramenta certa para essa finalidade.

Com essa integração, você pode automatizar vários processos sem escrever uma única linha de código. Você pode criar um aplicativo lógico com o conector de Application Insights para automatizar rapidamente qualquer processo de Application Insights. 

Você também pode adicionar ações adicionais. O recurso aplicativos lógicos do serviço Azure App torna centenas de ações disponíveis. Por exemplo, usando um aplicativo lógico, você pode enviar automaticamente uma notificação por email ou criar um bug no Azure DevOps. Você também pode usar um dos muitos [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponíveis para ajudar a acelerar o processo de criação de seu aplicativo lógico. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar um aplicativo lógico para Application Insights

Neste tutorial, você aprenderá a criar um aplicativo lógico que usa o algoritmo de autocluster do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia automaticamente os resultados por email, apenas um exemplo de como você pode usar Application Insights a análise e os aplicativos lógicos juntos. 

### <a name="step-1-create-a-logic-app"></a>Etapa 1: criar um aplicativo lógico
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Clique em **criar um recurso**, selecione **Web + celular**e, em seguida, selecione **aplicativo lógico**.

    ![Janela novo aplicativo lógico](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Etapa 2: criar um gatilho para seu aplicativo lógico
1. Na janela **designer do aplicativo lógico** , em **Iniciar com um gatilho comum**, selecione **recorrência**.

    ![Janela do designer de aplicativo lógico](./media/automate-with-logic-apps/2logicappdesigner.png)

1. Na caixa **intervalo** , digite **1** e, em seguida, caixa**frequência** , selecione **dia**.

    ![Janela "recorrência" do designer de aplicativo lógico](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: adicionar uma ação de Application Insights
1. Clique em **nova etapa**.

1. Na caixa de pesquisa **escolher uma ação** , digite **aplicativo Azure insights**.

1. Em **ações**, clique em **aplicativo Azure insights – Visualizar consulta do Analytics**.

    ![Janela "escolher uma ação" do designer de aplicativo lógico](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso de Application Insights

Para concluir esta etapa, você precisa de uma ID de aplicativo e uma chave de API para seu recurso. Você pode recuperá-los do portal do Azure, conforme mostrado no diagrama a seguir:

![ID do aplicativo no portal do Azure](./media/automate-with-logic-apps/5apiaccess.png)

![ID do aplicativo no portal do Azure](./media/automate-with-logic-apps/6apikey.png)

Forneça um nome para a conexão, a ID do aplicativo e a chave de API.

![Janela de conexão do fluxo do designer do aplicativo lógico](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar a consulta de análise e o tipo de gráfico
No exemplo a seguir, a consulta seleciona as solicitações com falha no último dia e as correlaciona com exceções que ocorreram como parte da operação. O Analytics correlaciona as solicitações com falha com base no identificador de operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo autocluster. 

Ao criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao seu fluxo.

1. Na caixa de **consulta** , adicione a seguinte consulta de análise:

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

1. Na caixa **tipo de gráfico** , selecione **tabela HTML**.

    ![Janela de configuração de consulta do Analytics](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Etapa 6: configurar o aplicativo lógico para enviar email

1. Clique em **nova etapa**.

1. Na caixa de pesquisa, digite **Office 365 Outlook**.

1. Clique em **Office 365 Outlook-enviar um email**.

    ![Seleção do Office 365 Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. Na janela **enviar um email** , faça o seguinte:

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **corpo** e, no menu conteúdo dinâmico que é aberto à direita, selecione **corpo**.
    
   d. Clique na lista suspensa **Adicionar novo parâmetro** e selecione anexos e é HTML.

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/10emailbody.png)

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/11emailparameter.png)

1. No menu conteúdo dinâmico, faça o seguinte:

    a. Selecione o **nome do anexo**.

    b. Selecione o **conteúdo do anexo**.
    
    c. Na caixa **é HTML** , selecione **Sim**.

      ![Tela de configuração de email do Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Etapa 7: salvar e testar seu aplicativo lógico
* Clique em **guardar** para guardar as alterações.

Você pode aguardar até que o gatilho execute o aplicativo lógico ou pode executar o aplicativo lógico imediatamente selecionando **executar**.

![Tela de criação de aplicativo lógico](./media/automate-with-logic-apps/13save.png)

Quando seu aplicativo lógico for executado, os destinatários especificados na lista de emails receberão um email semelhante ao seguinte:

![Mensagem de email do aplicativo lógico](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como criar [consultas de análise](../../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





