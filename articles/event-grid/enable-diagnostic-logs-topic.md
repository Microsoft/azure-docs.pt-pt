---
title: Grelha de Eventos Azure - Ativar registos de diagnóstico para tópicos ou domínios
description: Este artigo fornece instruções passo a passo sobre como ativar registos de diagnóstico para um tópico de grelha de eventos Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: 13a2168c854475b841b0ebc52bb678c7ca22a1bb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626467"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Ativar registos de diagnóstico para tópicos ou domínios da grelha de eventos do Azure
As definições de diagnóstico permitem que os utilizadores da Rede de Eventos capturem e vejam registos de falhas de **publicação e entrega** numa conta de Armazenamento, num hub de eventos ou num Espaço de Trabalho de Log Analytics. Este artigo fornece instruções passo a passo para ativar estas definições num tópico de grelha de eventos.

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grelha de eventos provisionado
- Um destino previsto para a captura de registos de diagnóstico. Pode ser um dos seguintes destinos no mesmo local que o tópico da grelha de eventos:
    - Conta de armazenamento do Azure
    - Hub de eventos
    - Área de trabalho do Log Analytics

## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Passos para ativar registos de diagnóstico para um tópico

> [!NOTE]
> O procedimento seguinte fornece instruções passo a passo para permitir registos de diagnóstico para um tópico. Os passos para permitir registos de diagnóstico para um domínio são muito semelhantes. No passo 2, navegue para o **domínio** da grelha de eventos no portal Azure.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Navegue para o tópico da grelha de eventos para o qual pretende ativar as definições de registo de diagnóstico. 
3. Selecione **definições** de diagnóstico sob **monitorização** no menu esquerdo.
4. Na página de **definições** de Diagnóstico, selecione **Adicionar Nova Definição de Diagnóstico**. 
    
    ![Adicione o botão de definição de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a definição de diagnóstico. 
7. Selecione as opções de Falhas de **Entrega** e **De publicação** na secção **Registo.** 
    ![Selecione as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
6. Ative um ou mais destinos de captura para os registos e, em seguida, configurá-los selecionando um recurso de captura criado anteriormente. 
    - Se selecionar o **Arquivo para uma conta**de armazenamento , selecione a conta de armazenamento - **Configure,** e, em seguida, selecione a conta de armazenamento na sua subscrição Azure. 

        ![Arquivo para uma conta de armazenamento Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se selecionar o Stream para um hub de **eventos**, selecione **o hub de eventos - Configure,** e, em seguida, selecione o espaço de nome do Event Hubs, o centro de eventos e a política de acesso. 
        ![Stream para um centro de eventos](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se selecionar **Enviar para Registar Análises,** selecione o espaço de trabalho do Log Analytics.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Selecione **Guardar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página de definições de **Diagnóstico,** confirme se vê uma nova entrada na tabela definições de **diagnóstico.** 
    ![Definição de diagnóstico na lista](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Também pode ativar a recolha de todas as métricas para o tema. 

## <a name="view-diagnostic-logs-in-azure-storage"></a>Ver registos de diagnóstico no Armazenamento Azure 

1. Assim que ativar uma conta de armazenamento como destino de captura e a Rede de Eventos começar a emitir registos de diagnóstico, deve ver novos recipientes chamados **insights-logs-deliveryfailures** e **insights-logs-publishfailures** na conta de armazenamento. 

    ![Armazenamento - recipientes para registos de diagnóstico](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Ao navegar por um dos contentores, acabará numa bolha em formato JSON. O ficheiro contém entradas de registo para uma falha de entrega ou uma falha de publicação. A trajetória de navegação representa o **ResourceId** do tópico da grelha de eventos e a marca de tempo (nível de minuto) sobre quando as entradas de registo foram emitidas. O ficheiro blob/JSON, que é transferível, adere ao esquema descrito na secção seguinte. 

    [![Arquivo JSON no](./media/enable-diagnostic-logs-topic/select-json.png) armazenamento](./media/enable-diagnostic-logs-topic/select-json.png)
3. Deve ver conteúdo no ficheiro JSON semelhante ao seguinte exemplo: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>Passos seguintes
Para o esquema de log e outras informações conceptuais sobre registos de diagnóstico para tópicos ou domínios, consulte [registos](diagnostic-logs.md)de diagnóstico .
