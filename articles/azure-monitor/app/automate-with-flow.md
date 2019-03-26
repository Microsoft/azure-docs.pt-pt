---
title: Automatizar processos de Azure Application Insights com o Microsoft Flow
description: Saiba como pode utilizar o Microsoft Flow para rapidamente automatizar processos repetíveis ao utilizar o conector do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438942"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de Azure Application Insights com o conector para o Microsoft Flow

Encontra-se a executar repetidamente as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Está à procura para automatizar estas consultas para localizar tendências e anomalias e, em seguida, criar seus próprios fluxos de trabalho em torno deles? O conector do Application Insights do Azure para Microsoft Flow é a ferramenta certa para estes fins.

Com esta integração, agora já pode automatizar vários processos sem ter de escrever uma única linha de código. Depois de criar um fluxo com uma ação do Application Insights, o fluxo executa automaticamente a consulta do Analytics do Application Insights. 

Pode adicionar ações adicionais também. Microsoft Flow torna a centenas de ações disponíveis. Por exemplo, pode utilizar o Microsoft Flow para enviar uma notificação por e-mail ou criar um bug no DevOps do Azure automaticamente. Também pode utilizar um dos muitos [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) que estão disponíveis para o conector para o Microsoft Flow. Estes modelos aceleram o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, irá aprender como criar um fluxo que utiliza o algoritmo de cluster automática de análise para atributos de grupo nos dados de um aplicativo web. O fluxo envia automaticamente os resultados por correio eletrónico, apenas um exemplo de como pode usar o Microsoft Flow e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo
1. Inicie sessão no [Microsoft Flow](https://flow.microsoft.com)e, em seguida, selecione **meus fluxos**.
2. Clique em **New** , em seguida, **criar do zero**.

    ![Criar novo fluxo do zero](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um acionador para o fluxo
1. Na compilação do separador select **agenda**e, em seguida, selecione **Schedule - Recurrence**.

    ![Selecione agendo na compilação](./media/automate-with-flow/2schedule.png)

1. Na **intervalo** , introduza **1**e, no **frequência** caixa, selecione **dia**.
2. Clique em **novo passo**

    ![Configurar a periodicidade da agenda com introduzir frequências e intervalos](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação do Application Insights
1. Procure **Azure Application Insights**.
2. Clique em **do Azure Application Insights - consulta do Analytics visualizar**.
 
    ![Escolha uma ação: Consulta de Analytics visualizar do Application Insights do Azure](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso do Application Insights

Para concluir este passo, terá um ID de aplicação e uma chave de API para o seu recurso. Pode recuperá-los no portal do Azure, conforme mostrado no diagrama seguinte:

![ID da aplicação no portal do Azure](./media/automate-with-flow/5apiaccess.png)

![Chave de API no portal do Azure](./media/automate-with-flow/6apikey.png)

- Forneça um nome para a sua ligação, juntamente com a chave de API e o ID da aplicação.

    ![Janela de ligação do Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especifique o tipo de gráfico e de consulta do Analytics
Esta consulta de exemplo seleciona os pedidos com falhas durante o último dia e correlaciona-los com exceções que ocorreram como parte da operação. Análise correlaciona-los com base no identificador do operation_Id. A consulta, em seguida, segmenta os resultados usando o algoritmo de autocluster. 

Ao criar suas próprias consultas, certifique-se de que estão a funcionar corretamente no Analytics antes de o adicionar ao seu fluxo.

- Adicione a seguinte consulta do Analytics e selecione o tipo de gráfico de tabela HTML. Em seguida, selecione **novo passo**.

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
    
    ![Janela de configuração de consulta do Analytics](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passo 6: Configurar o fluxo para enviar e-mail

1. Procure **Outlook do Office 365**.
2. Clique em **Office 365 Outlook - enviar um e-mail**.

    ![Janela de seleção do Outlook do Office 365](./media/automate-with-flow/9outlookaction.png)

1. Na **enviar um e-mail** janela, efetue o seguinte procedimento:

   a. Escreva o endereço de e-mail do destinatário.

   b. Escreva um assunto do e-mail.

   c. Clique em qualquer ponto do **corpo** caixa e, em seguida, no menu conteúdo dinâmico que abre à direita, selecione **corpo**.

   d. Clique em **Mostrar opções avançadas**.

    ![Configuração do Outlook do Office 365](./media/automate-with-flow/10sendemailbody.png)

1. No menu de conteúdo dinâmico, efetue o seguinte:

    a. Selecione **nome do anexo**.

    b. Selecione **conteúdo do anexo**.
    
    c. Na **é HTML** caixa, selecione **Sim**.

    ![Janela de configuração de e-mail do Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Passo 7: Guardar e testar o fluxo
- Na **nome do fluxo** caixa, adicione um nome para o fluxo e, em seguida, clique em **guardar**.

    ![Dê um nome fluxo e guardar](./media/automate-with-flow/12nameflow.png)

Pode aguardar que o acionador executar esta ação, ou pode executar o fluxo imediatamente por [executar o acionador a pedido](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que especificou na lista de correio eletrónico recebem uma mensagem de e-mail que é semelhante ao seguinte:

![E-mail de exemplo](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a criação [consultas de análise](../../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





