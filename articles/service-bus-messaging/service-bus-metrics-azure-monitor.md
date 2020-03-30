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
ms.openlocfilehash: 227dfaff211eb60c5c2b25b5c76ecc82b6ce3edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240792"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Métricas de ônibus de serviço azure no Monitor Azure

As métricas do Ônibus de serviço dão-lhe o estado dos recursos na sua subscrição Azure. Com um conjunto rico de dados de métricas, você pode avaliar a saúde geral dos seus recursos de Ônibus de serviço, não só ao nível do espaço de nome, mas também ao nível da entidade. Estas estatísticas podem ser importantes porque o ajudam a monitorizar o estado do Ônibus de Serviço. As métricas também podem ajudar a resolver problemas de causa sem necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces unificadas de utilizador para monitorização em vários serviços Do Azure. Para mais informações, consulte [a monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e nas [métricas do Monitor Retrieve Azure com](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) a amostra .NET no GitHub.

> [!IMPORTANT]
> Quando não houve qualquer interação com uma entidade durante 2 horas, as métricas começarão a mostrar "0" como valor até que a entidade já não esteja inativa.

## <a name="access-metrics"></a>Métricas de acesso

O Monitor Azure fornece várias formas de aceder às métricas. Pode aceder às métricas através do [portal Azure,](https://portal.azure.com)ou utilizar as APIs do Monitor Azure (REST e .NET) e soluções de análise como registos do Monitor Azure e Centros de Eventos. Para mais informações, consulte [Métricas no Monitor Azure](../azure-monitor/platform/data-platform-metrics.md).

As métricas são ativadas por padrão, e você pode aceder aos mais recentes 30 dias de dados. Se precisar de reter dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Este valor está configurado em [configurações](../azure-monitor/platform/diagnostic-settings.md) de diagnóstico no Monitor Azure.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo no [portal Azure.](https://portal.azure.com) O exemplo que se segue mostra como visualizar pedidos bem sucedidos e pedidos de entrada ao nível da conta:

![][1]

Também pode aceder às métricas diretamente através do espaço de nome. Para isso, selecione o seu espaço de nome e, em seguida, clique em **Métricas**. Para visualizar métricas filtradas ao alcance da entidade, selecione a entidade e, em seguida, clique em **Métricas**.

![][2]

Para métricas que suportem dimensões, deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

As métricas e alertas no Monitor Azure são carregados por alerta. Estes encargos devem estar disponíveis no portal quando o alerta estiver configurado e antes de ser guardado. 

Soluções adicionais que ingebem dados métricos são faturadas diretamente por essas soluções. Por exemplo, é cobrado pelo Azure Storage se arquivar dados de métricas para uma conta de Armazenamento Azure. Também é cobrado pelo Log Analytics se transmitir dados de métricas para Log Analytics para análise supérflua avançada.

As seguintes métricas dão-lhe uma visão geral da saúde do seu serviço. 

> [!NOTE]
> Estamos a depreciar várias métricas à medida que são movidas com um nome diferente. Isto pode exigir que atualize as suas referências. As métricas marcadas com a palavra-chave "depreciada" não serão suportadas para avançar.

Todos os valores das métricas são enviados para o Monitor Azure a cada minuto. A granularidade do tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Ônibus de serviço é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedido

Conta o número de pedidos de operações de dados e de gestão.

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Pedidos de Entrada| O número de pedidos feitos ao serviço de autocarros de serviço durante um período determinado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Pedidos bem sucedidos|O número de pedidos bem sucedidos feitos ao serviço de ônibus de serviço durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Erros do servidor|O número de pedidos não processados devido a um erro no serviço de ônibus de serviço durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Erros do utilizador (ver a seguinte subsecção)|O número de pedidos não processados devido a erros do utilizador durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Pedidos estrangulados|O número de pedidos que foram acelerados porque o uso foi ultrapassado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

### <a name="user-errors"></a>Erros do utilizador

Os dois tipos seguintes de erros são classificados como erros do utilizador:

1. Erros do lado do cliente (em HTTP seriam 400 erros).
2. Erros que ocorrem durante o processamento de mensagens, tais como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagem

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada|O número de eventos ou mensagens enviados para o Service Bus durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
|Mensagens de saída|O número de eventos ou mensagens recebidas do Service Bus durante um período determinado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|
| Mensagens| Contagem de mensagens numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome de entidade |
| Mensagens Ativas| Contagem de mensagens ativas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: Nome de entidade |
| Mensagens com letras mortas| Conde de mensagens com letras mortas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/>Dimensão: Nome de entidade |
| Mensagens programadas| Contagem de mensagens programadas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média  <br/> Dimensão: Nome de entidade |

> [!NOTE]
> Os valores para as seguintes métricas são valores ponto-a-tempo. As mensagens que foram consumidas imediatamente após esse ponto-no-tempo podem não se refletir nestas métricas. 
> - Mensagens
> - Mensagens ativas 
> - Mensagens com letras mortas 
> - Mensagens programadas 

## <a name="connection-metrics"></a>Métricas de ligação

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|ActiveConnections|O número de ligações ativas num espaço de nome, bem como numa entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: Nome de entidade|

## <a name="resource-usage-metrics"></a>Métricas de utilização de recursos

> [!NOTE] 
> As seguintes métricas estão disponíveis apenas com o nível **premium.** 
> 
> As métricas importantes para monitorizar para quaisquer interrupções para um espaço de nome de nível premium são: utilização de **CPU por espaço** de nome e tamanho de **memória por espaço de nome**. [Instale alertas](../azure-monitor/platform/alerts-metric.md) para estas métricas utilizando o Monitor Azure.
> 
> A outra métrica que pode monitorizar é: **pedidos acelerados.** Não deve ser um problema, desde que o espaço de nome permaneça dentro dos limites de memória, CPU e ligações intermediadas. Para mais informações, consulte [Throttling no nível Azure Service Bus Premium](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Nome métrico | Descrição |
| ------------------- | ----------------- |
|Utilização do CPU por espaço de nome|A percentagem de utilização do CPU do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: Nome de entidade|
|Utilização do tamanho da memória por espaço de nome|A percentagem de uso da memória do espaço de nome.<br/><br/> Unidade: Por cento <br/> Tipo de agregação: Máximo <br/> Dimensão: Nome de entidade|

## <a name="metrics-dimensions"></a>Dimensões métricas

O Azure Service Bus suporta as seguintes dimensões para métricas no Monitor Azure. Adicionar dimensões às suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas ao nível do espaço de nome. 

|Nome de dimensão|Descrição|
| ------------------- | ----------------- |
|Nome da entidade| O Service Bus suporta entidades de mensagens sob o espaço de nome.|

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


