---
title: Utilize registos de monitores Azure com aplicações lógicas azure e automatização automática de potência
description: Saiba como pode utilizar aplicações da Lógica Azure e automatizar automaticamente para automatizar rapidamente processos repetíveis utilizando o conector Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 6961b7bd94c9b3fe70365055851c488efa2cbeca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480016"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-flow"></a>Conector de registos de monitores Azure para aplicações lógicas e fluxo
[As Aplicações Lógicas Azure](/azure/logic-apps/) e o [Power Automate](https://ms.flow.microsoft.com) permitem-lhe criar fluxos de trabalho automatizados utilizando centenas de ações para uma variedade de serviços. O conector de registos Do Monitor Azure permite-lhe construir fluxos de trabalho que recuperem dados de um espaço de trabalho do Log Analytics ou de uma aplicação De insights de aplicação no Monitor Azure. Este artigo descreve as ações incluídas no conector e fornece uma passagem para construir um fluxo de trabalho usando estes dados.

Por exemplo, pode criar uma aplicação lógica para utilizar os dados de registo do Azure Monitor numa notificação por e-mail do Office 365, criar um bug em Azure DevOps ou publicar uma mensagem Slack.  Pode desencadear um fluxo de trabalho através de um simples horário ou de alguma ação num serviço conectado, como quando um correio ou um tweet são recebidos. 

## <a name="actions"></a>Ações
A tabela seguinte descreve as ações incluídas no conector de registos do Monitor Azure. Ambos permitem fazer uma consulta de log contra um espaço de trabalho log Analytics ou aplicação Application Insights. A diferença está na forma como os dados são devolvidos.

> [!NOTE]
> O conector de registos do Monitor Azure substitui o [conector Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalytics/) e o [conector Azure Application Insights](https://docs.microsoft.com/connectors/applicationinsights/). Este conector fornece a mesma funcionalidade que os outros e é o método preferido para executar uma consulta contra um espaço de trabalho log Analytics ou uma aplicação Application Insights.


| Ação | Descrição |
|:---|:---|
| [Executar consultas e e listar resultados](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-list-results) | Devolve cada linha como objeto próprio. Utilize esta ação quando quiser trabalhar com cada linha separadamente no resto do fluxo de trabalho. A ação é tipicamente seguida por um [Para cada atividade.](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop) |
| [Executar consulta e visualizar resultados](https://docs.microsoft.com/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Devolve todas as linhas do resultado definido como um único objeto formatado. Utilize esta ação quando pretender utilizar o resultado definido no resto do fluxo de trabalho, como por exemplo o envio dos resultados por correio.  |

## <a name="walkthroughs"></a>Instruções
Os seguintes tutoriais ilustram a utilização dos conectores Azure Monitor em Aplicações Lógicas Azure. Pode executar este mesmo exemplo com power automate, sendo a única diferença como criar o fluxo de trabalho inicial e executá-lo quando estiver completo. A configuração do fluxo de trabalho e das ações é a mesma entre ambos. Consulte [Criar um fluxo de um modelo em Power Automate](https://docs.microsoft.com/power-automate/get-started-logic-template) para começar.


### <a name="create-a-logic-app"></a>Criar uma Aplicação Lógica

Vá a **Aplicações Lógicas** no portal Azure e clique em **Adicionar**. Selecione uma **Subscrição,** **grupo de Recursos**e **Região** para armazenar a nova aplicação lógica e, em seguida, dar-lhe um nome único. Pode ativar a definição de **Log Analytics** para recolher informações sobre dados e eventos de tempo de execução, conforme descrito nos [registos do Azure Monitor e recolher dados de diagnóstico para aplicações lógicas do Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Esta definição não é necessária para a utilização do conector de registos do Monitor Azure.

![Criar uma aplicação lógica](media/logicapp-flow-connector/create-logic-app.png)


Clique em **Rever + criar** e, em seguida, **criar**. Quando a implementação estiver concluída, clique em **recorrer para** abrir o Logic **Apps Designer**.

### <a name="create-a-trigger-for-the-logic-app"></a>Criar um gatilho para a aplicação lógica
Em **iniciar com um gatilho comum,** selecione **Recurrence**. Isto cria uma aplicação lógica que funciona automaticamente a um intervalo regular. Na caixa **de frequência** da ação, selecione **Day** e na caixa **de intervalo,** introduza **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Walkthrough: Resultados visualizados por correio
O seguinte tutorial mostra como criar uma aplicação lógica que envia os resultados de uma consulta de registo do Monitor Azure por e-mail. 

### <a name="add-azure-monitor-logs-action"></a>Adicione a ação de registos do Monitor Azure
Clique **+ Novo passo** para adicionar uma ação que corre após a ação de recorrência. Em **'Escolha uma ação**- **digite monitor azul** e, em seguida, selecione **Registos de Monitor Azure**.

![Ação de registos do Monitor Azure](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Clique em **Azure Log Analytics – Faça consulta e visualize os resultados**.

![Executar consulta e visualizar ação de resultados](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Adicione a ação de registos do Monitor Azure

Selecione o Grupo **de Subscrição** e **Recursos** para o seu espaço de trabalho Log Analytics. Selecione *log analytics workspace* para o **Tipo de Recurso** e, em seguida, selecione o nome do espaço de trabalho sob nome de **recurso**.

Adicione a seguinte consulta de registo à janela **Consulta.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecione *set em consulta* para o intervalo de **tempo** e **tabela HTML** para o tipo **de gráfico**.
   
![Executar consulta e visualizar ação de resultados](media/logicapp-flow-connector/run-query-visualize-action.png)

O correio será enviado pela conta associada à ligação atual. Pode especificar outra conta clicando na **ligação Change**.

### <a name="add-email-action"></a>Adicionar ação de e-mail

Clique **+ Novo passo,** e depois clique **+ Adicione uma ação**. Em **'Escolha uma ação**- digite **outlook'** e, em seguida, selecione **Office 365 Outlook**.

![Selecione conector Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selecione **Enviar um e-mail (V2)**.

![Janela de seleção do Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Clique em qualquer lugar da caixa **Body** para abrir uma janela de **conteúdo Dinâmico** abre com valores das ações anteriores na aplicação lógica. Selecione **Ver mais** e, em seguida, **Corpo** quais são os resultados da consulta na ação Log Analytics.

![Selecione o corpo](media/logicapp-flow-connector/select-body.png)

Especifique o endereço de e-mail de um destinatário na janela **Para** e um assunto para o e-mail em **Assunto**. 

![Ação de correio](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Guarde e teste a sua aplicação lógica
Clique em **Guardar** **e,** em seguida, executar para realizar um teste da aplicação lógica.

![Salvar e correr](media/logicapp-flow-connector/save-run.png)


Quando a aplicação lógica estiver concluída, verifique o correio do destinatário que especificou.  Devia ter recebido um e-mail com um corpo semelhante ao seguinte:

![E-mail de amostra](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre consultas de [log no Monitor Azure.](../log-query/log-query-overview.md)
- Saiba mais sobre [aplicações lógicas](/azure/logic-apps/)
- Saiba mais sobre [o Microsoft Flow](https://ms.flow.microsoft.com).

