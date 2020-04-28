---
title: Automatizar relatórios personalizados com dados da Azure Application Insights
description: Automatizar relatórios diários/semanais/mensais personalizados com dados da Azure Application Insights
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: cf251d63645efc70ee93e84827db47ae3055ae33
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161520"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados da Azure Application Insights

Relatórios periódicos ajudam a manter uma equipa informada sobre como estão os seus serviços críticos de negócio. Desenvolvedores, equipas DevOps/SRE, e seus gestores podem ser produtivos com relatórios automatizados fornecendo informações fiavelmente sem exigir que todos assinem no portal. Estes relatórios também podem ajudar a identificar aumentos graduais de latenciências, taxas de carga ou falhas que podem não desencadear quaisquer regras de alerta.

Cada empresa tem as suas necessidades únicas de reporte, tais como:

* Agregações percentil específicas de métricas, ou métricas personalizadas num relatório.
* Tenha diferentes relatórios para roll-ups diários, semanais e mensais de dados para diferentes públicos.
* Segmentação por atributos personalizados como região, ou ambiente.
* Agrupar alguns recursos de IA num único relatório, mesmo que possam estar em diferentes assinaturas ou grupos de recursos, etc.
* Relatórios separados contendo métricas sensíveis enviadas para o público seletivo.
* Relatórios às partes interessadas que podem não ter acesso aos recursos do portal.

> [!NOTE] 
> O e-mail semanal da Aplicação Insights digesta não permitiu qualquer personalização, e será descontinuado a favor das opções personalizadas listadas abaixo. O último e-mail semanal de digestão será enviado no dia 11 de junho de 2018. Por favor, configure uma das seguintes opções para obter relatórios personalizados semelhantes (use a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar e-mails personalizados de relatório

Pode [consultar programáticamente](https://dev.applicationinsights.io/) os dados do Application Insights para gerar relatórios personalizados numa programação. As seguintes opções podem ajudá-lo a começar rapidamente:

* [Relatórios de automatização com o Microsoft Flow](automate-with-flow.md)
* [Automatizar relatórios com aplicações lógicas](automate-with-logic-apps.md)
* Utilize o modelo de [função Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application Insights scheduled" no cenário de monitorização. Esta função utiliza o SendGrid para entregar o e-mail. 

    ![Modelo de função azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Amostra de consulta para um e-mail semanal de digestão
A consulta seguinte mostra a junção em vários conjuntos de dados para um e-mail semanal de digestão como relatório. Personalize-o conforme necessário e use-o com qualquer uma das opções acima listadas para automatizar um relatório semanal.

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

## <a name="application-insights-scheduled-digest-report"></a>Relatório de digestão programado de Insights de aplicação

1. Crie uma aplicação de função Azure.(Os Insights de aplicação _são_ necessários apenas se quiser monitorizar a sua nova App de Funções com Insights de Aplicação)

   Visite a documentação das Funções Azure para aprender a [criar uma aplicação](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) de função

2. Assim que a sua nova App de Funções tiver concluído a implementação, selecione **Ir para o recurso**.

3. Selecione **Nova função**.

   ![Criar uma nova imagem de Função](./media/automate-custom-reports/new-function.png)

4. Selecione o modelo de **_digestão programado_** para insights de aplicação .

     > [!NOTE]
     > Por padrão, as aplicações de função são criadas com a versão 3.x do tempo de execução. Tem de visar as [funções do Azure](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) na versão **1.x** para utilizar o modelo de digestão programado para insights de aplicação. Vá às definições de funcionamento de configuração > função para alterar a versão tempo de execução. ![screenshot tempo de execução](./media/automate-custom-reports/change-runtime-v.png)

   ![Screenshot de modelo de insights de aplicação de nova função](./media/automate-custom-reports/function-app-04.png)

5. Insira um endereço de e-mail apropriado para o seu relatório e selecione **Criar**.

   ![Screenshot de definições de função](./media/automate-custom-reports/scheduled-digest.png)

6. Selecione a**configuração**da**plataforma** > de **aplicações** > de funções .

    ![Imagem de configuração da aplicação da função Azure](./media/automate-custom-reports/config.png)

7. Crie três novas definições ``AI_APP_ID`` ``AI_APP_KEY``de ``SendGridAPI``aplicação com valores correspondentes adequados, e . Selecione **Guardar**.

     ![Screenshot de interface de integração de funções](./media/automate-custom-reports/app-settings.png)
    
    (Os valores AI_ podem ser encontrados no âmbito do Acesso API para o Recurso de Insights de Aplicação que pretende reportar. Se não tiver uma chave API de Insights de Aplicação, existe a opção de Criar a **Chave API**.)
    
   * AI_APP_ID = ID de aplicação
   * AI_APP_KEY = Chave API
   * Chave SendGridAPI =SendGrid API

     > [!NOTE]
     > Se não tiver uma conta SendGrid, pode criar uma. A documentação da SendGrid para as Funções Azure está [aqui.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid) Se apenas quiser uma explicação mínima sobre como configurar sendGrid e gerar uma chave API é fornecida no final deste artigo. 

8. Selecione **Integrar** e em saídas clique **em SendGrid ($return)**.

     ![Screenshot de saída](./media/automate-custom-reports/integrate.png)

9. No âmbito da definição de aplicação de **chave SendGridAPI,** selecione a definição de aplicação recém-criada para **SendGridAPI**.

     ![Executar função app screenshot](./media/automate-custom-reports/sendgrid-output.png)

10. Executar e testar a sua App de Funções.

     ![Screenshot de teste](./media/automate-custom-reports/function-app-11.png)

11. Verifique o seu e-mail para confirmar que a mensagem foi enviada/recebida com sucesso.

     ![Imagem de linha de sujeito de e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid com Azure

Estes passos só se aplicam se ainda não tiver uma conta SendGrid configurada.

1. A partir do portal Azure, selecione **Criar um recurso** > procurar por **SendGrid Email Delivery** > clique em **Criar** > preencher as instruções específicas de criação da SendGrid.

     ![Criar screenshot de recurso sendgrid](./media/automate-custom-reports/sendgrid.png)

2. Uma vez criado seletivamente as Contas SendGrid, selecione **Gerir**.

     ![Configurações Imagem de chave API](./media/automate-custom-reports/sendgrid-manage.png)

3. Isto vai lançar o site da SendGrid. Selecione **Definições** > **Teclas API**.

     ![Criar e ver a imagem da aplicação chave API](./media/automate-custom-reports/function-app-15.png)

4. Criar uma chave API > escolha **Criar & Vista**. (Reveja a documentação da SendGrid sobre o acesso restrito para determinar o nível de permissões adequado para a sua Chave API. O Acesso Integral é selecionado aqui apenas para fins por exemplo.)

   ![Screenshot de acesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copie toda a chave, este valor é o que precisa nas definições da sua App de Função como o valor para SendGridAPI

   ![Copiar imagem de chave API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a criação de [consultas de Análise.](../../azure-monitor/log-query/get-started-queries.md)
* Saiba mais sobre a [consulta programática dos dados de Insights de Aplicações](https://dev.applicationinsights.io/)
* Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Saiba mais sobre [o Microsoft Flow](https://ms.flow.microsoft.com).
