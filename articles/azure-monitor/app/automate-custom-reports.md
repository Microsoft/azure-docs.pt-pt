---
title: Automatizar relatórios personalizados com dados do Azure Application Insights
description: Automatizar relatórios diários/semanais/mensais personalizados com dados do Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 37de2de18656f0f8ba85495b3fd72315e0bd885b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113083"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados do Azure Application Insights

Relatórios periódica ajudam a manter uma equipe informada sobre como os seus serviços críticos de negócios estão fazendo. Os desenvolvedores, equipes de DevOps/SRE e seus gerentes podem ser produtivos com relatórios automatizados, entrega fiável insights sem a necessidade de todos os utilizadores iniciar sessão no portal. Esses relatórios também podem ajudar a identificar o aumento gradual na latências, taxas de carga ou a falha não podem acionar quaisquer regras de alerta.

Cada empresa tem suas necessidades de geração de relatórios exclusivas, tais como: 

* Agregações de percentil específicos de métricas ou métricas personalizadas num relatório.
* Ter relatórios diferentes para diários, semanais e mensais pacotes cumulativos de dados para audiências diferentes.
* Segmentação por atributos personalizados, como a região ou ambiente. 
* Agrupe alguns recursos de IA num único relatório, mesmo que eles estejam em subscrições diferentes ou recurso grupos etc.
* Separar os relatórios que contêm métricas confidenciais enviadas para o público-alvo seletiva.
* Relatórios aos participantes que podem não ter acesso aos recursos do portais.

> [!NOTE] 
> O e-mail de resumo semanal do Application Insights não permitiu a qualquer personalização e será descontinuado em favor de opções personalizadas, listadas abaixo. O último email de resumo semanal será enviado de 11 de Junho de 2018. Configure uma das seguintes opções para obter relatórios personalizados semelhante (utilize a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar os e-mails de relatório personalizado

Pode [consultar o Application Insights através de programação](https://dev.applicationinsights.io/) dados para gerar relatórios personalizados com base numa agenda. As opções seguintes podem ajudá-lo a começar rapidamente:

* [Automatizar relatórios com o Microsoft Flow](automate-with-flow.md)
* [Automatizar relatórios com o Logic Apps](automate-with-logic-apps.md)
* Utilize o "Application Insights agendado Resumo" [função do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) modelo no cenário de monitorização. Essa função usa o SendGrid para entregar a mensagem de e-mail. 

    ![Modelo de função do Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Consulta de exemplo para um e-mail de resumo semanal
A seguinte consulta mostra a União de vários conjuntos de dados para um e-mail de resumo semanal, como o relatório. Personalizá-la conforme necessário e utilizá-lo com qualquer uma das opções listadas acima para automatizar um relatório semanal.   

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

## <a name="application-insights-scheduled-digest-report"></a>Relatório de resumo agendado do Application Insights

1. A partir do portal do Azure, selecione **criar um recurso** > **computação** > **Function App**.

   ![Criar uma captura de ecrã da aplicação de funções de recursos do Azure](./media/automate-custom-reports/function-app-01.png)

2. Introduza as informações adequadas para a sua aplicação e selecione _criar_. (Application Insights _no_ é necessário apenas se de que pretende monitorizar a sua nova aplicação de função com o Application Insights)

   ![Criar uma captura de ecrã de definições de aplicação de função de recursos do Azure](./media/automate-custom-reports/function-app-02.png)

3. Depois de concluída a implementação de sua nova aplicação de função, selecione **Ir para recurso**.

4. Selecione **nova função**.

   ![Criar uma nova captura de ecrã de função](./media/automate-custom-reports/function-app-03.png)

5. Selecione o  **_modelo de resumo agendada do Application Insights_**.

   ![Nova captura de ecrã do modelo de informações de aplicação de função](./media/automate-custom-reports/function-app-04.png)

6. Introduza um endereço de email do destinatário apropriado para o seu relatório e selecione **criar**.

   ![Captura de ecrã de definições de função](./media/automate-custom-reports/function-app-05.png)

7. Selecione seu **aplicação de funções** > **funcionalidades da plataforma** > **as definições da aplicação**.

    ![Captura de ecrã de definições do Azure a aplicação de função](./media/automate-custom-reports/function-app-07.png)

8. Criar três novas definições de aplicação com valores correspondentes corretos ``AI_APP_ID``, ``AI_APP_KEY``, e ``SendGridAPI``. Selecione **Guardar**.

     ![Captura de ecrã de interface de integração de função](./media/automate-custom-reports/function-app-08.png)
    
    (Os valores AI_ podem ser encontrados em acesso de API para o recurso do Application Insights que pretende um relatório sobre. Se não tiver uma chave de API do Application Insights, há a opção para **criar chave de API**.)
    
   * AI_APP_ID = o ID da aplicação
   * AI_APP_KEY = API Key
   * SendGridAPI = a chave de API de SendGrid

     > [!NOTE]
     > Se não tiver uma conta do SendGrid, pode criá-lo. Documentação do SendGrid para as funções do Azure está [aqui](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Se apenas quiser uma explicação um mínimo de como configurar o SendGrid e gerar uma chave de API for fornecido no final deste artigo. 

9. Selecione **integrar** e, em clique saídas **SendGrid ($return)**.

     ![Captura de ecrã da saída](./media/automate-custom-reports/function-app-09.png)

10. Sob o **definição de aplicação de chave SendGridAPI**, selecione a definição de aplicação criado recentemente para **SendGridAPI**.

     ![Executar a captura de ecrã da aplicação de funções](./media/automate-custom-reports/function-app-010.png)

11. Executar e testar a aplicação de função.

     ![Captura de ecrã do teste](./media/automate-custom-reports/function-app-11.png)

12. Verifique o seu correio eletrónico para confirmar que a mensagem foi enviado/recebido com êxito.

     ![Captura de ecrã de linha de assunto de e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid com o Azure

Estes passos aplicam-se apenas se ainda não tiver uma conta do SendGrid configurada.

1. No portal do Azure, selecione **criar um recurso** procure **entrega de correio eletrónico do SendGrid** > clique em **criar** > e preencha as específicas do SendGrid criar instruções. 

     ![Criar a captura de ecrã do recurso de SendGrid](./media/automate-custom-reports/function-app-13.png)

2. Selecione uma vez criado em contas de SendGrid **gerir**.

     ![Captura de ecrã de chave de API de definições](./media/automate-custom-reports/function-app-14.png)

3. Este procedimento inicia o site do SendGrid. Selecione **configurações** > **chaves de API**.

     ![Criar e ver a captura de ecrã de aplicação de chave de API](./media/automate-custom-reports/function-app-15.png)

4. Criar uma chave de API > Escolha **Criar & vista** (reveja a documentação do SendGrid no acesso restrito para determinar qual o nível de permissões é apropriado para a sua chave de API. Acesso total está selecionado aqui para apenas a fins de exemplo.)

   ![Captura de ecrã de acesso total](./media/automate-custom-reports/function-app-16.png)

5. Copie a chave completa, este valor é o que precisa nas definições da aplicação de funções como o valor para SendGridAPI

   ![Copiar captura de tela de chave de API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a criação [consultas de análise](../../azure-monitor/log-query/get-started-queries.md).
* Saiba mais sobre [programaticamente consultar dados do Application Insights](https://dev.applicationinsights.io/)
* Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).
