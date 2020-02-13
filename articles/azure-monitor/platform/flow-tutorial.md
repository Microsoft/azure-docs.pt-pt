---
title: Automatizar processos de log do Monitor Azure com o Microsoft Flow
description: Saiba como pode utilizar o Microsoft Flow para automatizar rapidamente processos repetíveis utilizando o conector Azure Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: a6097d38d3335be356ca75f5a9d0eadeed414b03
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166939"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de log do Monitor Azure com o conector para o Microsoft Flow
O [Microsoft Flow](https://ms.flow.microsoft.com) permite-lhe criar fluxos de trabalho automatizados utilizando centenas de ações para uma variedade de serviços. A saída de uma ação pode ser usada como entrada para outra, permitindo-lhe criar integração entre diferentes serviços.  O conector Azure Log Analytics para o Microsoft Flow permite-lhe construir fluxos de trabalho que incluam dados recuperados por consultas de registo a partir de um espaço de trabalho do Log Analytics no Monitor Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Por exemplo, pode utilizar o Microsoft Flow para utilizar os dados de registo do Monitor Do Azure numa notificação de e-mail do Office 365, criar um bug em Azure DevOps ou publicar uma mensagem Slack.  Pode desencadear um fluxo de trabalho através de um simples horário ou de alguma ação num serviço conectado, como quando um correio ou um tweet são recebidos.  

O tutorial deste artigo mostra-lhe como criar um fluxo que envia automaticamente os resultados de uma consulta de registo do Monitor Azure por e-mail, apenas um exemplo de como pode utilizar o conector Log Analytics no Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Passo 1: Criar um fluxo
1. Inscreva-se no [Microsoft Flow](https://flow.microsoft.com)e selecione **My Flows**.
2. Clique **+ Criar a partir de branco**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Passo 2: Criar um gatilho para o seu fluxo
1. Clique em **Pesquisar centenas de conectores e gatilhos**.
2. Digite a **agenda** na caixa de pesquisa.
3. Selecione **Agenda,** e, em seguida, selecione **Agenda - Recorrência**.
4. Na caixa **de frequênciase** selecione **Dia** e na caixa **intervalo,** introduza **1**.<br><br>![caixa de diálogo do gatilho do Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Passo 3: Adicionar uma ação de Log Analytics
1. Clique **+ Novo passo,** e depois clique **Em adicionar uma ação**.
2. Pesquisar por **Log Analytics**.
3. Clique em **Azure Log Analytics – Faça consulta e visualize os resultados**.<br><br>![Log Analytics executar janela de consulta](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Passo 4: Configure a ação Log Analytics

1. Especifique os detalhes para o seu espaço de trabalho, incluindo o ID de subscrição, o Grupo de Recursos e o Nome do Espaço de Trabalho.
2. Adicione a seguinte consulta de registo à janela **Consulta.**  Esta é apenas uma consulta de amostra, e você pode substituir por qualquer outro que retornar os dados.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Selecione **tabela HTML** para o **Tipo de Gráfico**.<br><br>![log analytics](media/flow-tutorial/flow03.png) de ação

## <a name="step-5-configure-the-flow-to-send-email"></a>Passo 5: Configure o fluxo para enviar e-mail

1. Clique em **novo passo,** e depois clique **+ Adicione uma ação**.
2. Pesquisa rumo ao **Office 365 Outlook**.
3. Clique no **Office 365 Outlook – Envie um e-mail**.<br><br>![Office 365 Outlook janela de seleção](media/flow-tutorial/flow04.png)

4. Especifique o endereço de e-mail de um destinatário na janela **Para** e um assunto para o e-mail em **Assunto**.
5. Clique em qualquer lugar da caixa **do corpo.**  Uma janela de **conteúdo dinâmico** abre com valores de ações anteriores.  
6. Selecione **Corpo**.  Estes são os resultados da consulta na ação Log Analytics.
6. Clique em **Mostrar opções avançadas**.
7. Na caixa **Is HTML,** selecione **Sim**.<br><br>![Office 365 janela de configuração de e-mail](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Passo 6: Guardar e testar o seu fluxo
1. Na caixa de **nome Sícise,** adicione um nome para o seu fluxo e, em seguida, clique em **Criar fluxo**.<br><br>![Salvar](media/flow-tutorial/flow06.png) de fluxo
2. O fluxo é agora criado e funcionará após um dia que é o horário que especificou. 
3. Para testar imediatamente o fluxo, clique em **Run Now** **e,** em seguida, executar o fluxo .<br><br>![run](media/flow-tutorial/flow07.png) de fluxo
3. Quando o fluxo estiver concluído, verifique o correio do destinatário que especificou.  Devia ter recebido um e-mail com um corpo semelhante ao seguinte:<br><br>![E-mail de amostra](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre consultas de [log no Monitor Azure.](../log-query/log-query-overview.md)
- Saiba mais sobre [o Microsoft Flow](https://ms.flow.microsoft.com).


