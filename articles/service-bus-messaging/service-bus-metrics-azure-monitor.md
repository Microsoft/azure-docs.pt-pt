---
title: Métricas do barramento de serviço do Azure no Azure Monitor | Microsoft Docs
description: Usar Azure Monitor para monitorar entidades do barramento de serviço
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 6d25bdf6ff8e790466f3a28e3b6043e347d74198
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261849"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Métricas do barramento de serviço do Azure no Azure Monitor

As métricas do barramento de serviço oferecem o estado dos recursos em sua assinatura do Azure. Com um rico conjunto de dados de métricas, você pode avaliar a integridade geral dos recursos do barramento de serviço, não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois ajudam você a monitorar o estado do barramento de serviço. Métricas também podem ajudar a resolver problemas de causa raiz sem a necessidade de contactar o suporte do Azure.

O Azure Monitor fornece interfaces do usuário unificada para monitoramento em vários serviços do Azure. Para obter mais informações, consulte [monitorização no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o [métricas de obter o Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) exemplo no GitHub.

> [!IMPORTANT]
> Quando não houve nenhuma interação com uma entidade por 2 horas, as métricas começarão a mostrar "0" como um valor até que a entidade não fique mais ociosa.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Você pode acessar as métricas por meio do [portal do Azure](https://portal.azure.com)ou usar as APIs de Azure monitor (REST e .net) e as soluções de análise, como logs de Azure monitor e hubs de eventos. Para obter mais informações, consulte [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

As métricas estão ativadas por predefinição e pode aceder os mais recentes 30 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Esse valor é definido nas [configurações de diagnóstico](../azure-monitor/platform/diagnostic-settings.md) no Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Métricas de acesso no portal

Pode monitorizar as métricas ao longo do tempo na [portal do Azure](https://portal.azure.com). O exemplo seguinte mostra como ver pedidos com êxito e solicitações de entrada ao nível da conta:

![][1]

Também pode aceder a métricas diretamente através do espaço de nomes. Para fazer isso, selecione o namespace e clique em **métricas**. Para exibir as métricas filtradas para o escopo da entidade, selecione a entidade e clique em **métricas**.

![][2]

Para métricas com suporte a dimensões, você deve filtrar com o valor de dimensão desejado.

## <a name="billing"></a>Faturação

As métricas e alertas em Azure Monitor são cobrados por alerta. Esses encargos devem estar disponíveis no portal quando o alerta for configurado e antes de ser salvo. 

Soluções adicionais que ingerir dados de métricas são cobradas diretamente por essas soluções. Por exemplo, é cobrado pelo armazenamento do Azure se arquivar dados de métricas para uma conta de armazenamento do Azure. Você também será cobrado por Log Analytics se você transmitir dados de métricas para Log Analytics para análise avançada.

As métricas seguintes dão-lhe uma descrição geral do Estado de funcionamento do seu serviço. 

> [!NOTE]
> Podemos são descontinuar várias métricas, como eles são movidos sob um nome diferente. Isto pode requerer a atualização de suas referências. Métricas marcadas com a palavra-chave "preterido" não serão suportadas no futuro.

Todos os valores de métricas são enviados para o Azure Monitor, a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do barramento de serviço é de 1 minuto.

## <a name="request-metrics"></a>Métricas de pedidos

Conta o número de pedidos de operações de gestão e de dados.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Pedidos Recebidos| O número de solicitações feitas ao serviço do barramento de serviço durante um período especificado. <br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Pedidos Com Êxito|O número de solicitações bem-sucedidas feitas ao serviço do barramento de serviço durante um período especificado.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Erros no Servidor|O número de solicitações não processadas devido a um erro no serviço do barramento de serviço durante um período especificado.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Erros do usuário (consulte a seguinte subseção)|O número de pedidos não processadas devido a erros de utilizador num determinado período.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Solicitações limitadas|O número de solicitações que foram limitadas porque o uso foi excedido.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

### <a name="user-errors"></a>Erros do usuário

Os dois tipos de erros a seguir são classificados como erros do usuário:

1. Erros do lado do cliente (em HTTP que seriam 400 erros).
2. Erros que ocorrem durante o processamento de mensagens, como [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Métricas de mensagem

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens Recebidas|O número de eventos ou mensagens enviadas ao barramento de serviço durante um período especificado.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
|Mensagens Enviadas|O número de eventos ou mensagens recebidas do barramento de serviço durante um período especificado.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|
| Mensagens| Contagem de mensagens em uma fila/tópico. <br/><br/> Unidade Count <br/> Tipo de agregação: Average <br/> Dimensões EntityName |
| ActiveMessages| Contagem de mensagens ativas em uma fila/tópico. <br/><br/> Unidade Count <br/> Tipo de agregação: Average <br/> Dimensões EntityName |
| Mensagens inativas| Contagem de mensagens mortas em uma fila/tópico. <br/><br/> Unidade Count <br/> Tipo de agregação: Average <br/>Dimensões EntityName |
| Mensagens agendadas| Contagem de mensagens agendadas em uma fila/tópico. <br/><br/> Unidade Count <br/> Tipo de agregação: Average  <br/> Dimensões EntityName |

## <a name="connection-metrics"></a>Métricas de ligação

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections|O número de ligações ativas num espaço de nomes, bem como numa entidade.<br/><br/> Unidade Count <br/> Tipo de agregação: Total <br/> Dimensões EntityName|

## <a name="resource-usage-metrics"></a>Métricas de uso de recursos

> [!NOTE] 
> As métricas a seguir estão disponíveis apenas com a camada **Premium** . 

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
|Uso da CPU por namespace|O percentual de uso da CPU do namespace.<br/><br/> Unidade Percent <br/> Tipo de agregação: Máximo <br/> Dimensões EntityName|
|Uso do tamanho da memória por namespace|O percentual de uso de memória do namespace.<br/><br/> Unidade Percent <br/> Tipo de agregação: Máximo <br/> Dimensões EntityName|

## <a name="metrics-dimensions"></a>Dimensões de métricas

O barramento de serviço do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões para as suas métricas é opcional. Se não adicionar dimensões, as métricas são especificadas no nível do espaço de nomes. 

|Nome da dimensão|Descrição|
| ------------------- | ----------------- |
|EntityName| O barramento de serviço dá suporte a entidades de mensagens no namespace.|

## <a name="set-up-alerts-on-metrics"></a>Configurar alertas em métricas

1. Na guia **métricas** da página **namespace do barramento de serviço** , selecione **configurar alertas**. 

    ![Página de métricas – menu configurar alertas](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecione a opção **Selecionar destino** e execute as seguintes ações na página **selecionar um recurso** : 
    1. Selecione os **namespaces do barramento de serviço** para o campo **Filtrar por tipo de recurso** . 
    2. Selecione sua assinatura para o campo **Filtrar por assinatura** .
    3. Selecione o **namespace do barramento de serviço** na lista. 
    4. Selecione **Done** (Concluído). 
    
        ![Selecione um espaço de nomes](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecione **Adicionar critérios**e execute as seguintes ações na página **Configurar lógica de sinal** :
    1. Selecione **métricas** para **tipo de sinal**. 
    2. Selecione um sinal. Por exemplo: **Erros de serviço**. 

        ![Selecionar erros do servidor](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecione **maior do que** para **condição**.
    2. Selecione **total** para **agregação de tempo**. 
    3. Insira **5** para **limite**. 
    4. Selecione **Done** (Concluído).    

        ![Especificar condição](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na página **criar regra** , expanda **definir detalhes do alerta**e execute as seguintes ações:
    1. Insira um **nome** para o alerta. 
    2. Insira uma **Descrição** para o alerta.
    3. Selecione **severidade** para o alerta. 

        ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na página **criar regra** , expanda **definir grupo de ações**, selecione **novo grupo**de ações e execute as ações a seguir na **página Adicionar grupo de ações**. 
    1. Insira um nome para o grupo de ações.
    2. Insira um nome curto para o grupo de ação. 
    3. Selecione a sua subscrição. 
    4. Selecione um grupo de recursos. 
    5. Para esta explicação, digite **Enviar email** para o **nome da ação**.
    6. Selecione **email/SMS/Push/voz** para o **tipo de ação**. 
    7. Selecione **Editar detalhes**. 
    8. Na página **email/SMS/Push/voz** , execute as seguintes ações:
        1. Selecione **email**. 
        2. Digite o **endereço de email**. 
        3. Selecione **OK**.

            ![Detalhes do alerta](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na página **Adicionar grupo de ações** , selecione **OK**. 
1. Na página **criar regra** , selecione **criar regra de alerta**. 

    ![Botão Criar regra de alerta](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Passos seguintes

Consulte a [visão geral do Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


