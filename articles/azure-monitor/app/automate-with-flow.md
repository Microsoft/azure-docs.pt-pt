---
title: Automatizar processos de insights de aplicação Azure com o Microsoft Flow
description: Saiba como pode utilizar o Microsoft Flow para automatizar rapidamente processos repetíveis utilizando o conector Application Insights.
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 7566ae87f92707180b09d50eb6e5eeccedae85b9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655096"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar os processos de insights de aplicação Azure com o conector para o Microsoft Flow

Encontra-se repetidamente a fazer as mesmas consultas nos seus dados de telemetria para verificar se o seu serviço está a funcionar corretamente? Está a tentar automatizar estas consultas para encontrar tendências e anomalias e depois construir os seus próprios fluxos de trabalho à sua volta? O conector Azure Application Insights para o Microsoft Flow é a ferramenta certa para estes fins.

Com esta integração, pode agora automatizar inúmeros processos sem escrever uma única linha de código. Depois de criar um fluxo utilizando uma ação de Insights de Aplicação, o fluxo executa automaticamente a sua consulta de Análise de Insights de Aplicação.

Também pode adicionar ações adicionais. O Microsoft Flow disponibiliza centenas de ações. Por exemplo, pode utilizar o Microsoft Flow para enviar automaticamente uma notificação de e-mail ou criar um bug em Azure DevOps. Também pode utilizar um dos muitos [modelos](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o conector para o Microsoft Flow. Estes modelos aceleram o processo de criação de um fluxo.

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para insights de aplicação

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de autocluster Analytics para atributos de grupo nos dados para uma aplicação web. O fluxo envia automaticamente os resultados por e-mail, apenas um exemplo de como pode utilizar o Microsoft Flow e o Application Insights Analytics juntos.

### <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo

1. Iniciar sessão no [Microsoft Flow](https://flow.microsoft.com), e depois selecionar **Os Meus Fluxos**.
2. Clique em **Novo** e **agendado — a partir de branco**.

    ![Criar novo fluxo a partir de espaços em branco programados](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um gatilho para o seu fluxo

1. No popup **Construa um fluxo programado,** preencha o nome do seu fluxo e quantas vezes quer que o seu fluxo corra.

    ![Configurar a recorrência do horário com a entrada de frequência e intervalo](./media/automate-with-flow/2-schedule.png)

1. Clique em **Criar**.

### <a name="step-3-add-an-application-insights-action"></a>Passo 3: Adicionar uma ação de Insights de Aplicação

1. Pesquisar insights de **aplicação**.
2. Clique em **Insights de Aplicação Azure - Visualizar consulta de Análise**.

    ![Escolha uma ação: Azure Application Insights Visualizar consulta de Analytics](./media/automate-with-flow/3-visualize.png)

3. Selecione **Novo passo**.

### <a name="step-4-connect-to-an-application-insights-resource"></a>Passo 4: Ligar a um recurso Deinsights de Aplicação

Para completar este passo, precisa de um ID de aplicação e de uma chave API para o seu recurso. Pode recuperá-los do portal Azure, como mostra o seguinte diagrama:

![ID de aplicação no portal Azure](./media/automate-with-flow/5apiaccess.png)

![Chave API no portal Azure](./media/automate-with-flow/6apikey.png)

Forneça um nome para a sua ligação, juntamente com o ID da aplicação e a chave API.

   ![Janela de ligação do Microsoft Flow](./media/automate-with-flow/4-connection.png)

Se a caixa de ligação não aparecer imediatamente e, em vez disso, entrar diretamente na consulta, clique nas elipses na parte superior direita da caixa. Em seguida, selecione as minhas ligações ou use uma existente.

Clique em **Criar**.

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Passo 5: Especificar a consulta de Analytics e o tipo de gráfico
Esta consulta de exemplo seleciona os pedidos falhados no último dia e correlaciona-os com exceções que ocorreram no âmbito da operação. A análise correlaciona-os com base no identificador operation_Id. A consulta segmenta então os resultados utilizando o algoritmo de autocluster.

Quando criar as suas próprias consultas, verifique se estão a funcionar corretamente no Analytics antes de adicioná-lo ao seu fluxo.

- Adicione a seguinte consulta de Analytics e selecione o tipo de tabela HTML. Em seguida, selecione **Novo passo**.

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
    
    ![Janela de configuração de consulta de análise](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Passo 6: Configure o fluxo para enviar e-mail

1. Pesquisa rumo ao **Office 365 Outlook**.
2. Clique no **Office 365 Outlook - Envie um e-mail**.

    ![Janela de seleção do Office 365 Outlook](./media/automate-with-flow/6-outlook.png)

1. No Enviar uma janela de **e-mail:**

   a. Digite o endereço de e-mail do destinatário.

   b. Digite um assunto para o e-mail.

   c. Clique em qualquer lugar da caixa **Body** e, em seguida, no menu de conteúdo dinâmico que se abre à direita, selecione **Body**.

   e. Selecionar **Mostrar opções avançadas**

1. No menu de conteúdo dinâmico:

    a. Selecione **Nome de anexo**.

    b. Selecione **o conteúdo do anexo**.
    
    c. Na caixa **Is HTML,** selecione **Sim**.

    ![Configuração do Office 365 Outlook](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>Passo 7: Poupe e teste o seu fluxo

Clique em **Guardar**.

Pode esperar que o gatilho execute esta ação, ou pode clicar no ícone de teste ![copo](./media/automate-with-flow/testicon.png) **Teste** na parte superior.

Depois de selecionar **o teste:**

1. Selecione **vou executar a ação do gatilho.**
2. Selecione **Run Flow**.

Quando o fluxo corre, os destinatários especificados na lista de e-mail recebem uma mensagem de e-mail como a que está abaixo.

![E-mail de amostra](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a criação de [consultas de Análise.](../../azure-monitor/log-query/get-started-queries.md)
- Saiba mais sobre [o Microsoft Flow](https://ms.flow.microsoft.com).

<!--Link references-->
