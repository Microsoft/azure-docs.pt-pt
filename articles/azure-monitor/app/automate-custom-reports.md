---
title: Automatizar relatórios personalizados com dados do Aplicativo Azure insights
description: Automatizar relatórios diários/semanais/mensais personalizados com dados do Aplicativo Azure insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 3becf5ef579acdc52a51f9ad618e37460491c2ec
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146752"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados do Aplicativo Azure insights

Os relatórios periódicos ajudam a manter uma equipe informada sobre como seus serviços críticos para os negócios estão fazendo. Os desenvolvedores, as equipes DevOps/SRE e seus gerentes podem ser produtivos com relatórios automatizados, fornecendo informações de maneira confiável sem exigir que todos entrem no Portal. Esses relatórios também podem ajudar a identificar aumentos graduais em latências, carga ou taxas de falha que podem não disparar nenhuma regra de alerta.

Cada empresa tem suas necessidades de relatórios exclusivas, como: 

* Agregações percentil específicas de métricas ou métricas personalizadas em um relatório.
* Ter relatórios diferentes para acúmulos diários, semanais e mensais de dados para públicos diferentes.
* Segmentação por atributos personalizados, como região ou ambiente. 
* Agrupe alguns recursos de ia em um único relatório, mesmo que eles possam estar em assinaturas ou grupos de recursos diferentes, etc.
* Relatórios separados contendo métricas confidenciais enviadas ao público seletivo.
* Relata para os participantes que podem não ter acesso aos recursos do Portal.

> [!NOTE] 
> O email de resumo Application Insights semanal não permitia nenhuma personalização e será descontinuado em favor das opções personalizadas listadas abaixo. O último email de resumo semanal será enviado em 11 de junho de 2018. Configure uma das opções a seguir para obter relatórios personalizados semelhantes (use a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar emails de relatório personalizados

Você pode [consultar Application insights dados programaticamente](https://dev.applicationinsights.io/) para gerar relatórios personalizados em um agendamento. As opções a seguir podem ajudá-lo a começar rapidamente:

* [Automatizar relatórios com o Microsoft Flow](automate-with-flow.md)
* [Automatizar relatórios com aplicativos lógicos](automate-with-logic-apps.md)
* Use o modelo de [função do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) "Application insights Resumo agendado" no cenário de monitoramento. Essa função usa SendGrid para entregar o email. 

    ![Modelo de função do Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exemplo de consulta para um email de resumo semanal
A consulta a seguir mostra a junção entre vários conjuntos de valores de um email de resumo semanal, como relatório. Personalize-o conforme necessário e use-o com qualquer uma das opções listadas acima para automatizar um relatório semanal.   

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

## <a name="application-insights-scheduled-digest-report"></a>Application Insights relatório de resumo agendado

1. No portal do Azure, selecione **criar um recurso** > **computação** > **aplicativo de funções**.

   ![Criar um recurso do Azure Aplicativo de funções captura de tela](./media/automate-custom-reports/function-app-01.png)

2. Insira as informações apropriadas para seu aplicativo e selecione _criar_. (Application Insights _em_ é necessário apenas se você quiser monitorar o novo Aplicativo de funções com Application insights)

   ![Criar uma captura de tela de configurações de Aplicativo de funções de recursos do Azure](./media/automate-custom-reports/function-app-02.png)

3. Depois que o novo Aplicativo de funções tiver concluído a implantação, selecione **ir para o recurso**.

4. Selecione **nova função**.

   ![Criar uma nova captura de tela de função](./media/automate-custom-reports/function-app-03.png)

5. Selecione o **_modelo de resumo Application insights agendado_** .

     > [!NOTE]
     > Por padrão, os aplicativos de funções são criados com a versão de tempo de execução 2. x. Você deve [direcionar Azure Functions tempo de execução versão](https://docs.microsoft.com/azure/azure-functions/set-runtime-version) **1. x** para usar o modelo de resumo Application insights agendado.  ![captura de tela de Runtime](./../../../includes/media/functions-view-update-version-portal/function-app-view-version.png)



   ![Nova função Application Insights captura de tela do modelo](./media/automate-custom-reports/function-app-04.png)

6. Insira um endereço de email de destinatário apropriado para seu relatório e selecione **criar**.

   ![Captura de tela de configurações de função](./media/automate-custom-reports/function-app-05.png)

7. Selecione os > recursosda > plataforma aplicativo de funções**configurações do aplicativo**.

    ![Captura de tela de configurações do aplicativo Azure function](./media/automate-custom-reports/function-app-07.png)

8. Crie três novas configurações de aplicativo com os valores ``AI_APP_ID``correspondentes ``AI_APP_KEY``apropriados, ``SendGridAPI``e. Selecione **Guardar**.

     ![Captura de tela da interface de integração de funções](./media/automate-custom-reports/function-app-08.png)
    
    (Os valores de AI_ podem ser encontrados em acesso à API para o recurso de Application Insights que você deseja relatar. Se você não tiver uma chave de API Application Insights, haverá a opção de **criar a chave de API**.)
    
   * AI_APP_ID = ID do aplicativo
   * AI_APP_KEY = chave de API
   * SendGridAPI = chave de API SendGrid

     > [!NOTE]
     > Se você não tiver uma conta do SendGrid, poderá criar uma. A documentação do SendGrid para Azure Functions está [aqui](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Se quiser apenas uma explicação mínima de como configurar o SendGrid e gerar uma chave de API, um será fornecido no final deste artigo. 

9. Selecione **integrar** e, em saídas, clique em **SendGrid ($Return)** .

     ![Captura de tela de saída](./media/automate-custom-reports/function-app-09.png)

10. Na **configuração do aplicativo de chave SendGridAPI**, selecione a configuração de aplicativo recém-criada para **SendGridAPI**.

     ![Executar Aplicativo de funções captura de tela](./media/automate-custom-reports/function-app-010.png)

11. Execute e teste seu Aplicativo de funções.

     ![Captura de tela de teste](./media/automate-custom-reports/function-app-11.png)

12. Verifique seu email para confirmar se a mensagem foi enviada/recebida com êxito.

     ![Captura de tela da linha de assunto do email](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid com o Azure

Essas etapas se aplicam somente se você ainda não tiver uma conta do SendGrid configurada.

1. No portal do Azure selecione **criar um recurso** Pesquisar para **entrega de email SendGrid** > clique em **criar** > e preencha as instruções de criação específicas do SendGrid. 

     ![Criar captura de tela de recurso SendGrid](./media/automate-custom-reports/function-app-13.png)

2. Depois de criado em contas do SendGrid, selecione **gerenciar**.

     ![Captura de tela da chave de API de configurações](./media/automate-custom-reports/function-app-14.png)

3. Isso iniciará o site do SendGrid. Selecione **configurações** > **chaves de API**.

     ![Criar e exibir captura de tela do aplicativo de chave de API](./media/automate-custom-reports/function-app-15.png)

4. Crie uma chave de API > escolha **criar & exibição** (examine a documentação do SendGrid sobre acesso restrito para determinar qual nível de permissões é apropriado para sua chave de API. O acesso completo é selecionado aqui apenas para fins de exemplo.)

   ![Captura de tela de acesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copiar a chave inteira, esse valor é o que você precisa em suas configurações de Aplicativo de funções como o valor para SendGridAPI

   ![Copiar captura de tela de chave de API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como criar [consultas de análise](../../azure-monitor/log-query/get-started-queries.md).
* Saiba mais sobre como [consultar dados de Application insights programaticamente](https://dev.applicationinsights.io/)
* Saiba mais sobre o [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).
