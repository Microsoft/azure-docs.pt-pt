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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: 65909e13c75ae4d2577ea29f562b841a1eb20477
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256430"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de Azure Application Insights com o conector para o Microsoft Flow

Encontra-se a executar repetidamente as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Está à procura para automatizar estas consultas para localizar tendências e anomalias e, em seguida, criar seus próprios fluxos de trabalho em torno deles? O conector do Azure Application Insights (pré-visualização) para o Microsoft Flow é a ferramenta certa para estes fins.

Com esta integração, agora já pode automatizar vários processos sem ter de escrever uma única linha de código. Depois de criar um fluxo com uma ação do Application Insights, o fluxo executa automaticamente a consulta do Analytics do Application Insights. 

Pode adicionar ações adicionais também. Microsoft Flow torna a centenas de ações disponíveis. Por exemplo, pode utilizar o Microsoft Flow para enviar uma notificação por e-mail ou criar um bug no DevOps do Azure automaticamente. Também pode utilizar um dos muitos [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) que estão disponíveis para o conector para o Microsoft Flow. Estes modelos aceleram o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, irá aprender como criar um fluxo que utiliza o algoritmo de cluster automática de análise para atributos de grupo nos dados de um aplicativo web. O fluxo envia automaticamente os resultados por correio eletrónico, apenas um exemplo de como pode usar o Microsoft Flow e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo
1. Inicie sessão no [Microsoft Flow](https://flow.microsoft.com)e, em seguida, selecione **meus fluxos**.
1. Clique em **criar um fluxo do zero**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um acionador para o fluxo
1. Selecione **agenda**e, em seguida, selecione **Schedule - Recurrence**.
1. Na **frequência** caixa, selecione **dia**e, no **intervalo** , digite **1**.

    ![Caixa de diálogo de Acionador do Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação do Application Insights
1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.
1. Procure **Azure Application Insights**.
1. Clique em **pré-visualização de consulta do Azure Application Insights - Analytics visualizar**.

    ![Execute a janela de consulta do Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso do Application Insights

Para concluir este passo, terá um ID de aplicação e uma chave de API para o seu recurso. Pode recuperá-los no portal do Azure, conforme mostrado no diagrama seguinte:

![ID da aplicação no portal do Azure](./media/app-insights-automate-with-flow/appid.png) 

- Forneça um nome para a sua ligação, juntamente com a chave de API e o ID da aplicação.

    ![Janela de ligação do Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especifique o tipo de gráfico e de consulta do Analytics
Esta consulta de exemplo seleciona os pedidos com falhas durante o último dia e correlaciona-los com exceções que ocorreram como parte da operação. Análise correlaciona-los com base no identificador do operation_Id. A consulta, em seguida, segmenta os resultados usando o algoritmo de autocluster. 

Ao criar suas próprias consultas, certifique-se de que estão a funcionar corretamente no Analytics antes de o adicionar ao seu fluxo.

- Adicione a seguinte consulta de análise e, em seguida, selecione o tipo de gráfico de tabela HTML. 

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
    
    ![Janela de configuração de consulta do Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passo 6: Configurar o fluxo para enviar e-mail

1. Clique em **novo passo**e, em seguida, clique em **adicionar uma ação**.
1. Procure **Outlook do Office 365**.
1. Clique em **Office 365 Outlook - enviar um e-mail**.

    ![Janela de seleção do Outlook do Office 365](./media/app-insights-automate-with-flow/flow2b.png)

1. Na **enviar um e-mail** janela, efetue o seguinte procedimento:

   a. Escreva o endereço de e-mail do destinatário.

   b. Escreva um assunto do e-mail.

   c. Clique em qualquer ponto do **corpo** caixa e, em seguida, no menu conteúdo dinâmico que abre à direita, selecione **corpo**.

   d. Clique em **Mostrar opções avançadas**.

    ![Configuração do Outlook do Office 365](./media/app-insights-automate-with-flow/flow5.png)

1. No menu de conteúdo dinâmico, efetue o seguinte:

    a. Selecione **nome do anexo**.

    b. Selecione **conteúdo do anexo**.
    
    c. Na **é HTML** caixa, selecione **Sim**.

    ![Janela de configuração de e-mail do Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Passo 7: Guardar e testar o fluxo
- Na **nome do fluxo** caixa, adicione um nome para o fluxo e, em seguida, clique em **criar fluxo**.

    ![Janela de criação de fluxos](./media/app-insights-automate-with-flow/flow8.png)

Pode aguardar que o acionador executar esta ação, ou pode executar o fluxo imediatamente por [executar o acionador a pedido](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que especificou na lista de correio eletrónico recebem uma mensagem de e-mail que é semelhante ao seguinte:

![E-mail de exemplo](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a criação [consultas de análise](../log-analytics/query-language/get-started-queries.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





