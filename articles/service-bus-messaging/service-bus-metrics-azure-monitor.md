---
title: Métricas de barramento de serviço do Azure no Azure Monitor (pré-visualização) | Documentos da Microsoft
description: Utilizar a monitorização do Azure para monitorizar as entidades do Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: e16b523a366cabd04a5f12441874aa60460d508f
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213982"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Métricas de barramento de serviço do Azure no Azure Monitor (pré-visualização)

Métricas de barramento de serviço oferecem-lhe o estado dos recursos na sua subscrição do Azure. Com um vasto conjunto de dados de métricas, pode avaliar o estado de funcionamento geral dos seus recursos do Service Bus, não apenas ao nível do espaço de nomes, mas também no nível de entidade. Essas estatísticas podem ser importantes como eles ajudam-na monitorizar o estado do Service Bus. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

> [!IMPORTANT]
> Quando não tiver ocorrido qualquer interação com uma entidade durante 2 horas, iniciará as métricas que mostra como um valor "0" até que a entidade já não está ociosa.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. É possível que qualquer uma das métricas de acesso através da [portal do Azure](https://portal.azure.com), ou utilize o Azure Monitor APIs (REST e .NET) e soluções de análise de como os registos do Azure Monitor e dos Hubs de eventos. Para obter mais informações, consulte [dados de monitorização recolhidos pelo Azure Monitor](../azure-monitor/platform/data-collection.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este valor está configurado nas [das definições de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para tal, selecione o seu espaço de nomes e, em seguida, clique em **métricas (pré-visualização)**. Para apresentar métricas filtradas para o âmbito da entidade, selecione a entidade e, em seguida, clique em **métricas (pré-visualização)**.

![][2]

Para as métricas que suporta dimensões, tem de filtrar com o valor de dimensão pretendida.

## <a name="billing"></a>Faturação

Uso de métricas no Azure Monitor é gratuita em pré-visualização. No entanto, se utilizar soluções adicionais que ingerir dados de métricas, pode ser cobrado por estas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Também são cobradas pelos registos do Azure Monitor se os transmitir dados de métricas para os registos do Azure Monitor para análises avançadas.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo suportado para todas as métricas do Service Bus é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos de entrada (pré-visualização) | O número de pedidos efetuados para o serviço do Service Bus num determinado período. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Pedidos com êxito (pré-visualização)|O número de pedidos com êxito efetuados para o serviço do Service Bus num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros de servidor (pré-visualização)|O número de pedidos não processadas devido a um erro no serviço do Service Bus num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros de utilizador (pré-visualizar - veja a subsecção seguinte)|O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Pedidos limitados (pré-visualização)|O número de pedidos que foram limitado porque a utilização foi excedida.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

### <a name="user-errors"></a>Erros de utilizador

Os seguintes dois tipos de erros são classificados como erros de utilizador:

1. Erros do lado do cliente (em HTTP que seriam 400 erros).
2. Erros que ocorrem durante o processamento das mensagens, tal como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada (pré-visualização)|O número de eventos ou as mensagens enviadas para o Service Bus num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída (pré-visualização)|O número de eventos ou mensagens recebidas do Service Bus num determinado período.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
| Mensagens (pré-visualização) | Contagem de mensagens em fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |
| ActiveMessages (pré-visualização) | Contagem de mensagens ativas numa fila/tópico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Dimensão: EntityName |

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections (pré-visualização)|O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ligações aberto (pré-visualização)|O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Ligações fechado (pré-visualização)|O número de conexões encerradas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |

## <a name="resource-usage-metrics"></a>Métricas de utilização de recursos

> [!NOTE] 
> As métricas seguintes estão disponíveis apenas com o **premium** escalão. 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Utilização da CPU por espaço de nomes (pré-visualização)|A percentagem de utilização da CPU do espaço de nomes.<br/><br/> Unidade: Percentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|
|Utilização de tamanho de memória por espaço de nomes (pré-visualização)|A utilização de memória de percentagem do espaço de nomes.<br/><br/> Unidade: Percentagem <br/> Tipo de agregação: Máximo <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

O Azure Service Bus suporta as seguintes dimensões de métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome da dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| Service Bus suporta entidades de mensagens sob o espaço de nomes.|

## <a name="set-up-alerts-on-metrics"></a>Configure alertas em métricas

1. Sobre o **métricas** separador do **o espaço de nomes do Service Bus** página, selecione **configurar alertas**. 

    ![Métricas de página - configurar o menu de alertas](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecione **selecionar destino**, e fazer as seguintes ações no **selecione um recurso** página: 
    1. Selecione **espaços de nomes do Service Bus** para o **filtrar por tipo de recurso** campo. 
    2. Selecione a sua subscrição para o **filtrar por subscrição** campo.
    3. Selecione o **espaço de nomes do service bus** da lista. 
    4. Selecione **Done** (Concluído). 
    
        ![Selecionar espaço de nomes](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecione **adicionar critérios**, e fazer as seguintes ações no **configurar lógica de sinal** página:
    1. Selecione **métricas** para **sinalizar tipo**. 
    2. Selecione um sinal. Por exemplo: **Erros (pré-visualização) do serviço**. 

        ![Selecione erros de servidor](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecione **maior** para **condição**.
    2. Selecione **Total** para **agregação de tempo**. 
    3. Introduza **5** para **limiar**. 
    4. Selecione **Done** (Concluído).    

        ![Especifique a condição](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Sobre o **criar regra** página, expanda **definir detalhes do alerta**, e fazer as seguintes ações:
    1. Introduza um **nome** para o alerta. 
    2. Introduza um **Descrição** para o alerta.
    3. Selecione **gravidade** para o alerta. 

        ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na **criar regra** página, expanda **grupo de ação de definir**, selecione **novo grupo de ação**, e fazer as seguintes ações no **página de grupo de ação de adicionar**. 
    1. Introduza um nome para o grupo de ação.
    2. Introduza um nome curto para o grupo de ação. 
    3. Selecione a sua subscrição. 
    4. Selecione um grupo de recursos. 
    5. Nestas instruções, introduza **enviar e-mail** para **nome da ação**.
    6. Selecione **E-Mail/SMS/Push/voz** para **tipo de ação**. 
    7. Selecione **editar detalhes**. 
    8. Sobre o **E-Mail/SMS/Push/voz** página, efetue as seguintes ações:
        1. Selecione **E-Mail**. 
        2. Tipo de **endereço de e-mail**. 
        3. Selecione **OK**.

            ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Sobre o **adicionar grupo de ação** página, selecione **OK**. 
1. Sobre o **criar regra** página, selecione **criar regra de alerta**. 

    ![Crie um botão de regra de alerta](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passos Seguintes

Consulte a [descrição geral da monitorização do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


