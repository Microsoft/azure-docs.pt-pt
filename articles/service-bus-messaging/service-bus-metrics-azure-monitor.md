---
title: Métricas de ônibus de serviço azure no Monitor Azure Microsoft Docs
description: Este artigo explica como usar o Monitor Azure para monitorizar as entidades de ônibus de serviço (filas, tópicos e subscrições).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 329b930c950ea7c58bdac798fce51af152aa8ff3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920608"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Métricas de ônibus de serviço azure no Monitor Azure

As métricas do Ônibus de serviço dão-lhe o estado dos recursos na sua subscrição Azure. Com um conjunto rico de dados de métricas, você pode avaliar a saúde geral dos seus recursos de Ônibus de serviço, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes porque o ajudam a monitorizar o estado do Ônibus de Serviço. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Quando não houve qualquer interação com uma entidade durante 2 horas, as métricas começarão a mostrar "0" como valor até que a entidade já não esteja inativa.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como registos do Monitor Azure e Centros de Eventos. Para mais informações, consulte [Métricas no Monitor Azure](../azure-monitor/platform/data-platform-metrics.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este valor está configurado em [configurações](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Métricas**. Para visualizar métricas filtradas ao alcance da entidade, selecione a entidade e, em seguida, clique em **Métricas**.

![][2]

Para métricas que suportem dimensões, deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

As métricas e alertas no Monitor Azure são carregados por alerta. Estes encargos devem estar disponíveis no portal quando o alerta estiver configurado e antes de ser guardado. 

Soluções adicionais que ingebem dados métricos são faturadas diretamente por essas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também é cobrado pelo Log Analytics se transmitir dados de métricas para Log Analytics para análise supérflua avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Ônibus de serviço é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos de Entrada| O número de pedidos feitos ao serviço de autocarros de serviço durante um período determinado. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Pedidos bem sucedidos|O número de pedidos bem sucedidos feitos ao serviço de ônibus de serviço durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do servidor|O número de pedidos não processados devido a um erro no serviço de ônibus de serviço durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros do utilizador (ver a seguinte subsecção)|O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Pedidos estrangulados|O número de pedidos que foram acelerados porque o uso foi ultrapassado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

### <a name="user-errors"></a>Erros do utilizador

Os dois tipos seguintes de erros são classificados como erros do utilizador:

1. Erros do lado do cliente (em HTTP seriam 400 erros).
2. Erros que ocorrem durante o processamento de mensagens, tais como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada|O número de eventos ou mensagens enviados para o Service Bus durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída|O número de eventos ou mensagens recebidas do Service Bus durante um período determinado.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
| Mensagens| Contagem de mensagens numa fila/tópico. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |
| ActiveMessages| Contagem de mensagens ativas numa fila/tópico. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |
| Mensagens com letras mortas| Conde de mensagens com letras mortas numa fila/tópico. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Média <br/>Dimensão: EntityName |
| Mensagens programadas| Contagem de mensagens programadas numa fila/tópico. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Média  <br/> Dimensão: EntityName |

> [!NOTE]
> Os valores para as seguintes métricas são valores ponto-a-tempo. As mensagens que foram consumidas imediatamente após esse ponto-no-tempo podem não se refletir nestas métricas. 
> - Mensagens
> - Mensagens ativas 
> - Mensagens com letras mortas 
> - Mensagens programadas 

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections|O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="resource-usage-metrics"></a>Métricas de utilização de recursos

> [!NOTE] 
> As seguintes métricas estão disponíveis apenas com o nível **premium.** 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Utilização do CPU por espaço de nome|A percentagem de utilização do CPU do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|
|Utilização do tamanho da memória por espaço de nome|A percentagem de uso da memória do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

O Azure Service Bus suporta as seguintes dimensões para métricas no Monitor Azure. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome de dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| O Service Bus suporta entidades de mensagens sob o espaço de nome.|

## <a name="set-up-alerts-on-metrics"></a>Configurar alertas sobre métricas

1. No separador **Métricas** da página Nomespace do autocarro de **serviço,** selecione **alertas Configure**. 

    ![Página de métricas - Configure menu de alertas](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecione a **opção'alvo Select** e faça as seguintes ações na página **Select a resource:** 
    1. Selecione espaços de nome de autocarro de **serviço** para o filtro por tipo **de recurso.** 
    2. Selecione a sua subscrição para o Filtro por campo de **subscrição.**
    3. Selecione o espaço de nome do **ônibus** de serviço da lista. 
    4. Selecione **Done** (Concluído). 
    
        ![Selecionar espaço de nomes](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecione **Adicionar critérios**e fazer as seguintes ações na página lógica do **sinal Configurar:**
    1. Selecione **Métricas** para **o tipo de sinal**. 
    2. Selecione um sinal. Por exemplo: **Erros de serviço**. 

        ![Selecione erros do servidor](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. **Selecione Maior do que** para **condição**.
    2. Selecione **Total** para **Agregação**de Tempo . 
    3. Insira **5** para **Threshold**. 
    4. Selecione **Done** (Concluído).    

        ![Especificar condição](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na página de **regra Criar,** expandir definir detalhes de **alerta,** e fazer as seguintes ações:
    1. Insira um **nome** para o alerta. 
    2. Insira uma **descrição** para o alerta.
    3. Selecione **a severidade** para o alerta. 

        ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na página de **regra Criar,** expandir o grupo de **ação Define,** selecionar Novo grupo de **ação,** e fazer as seguintes ações na página do grupo de **ação Add**. 
    1. Insira um nome para o grupo de ação.
    2. Insira um pequeno nome para o grupo de ação. 
    3. Selecione a sua subscrição. 
    4. Selecione um grupo de recursos. 
    5. Para este passe, insira **Enviar e-mail** para **ACTION NAME**.
    6. Selecione **e-mail/SMS/Push/Voice** for **ACTION TYPE**. 
    7. Selecione **Editar detalhes**. 
    8. Na página **email/SMS/Push/Voice,** faça as seguintes ações:
        1. Selecione **E-mail**. 
        2. Digite o endereço de **e-mail**. 
        3. Selecione **OK**.

            ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na página do **grupo de ação Adicionar,** selecione **OK**. 
1. Na página de **regra Criar,** selecione **Criar regra**de alerta . 

    ![Criar botão de regra de alerta](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passos seguintes

Consulte a visão geral do [Monitor Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


