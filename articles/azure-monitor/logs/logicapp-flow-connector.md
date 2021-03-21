---
title: Utilize registos de monitores Azure com apps lógicas Azure e automatizar energia
description: Saiba como pode utilizar apps e power automate Azure Logic para automatizar rapidamente processos repetíveis utilizando o conector Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 4a25d7a23d486c8ce22fa433cc1ead390726facc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048867"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Conector de registos Azure Monitor para Apps Lógicas e Automatização de Energia
[As Azure Logic Apps](../../logic-apps/index.yml) e [Power Automamate](https://flow.microsoft.com) permitem criar fluxos de trabalho automatizados usando centenas de ações para uma variedade de serviços. O conector Azure Monitor Logs permite-lhe construir fluxos de trabalho que recuperem dados de um espaço de trabalho do Log Analytics ou de uma aplicação de Insights de Aplicação no Azure Monitor. Este artigo descreve as ações incluídas com o conector e fornece uma passagem para construir um fluxo de trabalho usando estes dados.

Por exemplo, pode criar uma aplicação lógica para utilizar os dados de registo do Azure Monitor numa notificação de e-mail do Office 365, criar um bug em Azure DevOps ou publicar uma mensagem Slack.  Pode desencadear um fluxo de trabalho por um horário simples ou a partir de alguma ação num serviço conectado, como quando um correio ou um tweet são recebidos. 

## <a name="connector-limits"></a>Limites do conector
O conector Azure Monitor Logs tem estes limites:
* Tamanho máximo dos dados: 16 MB
* Tamanho de resposta de consulta máxima 100 MB
* Número máximo de registos: 500.000
* Max consulta prazo de 110 segundos.
* As visualizações de gráficos podem estar disponíveis na página de Logs e em falta no conector, uma vez que a página de conector e Logs não utiliza atualmente as mesmas bibliotecas de gráficos.

Dependendo do tamanho dos seus dados e da consulta que utiliza, o conector pode atingir os seus limites e falhar. Pode trabalhar em torno desses casos ao ajustar a recorrência do gatilho para executar com mais frequência e consultar menos dados. Pode utilizar consultas que agregam os seus dados para devolver menos registos e colunas.

## <a name="actions"></a>Ações
A tabela seguinte descreve as ações incluídas no conector Azure Monitor Logs. Ambos permitem executar uma consulta de registo contra um espaço de trabalho log Analytics ou aplicação Application Insights. A diferença está na forma como os dados são devolvidos.

> [!NOTE]
> O conector Azure Monitor Logs substitui o [conector Azure Log Analytics](/connectors/azureloganalytics/) e o [conector Azure Application Insights](/connectors/applicationinsights/). Este conector fornece a mesma funcionalidade que os outros e é o método preferido para executar uma consulta contra um espaço de trabalho Log Analytics ou uma aplicação Application Insights.


| Ação | Descrição |
|:---|:---|
| [Executar consulta e e listar resultados](/connectors/azuremonitorlogs/#run-query-and-list-results) | Devolve cada linha como seu próprio objeto. Utilize esta ação quando pretender trabalhar com cada linha separadamente no resto do fluxo de trabalho. A ação é normalmente seguida por um [Para cada atividade.](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop) |
| [Fazer consulta e visualizar resultados](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Devolve todas as linhas do conjunto de resultados como um único objeto formatado. Utilize esta ação quando pretender utilizar o resultado definido no resto do fluxo de trabalho, tal como o envio dos resultados por correio.  |

## <a name="walkthroughs"></a>Instruções
Os seguintes tutoriais ilustram a utilização dos conectores Azure Monitor em Azure Logic Apps. Pode executar estes mesmos exemplos com o Power Automamate, sendo a única diferença como criar o fluxo de trabalho inicial e executá-lo quando estiver concluído. A configuração do fluxo de trabalho e das ações é a mesma entre ambos. Ver [Criar um fluxo a partir de um modelo no Power Automamate](/power-automate/get-started-logic-template) para começar.


### <a name="create-a-logic-app"></a>Criar uma aplicação do Logic Apps

Vá a **Aplicações Lógicas** no portal Azure e clique em **Adicionar**. Selecione uma **Subscrição,** **Grupo de Recursos** e **Região** para armazenar a nova aplicação lógica e, em seguida, dar-lhe um nome único. Pode ligar a definição **de Log Analytics** para recolher informações sobre dados e eventos de tempo de execução, conforme descrito nos [registos do Azure Monitor e recolher dados de diagnóstico para apps Azure Logic](../../logic-apps/monitor-logic-apps-log-analytics.md). Esta definição não é necessária para a utilização do conector Azure Monitor Logs.

![Criar uma aplicação lógica](media/logicapp-flow-connector/create-logic-app.png)


Clique **em 'Rever +' criar** e, em seguida, **criar**. Quando a implementação estiver concluída, clique em **Ir ao recurso** para abrir o Design de **Aplicações Lógicas**.

### <a name="create-a-trigger-for-the-logic-app"></a>Criar um gatilho para a aplicação lógica
Em **Iniciar com um gatilho comum**, selecione **Recorrência**. Isto cria uma aplicação lógica que funciona automaticamente num intervalo regular. Na caixa de **frequência** da ação, selecione **Day** e na caixa **Interval,** introduza **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Walkthrough: Resultados visualizados por correio
O seguinte tutorial mostra-lhe como criar uma aplicação lógica que envia os resultados de uma consulta de registo do Azure Monitor por e-mail. 

### <a name="add-azure-monitor-logs-action"></a>Adicione ação de registos do monitor Azure
Clique **+ Novo passo** para adicionar uma ação que corre após a ação de recorrência. Em **Escolha uma ação**, escreva **monitor de azul** e, em seguida, selecione **Registos do Monitor Azure**.

![Ação de Registos do Monitor Azure](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Clique em **Azure Log Analytics – Faça consulta e visualize resultados.**

![Screenshot de uma nova ação sendo adicionada a um passo no Logic App Designer. Os Registos monitores Azure são destacados no Choose a action.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Adicione ação de registos do monitor Azure

Selecione o Grupo **de Subscrição** e **Recursos** para o seu espaço de trabalho Log Analytics. Selecione *Log Analytics Workspace* para o **Tipo de Recurso** e, em seguida, selecione o nome do espaço de trabalho no nome de **recurso**.

Adicione a seguinte consulta de registo à janela **'consulta'.**  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecione *Definir em consulta* para o intervalo de **tempo** e **tabela HTML** para o tipo **de gráfico**.
   
![Screenshot das definições para a nova ação Azure Monitor Logs chamada consulta run e visualizar resultados.](media/logicapp-flow-connector/run-query-visualize-action.png)

O correio será enviado pela conta associada à ligação atual. Pode especificar outra conta clicando na **ligação Change**.

### <a name="add-email-action"></a>Adicionar ação de e-mail

Clique **+ Novo passo** e, em seguida, clique + Adicione uma **ação**. Em **Escolha uma ação**, escreva **perspetivas** e, em seguida, selecione **Office 365 Outlook**.

![Selecione o conector Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selecione **Enviar um e-mail (V2)**.

![Janela de seleção do Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Clique em qualquer lugar da caixa **Body** para abrir uma janela **de conteúdo Dinâmico** abre com valores das ações anteriores na aplicação lógica. Selecione **Ver mais** e, em **seguida, Corpo** que são os resultados da consulta na ação Log Analytics.

![Selecione corpo](media/logicapp-flow-connector/select-body.png)

Especifique o endereço de e-mail de um destinatário na janela **To** e um assunto para o e-mail no **Assunto**. 

![Ação de correio](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Guarde e teste o seu aplicativo lógico
Clique **em Guardar** e, em seguida, **execute** para realizar um teste da aplicação lógica.

![Salvar e correr](media/logicapp-flow-connector/save-run.png)


Quando a aplicação lógica estiver concluída, verifique o correio do destinatário que especificou.  Devia ter recebido um e-mail com um corpo semelhante ao seguinte:

![E-mail de amostra](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [consultas de registo no Azure Monitor](./log-query-overview.md).
- Saiba mais sobre [aplicações lógicas](../../logic-apps/index.yml)
- Saiba mais sobre [o Power Automamate](https://flow.microsoft.com).