---
title: Automatizar relatórios personalizados com dados da Azure Application Insights
description: Automatizar relatórios personalizados diários/semanais/mensais com dados da Azure Application Insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: f35b7cbde7b719a91b23b19481ab704c445485cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827739"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados da Azure Application Insights

Relatórios periódicos ajudam a manter uma equipa informada sobre como os seus serviços críticos de negócio estão a fazer. Os desenvolvedores, equipas DevOps/SRE, e os seus gestores podem ser produtivos com relatórios automatizados que fornecem informações de forma fiável sem exigir que todos assinem no portal. Esses relatórios também podem ajudar a identificar aumentos graduais de latências, taxas de carga ou de falha que podem não desencadear quaisquer regras de alerta.

Cada empresa tem as suas necessidades únicas de reporte, tais como:

* Agregações percentil específicas de métricas, ou métricas personalizadas em um relatório.
* Tenha diferentes relatórios para roll-ups diários, semanais e mensais de dados para diferentes públicos.
* Segmentação por atributos personalizados como região, ou ambiente.
* Agrupam alguns recursos da IA num único relatório, mesmo que possam estar em diferentes subscrições ou grupos de recursos, etc.
* Relatórios separados contendo métricas sensíveis enviadas para audiência seletiva.
* Relatórios a partes interessadas que podem não ter acesso aos recursos do portal.

> [!NOTE] 
> O e-mail semanal Application Insights digest não permitiu qualquer personalização, e será descontinuado a favor das opções personalizadas listadas abaixo. O último e-mail semanal de digestão será enviado no dia 11 de junho de 2018. Por favor, configuure uma das seguintes opções para obter relatórios personalizados semelhantes (use a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar e-mails de relatório personalizados

Pode [consultar programáticamente](https://dev.applicationinsights.io/) os dados de Insights de Aplicação para gerar relatórios personalizados num horário. As seguintes opções podem ajudá-lo a começar rapidamente:

* [Automatizar relatórios com o Microsoft Flow](../platform/logicapp-flow-connector.md)
* [Automatizar relatórios com apps lógicas](automate-with-logic-apps.md)
* Utilize o modelo de [função Azure](../../azure-functions/functions-create-first-azure-function.md) "Application Insights programado" no cenário de Monitorização. Esta função utiliza o SendGrid para entregar o e-mail. 

    ![Modelo de função azul](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Consulta de amostra para um e-mail semanal de digestão
A seguinte consulta mostra a junção em vários conjuntos de dados para um e-mail de digestão semanal como relatório. Personalize-o conforme necessário e use-o com qualquer uma das opções listadas acima para automatizar um relatório semanal.

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Relatório de digestão programado de Insights de Aplicação

1. Crie uma App de Função Azure.(Os insights de aplicação _on_ são necessários apenas se quiser monitorizar a sua nova App de Função com Insights de Aplicação)

   Visite a documentação do Azure Functions para aprender a [criar uma aplicação de função](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app)

2. Uma vez concluída a sua nova App de Função, selecione **Ir para o recurso**.

3. Selecione **Nova função**.

   ![Criar uma nova imagem de função](./media/automate-custom-reports/new-function.png)

4. Selecione o **_modelo de digestão programado para Insights de Aplicação._**

     > [!NOTE]
     > Por predefinição, as aplicações de função são criadas com a versão 3.x do tempo de execução. Tem de direcionar a versão **1.x** do tempo de execução do [Azure Functions](../../azure-functions/set-runtime-version.md) para utilizar o modelo de digestão programado para Insights de Aplicação. Aceda a configurações > configurações de tempo de execução para alterar a versão de tempo de execução. ![screenshot de tempo de execução](./media/automate-custom-reports/change-runtime-v.png)

   ![Imagem do modelo de tela do modelo de novas aplicações de funções](./media/automate-custom-reports/function-app-04.png)

5. Insira um endereço de e-mail adequado para o seu relatório e selecione **Criar**.

   ![Screenshot de Definições de Função](./media/automate-custom-reports/scheduled-digest.png)

6. Selecione a configuração de funcionalidades da sua Plataforma **de Aplicação de**  >  **Platform features**  >  **Função.**

    ![Screenshot das definições de aplicação de função Azure](./media/automate-custom-reports/config.png)

7. Criar três novas definições de aplicação com valores correspondentes ``AI_APP_ID`` ``AI_APP_KEY`` adequados, e ``SendGridAPI`` . Selecione **Guardar**.

     ![Screenshot da interface de integração de funções](./media/automate-custom-reports/app-settings.png)
    
    (Os valores AI_ podem ser encontrados no API Access for the Application Insights Resource que pretende reportar. Se não tiver uma chave API de Insights de Aplicação, existe a opção de **Criar a Chave API**.)
    
   * AI_APP_ID = ID de aplicação
   * AI_APP_KEY = Chave API
   * SendGridAPI =Chave API de SendGrid

     > [!NOTE]
     > Se não tiver uma conta SendGrid, pode criar uma. A documentação da SendGrid para as Funções Azure está [aqui.](../../azure-functions/functions-bindings-sendgrid.md) Se apenas quiser uma explicação mínima de como configurar o SendGrid e gerar uma chave API é fornecida no final deste artigo. 

8. **Selecione Integrar** e em Saídas clique **em SendGrid ($return)**.

     ![Screenshot de saída](./media/automate-custom-reports/integrate.png)

9. No âmbito da Definição de **Aplicação da Chave SendGridAPI,** selecione a definição de aplicação recém-criada para **SendGridAPI**.

     ![Executar a imagem da app da função](./media/automate-custom-reports/sendgrid-output.png)

10. Executar e testar a sua App de Função.

     ![Screenshot de teste](./media/automate-custom-reports/function-app-11.png)

11. Verifique o seu e-mail para confirmar que a mensagem foi enviada/recebida com sucesso.

     ![Screenshot da linha de assunto de e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid com Azure

Estes passos só se aplicam se ainda não tiver uma conta SendGrid configurada.

1. A partir do portal Azure, **selecione Criar um recurso** > procurar por **SendGrid Email Delivery** > clique em **Criar** > preencha as instruções específicas de criação sendGrid.

     ![Criar screenshot de recursos SendGrid](./media/automate-custom-reports/sendgrid.png)

2. Uma vez criado em SendGrid Accounts select **Manage**.

     ![Definições API Key Screenshot](./media/automate-custom-reports/sendgrid-manage.png)

3. Isto lançará o site da SendGrid. Selecione **As**  >  **teclas API de Definições**.

     ![Criar e ver screenshot da aplicação chave da API](./media/automate-custom-reports/function-app-15.png)

4. Crie uma chave API > escolha **Criar & Vista**. (Reveja a documentação da SendGrid sobre o acesso restrito para determinar qual o nível de permissões adequado para a sua Chave API. O Acesso Completo é selecionado aqui apenas para fins por exemplo.)

   ![Screenshot de acesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copie toda a chave, este valor é o que precisa nas definições da sua App de Função como o valor para SendGridAPI

   ![Copy API key screenshot](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a criação [de consultas analíticas.](../log-query/get-started-queries.md)
* Saiba mais sobre [os dados programáticas de consultas de insights de aplicação](https://dev.applicationinsights.io/)
* Saiba mais sobre o [Logic Apps](../../logic-apps/logic-apps-overview.md).
* Saiba mais sobre [o Microsoft Power Automamate](https://ms.flow.microsoft.com).

