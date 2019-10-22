---
title: Automatizar processos do insights Aplicativo Azure com Microsoft Flow
description: Saiba como você pode usar Microsoft Flow para automatizar rapidamente os processos repetíveis usando o conector de Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: ff0896498c0270b8eb43b762228916985f924def
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678312"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos do insights Aplicativo Azure com o conector para Microsoft Flow

Você se encontra executando repetidamente as mesmas consultas nos dados de telemetria para verificar se o serviço está funcionando corretamente? Você pretende automatizar essas consultas para encontrar tendências e anomalias e, em seguida, criar seus próprios fluxos de trabalho em relação a eles? O conector do Aplicativo Azure insights para Microsoft Flow é a ferramenta certa para essas finalidades.

Com essa integração, agora você pode automatizar vários processos sem escrever uma única linha de código. Depois de criar um fluxo usando uma ação de Application Insights, o fluxo executa automaticamente a consulta Application Insights Analytics.

Você também pode adicionar ações adicionais. Microsoft Flow torna centenas de ações disponíveis. Por exemplo, você pode usar Microsoft Flow para enviar automaticamente uma notificação por email ou criar um bug no Azure DevOps. Você também pode usar um dos muitos [modelos](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o conector para Microsoft Flow. Esses modelos aceleram o processo de criação de um fluxo.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para Application Insights

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de autocluster do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia automaticamente os resultados por email, apenas um exemplo de como você pode usar Microsoft Flow e Application Insights Analytics juntos.

### <a name="step-1-create-a-flow"></a>Etapa 1: criar um fluxo

1. Entre no [Microsoft Flow](https://flow.microsoft.com)e, em seguida, selecione **meus fluxos**.
2. Clique em **novo** , **agendado**, em branco.

    ![Criar novo fluxo de em branco agendado](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: criar um gatilho para o fluxo

1. No pop-up, **crie um fluxo agendado**, preencha o nome do fluxo e com que frequência você deseja que o fluxo seja executado.

    ![Configurar a recorrência de agendamento com a frequência e o intervalo de entrada](./media/automate-with-flow/2-schedule.png)

1. Clique em **Criar**.

### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: adicionar uma ação de Application Insights

1. Procure **Application insights**.
2. Clique em **aplicativo Azure insights – Visualizar consulta do Analytics**.

    ![Escolha uma ação: consulta de análise de Aplicativo Azure insights Visualizar](./media/automate-with-flow/3-visualize.png)

3. Selecione **Novo passo**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso de Application Insights

Para concluir esta etapa, você precisa de uma ID de aplicativo e uma chave de API para seu recurso. Você pode recuperá-los do portal do Azure, conforme mostrado no diagrama a seguir:

![ID do aplicativo no portal do Azure](./media/automate-with-flow/5apiaccess.png)

![Chave de API no portal do Azure](./media/automate-with-flow/6apikey.png)

Forneça um nome para a conexão, juntamente com a ID do aplicativo e a chave de API.

   ![Janela de conexão Microsoft Flow](./media/automate-with-flow/4-connection.png)

Se a caixa de conexão não aparecer imediatamente e, em vez disso, chegar diretamente à inserção da consulta, clique nas reticências na parte superior direita da caixa. Em seguida, selecione minhas conexões ou use uma existente.

Clique em **Criar**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar a consulta de análise e o tipo de gráfico
Esta consulta de exemplo seleciona as solicitações com falha no último dia e as correlaciona com exceções que ocorreram como parte da operação. O Analytics os correlaciona com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo autocluster.

Ao criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao seu fluxo.

- Adicione a seguinte consulta de análise e selecione o tipo de gráfico de tabela HTML. Em seguida, selecione **nova etapa**.

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
    
    ![Janela de configuração de consulta do Analytics](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Etapa 6: configurar o fluxo para enviar email

1. Pesquise o **Outlook do Office 365**.
2. Clique em **Office 365 Outlook-enviar um email**.

    ![Janela de seleção do Outlook do Office 365](./media/automate-with-flow/6-outlook.png)

1. Na janela **enviar um email** :

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **corpo** e, no menu conteúdo dinâmico que é aberto à direita, selecione **corpo**.

   e. Selecione **Mostrar opções avançadas**

1. No menu de conteúdo dinâmico:

    a. Selecione o **nome do anexo**.

    b. Selecione o **conteúdo do anexo**.
    
    c. Na caixa **é HTML** , selecione **Sim**.

    ![Configuração do Office 365 Outlook](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Etapa 7: salvar e testar seu fluxo

Clique em **Guardar**.

Você pode aguardar até que o gatilho execute esta ação, ou pode clicar no ícone de teste ![beaker ](./media/automate-with-flow/testicon.png) **teste** na parte superior.

Depois de selecionar **teste**:

1. Selecione **eu executarei a ação do gatilho**.
2. Selecione **executar fluxo**.

Quando o fluxo é executado, os destinatários que você especificou na lista de emails recebem uma mensagem de email como a exibida abaixo.

![Email de exemplo](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como criar [consultas de análise](../../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->