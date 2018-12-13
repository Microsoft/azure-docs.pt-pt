---
title: Automatize processos de Azure Application Insights com o Logic Apps.
description: Saiba como rapidamente pode automatizar os processos repetíveis ao adicionar o conector do Application Insights à sua aplicação lógica.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: df7d709b51c864c38b9603d2e8ed02d41217bcfc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879718"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatize processos do Application Insights ao utilizar o Logic Apps

Encontra-se a executar repetidamente as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Está à procura para automatizar estas consultas para localizar tendências e anomalias e, em seguida, criar seus próprios fluxos de trabalho em torno deles? O conector do Azure Application Insights (pré-visualização) para o Logic Apps é a ferramenta certa para esta finalidade.

Com esta integração, pode automatizar vários processos sem ter de escrever uma única linha de código. Pode criar uma aplicação lógica com o conector do Application Insights para rapidamente automatizar qualquer processo do Application Insights. 

Pode adicionar ações adicionais também. A funcionalidade de aplicações lógicas do serviço de aplicações do Azure disponibiliza a centenas de ações. Por exemplo, ao utilizar uma aplicação lógica, pode automaticamente enviar uma notificação por e-mail ou criar um bug no Azure DevOps. Também pode utilizar um dos muitos disponíveis [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) para ajudar a agilizar o processo de criação da sua aplicação lógica. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar uma aplicação lógica para o Application Insights

Neste tutorial, saiba como criar uma aplicação lógica que utiliza o algoritmo de autocluster de análise para atributos de grupo nos dados de um aplicativo web. O fluxo envia automaticamente os resultados por correio eletrónico, apenas um exemplo de como pode usar o Application Insights Analytics e o Logic Apps em conjunto. 

### <a name="step-1-create-a-logic-app"></a>Passo 1: Criar uma aplicação lógica
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em **criar um recurso**, selecione **Web + móvel**e, em seguida, selecione **aplicação lógica**.

    ![Nova janela de aplicação lógica](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Passo 2: Criar um acionador para a aplicação lógica
1. Na **Estruturador da aplicação lógica** janela, em **começar com um acionador comum**, selecione **periodicidade**.

    ![Janela do Estruturador da aplicação lógica](./media/automate-with-logic-apps/logicapp2.png)

1. Na **frequência** caixa, selecione **dia** e, em seguida, no **intervalo** , escreva **1**.

    ![Estruturador da aplicação lógica "Recurrence" janela](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação do Application Insights
1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.

1. Na **escolher uma ação** caixa de pesquisa, escreva **Azure Application Insights**.

1. Sob **ações**, clique em **pré-visualização de consulta do Azure Application Insights - Analytics visualizar**.

    !["Escolher uma ação" janela do Estruturador da aplicação lógica](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso do Application Insights

Para concluir este passo, terá um ID de aplicação e uma chave de API para o seu recurso. Pode recuperá-los no portal do Azure, conforme mostrado no diagrama seguinte:

![ID da aplicação no portal do Azure](./media/automate-with-logic-apps/appid.png) 

Forneça um nome para a ligação, o ID da aplicação e a chave de API.

![Janela de ligação de fluxo de Estruturador da aplicação lógica](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especifique o tipo de gráfico e de consulta do Analytics
No exemplo seguinte, a consulta seleciona os pedidos com falhas durante o último dia e correlaciona-los com exceções que ocorreram como parte da operação. Análise correlaciona os pedidos com falhas, com base no identificador do operation_Id. A consulta, em seguida, segmenta os resultados usando o algoritmo de autocluster. 

Ao criar suas próprias consultas, certifique-se de que estão a funcionar corretamente no Analytics antes de o adicionar ao seu fluxo.

1. Na **consulta** caixa, adicione a seguinte consulta do Analytics: 

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

1. Na **tipo de gráfico** caixa, selecione **tabela Html**.

    ![Janela de configuração de consulta do Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Passo 6: Configurar a aplicação lógica para enviar e-mail

1. Clique em **novo passo**e, em seguida, selecione **adicionar uma ação**.

1. Na caixa de pesquisa, escreva **Outlook do Office 365**.

1. Clique em **Office 365 Outlook - enviar um e-mail**.

    ![Seleção do Outlook do Office 365](./media/automate-with-logic-apps/flow2b.png)

1. Na **enviar um e-mail** janela, efetue o seguinte procedimento:

   a. Escreva o endereço de e-mail do destinatário.

   b. Escreva um assunto do e-mail.

   c. Clique em qualquer ponto do **corpo** caixa e, em seguida, no menu conteúdo dinâmico que abre à direita, selecione **corpo**.

   d. Clique em **Mostrar opções avançadas**.

      ![Configuração do Outlook do Office 365](./media/automate-with-logic-apps/flow5.png)

1. No menu de conteúdo dinâmico, efetue o seguinte:

    a. Selecione **nome do anexo**.

    b. Selecione **conteúdo do anexo**.
    
    c. Na **é HTML** caixa, selecione **Sim**.

      ![Ecrã de configuração de e-mail do Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Passo 7: Guardar e testar a aplicação lógica
* Clique em **guardar** para guardar as alterações.

Pode aguardar que o acionador executar a aplicação lógica ou pode executar a aplicação lógica imediatamente selecionando **executar**.

![Ecrã de criação da aplicação lógica](./media/automate-with-logic-apps/step7.png)

Quando é executada a sua aplicação lógica, os destinatários especificados na lista de correio eletrónico irão receber um e-mail que é semelhante ao seguinte:

![Mensagem de e-mail de aplicação lógica](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a criação [consultas de análise](../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





