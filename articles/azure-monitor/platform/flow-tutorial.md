---
title: Automatizar processos de log de Azure Monitor com Microsoft Flow
description: Saiba como você pode usar Microsoft Flow para automatizar rapidamente os processos repetíveis usando o conector de Log Analytics do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: c74ff0f4d23df8f906870c3810a699db254d70b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932322"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de log de Azure Monitor com o conector para Microsoft Flow
[Microsoft Flow](https://ms.flow.microsoft.com) permite que você crie fluxos de trabalho automatizados usando centenas de ações para uma variedade de serviços. A saída de uma ação pode ser usada como entrada para outra, permitindo que você crie a integração entre diferentes serviços.  O conector do Log Analytics do Azure para Microsoft Flow permite que você crie fluxos de trabalho que incluem dados recuperados por consultas de log de um espaço Log Analytics no Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Por exemplo, você pode usar Microsoft Flow para usar Azure Monitor dados de log em uma notificação por email do Office 365, criar um bug no Azure DevOps ou postar uma mensagem de margem de atraso.  Você pode disparar um fluxo de trabalho por um agendamento simples ou por alguma ação em um serviço conectado, como quando um email ou um tweet é recebido.  

O tutorial neste artigo mostra como criar um fluxo que envia automaticamente os resultados de uma consulta de log de Azure Monitor por email, apenas um exemplo de como você pode usar o conector de Log Analytics no Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Etapa 1: criar um fluxo
1. Entre no [Microsoft Flow](https://flow.microsoft.com)e selecione **meus fluxos**.
2. Clique em **+ criar em branco**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: criar um gatilho para o fluxo
1. Clique em **Pesquisar centenas de conectores e gatilhos**.
2. Digite **agendamento** na caixa de pesquisa.
3. Selecione **agenda**e, em seguida, selecione **agenda-recorrência**.
4. Na caixa **frequência** , selecione **dia** e, na caixa **intervalo** , digite **1**.<br><br>![caixa de diálogo gatilho de Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Etapa 3: adicionar uma ação de Log Analytics
1. Clique em **+ nova etapa**e, em seguida, clique em **Adicionar uma ação**.
2. Procure **log Analytics**.
3. Clique em **log Analytics do Azure – executar consulta e Visualizar resultados**.<br><br>![Log Analytics janela de consulta de execução](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Etapa 4: configurar a ação de Log Analytics

1. Especifique os detalhes do seu espaço de trabalho, incluindo a ID da assinatura, o grupo de recursos e o nome do espaço de trabalho.
2. Adicione a seguinte consulta de log à janela de **consulta** .  Essa é apenas uma consulta de exemplo e você pode substituir por qualquer outra que retorne dados.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Selecione a **tabela HTML** para o **tipo de gráfico**.<br><br>![Log Analytics ação](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Etapa 5: configurar o fluxo para enviar email

1. Clique em **nova etapa**e, em seguida, clique em **+ Adicionar uma ação**.
2. Pesquise o **Outlook do Office 365**.
3. Clique em **Office 365 Outlook – enviar um email**.<br><br>![janela de seleção do Outlook do Office 365](media/flow-tutorial/flow04.png)

4. Especifique o endereço de email de um destinatário na janela **para** e um assunto para o email no **assunto**.
5. Clique em qualquer lugar na caixa **corpo** .  Uma janela de **conteúdo dinâmico** é aberta com valores de ações anteriores.  
6. Selecione **corpo**.  Estes são os resultados da consulta na ação de Log Analytics.
6. Clique em **Mostrar opções avançadas**.
7. Na caixa **é HTML** , selecione **Sim**.<br><br>![janela de configuração de email do Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Etapa 6: salvar e testar seu fluxo
1. Na caixa **nome do fluxo** , adicione um nome para o fluxo e, em seguida, clique em **criar fluxo**.<br><br>![salvar fluxo](media/flow-tutorial/flow06.png)
2. O fluxo agora é criado e será executado após um dia que é o agendamento especificado. 
3. Para testar o fluxo imediatamente, clique em **executar agora** e em **executar fluxo**.<br><br>![](media/flow-tutorial/flow07.png) de fluxo de execução
3. Quando o fluxo for concluído, verifique o email do destinatário que você especificou.  Você deve ter recebido um email com um corpo semelhante ao seguinte:<br><br>![Email de exemplo](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [consultas de log no Azure monitor](../log-query/log-query-overview.md).
- Saiba mais sobre [Microsoft Flow](https://ms.flow.microsoft.com).



